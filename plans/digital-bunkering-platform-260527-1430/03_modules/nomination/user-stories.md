# User Stories — Nomination & Order Management

## Epic: NOM — Quản lý đặt hàng nhiên liệu

Cho phép Buyer tạo và quản lý nomination, Supplier xem xét và xử lý đơn hàng nhiên liệu.

---

## US-NOM-001: Tạo Nomination

**Với tư cách là** Shipowner/Operator (Buyer),  
**tôi muốn** tạo một nomination đặt nhiên liệu cho vessel của tôi,  
**để** yêu cầu Supplier cung cấp nhiên liệu đúng loại, đúng số lượng tại cảng và thời gian mong muốn.

### Tiêu chí chấp nhận (Acceptance Criteria)

**AC1: Tạo nomination thành công**
- **Given** Buyer đã đăng nhập và có quyền tạo nomination
- **When** Buyer điền đầy đủ thông tin (vessel IMO, vessel name, fuel type code, quantity, port, delivery window) và submit
- **Then** hệ thống tạo nomination với status = PENDING, hiển thị thông báo thành công

**AC2: Validate fuel type code**
- **Given** Buyer nhập fuel type code
- **When** code không tồn tại trong registry SS 709
- **Then** hệ thống hiển thị lỗi "Fuel type code không hợp lệ" và không cho submit

**AC3: Validate delivery window**
- **Given** Buyer nhập delivery window start
- **When** thời gian bắt đầu < 24 giờ kể từ hiện tại
- **Then** hệ thống hiển thị lỗi "Delivery window phải ít nhất 24 giờ từ thời điểm hiện tại"

**AC4: Validate quantity**
- **Given** Buyer nhập quantity
- **When** quantity ≤ 0
- **Then** hệ thống hiển thị lỗi "Số lượng phải lớn hơn 0 MT"

### INVEST Validation
- **I**ndependent: Không phụ thuộc story khác để hoạt động
- **N**egotiable: UI/UX có thể thay đổi, business rules cố định
- **V**aluable: Buyer có thể bắt đầu quy trình đặt nhiên liệu
- **E**stimable: Rõ ràng scope — form + validation + create record
- **S**mall: 1 sprint
- **T**estable: Có Given/When/Then cụ thể

---

## US-NOM-002: Supplier xác nhận Nomination

**Với tư cách là** Supplier Admin,  
**tôi muốn** xác nhận nomination và assign barge,  
**để** cam kết cung cấp nhiên liệu theo yêu cầu của Buyer.

### Tiêu chí chấp nhận (Acceptance Criteria)

**AC1: Confirm thành công**
- **Given** Supplier đang xem nomination có status = PENDING và sanctions screening đã pass
- **When** Supplier chọn barge và nhấn Confirm
- **Then** nomination status chuyển sang CONFIRMED, barge được gán, scheduling task tự động được tạo

**AC2: Bắt buộc assign barge**
- **Given** Supplier muốn confirm nomination
- **When** Supplier nhấn Confirm mà chưa chọn barge
- **Then** hệ thống hiển thị lỗi "Vui lòng chọn barge trước khi xác nhận"

**AC3: Thông báo Buyer**
- **Given** Supplier đã confirm nomination thành công
- **When** hệ thống hoàn tất xử lý
- **Then** Buyer nhận thông báo nomination đã được xác nhận kèm thông tin barge

### INVEST Validation
- **I**ndependent: Phụ thuộc US-NOM-001 (nomination phải tồn tại)
- **N**egotiable: Cách chọn barge có thể linh hoạt (dropdown, search, suggest)
- **V**aluable: Chuyển nomination sang giai đoạn scheduling
- **E**stimable: Logic rõ ràng — confirm + assign + event publish
- **S**mall: 1 sprint
- **T**estable: State transition + side effect kiểm chứng được

---

## US-NOM-003: Supplier từ chối Nomination

**Với tư cách là** Supplier Admin,  
**tôi muốn** từ chối nomination với lý do cụ thể,  
**để** Buyer hiểu tại sao yêu cầu không được đáp ứng và có thể điều chỉnh.

### Tiêu chí chấp nhận (Acceptance Criteria)

**AC1: Reject thành công**
- **Given** Supplier đang xem nomination có status = PENDING
- **When** Supplier nhập lý do từ chối và nhấn Reject
- **Then** nomination status chuyển sang REJECTED, lý do được lưu

**AC2: Bắt buộc nhập lý do**
- **Given** Supplier muốn reject nomination
- **When** Supplier nhấn Reject mà không nhập lý do
- **Then** hệ thống hiển thị lỗi "Vui lòng nhập lý do từ chối"

**AC3: Thông báo Buyer**
- **Given** Supplier đã reject nomination
- **When** hệ thống hoàn tất xử lý
- **Then** Buyer nhận thông báo nomination bị từ chối kèm lý do

### INVEST Validation
- **I**ndependent: Phụ thuộc US-NOM-001
- **N**egotiable: Format lý do (free text vs predefined options)
- **V**aluable: Buyer biết nguyên nhân để điều chỉnh
- **E**stimable: Đơn giản — update status + reason + notify
- **S**mall: < 1 sprint
- **T**estable: State + notification kiểm chứng được

---

## US-NOM-004: Buyer hủy Nomination

**Với tư cách là** Shipowner/Operator (Buyer),  
**tôi muốn** hủy nomination đã tạo,  
**để** dừng quy trình khi kế hoạch thay đổi.

### Tiêu chí chấp nhận (Acceptance Criteria)

**AC1: Hủy nomination DRAFT/PENDING**
- **Given** Buyer có nomination ở status DRAFT hoặc PENDING
- **When** Buyer nhấn Cancel
- **Then** nomination status chuyển sang CANCELLED

**AC2: Hủy nomination CONFIRMED**
- **Given** Buyer có nomination ở status CONFIRMED (đã có scheduling task)
- **When** Buyer nhấn Cancel và xác nhận
- **Then** nomination status = CANCELLED, scheduling task liên quan bị hủy, Supplier nhận thông báo

**AC3: Không cho hủy nomination đã kết thúc**
- **Given** Buyer có nomination ở status REJECTED hoặc CANCELLED
- **When** Buyer cố gắng cancel
- **Then** hệ thống hiển thị lỗi "Không thể hủy nomination ở trạng thái này"

### INVEST Validation
- **I**ndependent: Phụ thuộc US-NOM-001
- **N**egotiable: Confirmation dialog UX
- **V**aluable: Buyer kiểm soát được đơn hàng
- **E**stimable: State transition + cascade cancel logic
- **S**mall: 1 sprint
- **T**estable: Mỗi case có expected outcome rõ ràng

---

## US-NOM-005: Xem lịch sử Nomination

**Với tư cách là** Shipowner/Operator (Buyer) hoặc Supplier Admin,  
**tôi muốn** xem danh sách nomination đã tạo/nhận với bộ lọc,  
**để** theo dõi trạng thái và lịch sử giao dịch.

### Tiêu chí chấp nhận (Acceptance Criteria)

**AC1: Hiển thị danh sách**
- **Given** User đã đăng nhập
- **When** User mở trang Nomination History
- **Then** hệ thống hiển thị danh sách nomination có phân trang, sắp xếp theo created_at DESC

**AC2: Filter theo status**
- **Given** User đang xem danh sách nomination
- **When** User chọn filter status = CONFIRMED
- **Then** chỉ hiển thị các nomination có status = CONFIRMED

**AC3: Phân quyền hiển thị**
- **Given** Buyer đăng nhập
- **When** Buyer xem nomination history
- **Then** chỉ thấy nomination do mình tạo (buyer_id = current user)

### INVEST Validation
- **I**ndependent: Hoạt động độc lập khi có data
- **N**egotiable: UI layout, filter options có thể mở rộng
- **V**aluable: Transparency và tracking cho cả hai bên
- **E**stimable: Standard list + filter + pagination
- **S**mall: 1 sprint
- **T**estable: Data hiển thị đúng permission + filter

---

## US-NOM-006: Sanctions screening tự động

**Với tư cách là** hệ thống (System),  
**tôi muốn** tự động chạy sanctions screening khi nomination được submit,  
**để** đảm bảo tuân thủ quy định trước khi Supplier xử lý đơn hàng.

### Tiêu chí chấp nhận (Acceptance Criteria)

**AC1: Auto-trigger khi submit**
- **Given** Buyer submit nomination (DRAFT → PENDING)
- **When** hệ thống nhận sự kiện nomination submitted
- **Then** tự động gọi sanctions-kyc module để screen vessel IMO và buyer

**AC2: Clear — cho phép Supplier xử lý**
- **Given** Sanctions screening hoàn tất và kết quả = CLEAR
- **When** Supplier mở nomination
- **Then** Supplier có thể Confirm hoặc Reject bình thường

**AC3: Flagged — block xử lý**
- **Given** Sanctions screening hoàn tất và kết quả = FLAGGED
- **When** Supplier mở nomination
- **Then** nomination bị block, hiển thị cảnh báo "Sanctions screening failed — escalated to Compliance"

**AC4: Screening chưa hoàn tất**
- **Given** Sanctions screening đang chạy (chưa có kết quả)
- **When** Supplier mở nomination
- **Then** hiển thị trạng thái "Đang kiểm tra sanctions..." và disable nút Confirm/Reject

### INVEST Validation
- **I**ndependent: Phụ thuộc sanctions-kyc module
- **N**egotiable: Timeout handling, retry logic
- **V**aluable: Đảm bảo compliance trước khi giao dịch
- **E**stimable: Integration call + status tracking
- **S**mall: 1 sprint (integration + UI states)
- **T**estable: Mock sanctions API cho các scenario
