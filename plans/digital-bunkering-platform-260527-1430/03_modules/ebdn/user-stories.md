# User Stories — eBDN Generation & Signing

## Epic: EBDN — Bunker Delivery Note điện tử

Tạo, ký, lưu trữ eBDN tự động từ MFM data, đảm bảo compliance và nộp MPA.

---

## US-EBDN-001: Auto-generate eBDN từ MFM data

**Với tư cách là** hệ thống (System),  
**tôi muốn** tự động tạo eBDN khi delivery hoàn tất,  
**để** giảm thiểu nhập liệu thủ công và đảm bảo accuracy từ MFM.

### Tiêu chí chấp nhận

**AC1: Auto-generate khi delivery complete**
- **Given** Delivery status vừa chuyển COMPLETED và MFM final reading available
- **When** hệ thống xử lý event
- **Then** eBDN tự động tạo với tất cả fields populated, status = DRAFT

**AC2: Quantity từ MFM only**
- **Given** eBDN được generate
- **When** kiểm tra quantity field
- **Then** quantity = MFM end_totalizer - start_totalizer, không có option nhập tay

**AC3: Reference auto-generated**
- **Given** Supplier đã có 150 eBDN trước đó
- **When** eBDN mới tạo
- **Then** reference = supplier prefix + sequential number (e.g., "SUP-A/0151")

### INVEST Validation
- **I**ndependent: Triggered by delivery completion event
- **N**egotiable: Reference format
- **V**aluable: Eliminates manual data entry, ensures MFM accuracy
- **E**stimable: Event handler + data mapping
- **S**mall: 1 sprint
- **T**estable: Generated fields match source data

---

## US-EBDN-002: Barge crew ký eBDN

**Với tư cách là** Barge Operator,  
**tôi muốn** review và ký eBDN sau khi giao hàng,  
**để** xác nhận thông tin giao hàng là chính xác.

### Tiêu chí chấp nhận

**AC1: Review và ký**
- **Given** eBDN status = DRAFT
- **When** Barge Operator review nội dung và nhấn Sign
- **Then** signature record lưu, status = BARGE_SIGNED

**AC2: Hiển thị đầy đủ thông tin**
- **Given** Barge Operator mở eBDN
- **When** trang review hiển thị
- **Then** hiện đầy đủ: vessel info, barge info, fuel code, quantity, MFM serial/readings, timestamps, location

**AC3: Không cho sửa nội dung**
- **Given** eBDN auto-generated
- **When** Barge Operator muốn sửa quantity hoặc fields khác
- **Then** hệ thống không cho phép edit — chỉ sign hoặc escalate

### INVEST Validation
- **I**ndependent: Phụ thuộc US-EBDN-001
- **N**egotiable: Signature method (pin, biometric, drawn)
- **V**aluable: Legal confirmation từ barge side
- **E**stimable: Sign action + record
- **S**mall: < 1 sprint
- **T**estable: Status transition + signature stored

---

## US-EBDN-003: Vessel Chief Engineer ký eBDN

**Với tư cách là** Vessel Chief Engineer,  
**tôi muốn** review và ký eBDN xác nhận đã nhận nhiên liệu,  
**để** hoàn tất thủ tục pháp lý cho barge có thể rời đi.

### Tiêu chí chấp nhận

**AC1: Ký sau barge**
- **Given** eBDN status = BARGE_SIGNED
- **When** Vessel CE review và nhấn Sign
- **Then** signature record lưu, status = FULLY_SIGNED, eBDN trở thành immutable

**AC2: Trigger B2G submission**
- **Given** eBDN vừa chuyển FULLY_SIGNED
- **When** hệ thống xử lý
- **Then** tự động trigger B2G submission to MPA

**AC3: Không ký nếu barge chưa ký**
- **Given** eBDN status = DRAFT (barge chưa ký)
- **When** Vessel CE cố ký
- **Then** hệ thống từ chối "Barge crew phải ký trước"

### INVEST Validation
- **I**ndependent: Phụ thuộc US-EBDN-002
- **N**egotiable: Signing UX
- **V**aluable: Complete legal document, enable B2G submission
- **E**stimable: Sign + immutability + event
- **S**mall: < 1 sprint
- **T**estable: Sequential signing enforced

---

## US-EBDN-004: Signing timeout escalation

**Với tư cách là** Supplier Admin,  
**tôi muốn** nhận cảnh báo khi eBDN chưa được ký sau 2 giờ,  
**để** can thiệp kịp thời và tránh delay.

### Tiêu chí chấp nhận

**AC1: Alert sau 2 giờ**
- **Given** eBDN status = DRAFT hoặc BARGE_SIGNED quá 2 giờ
- **When** timeout threshold reached
- **Then** Supplier Admin nhận escalation alert

**AC2: Alert chỉ gửi 1 lần**
- **Given** Alert đã gửi cho eBDN X
- **When** vẫn chưa ký thêm 1 giờ nữa
- **Then** không gửi duplicate alert (hoặc gửi reminder với frequency khác)

### INVEST Validation
- **I**ndependent: Scheduled job
- **N**egotiable: Timeout duration (configurable), escalation channel
- **V**aluable: Prevent operational delays
- **E**stimable: Timer + notification
- **S**mall: < 1 sprint
- **T**estable: Time-based trigger

---

## US-EBDN-005: Xem lịch sử eBDN

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem danh sách tất cả eBDN đã tạo,  
**để** quản lý và tra cứu khi cần.

### Tiêu chí chấp nhận

**AC1: Danh sách có filter**
- **Given** Supplier Admin mở eBDN History
- **When** trang tải
- **Then** hiển thị danh sách eBDN với filter: status, date range, vessel, fuel type

**AC2: View detail**
- **Given** Admin chọn 1 eBDN từ danh sách
- **When** mở detail
- **Then** hiển thị toàn bộ nội dung + signatures + submission status

### INVEST Validation
- **I**ndependent: Read-only, hoạt động khi có data
- **N**egotiable: Filter options, export format
- **V**aluable: Audit trail, compliance record
- **E**stimable: List + detail view
- **S**mall: 1 sprint
- **T**estable: Data display accuracy

---

## US-EBDN-006: eBDN immutability

**Với tư cách là** hệ thống (System),  
**tôi muốn** lock eBDN sau khi FULLY_SIGNED,  
**để** đảm bảo tính toàn vẹn pháp lý của document.

### Tiêu chí chấp nhận

**AC1: No edit after FULLY_SIGNED**
- **Given** eBDN status = FULLY_SIGNED
- **When** bất kỳ ai cố edit bất kỳ field nào
- **Then** hệ thống từ chối "eBDN đã ký không thể chỉnh sửa"

**AC2: API enforce**
- **Given** eBDN FULLY_SIGNED
- **When** PUT/PATCH request đến API
- **Then** return HTTP 403 "eBDN is immutable after dual-signing"

### INVEST Validation
- **I**ndependent: Enforcement rule
- **N**egotiable: Error message wording
- **V**aluable: Legal document integrity
- **E**stimable: Guard check on update endpoints
- **S**mall: < 1 sprint
- **T**estable: Attempt edit → reject

---

## US-EBDN-007: Resubmit sau MPA rejection

**Với tư cách là** Supplier Admin,  
**tôi muốn** tạo correction eBDN khi MPA reject bản gốc,  
**để** tuân thủ yêu cầu nộp lại.

### Tiêu chí chấp nhận

**AC1: Tạo correction eBDN**
- **Given** eBDN status = REJECTED (by MPA)
- **When** Supplier Admin chọn Resubmit
- **Then** tạo new eBDN reference original, cho phép chỉnh sửa fields cần fix

**AC2: Correction cần dual-sign lại**
- **Given** Correction eBDN được tạo
- **When** trước khi resubmit to MPA
- **Then** bắt buộc dual-sign (barge + vessel) lại

### INVEST Validation
- **I**ndependent: Phụ thuộc B2G rejection event
- **N**egotiable: Which fields can be corrected
- **V**aluable: Compliance recovery
- **E**stimable: Clone + edit + re-sign flow
- **S**mall: 1 sprint
- **T**estable: New eBDN references original
