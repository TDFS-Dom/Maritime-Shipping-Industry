# User Stories — Platform Core

## Epic: PLAT — Quản lý nền tảng, xác thực và dữ liệu chủ

Quản lý workspace (tenant), users, roles, barges, vessels (KYC), authentication, notifications, và audit logging.

---

## US-PLAT-001: Login với email/password

**Với tư cách là** User (bất kỳ role),  
**tôi muốn** đăng nhập bằng email và password,  
**để** truy cập hệ thống với đúng quyền hạn của mình.

### Tiêu chí chấp nhận

**AC1: Đăng nhập thành công**
- **Given** User có tài khoản active với email "operator@supplier.com"
- **When** nhập đúng email + password và click Login
- **Then** hệ thống trả về JWT access token (15 min) + refresh token (7 ngày) và redirect đến dashboard tương ứng

**AC2: Đăng nhập sai credentials**
- **Given** User nhập email đúng nhưng password sai
- **When** click Login
- **Then** hiển thị lỗi "Invalid credentials" (không nói rõ email hay password sai)

**AC3: Account locked sau 5 lần sai**
- **Given** User đã nhập sai password 5 lần liên tiếp
- **When** thử đăng nhập lần thứ 6
- **Then** hiển thị "Account locked. Try again after 30 minutes." và account bị khóa 30 phút

**AC4: Password validation**
- **Given** User đang đăng ký hoặc đổi mật khẩu
- **When** nhập password không đáp ứng (min 8 chars, 1 uppercase, 1 number, 1 special char)
- **Then** hiển thị lỗi validation cụ thể cho từng tiêu chí chưa đạt

### INVEST Validation
- **I**ndependent: Standalone auth flow
- **V**aluable: Cho phép user truy cập hệ thống
- **S**mall: 1 sprint
- **T**estable: Verify token issuance, error messages, lock behavior

---

## US-PLAT-002: Mời user mới vào workspace

**Với tư cách là** Supplier Admin,  
**tôi muốn** mời user mới vào workspace bằng email,  
**để** cấp quyền truy cập hệ thống cho nhân viên mới.

### Tiêu chí chấp nhận

**AC1: Mời user thành công**
- **Given** Supplier Admin mở User Management
- **When** click "Invite User", nhập email "newuser@company.com" và chọn roles [ROLE_SCHEDULER]
- **Then** hệ thống tạo user record, gửi email mời chứa temporary password, hiển thị success toast

**AC2: Email đã tồn tại**
- **Given** email "existing@company.com" đã có trong workspace
- **When** Supplier Admin mời email đó
- **Then** hiển thị lỗi "Email đã tồn tại trong workspace"

**AC3: Non-admin không thể mời**
- **Given** User có role ROLE_SCHEDULER (không phải SUPPLIER_ADMIN)
- **When** truy cập User Management
- **Then** không thấy nút "Invite User", API trả 403 nếu gọi trực tiếp

### INVEST Validation
- **I**ndependent: User creation flow riêng biệt
- **V**aluable: Onboard nhân viên mới
- **S**mall: 1 sprint
- **T**estable: Email sent, user created, permission check

---

## US-PLAT-003: Gán role cho user

**Với tư cách là** Supplier Admin,  
**tôi muốn** gán hoặc thay đổi roles cho user trong workspace,  
**để** kiểm soát quyền truy cập phù hợp với nhiệm vụ.

### Tiêu chí chấp nhận

**AC1: Gán role mới**
- **Given** User "Nguyễn Văn A" hiện có role [ROLE_BARGE_OPERATOR]
- **When** Supplier Admin thêm role ROLE_SCHEDULER cho user
- **Then** user có cả 2 roles, permissions được cập nhật ngay lập tức, ghi audit log

**AC2: Xóa role**
- **Given** User "Nguyễn Văn A" có roles [ROLE_BARGE_OPERATOR, ROLE_SCHEDULER]
- **When** Supplier Admin remove role ROLE_SCHEDULER
- **Then** user chỉ còn ROLE_BARGE_OPERATOR, permissions cập nhật, ghi audit log

**AC3: Không thể tự xóa SUPPLIER_ADMIN role của chính mình**
- **Given** Supplier Admin đang edit roles của chính mình
- **When** cố gắng remove ROLE_SUPPLIER_ADMIN
- **Then** hiển thị lỗi "Cannot remove your own admin role"

### INVEST Validation
- **I**ndependent: RBAC management
- **V**aluable: Flexible access control
- **S**mall: < 1 sprint
- **T**estable: Permission enforcement, audit log

---

## US-PLAT-004: Đăng ký barge mới

**Với tư cách là** Supplier Admin,  
**tôi muốn** đăng ký barge mới vào hệ thống,  
**để** barge sẵn sàng được dispatch cho các delivery.

### Tiêu chí chấp nhận

**AC1: Đăng ký thành công**
- **Given** Supplier Admin mở Barge Management
- **When** click "Add Barge" và nhập: name="Pacific Star", IMO="1234567", capacity=5000 MT, fuel certs=[VLSFO, LSMGO], MFM serial="MFM-001"
- **Then** barge được tạo với status ACTIVE, hiển thị trong danh sách, ghi audit log

**AC2: IMO trùng**
- **Given** workspace đã có barge với IMO "1234567"
- **When** Supplier Admin thêm barge mới với cùng IMO
- **Then** hiển thị lỗi "IMO number already registered in this workspace"

**AC3: Validation lỗi**
- **Given** Supplier Admin nhập IMO="12345" (chỉ 5 ký tự)
- **When** submit form
- **Then** hiển thị inline error "IMO must be exactly 7 digits"

**AC4: Chuyển trạng thái maintenance**
- **Given** Barge "Pacific Star" có status ACTIVE
- **When** Supplier Admin chuyển status → MAINTENANCE
- **Then** barge không xuất hiện trong danh sách dispatch của scheduling module

### INVEST Validation
- **I**ndependent: Barge CRUD riêng biệt
- **V**aluable: Fleet management
- **S**mall: 1 sprint
- **T**estable: CRUD operations, IMO validation, state transitions

---

## US-PLAT-005: Cập nhật vessel KYC profile

**Với tư cách là** Supplier Admin,  
**tôi muốn** cập nhật thông tin KYC của vessel (tàu nhận hàng),  
**để** dữ liệu luôn chính xác phục vụ compliance và screening.

### Tiêu chí chấp nhận

**AC1: Cập nhật vessel info**
- **Given** Vessel "MV Northern Star" đã tồn tại trong workspace
- **When** Supplier Admin edit beneficial_owner từ "Company A" → "Company B"
- **Then** thông tin được cập nhật, ghi audit log với old/new values

**AC2: Hiển thị screening status**
- **Given** Vessel vừa được screen với result = FLAGGED
- **When** mở vessel profile
- **Then** hiển thị badge FLAGGED (yellow), last screened date, screening provider

**AC3: Vessel SANCTIONED block delivery**
- **Given** Vessel có screening_result = SANCTIONED
- **When** user cố tạo nomination cho vessel này
- **Then** nomination bị block với lỗi "Vessel is sanctioned — delivery not permitted"

### INVEST Validation
- **I**ndependent: Vessel profile management
- **V**aluable: KYC compliance
- **S**mall: 1 sprint
- **T**estable: Update flow, screening display, block logic

---

## US-PLAT-006: Xem notification center

**Với tư cách là** User (bất kỳ role),  
**tôi muốn** xem thông báo từ hệ thống (delivery started, eBDN pending sign, deadline warning...),  
**để** kịp thời nắm bắt và xử lý công việc.

### Tiêu chí chấp nhận

**AC1: Bell icon + unread count**
- **Given** User có 5 unread notifications
- **When** nhìn header bar
- **Then** bell icon hiển thị badge "5"

**AC2: Mở notification panel**
- **Given** User click bell icon
- **When** panel mở ra
- **Then** hiển thị danh sách notifications, unread lên trước, mỗi item có title + message + relative timestamp

**AC3: Click notification navigate**
- **Given** Notification "eBDN #2025-0042 pending your signature" với reference_type=EBDN
- **When** user click notification
- **Then** notification marked as read, navigate đến eBDN detail page

**AC4: Mark all as read**
- **Given** User có 12 unread notifications
- **When** click "Mark all as read"
- **Then** tất cả notifications chuyển sang read, badge count = 0

### INVEST Validation
- **I**ndependent: Notification UI riêng biệt
- **V**aluable: Timely awareness
- **S**mall: 1 sprint
- **T**estable: Count, navigation, mark-read

---

## US-PLAT-007: Xem audit logs

**Với tư cách là** Supplier Admin hoặc Compliance Officer,  
**tôi muốn** xem lịch sử mọi thao tác trong hệ thống,  
**để** truy vết khi có tranh chấp hoặc audit compliance.

### Tiêu chí chấp nhận

**AC1: Hiển thị audit log table**
- **Given** User có quyền xem audit logs (SUPPLIER_ADMIN hoặc COMPLIANCE_OFFICER)
- **When** mở Audit Log Viewer
- **Then** hiển thị bảng: timestamp, actor name, action, entity type, entity ID, changes summary — sort theo timestamp DESC

**AC2: Filter audit logs**
- **Given** Audit log viewer đang hiển thị
- **When** user filter entity_type = "DELIVERY" và date range = "01/06/2026 – 30/06/2026"
- **Then** chỉ hiển thị logs liên quan đến DELIVERY trong tháng 6/2026

**AC3: Read-only — không cho phép edit/delete**
- **Given** User xem audit log detail
- **When** xem bất kỳ record nào
- **Then** không có nút edit, delete, hoặc bất kỳ action thay đổi nào

**AC4: Permission restriction**
- **Given** User có role ROLE_BARGE_OPERATOR (không phải admin/compliance)
- **When** cố truy cập Audit Log Viewer
- **Then** redirect về dashboard hoặc hiển thị "Access Denied"

### INVEST Validation
- **I**ndependent: Read-only log viewer
- **V**aluable: Compliance + dispute resolution
- **S**mall: 1 sprint
- **T**estable: Data display, filters, permission gate

---

## US-PLAT-008: Cập nhật workspace settings

**Với tư cách là** Supplier Admin,  
**tôi muốn** cấu hình workspace settings (currency, timezone, notifications),  
**để** hệ thống hoạt động phù hợp với đặc thù tổ chức.

### Tiêu chí chấp nhận

**AC1: Cập nhật currency + timezone**
- **Given** Supplier Admin mở Workspace Settings
- **When** đổi currency từ "USD" → "SGD" và timezone từ "UTC" → "Asia/Singapore"
- **Then** settings được lưu, ghi audit log, toàn bộ module hiển thị theo currency/timezone mới

**AC2: Cấu hình notification preferences**
- **Given** Supplier Admin đang ở tab Notifications trong Settings
- **When** tắt channel EMAIL cho notification type "DELIVERY_COMPLETED"
- **Then** delivery completion sẽ không gửi email nữa (chỉ in-app)

**AC3: Chỉ SUPPLIER_ADMIN truy cập**
- **Given** User có role ROLE_FINANCE
- **When** cố truy cập Workspace Settings
- **Then** không thấy menu item Settings, API trả 403

### INVEST Validation
- **I**ndependent: Configuration management
- **V**aluable: Customization per organization
- **S**mall: < 1 sprint
- **T**estable: Settings persistence, permission gate
