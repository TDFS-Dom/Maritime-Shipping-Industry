# SRS — eBDN (Electronic Bunker Delivery Note)

**Version:** 1.0  
**Module:** ebdn  
**Ngày:** 2026-05-27

---

## §1 Mục đích & Phạm vi

### 1.1 Mục đích

Module eBDN tự động tạo Bunker Delivery Note điện tử từ dữ liệu MFM + delivery metadata, hỗ trợ dual-party digital signing (barge crew + vessel Chief Engineer), đảm bảo immutability sau khi ký, và trigger nộp B2G cho MPA. eBDN là **tài liệu pháp lý** ghi nhận giao dịch bunkering theo chuẩn SS 648.

### 1.2 Phạm vi

- Auto-generate eBDN từ MFM data khi delivery hoàn tất
- Dual-party signing workflow (barge → vessel)
- Immutability enforcement sau FULLY_SIGNED
- Reference number generation (sequential per supplier)
- Timeout monitoring + escalation
- MPA B2G submission trigger
- Resubmit workflow khi bị reject

### 1.3 Actors

| Actor | Vai trò | Quyền |
|-------|---------|-------|
| Barge Operator (Crew) | Review + sign eBDN | SIGN (barge side) |
| Vessel Chief Engineer | Review + sign eBDN | SIGN (vessel side) |
| Supplier Admin | Monitor, view history, handle rejections | VIEW, MANAGE |

### 1.4 Dependencies

| Module | Quan hệ | Mô tả |
|--------|---------|--------|
| delivery-ops | Inbound event | `DeliveryCompleted` → trigger eBDN generation |
| mfm-integration | Query | Get session summary (final reading) |
| fuel-grades | Query | Validate fuel code consistency |
| sampling-quality | Query | Get MARPOL sample reference |
| b2g-compliance | Outbound event | `EBDNFullySigned` → trigger B2G submission |

---

## §2 Mô tả tổng thể

### 2.1 State Machine

```mermaid
stateDiagram-v2
    [*] --> DRAFT : Auto-generated from delivery data
    DRAFT --> BARGE_SIGNED : Barge crew signs
    BARGE_SIGNED --> FULLY_SIGNED : Vessel CE signs
    FULLY_SIGNED --> SUBMITTED : B2G submission triggered
    SUBMITTED --> ACKNOWLEDGED : MPA acknowledges
    SUBMITTED --> REJECTED : MPA rejects

    DRAFT --> VOID : Error in generation (rare)
    REJECTED --> RESUBMITTED : Correction submitted

    note right of FULLY_SIGNED : IMMUTABLE from this point
    note right of DRAFT : Timeout: 2h → escalation
    note right of BARGE_SIGNED : Timeout: 2h → escalation
```

**Immutability Rule:** Sau khi status = FULLY_SIGNED, **KHÔNG** field nào có thể thay đổi (trừ status progression SUBMITTED → ACKNOWLEDGED/REJECTED). Enforcement: application-level guard + DB trigger.

### 2.2 eBDN Reference Number Algorithm

```
Format: {SUPPLIER_PREFIX}-{YEAR}-{SEQUENTIAL_6_DIGITS}
Example: BSP-2026-000142

Generation:
1. Get supplier prefix from workspace settings (3 chars uppercase)
2. Get current year (4 digits)
3. Get next sequence for (workspace_id, year) → atomic increment
4. Pad to 6 digits
```

---

## §3 Yêu cầu chức năng chi tiết

### FR-EBDN-001: Generate eBDN from MFM + Delivery Data

**Mô tả:** Tự động tạo eBDN khi delivery hoàn tất.

**Preconditions:**
- Delivery status = COMPLETED
- MFM session status = COMPLETED
- MFM session.total_quantity_mt > 0

**Postconditions:**
- BunkerDeliveryNote record created (status = DRAFT)
- All required fields populated from delivery + MFM + nomination data
- eBDN reference number generated (unique, sequential)
- Timeout monitoring job scheduled (2h)

**Auto-populated Fields Mapping:**

| eBDN Field | Source |
|------------|--------|
| vessel_name | nomination.vessel_name |
| vessel_imo | nomination.vessel_imo |
| barge_name | barge.name (from delivery.barge_id) |
| barge_imo | barge.imo (from delivery.barge_id) |
| supplier_licence | workspace.supplier_licence_number |
| delivery_date | delivery.started_at (date part) |
| delivery_time_start | delivery.started_at |
| delivery_time_end | delivery.completed_at |
| location_lat | delivery.end_location_lat |
| location_long | delivery.end_location_long |
| fuel_type_code | delivery.fuel_type_code |
| quantity_mt | mfm_session.total_quantity_mt |
| mfm_serial | mfm_session.meter_serial |
| mfm_start_reading | mfm_session.start_totalizer |
| mfm_end_reading | mfm_session.end_totalizer |
| sample_reference | sampling_quality.sample_reference (linked to delivery) |

**Error Cases:**

| HTTP | Code | Condition |
|------|------|-----------|
| 422 | MFM_SESSION_INCOMPLETE | MFM session not completed |
| 422 | QUANTITY_ZERO | Calculated quantity = 0 |
| 409 | EBDN_ALREADY_EXISTS | eBDN already generated for this delivery |

---

### FR-EBDN-002: eBDN Required Fields (SS 648 Compliance)

**Mô tả:** eBDN PHẢI chứa tất cả fields theo SS 648 và MPA requirements.

**Required Fields Spec:**

| Field | SS 648 Requirement | Validation |
|-------|-------------------|------------|
| vessel_name | Yes | Not blank, max 255 |
| vessel_imo | Yes | 7 digits |
| barge_name | Yes | Not blank, max 255 |
| barge_imo | Yes | 7 digits |
| supplier_licence | Yes (MPA) | Not blank, max 50 |
| delivery_date | Yes | Not null |
| delivery_time_start | Yes | Not null |
| delivery_time_end | Yes | > time_start |
| location_lat | Yes | Valid coordinate |
| location_long | Yes | Valid coordinate |
| fuel_type_code | Yes (SS 709) | Valid SS 709 code |
| quantity_mt | Yes | > 0, from MFM |
| mfm_serial | Yes (SS 648-2) | Matches registered device |
| mfm_start_reading | Yes | ≥ 0 |
| mfm_end_reading | Yes | > start_reading |
| ebdn_reference | Yes | Unique, auto-generated |
| sample_reference | MPA recommendation | Nullable |

---

### FR-EBDN-003: Dual-party Signing

**Mô tả:** Cả barge crew VÀ vessel Chief Engineer phải ký trước khi barge rời đi.

**Signing Order:** Barge crew FIRST → Vessel CE SECOND (sequential, not parallel)

**Signature Data:**

| Field | Type | Description |
|-------|------|-------------|
| signer_name | String | Full name of signer |
| signer_role | Enum | BARGE_CREW or VESSEL_CE |
| signed_at | OffsetDateTime | Timestamp of signing |
| signature_hash | String | SHA-256 hash of eBDN content at signing time |
| certificate_ref | String | Reference to signing certificate (nullable) |
| device_id | String | Device identifier used for signing |
| ip_address | String | IP at time of signing |

---

### FR-EBDN-004: Signing Timeout & Escalation

**Mô tả:** Background job checks unsigned eBDNs every 15 minutes.

**Escalation Rules:**

| Duration Unsigned | Action |
|-------------------|--------|
| 1 hour | Push notification reminder to pending signer |
| 2 hours | Escalation alert to Supplier Admin |
| 4 hours | Critical alert + SMS to Supplier Admin |
| 8 hours | Compliance Officer notified |

---

## §4 Use Case Specifications

### UC-EBDN-01: Generate & Sign eBDN

**Actor:** System (auto-generate) → Barge Operator → Vessel CE  
**Goal:** Complete eBDN documentation before barge departure

**Main Success Scenario:**

1. Delivery completes → System receives `DeliveryCompleted` event
2. System queries MFM session summary
3. System queries delivery metadata
4. System generates eBDN reference number
5. System populates all required fields
6. System creates BunkerDeliveryNote (DRAFT)
7. System notifies Barge Operator: "eBDN ready for review & signing"
8. Barge Operator opens eBDN on mobile app
9. Barge Operator reviews all fields (read-only, auto-populated)
10. Barge Operator taps "Sign"
11. System captures signature data (hash, timestamp, device)
12. System creates Signature record (BARGE_CREW)
13. Status → BARGE_SIGNED
14. System notifies Vessel CE: "eBDN pending your signature"
15. Vessel CE opens eBDN on mobile app
16. Vessel CE reviews content
17. Vessel CE taps "Sign"
18. System captures signature data
19. System creates Signature record (VESSEL_CE)
20. Status → FULLY_SIGNED
21. System locks eBDN (immutable)
22. System publishes `EBDNFullySigned` event → B2G compliance module

**Exception Flows:**

- **10a.** Barge Operator doesn't sign within 2h → Escalation to Supplier Admin
- **17a.** Vessel CE disputes quantity → Cannot modify; must raise dispute separately
- **2a.** MFM session incomplete → eBDN generation blocked, alert to operator

---

### UC-EBDN-02: Resubmit After MPA Rejection

**Actor:** Supplier Admin  
**Goal:** Fix and resubmit rejected eBDN

**Main Success Scenario:**

1. MPA rejects eBDN submission
2. Supplier Admin receives notification with rejection reason
3. Supplier Admin reviews rejection reason
4. Supplier Admin initiates "Create Correction"
5. System creates new eBDN (DRAFT) referencing original
6. If data error: Supplier Admin can adjust fields (new eBDN, not editing original)
7. New eBDN goes through dual-sign again
8. New eBDN submitted to MPA

---

## §5 Data Model

### 5.1 Entity: BunkerDeliveryNote

```sql
CREATE TABLE bunker_delivery_notes (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id        UUID NOT NULL REFERENCES workspaces(id),
    ebdn_reference      VARCHAR(30) NOT NULL UNIQUE,
    delivery_id         UUID NOT NULL REFERENCES deliveries(id),
    
    -- Vessel info
    vessel_name         VARCHAR(255) NOT NULL,
    vessel_imo          VARCHAR(7) NOT NULL,
    
    -- Barge info
    barge_name          VARCHAR(255) NOT NULL,
    barge_imo           VARCHAR(7) NOT NULL,
    
    -- Supplier info
    supplier_licence    VARCHAR(50) NOT NULL,
    
    -- Delivery details
    delivery_date       DATE NOT NULL,
    delivery_time_start TIMESTAMPTZ NOT NULL,
    delivery_time_end   TIMESTAMPTZ NOT NULL,
    location_lat        DECIMAL(9,6) NOT NULL,
    location_long       DECIMAL(9,6) NOT NULL,
    
    -- Fuel & quantity
    fuel_type_code      VARCHAR(20) NOT NULL,
    quantity_mt         DECIMAL(10,3) NOT NULL CHECK (quantity_mt > 0),
    
    -- MFM data
    mfm_serial          VARCHAR(50) NOT NULL,
    mfm_start_reading   DECIMAL(12,3) NOT NULL,
    mfm_end_reading     DECIMAL(12,3) NOT NULL CHECK (mfm_end_reading > mfm_start_reading),
    
    -- Sample
    sample_reference    VARCHAR(50),
    
    -- Status & signing
    status              VARCHAR(20) NOT NULL DEFAULT 'DRAFT',
    signed_by_barge_at  TIMESTAMPTZ,
    signed_by_vessel_at TIMESTAMPTZ,
    
    -- Immutability hash (computed at FULLY_SIGNED)
    content_hash        VARCHAR(64),  -- SHA-256 of all content fields
    
    -- Correction reference
    correction_of_id    UUID REFERENCES bunker_delivery_notes(id),
    
    -- Timestamps
    created_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),

    CONSTRAINT chk_ebdn_status CHECK (status IN ('DRAFT','BARGE_SIGNED','FULLY_SIGNED','SUBMITTED','ACKNOWLEDGED','REJECTED','VOID','RESUBMITTED'))
);
```

### 5.2 Entity: Signature

```sql
CREATE TABLE ebdn_signatures (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ebdn_id         UUID NOT NULL REFERENCES bunker_delivery_notes(id),
    signer_role     VARCHAR(20) NOT NULL,  -- BARGE_CREW, VESSEL_CE
    signer_id       UUID NOT NULL REFERENCES users(id),
    signer_name     VARCHAR(255) NOT NULL,
    signed_at       TIMESTAMPTZ NOT NULL,
    signature_hash  VARCHAR(64) NOT NULL,   -- SHA-256 of eBDN content at signing time
    certificate_ref VARCHAR(100),
    device_id       VARCHAR(100),
    ip_address      VARCHAR(45),
    
    CONSTRAINT chk_signer_role CHECK (signer_role IN ('BARGE_CREW','VESSEL_CE')),
    CONSTRAINT uq_ebdn_signer_role UNIQUE (ebdn_id, signer_role)
);
```

### 5.3 Entity: EBDNSubmission

```sql
CREATE TABLE ebdn_submissions (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ebdn_id         UUID NOT NULL REFERENCES bunker_delivery_notes(id),
    submission_type VARCHAR(20) NOT NULL DEFAULT 'INITIAL',  -- INITIAL, RESUBMISSION
    submitted_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    status          VARCHAR(20) NOT NULL DEFAULT 'PENDING',  -- PENDING, SUBMITTED, ACKNOWLEDGED, REJECTED, FAILED
    mpa_reference   VARCHAR(50),
    rejection_reason TEXT,
    response_at     TIMESTAMPTZ,
    retry_count     INTEGER NOT NULL DEFAULT 0,
    next_retry_at   TIMESTAMPTZ,
    error_details   JSONB
);
```

### 5.4 Entity: EBDNSequence (Reference Number)

```sql
CREATE TABLE ebdn_sequences (
    workspace_id    UUID NOT NULL REFERENCES workspaces(id),
    year            INTEGER NOT NULL,
    current_value   INTEGER NOT NULL DEFAULT 0,
    PRIMARY KEY (workspace_id, year)
);
```

### 5.5 Indexes

```sql
CREATE INDEX idx_ebdn_workspace_status ON bunker_delivery_notes(workspace_id, status);
CREATE INDEX idx_ebdn_delivery ON bunker_delivery_notes(delivery_id);
CREATE INDEX idx_ebdn_reference ON bunker_delivery_notes(ebdn_reference);
CREATE INDEX idx_ebdn_vessel_imo ON bunker_delivery_notes(vessel_imo);
CREATE INDEX idx_ebdn_signatures_ebdn ON ebdn_signatures(ebdn_id);
CREATE INDEX idx_ebdn_submissions_ebdn ON ebdn_submissions(ebdn_id, submitted_at DESC);

-- Immutability trigger
CREATE OR REPLACE FUNCTION prevent_ebdn_modification()
RETURNS TRIGGER AS $$
BEGIN
    IF OLD.status IN ('FULLY_SIGNED','SUBMITTED','ACKNOWLEDGED') THEN
        IF NEW.status != OLD.status THEN
            -- Allow only status progression
            RETURN NEW;
        END IF;
        RAISE EXCEPTION 'Cannot modify eBDN after signing (status: %)', OLD.status;
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_ebdn_immutability
    BEFORE UPDATE ON bunker_delivery_notes
    FOR EACH ROW
    EXECUTE FUNCTION prevent_ebdn_modification();
```

---

## §6 API Specifications

### 6.1 POST /api/v1/ebdns/generate

**Mô tả:** Generate eBDN from completed delivery (usually triggered by system event, but exposed for manual trigger)  
**Auth:** Bearer JWT, role = SUPPLIER_ADMIN | BARGE_OPERATOR

**Request Body:**
```json
{
  "delivery_id": "01902a3b-4c5d-7e8f-delivery001"
}
```

**Response (201 Created):** `EBDNDto` (full detail)

---

### 6.2 GET /api/v1/ebdns

**Mô tả:** List eBDNs  
**Auth:** Bearer JWT  
**Query Params:** page, size, status, vessel_imo, from_date, to_date, barge_imo

**Response (200):** `PaginatedResponse<EBDNSummaryDto>`

---

### 6.3 GET /api/v1/ebdns/{id}

**Mô tả:** Get eBDN detail  
**Auth:** Bearer JWT

**Response (200):** `EBDNDto` including signatures list

---

### 6.4 POST /api/v1/ebdns/{id}/sign

**Mô tả:** Sign eBDN (barge crew or vessel CE)  
**Auth:** Bearer JWT, role = BARGE_OPERATOR | VESSEL_CE

**Request Body:**
```json
{
  "signer_name": "Captain John Smith",
  "device_id": "iPhone-14-Pro-ABC123"
}
```

**Response (200):** Updated `EBDNDto`

**Error Cases:**
| HTTP | Code | Condition |
|------|------|-----------|
| 409 | INVALID_STATE_TRANSITION | Status doesn't allow signing |
| 409 | ALREADY_SIGNED | This role already signed |
| 422 | WRONG_SIGNING_ORDER | Vessel CE trying to sign before barge crew |
| 403 | NOT_AUTHORIZED_SIGNER | User not assigned to this delivery |

**Signing Logic:**
```
IF current_status = DRAFT AND user.role = BARGE_OPERATOR:
    sign + transition to BARGE_SIGNED
ELIF current_status = BARGE_SIGNED AND user.role = VESSEL_CE:
    sign + transition to FULLY_SIGNED + lock record + trigger B2G
ELSE:
    reject with appropriate error
```

---

### 6.5 POST /api/v1/ebdns/{id}/resubmit

**Mô tả:** Create correction eBDN for rejected submission  
**Auth:** Bearer JWT, role = SUPPLIER_ADMIN

**Request Body:**
```json
{
  "correction_notes": "Fixed fuel code typo"
}
```

**Response (201):** New `EBDNDto` (DRAFT, references original via `correction_of_id`)

---

## §7 Yêu cầu phi chức năng

| ID | Category | Requirement |
|----|----------|-------------|
| NFR-EBDN-01 | Performance | eBDN generation < 3 seconds (NFR-01 from backbone) |
| NFR-EBDN-02 | Security | Digital signatures non-repudiable, timestamped (NFR-06) |
| NFR-EBDN-03 | Compliance | Data retention 5 years (NFR-08) |
| NFR-EBDN-04 | Data Integrity | FULLY_SIGNED eBDN immutable — DB trigger enforced |
| NFR-EBDN-05 | Availability | Signing workflow must work offline (queue + sync) |
| NFR-EBDN-06 | Audit | Complete audit trail: who signed, when, from where |
| NFR-EBDN-07 | Compliance | Content hash (SHA-256) computed at FULLY_SIGNED for tamper detection |

---

## §8 Quy tắc nghiệp vụ

| ID | Quy tắc | Implementation Notes |
|----|---------|---------------------|
| BR-EBDN-001 | MFM-only quantity | No manual quantity input endpoint. `quantity_mt` = MFM session summary. |
| BR-EBDN-002 | Required fields SS 648 | Validation at generation time. Missing any → generation fails. |
| BR-EBDN-003 | Dual-sign before departure | Signing order enforced: BARGE_CREW first, VESSEL_CE second. |
| BR-EBDN-004 | Signing timeout 2h | Scheduled job every 15 min: query eBDNs WHERE status IN (DRAFT, BARGE_SIGNED) AND (NOW() - created_at > interval '2h' OR NOW() - signed_by_barge_at > interval '2h'). Send escalation. |
| BR-EBDN-005 | Reference auto-generated | Atomic: `UPDATE ebdn_sequences SET current_value = current_value + 1 ... RETURNING current_value`. Race-condition safe. |
| BR-EBDN-006 | Immutable after FULLY_SIGNED | DB trigger prevents UPDATE on content fields. Application guard as defense-in-depth. |
| BR-EBDN-007 | Content hash | At FULLY_SIGNED: SHA-256(concat all content fields in defined order). Stored in `content_hash`. Verifiable later for tamper detection. |
| BR-EBDN-008 | B2G trigger | Domain event `EBDNFullySigned` dispatched → b2g-compliance module consumes → submits to MPA. |
