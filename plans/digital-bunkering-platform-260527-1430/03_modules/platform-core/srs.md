# SRS — Platform Core (Shared Infrastructure)

**Version:** 1.0  
**Module:** platform-core  
**Ngày:** 2026-05-28  
**Trạng thái:** Draft  
**Tác giả:** Digital Bunkering Platform Team

---

## Mục lục

- [§1 Mục đích & Phạm vi](#1-mục-đích--phạm-vi)
- [§2 Authentication & Authorization](#2-authentication--authorization)
- [§3 Shared Entities](#3-shared-entities)
- [§4 Indexes](#4-indexes)
- [§5 Row-Level Security](#5-row-level-security-multi-tenant-isolation)
- [§6 Common API Patterns](#6-common-api-patterns)
- [§7 Platform APIs](#7-platform-apis)
- [§8 Yêu cầu phi chức năng (NFR)](#8-yêu-cầu-phi-chức-năng-nfr)

---

## §1 Mục đích & Phạm vi

### 1.1 Mục đích

Module **platform-core** định nghĩa các thực thể dùng chung (shared entities), cơ chế xác thực/phân quyền (authentication/authorization), và hạ tầng kỹ thuật chung mà TẤT CẢ feature modules khác trong hệ thống Digital Bunkering Platform đều phụ thuộc.

### 1.2 Phạm vi

| Trong phạm vi | Ngoài phạm vi |
|---------------|---------------|
| Quản lý Workspace (multi-tenant) | Business logic của từng module riêng (nomination, eBDN, scheduling...) |
| Quản lý User & Role (RBAC) | UI/Frontend implementation |
| JWT Authentication & Token lifecycle | Payment gateway integration |
| Shared entities: Barge, Vessel, Port | MFM hardware integration |
| Notification infrastructure | External regulatory API connectors (MPA) |
| Audit logging framework | Analytics & reporting dashboards |
| Row-Level Security (RLS) policies | Module-specific state machines |
| Common API patterns & conventions | — |

### 1.3 Đối tượng sử dụng tài liệu

- Backend developers (implement shared services)
- Frontend developers (consume shared APIs)
- DevOps engineers (deploy & configure infrastructure)
- QA engineers (test shared functionalities)
- Architects (review cross-cutting concerns)

### 1.4 Modules phụ thuộc

Tất cả modules sau phụ thuộc vào platform-core:

| Module | Phụ thuộc chính |
|--------|-----------------|
| nomination | User, Workspace, Vessel, Port |
| scheduling | User, Workspace, Barge, Port |
| delivery-ops | User, Workspace, Barge, Vessel |
| ebdn | User, Workspace, Barge, Vessel, AuditLog |
| finance | User, Workspace, Notification |
| b2g-compliance | User, Workspace, Vessel, AuditLog |
| mfm-integration | Workspace, Barge |
| sanctions-kyc | Workspace, Vessel |
| analytics | User, Workspace (read-only) |
| messaging | User, Workspace, Notification |

---

## §2 Authentication & Authorization

### 2.1 Cấu trúc JWT Token

| Thuộc tính | Access Token | Refresh Token |
|------------|-------------|---------------|
| Algorithm | RS256 (asymmetric) | HS256 (symmetric) |
| Thời hạn | 15 phút | 7 ngày |
| Storage (client) | Memory only | HttpOnly Secure Cookie |
| Revocable | Không (short-lived) | Có (blacklist on logout) |

**Claims trong Access Token:**

```json
{
  "sub": "user_id (UUID)",
  "workspace_id": "workspace_id (UUID)",
  "roles": ["ROLE_SUPPLIER_ADMIN", "ROLE_SCHEDULER"],
  "permissions": ["nominations.create", "schedules.manage", "barges.read"],
  "iat": 1716880000,
  "exp": 1716880900,
  "iss": "bunkering-platform",
  "jti": "unique-token-id (UUID)"
}
```

**Header format:**
```
Authorization: Bearer {access_token}
```

### 2.2 Định nghĩa Roles

| Role | Code | Mô tả | Portal Access |
|------|------|--------|---------------|
| Supplier Admin | ROLE_SUPPLIER_ADMIN | Toàn quyền trong workspace supplier. Quản lý users, barges, cấu hình hệ thống. | P-SUPP |
| Barge Operator | ROLE_BARGE_OPERATOR | Thực hiện delivery operations, checklists, ký eBDN tại hiện trường. | P-OPS |
| Vessel Chief Engineer | ROLE_VESSEL_CE | Review & ký xác nhận eBDN từ phía vessel. | P-VESSEL |
| Buyer | ROLE_BUYER | Tạo nominations, theo dõi delivery, xác nhận nhận hàng. | P-BUYER |
| Compliance Officer | ROLE_COMPLIANCE | Giám sát B2G compliance, sanctions screening, audit trail. | P-SUPP |
| Finance | ROLE_FINANCE | Quản lý invoicing, payments, settlements, credit notes. | P-SUPP |
| Scheduler | ROLE_SCHEDULER | Lập lịch barge, dispatch, tối ưu routes. | P-SUPP |

### 2.3 Mô hình phân quyền (Permission Model)

**Phương pháp:** RBAC (Role-Based Access Control)

**Format permission:** `{module}.{action}`

Ví dụ:
- `nominations.create` — Tạo nomination mới
- `nominations.confirm` — Xác nhận nomination
- `ebdn.sign` — Ký eBDN
- `schedules.manage` — Quản lý lịch barge
- `barges.write` — Tạo/sửa thông tin barge
- `vessels.screen` — Chạy sanctions screening
- `finance.invoice` — Tạo invoice
- `users.manage` — Quản lý users trong workspace

**Workspace scoping:**
- Mọi query đều được filter theo `workspace_id` từ JWT claims
- Không có cross-workspace access trừ khi có explicit system permission

**Multi-tenant isolation:**
- Row-Level Security (RLS) trong PostgreSQL
- Application-level enforcement (double-check)
- Mỗi request SET LOCAL `app.workspace_id` trước khi thực thi queries

### 2.4 Authentication APIs

#### POST /api/v1/auth/login

**Mô tả:** Đăng nhập bằng email + password, trả về cặp tokens.

**Request:**
```json
{
  "email": "operator@supplier.com",
  "password": "securePassword123!"
}
```

**Response (200):**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 900,
  "refresh_token": "dGhpcyBpcyBhIHJlZnJl..."
}
```

**Error cases:**
| HTTP | Code | Khi nào |
|------|------|---------|
| 400 | VALIDATION_ERROR | Email/password trống |
| 401 | INVALID_CREDENTIALS | Sai email hoặc password (không phân biệt) |
| 423 | ACCOUNT_LOCKED | Tài khoản bị khóa sau 5 lần đăng nhập sai |

**Quy tắc bảo mật:**
- Không leak thông tin email tồn tại hay không
- Rate limit: 5 attempts / 15 phút / IP
- Account lock sau 5 consecutive failures → unlock sau 30 phút hoặc admin unlock

#### POST /api/v1/auth/refresh

**Mô tả:** Dùng refresh token để lấy access token mới.

**Request:**
```json
{
  "refresh_token": "dGhpcyBpcyBhIHJlZnJl..."
}
```

**Response (200):**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIs...(new)",
  "token_type": "Bearer",
  "expires_in": 900
}
```

**Error cases:**
| HTTP | Code | Khi nào |
|------|------|---------|
| 401 | INVALID_REFRESH_TOKEN | Token không hợp lệ hoặc đã bị revoke |
| 401 | REFRESH_TOKEN_EXPIRED | Token hết hạn (>7 ngày) |

#### POST /api/v1/auth/logout

**Mô tả:** Invalidate refresh token hiện tại. Access token vẫn valid đến khi hết hạn (max 15 phút).

**Request:** Header `Authorization: Bearer {access_token}`

**Response:** 204 No Content

**Side effect:** Refresh token được thêm vào blacklist.

#### GET /api/v1/auth/me

**Mô tả:** Lấy thông tin profile & roles của user đang đăng nhập.

**Response (200):**
```json
{
  "id": "uuid",
  "email": "operator@supplier.com",
  "full_name": "Nguyễn Văn A",
  "workspace": {
    "id": "uuid",
    "name": "ABC Bunkering Pte Ltd",
    "slug": "abc-bunkering"
  },
  "roles": ["ROLE_BARGE_OPERATOR"],
  "permissions": ["deliveries.execute", "ebdn.sign", "checklists.complete"],
  "avatar_url": null,
  "last_login_at": "2026-05-28T08:30:00.000+08:00"
}
```

---

## §3 Shared Entities

### 3.1 Entity: Workspace (Tenant)

**Mô tả:** Đơn vị cách ly dữ liệu chính (tenant). Mỗi workspace đại diện cho một tổ chức (supplier, buyer, operator). Mọi dữ liệu business đều thuộc về một workspace.

**Schema:**

```sql
CREATE TABLE workspaces (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            VARCHAR(255) NOT NULL,
    slug            VARCHAR(100) NOT NULL UNIQUE,
    supplier_licence_number VARCHAR(50),  -- MPA licence (nullable cho non-supplier tenants)
    supplier_prefix VARCHAR(3),           -- Prefix cho eBDN reference generation
    country         VARCHAR(3) NOT NULL DEFAULT 'SGP',  -- ISO 3166-1 alpha-3
    timezone        VARCHAR(50) NOT NULL DEFAULT 'Asia/Singapore',
    settings        JSONB NOT NULL DEFAULT '{}',
    status          VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ,
    
    CONSTRAINT chk_workspace_status CHECK (status IN ('ACTIVE','SUSPENDED','DEACTIVATED'))
);
```

**Trạng thái (State Machine):**

```
ACTIVE → SUSPENDED → ACTIVE (reactivate)
ACTIVE → DEACTIVATED (terminal)
SUSPENDED → DEACTIVATED (terminal)
```

| Trạng thái | Mô tả |
|------------|--------|
| ACTIVE | Hoạt động bình thường, mọi chức năng khả dụng |
| SUSPENDED | Tạm ngưng — users không thể đăng nhập, dữ liệu giữ nguyên |
| DEACTIVATED | Vô hiệu hóa vĩnh viễn — soft-deleted, không khôi phục |

**Settings (JSONB) — cấu trúc mẫu:**

```json
{
  "default_currency": "USD",
  "ebdn_auto_numbering": true,
  "ebdn_prefix_format": "{supplier_prefix}-{YYYY}-{SEQ:5}",
  "require_mfm_data": true,
  "notification_channels": ["IN_APP", "EMAIL"],
  "sanctions_auto_screen": true,
  "delivery_checklist_version": "v2.1"
}
```

### 3.2 Entity: User

**Mô tả:** Người dùng hệ thống. Mỗi user thuộc về đúng 1 workspace và có thể có nhiều roles.

**Schema:**

```sql
CREATE TABLE users (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id    UUID NOT NULL REFERENCES workspaces(id),
    email           VARCHAR(255) NOT NULL,
    password_hash   VARCHAR(255) NOT NULL,
    full_name       VARCHAR(255) NOT NULL,
    phone           VARCHAR(20),
    avatar_url      VARCHAR(500),
    status          VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    email_verified  BOOLEAN NOT NULL DEFAULT FALSE,
    last_login_at   TIMESTAMPTZ,
    failed_login_count INTEGER NOT NULL DEFAULT 0,
    locked_until    TIMESTAMPTZ,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ,
    
    CONSTRAINT uq_user_email_workspace UNIQUE (workspace_id, email),
    CONSTRAINT chk_user_status CHECK (status IN ('ACTIVE','INACTIVE','LOCKED'))
);

CREATE TABLE user_roles (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID NOT NULL REFERENCES users(id),
    role_code       VARCHAR(50) NOT NULL,
    granted_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    granted_by      UUID REFERENCES users(id),
    
    CONSTRAINT uq_user_role UNIQUE (user_id, role_code)
);
```

**Trạng thái User:**

| Trạng thái | Mô tả |
|------------|--------|
| ACTIVE | Hoạt động bình thường |
| INACTIVE | Bị vô hiệu hóa bởi admin (không thể đăng nhập) |
| LOCKED | Tự động khóa sau 5 lần đăng nhập sai (auto-unlock sau 30 phút) |

### 3.3 Entity: Barge

**Mô tả:** Tàu barge cung cấp nhiên liệu. Thuộc workspace của supplier. Được tham chiếu bởi modules scheduling, delivery-ops, ebdn, mfm-integration.

**Schema:**

```sql
CREATE TABLE barges (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id        UUID NOT NULL REFERENCES workspaces(id),
    name                VARCHAR(255) NOT NULL,
    imo                 VARCHAR(7) NOT NULL,
    registration_number VARCHAR(50),
    capacity_mt         DECIMAL(10,2),
    fuel_certifications VARCHAR[] NOT NULL DEFAULT '{}',  -- Array SS 709 fuel codes certified
    mfm_serial          VARCHAR(50),  -- Registered MFM serial number
    mfm_calibration_date DATE,        -- Ngày hiệu chuẩn MFM gần nhất
    status              VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    home_port           VARCHAR(100),
    owner               VARCHAR(255),
    notes               TEXT,
    created_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at          TIMESTAMPTZ,
    
    CONSTRAINT uq_barge_imo_workspace UNIQUE (workspace_id, imo),
    CONSTRAINT chk_barge_status CHECK (status IN ('ACTIVE','MAINTENANCE','DECOMMISSIONED'))
);
```

**Trạng thái Barge:**

| Trạng thái | Mô tả | Có thể dispatch? |
|------------|--------|-------------------|
| ACTIVE | Sẵn sàng hoạt động | Có |
| MAINTENANCE | Đang bảo trì/sửa chữa | Không |
| DECOMMISSIONED | Ngừng hoạt động vĩnh viễn | Không |

**Validation rules:**
- `imo`: Đúng 7 ký tự số (IMO number format)
- `capacity_mt`: > 0, tối đa 99999999.99
- `fuel_certifications`: Mỗi phần tử phải nằm trong danh sách fuel codes hợp lệ (ref: fuel-grades module)
- `mfm_serial`: Unique trong workspace nếu có giá trị

### 3.4 Entity: Vessel (KYC Profile)

**Mô tả:** Hồ sơ vessel (tàu nhận nhiên liệu). Chứa thông tin KYC và kết quả sanctions screening. Được tham chiếu bởi modules nomination, delivery-ops, ebdn, sanctions-kyc, b2g-compliance.

**Schema:**

```sql
CREATE TABLE vessels (
    id                      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id            UUID NOT NULL REFERENCES workspaces(id),
    imo                     VARCHAR(7) NOT NULL,
    name                    VARCHAR(255) NOT NULL,
    flag_state              VARCHAR(3),  -- ISO 3166-1 alpha-3
    vessel_type             VARCHAR(50),
    gross_tonnage           DECIMAL(10,2),
    deadweight_tonnage      DECIMAL(10,2),
    beneficial_owner        VARCHAR(255),
    operator                VARCHAR(255),
    manager                 VARCHAR(255),
    classification_society  VARCHAR(100),
    country_of_registration VARCHAR(3),
    built_year              INTEGER,
    last_screened_at        TIMESTAMPTZ,
    last_screening_result   VARCHAR(20),  -- CLEAR, FLAGGED, SANCTIONED
    screening_provider      VARCHAR(50),  -- e.g. 'WINDWARD', 'POLE_STAR'
    risk_score              INTEGER CHECK (risk_score BETWEEN 0 AND 100),
    status                  VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    notes                   TEXT,
    created_at              TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at              TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    
    CONSTRAINT uq_vessel_imo_workspace UNIQUE (workspace_id, imo)
);
```

**Screening Result values:**

| Giá trị | Mô tả | Hành động |
|---------|--------|-----------|
| CLEAR | Không phát hiện sanctions/risk | Cho phép delivery |
| FLAGGED | Có cảnh báo, cần review manual | Tạm hold cho đến khi compliance officer review |
| SANCTIONED | Nằm trong danh sách sanctions | Block delivery, báo compliance |

**Validation rules:**
- `imo`: Đúng 7 ký tự số
- `flag_state`: ISO 3166-1 alpha-3 code hợp lệ
- `risk_score`: 0-100, tính bởi hệ thống (không cho user nhập trực tiếp)
- `built_year`: 1900 - current year

### 3.5 Entity: Port

**Mô tả:** Thông tin cảng. Dữ liệu tham chiếu (reference data) — không thuộc workspace cụ thể mà dùng chung toàn hệ thống. Được tham chiếu bởi nomination, scheduling, b2g-compliance.

**Schema:**

```sql
CREATE TABLE ports (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code            VARCHAR(10) NOT NULL UNIQUE,  -- UN/LOCODE (e.g. SGSIN, MYJOHOR)
    name            VARCHAR(255) NOT NULL,
    country         VARCHAR(3) NOT NULL,  -- ISO 3166-1 alpha-3
    latitude        DECIMAL(9,6),
    longitude       DECIMAL(9,6),
    timezone        VARCHAR(50),
    is_eca          BOOLEAN NOT NULL DEFAULT FALSE,  -- Inside Emission Control Area
    anchorage_zones JSONB DEFAULT '[]',  -- Danh sách vùng neo đậu
    status          VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Anchorage zones (JSONB) — cấu trúc mẫu:**

```json
[
  {
    "code": "ERSA",
    "name": "Eastern Raffles Anchorage",
    "latitude": 1.2500,
    "longitude": 103.9800
  },
  {
    "code": "WRSA",
    "name": "Western Raffles Anchorage",
    "latitude": 1.2300,
    "longitude": 103.7900
  }
]
```

**Lưu ý:**
- Port data được seed từ UN/LOCODE database
- `is_eca` ảnh hưởng đến fuel type restrictions (ref: fuel-grades module)
- Không có `workspace_id` — đây là system-level reference data

### 3.6 Entity: Notification

**Mô tả:** Thông báo gửi đến user. Hỗ trợ nhiều kênh (in-app, email, push, SMS). Framework notification — các module khác dispatch notification qua notification service.

**Schema:**

```sql
CREATE TABLE notifications (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id    UUID NOT NULL REFERENCES workspaces(id),
    recipient_id    UUID NOT NULL REFERENCES users(id),
    type            VARCHAR(50) NOT NULL,
    title           VARCHAR(255) NOT NULL,
    message         TEXT NOT NULL,
    reference_type  VARCHAR(30),  -- NOMINATION, DELIVERY, EBDN, SCHEDULE, ALERT
    reference_id    UUID,
    channel         VARCHAR(20) NOT NULL DEFAULT 'IN_APP',
    priority        VARCHAR(10) NOT NULL DEFAULT 'NORMAL',  -- LOW, NORMAL, HIGH, URGENT
    read            BOOLEAN NOT NULL DEFAULT FALSE,
    read_at         TIMESTAMPTZ,
    sent_at         TIMESTAMPTZ,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Notification Types:**

| Type | Trigger | Mô tả | Priority |
|------|---------|--------|----------|
| NOMINATION_NEW | Nomination created | Buyer tạo nomination mới | NORMAL |
| NOMINATION_CONFIRMED | Nomination confirmed | Supplier xác nhận nomination | NORMAL |
| DELIVERY_STARTED | Delivery begins | Barge bắt đầu bơm nhiên liệu | HIGH |
| DELIVERY_COMPLETED | Delivery ends | Delivery hoàn tất | NORMAL |
| EBDN_PENDING_SIGN | eBDN ready for signature | eBDN chờ ký | HIGH |
| EBDN_SIGNED | eBDN signed | eBDN đã được ký | NORMAL |
| DEADLINE_WARNING | Deadline approaching | Cảnh báo deadline sắp đến | HIGH |
| SANCTIONS_ALERT | Screening flagged | Vessel bị flag sanctions | URGENT |
| SCHEDULE_CONFLICT | Schedule overlap | Phát hiện xung đột lịch | HIGH |
| SYSTEM_MAINTENANCE | System event | Thông báo bảo trì hệ thống | LOW |

**Channels:**

| Channel | Mô tả | Delivery guarantee |
|---------|--------|-------------------|
| IN_APP | Hiển thị trong app (badge counter) | Best effort, real-time |
| EMAIL | Gửi email qua SMTP/SES | At-least-once (async queue) |
| PUSH | Push notification (mobile/browser) | Best effort |
| SMS | Tin nhắn SMS (cho URGENT only) | At-least-once |

### 3.7 Entity: AuditLog

**Mô tả:** Ghi lại mọi thao tác thay đổi dữ liệu (CUD) trong hệ thống. Dùng cho compliance, dispute resolution, và forensic analysis. Retention tối thiểu 7 năm theo quy định MPA.

**Schema:**

```sql
CREATE TABLE audit_logs (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id    UUID NOT NULL REFERENCES workspaces(id),
    actor_id        UUID REFERENCES users(id),
    actor_type      VARCHAR(20) NOT NULL DEFAULT 'USER',
    action          VARCHAR(50) NOT NULL,
    entity_type     VARCHAR(50) NOT NULL,
    entity_id       UUID NOT NULL,
    old_values      JSONB,
    new_values      JSONB,
    metadata        JSONB DEFAULT '{}',
    ip_address      VARCHAR(45),
    user_agent      TEXT,
    occurred_at     TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Actor Types:**

| Giá trị | Mô tả |
|---------|--------|
| USER | Người dùng thực hiện thao tác |
| SYSTEM | Hệ thống tự động (scheduled jobs, triggers) |
| EXTERNAL | Bên ngoài (webhook, API integration) |

**Actions:**

| Action | Mô tả |
|--------|--------|
| CREATE | Tạo mới entity |
| UPDATE | Cập nhật entity |
| DELETE | Soft-delete entity |
| SIGN | Ký điện tử (eBDN, checklists) |
| SUBMIT | Submit cho approval |
| CONFIRM | Xác nhận/approve |
| REJECT | Từ chối |
| CANCEL | Hủy |
| SCREEN | Chạy sanctions screening |
| EXPORT | Export dữ liệu (B2G submission) |

**Entity Types:** NOMINATION, DELIVERY, EBDN, SCHEDULE, BARGE, VESSEL, USER, WORKSPACE, INVOICE, PAYMENT, ALERT

**Metadata (JSONB) — thông tin bổ sung theo context:**

```json
{
  "reason": "Quantity discrepancy > 0.5%",
  "source_module": "delivery-ops",
  "correlation_id": "uuid-of-related-transaction",
  "signature_hash": "sha256:abc123..."
}
```

**Quy tắc quan trọng:**
- Audit logs KHÔNG BAO GIỜ bị xóa (no soft-delete, no hard-delete)
- Partition theo tháng cho performance (`audit_logs_2026_05`, `audit_logs_2026_06`...)
- Retention: tối thiểu 7 năm (MPA regulatory requirement)
- Index cho query patterns phổ biến (xem §4)

---

## §4 Indexes (Tất cả shared entities)

```sql
-- ============================================
-- WORKSPACE INDEXES
-- ============================================
CREATE INDEX idx_workspaces_slug ON workspaces(slug) WHERE deleted_at IS NULL;
CREATE INDEX idx_workspaces_status ON workspaces(status) WHERE deleted_at IS NULL;

-- ============================================
-- USER INDEXES
-- ============================================
CREATE INDEX idx_users_workspace ON users(workspace_id, status) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_email ON users(email) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_last_login ON users(workspace_id, last_login_at DESC) WHERE deleted_at IS NULL;
CREATE INDEX idx_user_roles_user ON user_roles(user_id);
CREATE INDEX idx_user_roles_role ON user_roles(role_code);

-- ============================================
-- BARGE INDEXES
-- ============================================
CREATE INDEX idx_barges_workspace ON barges(workspace_id, status) WHERE deleted_at IS NULL;
CREATE INDEX idx_barges_imo ON barges(workspace_id, imo) WHERE deleted_at IS NULL;
CREATE INDEX idx_barges_active ON barges(workspace_id) 
    WHERE deleted_at IS NULL AND status = 'ACTIVE';
CREATE INDEX idx_barges_fuel_cert ON barges USING GIN(fuel_certifications) 
    WHERE deleted_at IS NULL AND status = 'ACTIVE';

-- ============================================
-- VESSEL INDEXES
-- ============================================
CREATE INDEX idx_vessels_workspace ON vessels(workspace_id, status);
CREATE INDEX idx_vessels_imo ON vessels(workspace_id, imo);
CREATE INDEX idx_vessels_screening ON vessels(workspace_id, last_screening_result, last_screened_at DESC);
CREATE INDEX idx_vessels_risk ON vessels(workspace_id, risk_score DESC) 
    WHERE risk_score IS NOT NULL;

-- ============================================
-- PORT INDEXES
-- ============================================
CREATE INDEX idx_ports_country ON ports(country, status);
CREATE INDEX idx_ports_code ON ports(code);
CREATE INDEX idx_ports_eca ON ports(is_eca) WHERE status = 'ACTIVE';

-- ============================================
-- NOTIFICATION INDEXES
-- ============================================
CREATE INDEX idx_notifications_recipient ON notifications(recipient_id, read, created_at DESC);
CREATE INDEX idx_notifications_workspace ON notifications(workspace_id, created_at DESC);
CREATE INDEX idx_notifications_reference ON notifications(reference_type, reference_id);
CREATE INDEX idx_notifications_unread ON notifications(recipient_id, created_at DESC) 
    WHERE read = FALSE;

-- ============================================
-- AUDIT LOG INDEXES (trên partitioned table)
-- ============================================
CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id, occurred_at DESC);
CREATE INDEX idx_audit_logs_workspace ON audit_logs(workspace_id, occurred_at DESC);
CREATE INDEX idx_audit_logs_actor ON audit_logs(actor_id, occurred_at DESC);
CREATE INDEX idx_audit_logs_action ON audit_logs(workspace_id, action, occurred_at DESC);
```

---

## §5 Row-Level Security (Multi-tenant Isolation)

### 5.1 Nguyên tắc

- **Zero cross-tenant data leakage:** Không bao giờ có trường hợp user workspace A nhìn thấy dữ liệu workspace B
- **Defense in depth:** RLS là lớp bảo vệ cuối cùng (last line of defense), application-level filtering là lớp đầu
- **Fail-closed:** Nếu workspace context không được set → DENY ALL (không trả dữ liệu)

### 5.2 Cài đặt RLS

```sql
-- ============================================
-- Enable RLS trên TẤT CẢ tables có workspace_id
-- ============================================
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE barges ENABLE ROW LEVEL SECURITY;
ALTER TABLE vessels ENABLE ROW LEVEL SECURITY;
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;
ALTER TABLE audit_logs ENABLE ROW LEVEL SECURITY;

-- Module-specific tables (enabled trong SRS của từng module)
ALTER TABLE nominations ENABLE ROW LEVEL SECURITY;
ALTER TABLE schedules ENABLE ROW LEVEL SECURITY;
ALTER TABLE deliveries ENABLE ROW LEVEL SECURITY;
ALTER TABLE bunker_delivery_notes ENABLE ROW LEVEL SECURITY;
ALTER TABLE invoices ENABLE ROW LEVEL SECURITY;

-- ============================================
-- RLS Policies
-- ============================================

-- Generic policy template cho workspace isolation
CREATE POLICY workspace_isolation ON users
    USING (workspace_id = current_setting('app.workspace_id')::UUID);

CREATE POLICY workspace_isolation ON barges
    USING (workspace_id = current_setting('app.workspace_id')::UUID);

CREATE POLICY workspace_isolation ON vessels
    USING (workspace_id = current_setting('app.workspace_id')::UUID);

CREATE POLICY workspace_isolation ON notifications
    USING (workspace_id = current_setting('app.workspace_id')::UUID);

CREATE POLICY workspace_isolation ON audit_logs
    USING (workspace_id = current_setting('app.workspace_id')::UUID);
```

### 5.3 Application Layer Integration

```sql
-- Mỗi request, application layer SET workspace context:
BEGIN;
SET LOCAL app.workspace_id = '{workspace_uuid_from_jwt}';

-- Tất cả queries trong transaction này tự động filtered
SELECT * FROM barges WHERE status = 'ACTIVE';
-- → Chỉ trả về barges của workspace hiện tại

COMMIT;
```

**Quy trình:**
1. JWT middleware extract `workspace_id` từ token claims
2. Database connection middleware gọi `SET LOCAL app.workspace_id = ?`
3. Mọi query trong request đều tự động filtered bởi RLS
4. Khi transaction kết thúc, setting tự động reset

### 5.4 Bypass RLS (System Operations)

Một số operations cần bypass RLS (ví dụ: cron jobs, system migrations):
- Dùng dedicated database role có `BYPASSRLS` privilege
- KHÔNG BAO GIỜ expose role này cho application user connections
- Audit mọi lần sử dụng bypass role

---

## §6 Common API Patterns

### 6.1 Standard Error Response Format

Tất cả API errors PHẢI tuân theo format sau:

```json
{
  "code": "ERROR_CODE",
  "message": "Human-readable message mô tả lỗi",
  "errors": [
    { "field": "quantity_mt", "message": "must be greater than 0" },
    { "field": "delivery_date", "message": "must be in the future" }
  ]
}
```

| Field | Type | Required | Mô tả |
|-------|------|----------|--------|
| code | String | Có | Error code (UPPER_SNAKE_CASE) |
| message | String | Có | Mô tả ngắn gọn cho developer/user |
| errors | Array | Không | Chi tiết lỗi per-field (chỉ cho validation errors) |

### 6.2 Pagination Response Wrapper

Tất cả list endpoints PHẢI trả về paginated response theo format:

```json
{
  "content": [
    { "id": "uuid-1", "name": "..." },
    { "id": "uuid-2", "name": "..." }
  ],
  "page": 0,
  "size": 20,
  "total_elements": 156,
  "total_pages": 8
}
```

**Query params:**
- `page` — trang hiện tại (0-based, default: 0)
- `size` — số items per page (default: 20, max: 100)
- `sort` — field,direction (e.g. `created_at,desc`)

### 6.3 Common HTTP Status Codes

| HTTP | Code | Khi nào sử dụng |
|------|------|-----------------|
| 200 | OK | Request thành công (GET, PATCH, PUT) |
| 201 | CREATED | Tạo mới thành công (POST) |
| 204 | NO_CONTENT | Xóa thành công, không có response body |
| 400 | VALIDATION_ERROR | Input validation failed (missing fields, wrong format) |
| 401 | UNAUTHORIZED | Missing/invalid/expired JWT token |
| 403 | FORBIDDEN | Authenticated nhưng không có permission cho action này |
| 404 | NOT_FOUND | Resource không tồn tại HOẶC không thuộc workspace hiện tại |
| 409 | CONFLICT | Duplicate resource, state conflict, optimistic lock failure |
| 422 | UNPROCESSABLE | Business rule violation (valid input nhưng vi phạm logic nghiệp vụ) |
| 429 | RATE_LIMITED | Vượt quá rate limit |
| 500 | INTERNAL_ERROR | Unexpected server error |

**Quy tắc bảo mật:** 404 dùng cho CẢ "not found" VÀ "not in your workspace" — không phân biệt để tránh information leak.

### 6.4 Soft Delete Convention

- Tất cả main entities có column `deleted_at TIMESTAMPTZ`
- DELETE endpoint thực hiện: `UPDATE SET deleted_at = NOW()` (KHÔNG hard-delete)
- Tất cả queries mặc định append `WHERE deleted_at IS NULL`
- RLS policy cũng enforce filter này
- Audit log entry được tạo khi soft-delete
- Soft-deleted records có thể restore bởi admin (set `deleted_at = NULL`)

### 6.5 Timestamp Convention

| Aspect | Quy ước |
|--------|---------|
| Storage | `TIMESTAMPTZ` (PostgreSQL, luôn lưu UTC) |
| Serialization | ISO 8601: `yyyy-MM-dd'T'HH:mm:ss.SSSXXX` |
| Internal timezone | UTC |
| Display timezone | Client tự convert theo user preference hoặc workspace timezone |
| Created/Updated | Auto-set bởi application layer (không trust client) |

### 6.6 ID Convention

- Tất cả entity IDs dùng **UUIDv7** (time-ordered cho index performance)
- Generated bởi application layer (KHÔNG dùng DB default `gen_random_uuid()` trong production)
- Format: standard UUID string (`xxxxxxxx-xxxx-7xxx-yxxx-xxxxxxxxxxxx`)
- UUIDv7 cho phép sort theo thời gian tạo mà không cần column `created_at`

### 6.7 Optimistic Locking

Áp dụng cho tables có concurrent write risk (nominations, deliveries, eBDN, schedules):

```sql
-- Column bổ sung:
version INTEGER NOT NULL DEFAULT 0

-- Update pattern:
UPDATE nominations 
SET status = 'CONFIRMED', version = version + 1, updated_at = NOW()
WHERE id = :id AND version = :expected_version;

-- Nếu 0 rows affected → 409 CONFLICT response:
{
  "code": "OPTIMISTIC_LOCK_CONFLICT",
  "message": "Resource was modified by another user. Please refresh and try again."
}
```

### 6.8 Search & Filter Convention

Các list endpoints hỗ trợ filter qua query params:

```
GET /api/v1/barges?status=ACTIVE&fuel_type=VLSFO&q=Pacific
```

| Param | Type | Mô tả |
|-------|------|--------|
| q | String | Full-text search (tìm trong name, code, description) |
| status | Enum | Filter theo trạng thái |
| from / to | DateTime | Date range filter |
| {field} | Varies | Exact match filter |

### 6.9 Bulk Operations Convention

Khi cần thao tác nhiều items cùng lúc:

```json
// POST /api/v1/notifications/read-all
{
  "ids": ["uuid-1", "uuid-2", "uuid-3"]
}

// Response 200:
{
  "processed": 3,
  "failed": 0,
  "errors": []
}
```

---

## §7 Platform APIs

### 7.1 Workspace Management APIs

#### GET /api/v1/workspaces/current

**Mô tả:** Lấy thông tin workspace hiện tại (từ JWT claims).  
**Permission:** Bất kỳ authenticated user  
**Response (200):**

```json
{
  "id": "uuid",
  "name": "ABC Bunkering Pte Ltd",
  "slug": "abc-bunkering",
  "supplier_licence_number": "MPA-2024-001234",
  "supplier_prefix": "ABC",
  "country": "SGP",
  "timezone": "Asia/Singapore",
  "settings": { ... },
  "status": "ACTIVE",
  "created_at": "2024-01-15T00:00:00.000+08:00"
}
```

#### PATCH /api/v1/workspaces/current/settings

**Mô tả:** Cập nhật settings workspace.  
**Permission:** `workspace.manage` (thường chỉ ROLE_SUPPLIER_ADMIN)  
**Request:**

```json
{
  "default_currency": "SGD",
  "require_mfm_data": true,
  "notification_channels": ["IN_APP", "EMAIL", "PUSH"]
}
```

**Response:** 200 với workspace DTO đã cập nhật.

### 7.2 User Management APIs

| Method | Endpoint | Permission | Mô tả |
|--------|----------|-----------|--------|
| GET | /api/v1/users | `users.read` | Danh sách users trong workspace (paginated) |
| POST | /api/v1/users | `users.manage` | Tạo/mời user mới |
| GET | /api/v1/users/{id} | `users.read` | Chi tiết user |
| PATCH | /api/v1/users/{id} | `users.manage` | Cập nhật user (name, phone, status) |
| POST | /api/v1/users/{id}/roles | `users.manage` | Gán role cho user |
| DELETE | /api/v1/users/{id}/roles/{role_code} | `users.manage` | Xóa role khỏi user |
| POST | /api/v1/users/{id}/reset-password | `users.manage` | Trigger reset password email |

#### POST /api/v1/users (Create/Invite User)

**Request:**
```json
{
  "email": "newuser@supplier.com",
  "full_name": "Trần Thị B",
  "phone": "+6591234567",
  "roles": ["ROLE_BARGE_OPERATOR"]
}
```

**Response (201):**
```json
{
  "id": "uuid",
  "email": "newuser@supplier.com",
  "full_name": "Trần Thị B",
  "status": "ACTIVE",
  "email_verified": false,
  "roles": ["ROLE_BARGE_OPERATOR"]
}
```

**Side effects:**
- Gửi invitation email với temporary password
- Tạo audit log entry (action: CREATE, entity_type: USER)

**Error cases:**
| HTTP | Code | Khi nào |
|------|------|---------|
| 409 | EMAIL_ALREADY_EXISTS | Email đã tồn tại trong workspace |
| 400 | INVALID_ROLE | Role code không hợp lệ |

### 7.3 Barge Management APIs

| Method | Endpoint | Permission | Mô tả |
|--------|----------|-----------|--------|
| GET | /api/v1/barges | `barges.read` | Danh sách barges (paginated, filterable) |
| POST | /api/v1/barges | `barges.write` | Đăng ký barge mới |
| GET | /api/v1/barges/{id} | `barges.read` | Chi tiết barge |
| PATCH | /api/v1/barges/{id} | `barges.write` | Cập nhật thông tin barge |
| GET | /api/v1/barges/available | `schedules.read` | Barges khả dụng cho khoảng thời gian |

#### GET /api/v1/barges/available

**Mô tả:** Lấy danh sách barges có thể dispatch cho khoảng thời gian & loại nhiên liệu cụ thể.

**Query params:**
- `fuel_type` (required) — SS 709 fuel code (e.g. `VLSFO`, `HSFO`, `MGO`)
- `from` (required) — ISO datetime bắt đầu
- `to` (required) — ISO datetime kết thúc

**Logic:**
1. Filter barges có `status = 'ACTIVE'`
2. Filter barges có `fuel_type` trong `fuel_certifications` array
3. Exclude barges đã có schedule conflict trong khoảng `from`-`to`
4. Return sorted theo capacity (desc)

**Response (200):**
```json
{
  "content": [
    {
      "id": "uuid",
      "name": "Pacific Star",
      "imo": "9876543",
      "capacity_mt": 3500.00,
      "fuel_certifications": ["VLSFO", "MGO", "HSFO"],
      "mfm_serial": "MFM-2024-001"
    }
  ],
  "total_elements": 4
}
```

### 7.4 Vessel Management APIs (KYC)

| Method | Endpoint | Permission | Mô tả |
|--------|----------|-----------|--------|
| GET | /api/v1/vessels | `vessels.read` | Danh sách vessels đã registered |
| POST | /api/v1/vessels | `vessels.write` | Đăng ký vessel mới (KYC profile) |
| GET | /api/v1/vessels/{imo} | `vessels.read` | Vessel theo IMO number |
| PUT | /api/v1/vessels/{imo} | `vessels.write` | Cập nhật vessel profile |
| POST | /api/v1/vessels/{imo}/screen | `vessels.screen` | Trigger sanctions screening |

#### POST /api/v1/vessels/{imo}/screen

**Mô tả:** Chạy sanctions screening cho vessel. Gọi external provider (Windward/Pole Star) và cập nhật `last_screened_at`, `last_screening_result`, `risk_score`.

**Response (202 Accepted):**
```json
{
  "screening_id": "uuid",
  "status": "PROCESSING",
  "message": "Screening initiated. Results will be available shortly."
}
```

**Side effects:**
- Dispatch async job để gọi screening provider
- Khi hoàn tất: update vessel record + tạo notification cho compliance officer (nếu FLAGGED/SANCTIONED)
- Tạo audit log entry

### 7.5 Notification APIs

| Method | Endpoint | Permission | Mô tả |
|--------|----------|-----------|--------|
| GET | /api/v1/notifications | authenticated | Danh sách notifications (unread first) |
| GET | /api/v1/notifications/unread-count | authenticated | Số notifications chưa đọc |
| PATCH | /api/v1/notifications/{id}/read | authenticated | Đánh dấu đã đọc |
| POST | /api/v1/notifications/read-all | authenticated | Đánh dấu tất cả đã đọc |

#### GET /api/v1/notifications

**Query params:**
- `read` (optional) — `true`/`false` filter
- `type` (optional) — filter theo notification type
- `reference_type` (optional) — filter theo entity type
- `page`, `size` — pagination

**Response:** Paginated, sorted `read ASC, created_at DESC` (unread trước, mới nhất trước).

### 7.6 Audit Log APIs

| Method | Endpoint | Permission | Mô tả |
|--------|----------|-----------|--------|
| GET | /api/v1/audit-logs | `audit.read` | Danh sách audit logs (paginated) |
| GET | /api/v1/audit-logs/entity/{type}/{id} | `audit.read` | Lịch sử thay đổi của 1 entity |

**Filter params:**
- `entity_type` — filter theo loại entity
- `action` — filter theo action
- `actor_id` — filter theo người thực hiện
- `from` / `to` — khoảng thời gian

**Lưu ý:** Audit logs KHÔNG có DELETE endpoint. Read-only API.

---

## §8 Yêu cầu phi chức năng (NFR)

### 8.1 Security

| ID | Yêu cầu | Mô tả chi tiết |
|----|----------|-----------------|
| NFR-PLAT-01 | Password hashing | Argon2id với params: memory=65536KB, iterations=3, parallelism=4 |
| NFR-PLAT-02 | JWT access token lifetime | Tối đa 15 phút. Không gia hạn — phải dùng refresh token |
| NFR-PLAT-03 | Rate limiting | 60 req/min per user, 200 req/min per workspace, 5 login attempts/15 min per IP |
| NFR-PLAT-04 | Tenant isolation | Zero cross-tenant data leakage. RLS enforced ở DB level + application level |
| NFR-PLAT-05 | Data encryption at rest | AES-256 cho database (PostgreSQL TDE hoặc volume encryption) |
| NFR-PLAT-06 | Data encryption in transit | TLS 1.3 cho tất cả connections (API, DB, external services) |
| NFR-PLAT-07 | Secret management | Credentials, API keys lưu trong secrets manager (AWS SM / HashiCorp Vault). Không hardcode |
| NFR-PLAT-08 | CORS policy | Whitelist specific origins per workspace. No wildcard `*` in production |
| NFR-PLAT-09 | Input sanitization | Tất cả user input sanitized trước khi persist. XSS protection on output |
| NFR-PLAT-10 | Session invalidation | Logout invalidates refresh token ngay lập tức. Admin có thể force-logout user |

### 8.2 Audit & Compliance

| ID | Yêu cầu | Mô tả chi tiết |
|----|----------|-----------------|
| NFR-PLAT-11 | Audit trail completeness | Mọi CUD operations logged trong audit_logs. Không exception |
| NFR-PLAT-12 | Audit retention | Tối thiểu 7 năm (MPA regulatory requirement cho bunker documentation) |
| NFR-PLAT-13 | Audit immutability | Audit logs không thể sửa/xóa. Append-only |
| NFR-PLAT-14 | Audit searchability | Query audit logs theo entity, actor, action, time range — response < 500ms P95 |
| NFR-PLAT-15 | Digital signature integrity | eBDN signatures verified bằng PKI. Tamper-evident |

### 8.3 Performance

| ID | Yêu cầu | Target | Mô tả |
|----|----------|--------|--------|
| NFR-PLAT-16 | Auth endpoints latency | < 200ms P95 | Login, refresh, logout, /me |
| NFR-PLAT-17 | CRUD endpoints latency | < 300ms P95 | Standard list/get/create/update |
| NFR-PLAT-18 | Search endpoints latency | < 500ms P95 | Full-text search, complex filters |
| NFR-PLAT-19 | Notification delivery | < 2s (in-app), < 30s (email) | Thời gian từ trigger → user nhận |
| NFR-PLAT-20 | Database connection pool | Min 10, Max 50 per instance | Tuned cho expected concurrency |
| NFR-PLAT-21 | Pagination limit | Max 100 items per page | Ngăn OOM với large datasets |

### 8.4 Availability & Reliability

| ID | Yêu cầu | Target | Mô tả |
|----|----------|--------|--------|
| NFR-PLAT-22 | Auth service uptime | 99.99% | Critical path — nếu auth down, toàn bộ system down |
| NFR-PLAT-23 | Platform APIs uptime | 99.95% | Shared services (user, barge, vessel management) |
| NFR-PLAT-24 | Database availability | 99.99% | PostgreSQL HA with read replicas |
| NFR-PLAT-25 | Graceful degradation | — | Notification failure không block business operations |
| NFR-PLAT-26 | Backup & Recovery | RPO < 1h, RTO < 4h | Point-in-time recovery cho PostgreSQL |

### 8.5 Scalability

| ID | Yêu cầu | Target | Mô tả |
|----|----------|--------|--------|
| NFR-PLAT-27 | Concurrent users | 500 simultaneous per workspace | Peak load capacity |
| NFR-PLAT-28 | Total workspaces | 1000+ | Horizontal scaling capability |
| NFR-PLAT-29 | Audit log volume | 10M+ records/month | Partitioned storage, archive strategy |
| NFR-PLAT-30 | Notification throughput | 1000 msg/min | Async queue processing |

### 8.6 Observability

| ID | Yêu cầu | Mô tả |
|----|----------|--------|
| NFR-PLAT-31 | Structured logging | JSON format, correlation_id per request, workspace_id in context |
| NFR-PLAT-32 | Metrics | Prometheus metrics: latency, error rate, throughput per endpoint |
| NFR-PLAT-33 | Tracing | Distributed tracing (OpenTelemetry) across services |
| NFR-PLAT-34 | Alerting | PagerDuty/OpsGenie alerts cho P95 latency > 1s, error rate > 5%, 5xx > 10/min |
| NFR-PLAT-35 | Health check | GET /health — readiness + liveness probes cho K8s |

---

## Phụ lục A: Shared Rules Reference

Module này áp dụng và ĐỊNH NGHĨA các shared rules sau cho toàn hệ thống. Các module khác REFERENCE tới đây:

| Rule ID | Tên | Section tham chiếu |
|---------|-----|---------------------|
| PLAT-001 | API Base Path `/api/v1/` | §6.1 |
| PLAT-002 | Timestamp Format (ISO 8601) | §6.5 |
| PLAT-003 | ID Format (UUIDv7) | §6.6 |
| PLAT-004 | Soft Delete Convention | §6.4 |
| PLAT-005 | Workspace Scoping (RLS) | §5 |
| AUTHN-001 | JWT Authentication | §2.1 |
| AUTHN-002 | Workspace Context | §2.3 |
| CRUD-001 | List Operation Defaults (Pagination) | §6.2 |
| ERR-001 | Standard Error Format | §6.1 |
| ERR-002 | Common HTTP Status Codes | §6.3 |
| OBS-003 | Audit Trail | §3.7 |

---

## Phụ lục B: Glossary

| Thuật ngữ | Mô tả |
|-----------|--------|
| Workspace | Đơn vị tenant isolation — mỗi tổ chức có 1 workspace |
| eBDN | Electronic Bunker Delivery Note — phiên bản điện tử của BDN |
| MFM | Mass Flow Meter — thiết bị đo lưu lượng nhiên liệu |
| IMO | International Maritime Organization number — mã định danh tàu |
| MPA | Maritime and Port Authority of Singapore |
| RLS | Row-Level Security — cơ chế isolation data ở database level |
| KYC | Know Your Customer — hồ sơ nhận diện khách hàng/vessel |
| SS 648/600/709 | Singapore Standards cho bunker operations |
| ECA | Emission Control Area — vùng kiểm soát khí thải |
| UN/LOCODE | United Nations Code for Trade and Transport Locations |
| VLSFO | Very Low Sulphur Fuel Oil (≤0.50% S) |
| HSFO | High Sulphur Fuel Oil (>0.50% S) |
| MGO | Marine Gas Oil |
| BDN | Bunker Delivery Note |
| P-SUPP | Portal Supplier (web app cho supplier team) |
| P-OPS | Portal Operations (mobile app cho barge operators) |
| P-VESSEL | Portal Vessel (mobile app cho vessel crew) |
| P-BUYER | Portal Buyer (web app cho buyers/charterers) |

---

*— Hết tài liệu SRS Platform Core —*
