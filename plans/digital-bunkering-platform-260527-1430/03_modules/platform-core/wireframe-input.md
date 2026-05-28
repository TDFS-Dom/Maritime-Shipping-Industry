# Wireframe Input — Platform Core

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Platform Core.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Screens trong module này thuộc P-SUPP (Supplier Portal, Desktop/Web) trừ Notification Center (all portals). Login page là public (không auth).

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-PLAT-01 | Login Page | Public | Low | Must |
| SCR-PLAT-02 | User Management | P-SUPP | Medium | Must |
| SCR-PLAT-03 | Barge Management | P-SUPP | Medium | Must |
| SCR-PLAT-04 | Workspace Settings | P-SUPP | Low | Should |
| SCR-PLAT-05 | Notification Center | All Portals | Low | Should |
| SCR-PLAT-06 | Audit Log Viewer | P-SUPP | Medium | Must |

---

## 2. Screen Descriptions

### SCR-PLAT-01: Login Page

- **Portal:** Public (không yêu cầu auth)
- **Actor:** Bất kỳ user chưa đăng nhập
- **Goal:** Đăng nhập vào hệ thống bằng email + password
- **Entry point:** URL gốc hoặc redirect khi session hết hạn
- **Layout type:** Centered form (single card, vertically centered)
- **Content:**
  - **Logo:** Platform logo (top center, above form card)
  - **Form Card (centered, max-width 400px):**
    - Heading: "Đăng nhập" (24px, bold)
    - Email input: label "Email", placeholder "name@company.com", type=email
    - Password input: label "Mật khẩu", type=password, show/hide toggle icon (eye)
    - "Quên mật khẩu?" link (right-aligned, below password field)
    - Submit button: "Đăng nhập" (Primary, full-width)
  - **Footer:** "© 2026 Digital Bunkering Platform" (caption, muted)
- **Actions available:**
  - Submit login form
  - Click "Quên mật khẩu?" → forgot password flow
  - Toggle password visibility
- **State variations:**
  - Default: Empty form, button enabled
  - Validation error: Inline error dưới field (red text) — e.g., "Email is required"
  - Login failed: Toast/banner error "Invalid credentials" (KHÔNG nói rõ email hay password sai)
  - Account locked: Toast/banner "Account locked. Try again after 30 minutes."
  - Loading: Button loading spinner, inputs disabled
- **Mobile behavior:** Form card full-width với padding 16px. Logo nhỏ hơn. Keyboard-friendly (auto-focus email field).

---

### SCR-PLAT-02: User Management

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Quản lý danh sách users trong workspace: mời mới, xem, edit roles, deactivate
- **Entry point:** Sidebar menu → "Users" (under "Settings" group)
- **Layout type:** Data table + action bar
- **Content:**
  - **Page Header:**
    - Title: "User Management" (28px, bold)
    - Subtitle: "Quản lý người dùng và phân quyền"
    - Action button (top-right): "Invite User" (Primary button + icon mail)
  - **Data Table:**
    - Columns: Full Name | Email | Roles (badges) | Status (badge) | Last Login | Actions
    - Roles column: hiển thị dạng pill badges (e.g., `Scheduler`, `Operator`) — multiple badges nếu nhiều roles
    - Status column: ACTIVE (green badge), INACTIVE (gray badge), LOCKED (red badge)
    - Last Login: relative time (e.g., "2 giờ trước") hoặc "Chưa đăng nhập"
    - Actions column: icon buttons [Edit roles] [Deactivate/Activate]
  - **Search bar:** Phía trên table, placeholder "Tìm theo tên hoặc email..."
  - **Pagination:** Bottom, 20 items/page
- **Actions available:**
  - Invite User → modal form (email + role multi-select)
  - Edit roles → modal/inline role editor
  - Deactivate → confirm dialog "Vô hiệu hóa user này?"
  - Activate → re-enable user (if INACTIVE)
  - Search/filter users
- **State variations:**
  - Has data: Table with user rows
  - Empty: "Chưa có user nào" + illustration + "Invite User" CTA
  - Loading: Skeleton rows (5 rows)
  - Invite modal open: Overlay modal with email + role fields
- **Mobile behavior:** Table chuyển sang card list (1 card per user). Actions via swipe hoặc "..." menu.

---

### SCR-PLAT-03: Barge Management

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Quản lý fleet barges: đăng ký mới, cập nhật thông tin, quản lý trạng thái
- **Entry point:** Sidebar menu → "Barges" (under "Fleet" group)
- **Layout type:** Data table + action bar
- **Content:**
  - **Page Header:**
    - Title: "Barge Management" (28px, bold)
    - Subtitle: "Quản lý fleet barge cung cấp nhiên liệu"
    - Action button (top-right): "Add Barge" (Primary button + icon ship)
  - **Data Table:**
    - Columns: Name | IMO | Capacity (MT) | Fuel Certs (badges) | MFM Serial | Status | Actions
    - Fuel Certs column: pill badges cho mỗi fuel type certified (e.g., `VLSFO`, `LSMGO`, `MGO`)
    - Status column: ACTIVE (green), MAINTENANCE (yellow), DECOMMISSIONED (gray)
    - MFM Serial: text hoặc "—" nếu chưa gắn
    - Actions column: icon buttons [Edit] [Change Status]
  - **Filters row:** Status filter dropdown (All / Active / Maintenance / Decommissioned)
  - **Pagination:** Bottom, 20 items/page
- **Actions available:**
  - Add Barge → modal/page form (name, IMO, capacity, fuel certs multi-select, MFM serial)
  - Edit → modal/page form (pre-filled)
  - Change Status → dropdown/dialog (ACTIVE ↔ MAINTENANCE → DECOMMISSIONED)
  - Filter by status
- **State variations:**
  - Has data: Table with barge rows
  - Empty: "Chưa có barge nào" + illustration + "Add Barge" CTA
  - Loading: Skeleton rows
  - Add/Edit form open: Modal with validation
  - IMO duplicate error: Inline error "IMO đã được đăng ký trong workspace"
- **Mobile behavior:** Card list layout. Mỗi card hiển thị name + IMO + status badge. Tap → detail/edit.

---

### SCR-PLAT-04: Workspace Settings

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Cấu hình thông tin workspace và preferences
- **Entry point:** Sidebar menu → "Settings" → "Workspace"
- **Layout type:** Form (sectioned)
- **Content:**
  - **Page Header:**
    - Title: "Workspace Settings" (28px, bold)
    - Subtitle: "Cấu hình tổ chức và preferences"
  - **Section 1 — Company Info:**
    - Company Name: text input (pre-filled)
    - Supplier Licence Number: text input (MPA licence, pre-filled)
    - Country: dropdown (pre-selected, read-only nếu đã set)
  - **Section 2 — Regional:**
    - Default Currency: dropdown (USD, SGD, EUR, etc.)
    - Timezone: dropdown (Asia/Singapore, UTC, etc.)
  - **Section 3 — Notification Preferences:**
    - Table/grid: notification type × channel (IN_APP, EMAIL, PUSH) — toggle on/off per cell
    - Notification types: Delivery Started, Delivery Completed, eBDN Pending Sign, Deadline Warning, Sanctions Alert, Schedule Conflict
  - **Bottom Action Bar:**
    - "Save Changes" button (Primary) — disabled nếu không thay đổi gì
    - "Reset" link (secondary) — revert về saved values
- **Actions available:**
  - Edit fields
  - Toggle notification channels
  - Save settings
  - Reset changes
- **State variations:**
  - Default: Form pre-filled with current settings
  - Dirty (changed): "Save Changes" button enabled, unsaved indicator
  - Saving: Button loading
  - Saved: Success toast "Settings updated"
- **Mobile behavior:** Vertical form layout (1 column). Notification toggles dạng switches.

---

### SCR-PLAT-05: Notification Center

- **Portal:** All Portals (P-SUPP, P-OPS, P-BUYER, P-VESSEL)
- **Actor:** Bất kỳ authenticated user
- **Goal:** Xem, đọc, và quản lý thông báo hệ thống
- **Entry point:** Bell icon trên header bar (global component)
- **Layout type:** Dropdown panel / Slide-out panel
- **Content:**
  - **Bell Icon (Header):**
    - Icon bell + badge counter (unread count)
    - Badge: số chính xác nếu ≤99, hiển thị "99+" nếu >99
    - Badge ẩn nếu unread = 0
  - **Notification Panel (mở khi click bell):**
    - Header: "Thông báo" + "Mark all as read" link (right)
    - **Notification List (scrollable):**
      - Unread items lên trước (background nhẹ khác biệt hoặc dot indicator)
      - Mỗi item:
        - Icon (theo type: 📦 delivery, ✍️ eBDN, ⚠️ alert, 📅 schedule)
        - Title (bold nếu unread): e.g., "eBDN #2025-0042 chờ ký"
        - Message (1-2 dòng, truncate nếu dài)
        - Timestamp: relative (e.g., "5 phút trước", "2 giờ trước")
      - Click item → mark read + navigate to reference entity
    - **Empty state:** "Không có thông báo mới" + illustration nhỏ
    - **Load more:** "Xem tất cả thông báo" link → full notification page (optional)
- **Actions available:**
  - Click bell → open/close panel
  - Click notification → mark read + navigate
  - "Mark all as read"
  - Scroll for more
- **State variations:**
  - No notifications: Empty illustration
  - Has unread: Badge visible, unread items highlighted
  - All read: No badge, items uniform style
  - Loading: Skeleton items (3 items)
- **Mobile behavior:** Full-screen page thay vì dropdown. Bell icon trên header. Tap → navigate to notification list page.

---

### SCR-PLAT-06: Audit Log Viewer

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin, Compliance Officer
- **Goal:** Xem lịch sử mọi thao tác thay đổi dữ liệu trong workspace cho compliance
- **Entry point:** Sidebar menu → "Audit Logs" (under "Compliance" group)
- **Layout type:** Data table + filter bar
- **Content:**
  - **Page Header:**
    - Title: "Audit Logs" (28px, bold)
    - Subtitle: "Lịch sử thao tác hệ thống — Read-only"
  - **Filter Bar (above table):**
    - Entity Type: dropdown (All / NOMINATION / DELIVERY / EBDN / BARGE / VESSEL / USER / INVOICE / SCHEDULE)
    - Action: dropdown (All / CREATE / UPDATE / DELETE / SIGN / SUBMIT / CONFIRM / REJECT / CANCEL)
    - Date Range: date picker (from – to)
    - Actor: searchable dropdown (list users in workspace)
    - "Apply Filters" button + "Clear" link
  - **Data Table:**
    - Columns: Timestamp | Actor | Action | Entity Type | Entity ID | Changes Summary
    - Timestamp: `dd/MM/yyyy HH:mm:ss` (local timezone)
    - Actor: user full name (hoặc "System" cho automated actions)
    - Action: badge style (CREATE=blue, UPDATE=gray, DELETE=red, SIGN=green)
    - Entity ID: truncated UUID (first 8 chars), click → copy full ID
    - Changes Summary: condensed text (e.g., "status: PENDING → CONFIRMED") — truncate + hover expand
  - **Pagination:** Bottom, 20 items/page, showing "Hiển thị 1-20 của 1,547 records"
  - **NO action buttons per row:** Read-only, không có edit/delete/archive
- **Actions available:**
  - Filter logs
  - Sort by column (click header)
  - Copy entity ID
  - Paginate
  - Export (optional): "Export CSV" button (top-right, secondary)
- **State variations:**
  - Has data: Table populated
  - No results (after filter): "Không tìm thấy kết quả phù hợp" + "Clear filters" link
  - Loading: Skeleton rows + filter bar disabled
  - Large dataset: Pagination + infinite scroll option
- **Mobile behavior:** Card list (1 card per log entry). Filters dạng collapsible panel. Timestamp + actor + action visible, details expandable.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Nav Type |
|---|---|---|---|
| SCR-PLAT-01 | Public | (None — no nav) | Standalone page |
| SCR-PLAT-02 | P-SUPP | Sidebar → Settings → Users | Desktop sidebar |
| SCR-PLAT-03 | P-SUPP | Sidebar → Fleet → Barges | Desktop sidebar |
| SCR-PLAT-04 | P-SUPP | Sidebar → Settings → Workspace | Desktop sidebar |
| SCR-PLAT-05 | All | Header → Bell icon | Global header component |
| SCR-PLAT-06 | P-SUPP | Sidebar → Compliance → Audit Logs | Desktop sidebar |

**P-SUPP Sidebar Structure:**
- Dashboard
- Fleet: Barges, Vessels
- Operations: Deliveries, eBDN, Scheduling
- Finance: Invoices, Payments
- Compliance: Audit Logs, Sanctions
- Settings: Workspace, Users

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Login page KHÔNG ĐƯỢC leak email existence | Bảo mật: attacker không thể enumerate valid emails. "Invalid credentials" cho mọi trường hợp sai. |
| 2 | Audit logs hoàn toàn read-only — KHÔNG có nút edit/delete | Compliance MPA: audit trail phải immutable. Không cho phép tampering. |
| 3 | Password field phải có show/hide toggle | UX: giảm lỗi nhập trên mobile/desktop |
| 4 | Notification panel unread items PHẢI hiển thị trước read items | UX: ưu tiên thông tin mới nhất cần xử lý |
| 5 | Roles/permissions thay đổi phải reflect ngay lập tức trên UI | Security: tránh trường hợp user bị revoke quyền nhưng vẫn thấy menu items |
| 6 | IMO number input chỉ cho phép nhập số (7 digits) | Data integrity: IMO format cố định, tránh typo |
| 7 | Deactivate user phải có confirm dialog | Safety: tránh vô tình lock người khác ra khỏi hệ thống |
| 8 | Workspace settings "Save" button disabled khi không có thay đổi | UX: prevent accidental saves, clear dirty state indicator |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Email | lowercase, full display | Max 40 chars + "..." | Monospace font optional |
| User full name | Title case | Max 30 chars + "..." | Bold trong table |
| Roles | Pill badges, multiple | Max 3 visible + "+N" | Color per role type |
| Status (User) | Badge: ACTIVE (green), INACTIVE (gray), LOCKED (red) | — | Consistent across screens |
| Status (Barge) | Badge: ACTIVE (green), MAINTENANCE (yellow), DECOMMISSIONED (gray) | — | Consistent |
| IMO number | 7 digits, monospace | — | Không format (no dashes) |
| Capacity | `X,XXX MT` | — | Thousand separator, 0 decimal |
| Timestamp (audit) | `dd/MM/yyyy HH:mm:ss` | — | Local timezone (workspace setting) |
| Timestamp (notification) | Relative: "5 phút trước", "2 giờ trước" | — | Xem UIB-004 rules |
| Last login | Relative hoặc "Chưa đăng nhập" | — | Gray text nếu > 30 days |
| UUID (entity ID) | First 8 chars + "..." | Click → copy full | Monospace font |
| Changes summary (audit) | `field: old → new` | Max 60 chars + expand | Muted text |
| Notification badge | Exact count ≤ 99, "99+" for > 99 | — | Red badge on bell icon |
| Fuel certifications | Pill badges per code | Max 4 visible + "+N" | Design system colors per fuel family |
