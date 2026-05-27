# BA-kit Power — Hướng dẫn chi tiết cho Digital Bunkering Platform

## Tổng quan

BA-kit biến Kiro thành một BA workstation có lifecycle rõ ràng. Dự án "Digital Bunkering Platform" sẽ đi qua các bước sau:

```
Intake → Backbone → FRD → User Stories → SRS → Wireframe Handoff → Package
```

Document này hướng dẫn cách sử dụng BA-kit cho dự án bunkering software, bao gồm: cách chuẩn bị input, commands cần chạy, output mong đợi, và cách tận dụng tối đa từng bước.

---

## 1. Chuẩn bị Input

### Raw Input Files (đã có sẵn)

| File | Vai trò trong BA-kit |
|---|---|
| `Introduction to Fuel Types.md` | Domain knowledge — fuel grades, ISO 8217, energy characteristics |
| `Introduction to Regulations Compliance.md` | Compliance requirements — IMO, MARPOL, EU ETS, sanctions |
| `MPA and Singapore Bunkering Standards.md` | Core operational rules — SS 648, SS 600, SS 709, eBDN, B2G |
| `Bunkering Software Landscape Research.md` | Market context — competitors, module patterns, workflows, stakeholders |

### Thông tin bổ sung cần chuẩn bị trước khi chạy Intake

BA-kit sẽ hỏi clarifying questions. Chuẩn bị sẵn câu trả lời cho:

| Câu hỏi dự kiến | Gợi ý trả lời |
|---|---|
| Target users chính là ai? | Bunker suppliers (physical), shipowners/operators, barge crew |
| Platform web hay mobile? | Web-first (responsive), mobile cho barge/vessel crew |
| Region nào ưu tiên? | Singapore-first (MPA compliance), sau đó ARA, global |
| Fuel types nào support? | All: conventional (HSFO, VLSFO, MGO) + LNG + methanol + biofuels + ammonia |
| Có cần trading/ETRM không? | Không — focus operations & compliance, không phải trading/hedging |
| Engagement mode? | `hybrid` (backbone + FRD + stories + selective SRS) |
| Ngôn ngữ deliverables? | Vietnamese |

---

## 2. Lifecycle Steps — Chi tiết từng bước

### Step 1-4: Intake

**Command:**
```
Tạo dự án mới cho Digital Bunkering Platform, sử dụng 4 file tài liệu Maritime làm input
```

**BA-kit sẽ làm:**
1. Đọc 4 file MD làm raw input
2. Parse thành Intake Form chuẩn:
   - Business context (problem, goals, stakeholders)
   - Raw requirements (trích từ domain docs)
   - Screens/UI mentioned
   - Processes/workflows
   - Constraints, compliance requirements
   - Open questions
3. Gap analysis — flag thiếu gì
4. Hỏi 3-8 clarifying questions
5. Generate work plan

**Output files:**
```
plans/digital-bunkering-platform-{date}/
├── PROJECT-HOME.md          ← Dashboard BA
├── 01_intake/
│   ├── intake.md            ← Phiếu tiếp nhận đã normalize
│   └── plan.md              ← Work plan
```

**Tips:**
- Trả lời questions càng cụ thể càng tốt — giảm ambiguity cho backbone
- Nếu BA-kit hỏi về priority, dùng MoSCoW (Must/Should/Could/Won't)
- Confirm scope lock trước khi qua backbone

---

### Step 5: Backbone

**Command:**
```
Tiếp tục dự án digital-bunkering-platform, bước tiếp theo
```
hoặc explicit:
```
ba-start backbone --slug digital-bunkering-platform
```

**BA-kit sẽ tạo Requirements Backbone gồm:**

| Section | Nội dung dự kiến cho bunkering platform |
|---|---|
| Scope lock | Operations & compliance focus, NOT trading/ETRM |
| Business goals | Digitize bunkering ops, MPA compliance, reduce manual work |
| Actors | Supplier Admin, Barge Operator, Vessel Chief Engineer, Compliance Officer, Finance |
| Feature map | 10 modules (see Research file section 9) |
| FR inventory | ~30-50 functional requirements across modules |
| NFR inventory | Performance, security, compliance, availability |
| Story map | Epics → Capabilities → Stories |
| UI/screen coverage | Portal matrix (Supplier portal, Operations portal, Vessel portal) |

**Output files:**
```
plans/digital-bunkering-platform-{date}/
├── 02_backbone/
│   ├── backbone.md           ← Source of truth
│   └── project-memory.md    ← Anti-drift layer
```

**Modules dự kiến trong backbone:**

| Module Slug | Module Name | Priority |
|---|---|---|
| `nomination` | Nomination & Order Management | Must |
| `scheduling` | Scheduling & Dispatch | Must |
| `delivery-ops` | Delivery Operations & Safety Checklists | Must |
| `ebdn` | eBDN Generation & Signing | Must |
| `mfm-integration` | MFM Data Integration | Must |
| `fuel-grades` | Fuel Grade Management (SS 709) | Must |
| `b2g-compliance` | B2G Compliance Reporting | Must |
| `sampling-quality` | Sampling & Quality (SS 524) | Should |
| `sanctions-kyc` | Sanctions & KYC Screening | Should |
| `finance` | Finance & Settlements | Should |
| `analytics` | Analytics & Business Intelligence | Could |
| `messaging` | Integrated Messaging | Could |

---

### Step 6: FRD (per module)

**Command:**
```
ba-start frd --slug digital-bunkering-platform --module ebdn
```

**BA-kit sẽ tạo FRD cho module chỉ định, gồm:**
- Functional overview
- User personas liên quan
- Feature list với MoSCoW priorities
- Workflows (Mermaid diagrams)
- Data requirements
- Business rules
- Integration points
- Acceptance criteria

**Ví dụ FRD cho module `ebdn`:**

```markdown
## Luồng nghiệp vụ

┌──────────────┐     ┌───────────────┐     ┌──────────────┐
│ MFM System   │────▶│ eBDN Engine   │────▶│ Signing      │
│ (data feed)  │     │ (generate)    │     │ (dual-party) │
└──────────────┘     └───────────────┘     └──────┬───────┘
                                                    │
                                           ┌────────▼───────┐
                                           │ B2G Submission  │
                                           │ (MPA platform)  │
                                           └────────────────┘

## Business Rules
- BR-EBDN-001: eBDN PHẢI được generate từ MFM data (SS 648)
- BR-EBDN-002: Cả barge crew VÀ Chief Engineer phải sign trước khi barge departs
- BR-EBDN-003: Fuel type code PHẢI đúng SS 709
- BR-EBDN-004: Submit to MPA trong required timeframe
- BR-EBDN-005: MARPOL sample reference PHẢI có trong eBDN record
```

**Output:**
```
plans/.../03_modules/ebdn/frd.md
```

**Chạy cho mỗi module in-scope.** Thứ tự khuyến nghị:
1. `nomination` → 2. `scheduling` → 3. `delivery-ops` → 4. `mfm-integration` → 5. `ebdn` → 6. `fuel-grades` → 7. `b2g-compliance` → 8. `sampling-quality` → 9. `sanctions-kyc` → 10. `finance`

---

### Step 7: User Stories (per module)

**Command:**
```
ba-start stories --slug digital-bunkering-platform --module ebdn
```

**Output mẫu:**

```markdown
## Epic: eBDN Lifecycle Management

### US-EBDN-001: Generate eBDN from MFM data
Với tư cách là **Barge Operator**, tôi muốn **hệ thống tự động generate eBDN từ MFM data khi delivery hoàn tất** để **không phải nhập liệu thủ công và đảm bảo accuracy**.

**AC:**
- Given delivery completed và MFM final reading available
- When operator triggers eBDN generation
- Then eBDN populated with: vessel name, IMO, supplier, barge, date/time, location, SS 709 fuel code, quantity (MT), MFM serial & reading

### US-EBDN-002: Dual-party digital signing
Với tư cách là **Vessel Chief Engineer**, tôi muốn **ký eBDN bằng digital signature trên thiết bị di động** để **không cần in giấy và ký tay**.

**AC:**
- Given eBDN generated và waiting for signatures
- When Chief Engineer reviews và confirms quantity
- Then digital signature recorded, timestamp logged, eBDN status → "Signed by Vessel"
```

**Output:**
```
plans/.../03_modules/ebdn/user-stories.md
```

---

### Steps 8-11: SRS (per module)

**Command:**
```
ba-start srs --slug digital-bunkering-platform --module ebdn
```

**SRS được chia thành 6 groups:**

| Group | Sections | Khi nào cần |
|---|---|---|
| A | Purpose, Overall Description, Functional Requirements | Always |
| B | Use Case Specifications | Always |
| C | Screen Contract Plus, Screen Inventory | Khi có UI |
| D | NFR, Data Flow, ERD, API Specs | Always |
| E | Screen Descriptions, Wireframe References | Khi có wireframe |
| F | Common Rules, Messages, Test Cases, Glossary | Always |

**Ví dụ SRS content cho module `ebdn`:**

**Group A — Functional Requirements:**
```markdown
### FR-EBDN-001: Auto-populate eBDN from MFM
- Input: MFM final reading, delivery metadata
- Process: Map MFM fields → eBDN template, apply SS 709 fuel code
- Output: Draft eBDN record
- Validation: Quantity > 0, fuel code exists in SS 709 registry

### FR-EBDN-002: Dual-party signing workflow
- States: Draft → Pending Barge Sign → Pending Vessel Sign → Fully Signed
- Timeout: 4 hours from generation to full signature
- Escalation: Notify supervisor if unsigned after 2 hours
```

**Group D — Data Model (ERD):**
```markdown
### Entity: BunkerDeliveryNote
| Field | Type | Constraint | Source |
|---|---|---|---|
| id | UUID | PK | System |
| ebdn_reference | String(20) | Unique | Generated |
| vessel_name | String(255) | Not null | Nomination |
| vessel_imo | String(7) | Not null, IMO format | Nomination |
| barge_name | String(255) | Not null | Scheduling |
| barge_imo | String(7) | Not null | Scheduling |
| delivery_date | OffsetDateTime | Not null | MFM |
| fuel_type_code | String(20) | FK → SS709FuelCode | MFM config |
| quantity_mt | Decimal(10,3) | > 0 | MFM reading |
| mfm_serial | String(50) | Not null | MFM |
| mfm_reading_start | Decimal(12,3) | Not null | MFM |
| mfm_reading_end | Decimal(12,3) | Not null | MFM |
| status | Enum | DRAFT/PENDING/SIGNED/SUBMITTED | System |
| barge_signature | JSON | Nullable | Signing |
| vessel_signature | JSON | Nullable | Signing |
| mpa_submission_id | String(50) | Nullable | B2G |
| submitted_at | OffsetDateTime | Nullable | B2G |
```

**Output:**
```
plans/.../03_modules/ebdn/srs.md
plans/.../03_modules/ebdn/wireframe-input.md  (nếu có UI)
```

---

### Step 9: Wireframe Handoff (optional, per module)

**Command:**
```
ba-start wireframes --slug digital-bunkering-platform --module ebdn
```

**BA-kit tạo:**
- `DESIGN.md` — Design system (colors, typography, components)
- `wireframe-input.md` — Constraint pack cho designer/Stitch
- `wireframe-map.md` — Screen inventory + handoff checklist

**Screens dự kiến cho module `ebdn`:**

| Screen ID | Screen Name | Complexity |
|---|---|---|
| EBDN-01 | eBDN List (supplier view) | Medium |
| EBDN-02 | eBDN Detail / Review | High |
| EBDN-03 | eBDN Signing (mobile, vessel crew) | Medium |
| EBDN-04 | eBDN Submission Status | Low |

---

### Step 12: Package

**Command:**
```
ba-start package --slug digital-bunkering-platform
```

**BA-kit compile tất cả artifacts thành:**
```
plans/.../04_compiled/
├── compiled-frd.html      ← All FRDs merged, formatted
├── compiled-srs.html      ← All SRS merged, formatted
```

Dùng để bàn giao cho stakeholders, dev team, hoặc external parties.

---

## 3. Workflow thực tế — Step by step

```
┌─────────────────────────────────────────────────────────────────────┐
│                        RECOMMENDED WORKFLOW                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  PHASE 1: Foundation (1 session)                                      │
│  ─────────────────────────────────                                    │
│  1. "Tạo dự án mới cho Digital Bunkering Platform"                   │
│     → Intake (Steps 1-4) → scope lock                                │
│  2. "Tiếp tục" → Backbone (Step 5)                                   │
│                                                                       │
│  PHASE 2: Module-by-module (1 session per module)                     │
│  ────────────────────────────────────────────────                     │
│  3. "ba-start frd --module nomination"                                │
│  4. "ba-start stories --module nomination"                            │
│  5. "ba-start srs --module nomination"                                │
│  ... repeat cho mỗi module ...                                        │
│                                                                       │
│  PHASE 3: Design & Package (1-2 sessions)                             │
│  ─────────────────────────────────────────                            │
│  6. "ba-start wireframes --module ebdn"  (cho modules có UI)          │
│  7. "ba-start package"                                                │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Tips & Best Practices

### Khi nào dùng `impact` thay vì edit trực tiếp

```
Đánh giá thay đổi: Thêm requirement ammonia bunkering phải có toxicity monitoring
```

BA-kit sẽ phân tích:
- Modules nào bị ảnh hưởng
- Artifacts nào cần cập nhật
- Impact level (low/medium/high)
- Recommended actions

### Kiểm tra trạng thái bất kỳ lúc nào

```
ba-start status --slug digital-bunkering-platform
```

### Resume sau khi tắt session

```
Tiếp tục dự án digital-bunkering-platform, bước tiếp theo là gì?
```

BA-kit đọc `PROJECT-HOME.md` + artifacts đã có → recommend next step.

### Module priority cho project này

| Priority | Modules | Lý do |
|---|---|---|
| **Sprint 1** | nomination, scheduling, delivery-ops | Core operational flow |
| **Sprint 2** | mfm-integration, ebdn, fuel-grades | Singapore compliance (SS 648, SS 709) |
| **Sprint 3** | b2g-compliance, sampling-quality | Regulatory submission |
| **Sprint 4** | sanctions-kyc, finance | Risk & settlement |
| **Sprint 5** | analytics, messaging | Value-add, not blocking |

---

## 5. Output Directory Structure (Final)

```
plans/
  digital-bunkering-platform-{YYMMDD-HHmm}/
    PROJECT-HOME.md
    01_intake/
      intake.md
      plan.md
    02_backbone/
      backbone.md
      project-memory.md
    03_modules/
      nomination/
        frd.md
        user-stories.md
        srs.md
        wireframe-input.md
      scheduling/
        frd.md
        user-stories.md
        srs.md
      delivery-ops/
        frd.md
        user-stories.md
        srs.md
      mfm-integration/
        frd.md
        user-stories.md
        srs.md
      ebdn/
        frd.md
        user-stories.md
        srs.md
        wireframe-input.md
        wireframe-map.md
      fuel-grades/
        frd.md
        user-stories.md
        srs.md
      b2g-compliance/
        frd.md
        user-stories.md
        srs.md
      sampling-quality/
        frd.md
        user-stories.md
        srs.md
      sanctions-kyc/
        frd.md
        user-stories.md
        srs.md
      finance/
        frd.md
        user-stories.md
        srs.md
      analytics/
        frd.md
        user-stories.md
    04_compiled/
      compiled-frd.html
      compiled-srs.html

designs/
  digital-bunkering-platform/
    DESIGN.md
```

---

## 6. Bắt đầu ngay

Chạy command sau để khởi động BA-kit intake:

```
Tạo dự án mới cho Digital Bunkering Platform — end-to-end platform quản lý 
bunkering operations từ nomination đến settlement, covering all fuel types, 
compliant với MPA Singapore (SS 648, SS 600, SS 709) và international regulations.

Input files:
- Introduction to Fuel Types.md
- Introduction to Regulations Compliance.md
- MPA and Singapore Bunkering Standards.md
- Bunkering Software Landscape Research.md

Target users: Bunker suppliers, shipowners/operators, barge crew, vessel engineers
Platform: Web-first (responsive) + mobile cho field operations
Region: Singapore-first, sau đó global
Mode: hybrid
```

BA-kit sẽ bắt đầu Step 1 (Accept input) và dẫn bạn qua toàn bộ lifecycle.
