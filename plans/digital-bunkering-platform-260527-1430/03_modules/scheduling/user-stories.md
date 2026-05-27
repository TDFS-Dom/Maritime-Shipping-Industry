# User Stories — Scheduling & Dispatch

## Epic: SCH — Phân bổ lịch và điều phối barge

Cho phép Scheduler quản lý barge fleet, phân bổ time slots, phát hiện xung đột, và dispatch barge.

---

## US-SCH-001: Xem Barge Availability

**Với tư cách là** Supplier Admin (Scheduler),  
**tôi muốn** xem lịch trống/bận của từng barge trong fleet,  
**để** chọn barge phù hợp cho nomination mới.

### Tiêu chí chấp nhận

**AC1: Hiển thị timeline**
- **Given** Scheduler mở trang Barge Availability
- **When** trang tải xong
- **Then** hiển thị timeline/calendar của tất cả barge với các slot đã book (màu), turnaround gaps, và slot trống

**AC2: Filter theo fuel type**
- **Given** Nomination yêu cầu fuel type = LNG
- **When** Scheduler filter barge theo fuel certification
- **Then** chỉ hiển thị barge có chứng nhận vận chuyển LNG

**AC3: Hiển thị turnaround gaps**
- **Given** Barge có slot kết thúc lúc 14:00
- **When** Scheduler xem availability
- **Then** slot 14:00–16:00 hiển thị là turnaround (2h), không thể book

### INVEST Validation
- **I**ndependent: Hoạt động khi có barge data
- **N**egotiable: UI format (Gantt, calendar, list)
- **V**aluable: Scheduler đưa ra quyết định phân bổ chính xác
- **E**stimable: Query barge + schedule data + render
- **S**mall: 1 sprint
- **T**estable: Visual + data accuracy

---

## US-SCH-002: Phân bổ Time Slot

**Với tư cách là** Supplier Admin (Scheduler),  
**tôi muốn** phân bổ time slot cho barge giao nomination,  
**để** lên lịch chính thức cho delivery.

### Tiêu chí chấp nhận

**AC1: Allocate thành công**
- **Given** Scheduler chọn barge có slot trống trong delivery window
- **When** Scheduler allocate slot (start + end time)
- **Then** schedule record được tạo status = PLANNED, hiển thị trên calendar

**AC2: Slot ngoài delivery window**
- **Given** Nomination có delivery window 01/06 08:00 → 03/06 18:00
- **When** Scheduler chọn slot 04/06 10:00
- **Then** hệ thống cảnh báo "Slot nằm ngoài delivery window của nomination"

**AC3: Barge không certified**
- **Given** Nomination yêu cầu LNG, barge ABC chỉ certified conventional fuel
- **When** Scheduler chọn barge ABC
- **Then** hệ thống từ chối "Barge không có chứng nhận cho loại nhiên liệu này"

### INVEST Validation
- **I**ndependent: Phụ thuộc nomination confirmed
- **N**egotiable: Cách chọn slot (drag-drop, form input)
- **V**aluable: Chuyển nomination sang scheduled state
- **E**stimable: Create record + validation logic
- **S**mall: 1 sprint
- **T**estable: Record created + constraints enforced

---

## US-SCH-003: Phát hiện xung đột lịch

**Với tư cách là** hệ thống (System),  
**tôi muốn** tự động phát hiện xung đột khi allocate slot,  
**để** đảm bảo không barge nào bị book trùng.

### Tiêu chí chấp nhận

**AC1: Detect overlapping**
- **Given** Barge X đã có slot 10:00–14:00 ngày 01/06
- **When** Scheduler cố allocate slot 12:00–16:00 cùng ngày cho Barge X
- **Then** hệ thống hiển thị lỗi conflict và suggest slot 16:00–20:00

**AC2: Detect turnaround violation**
- **Given** Barge X slot kết thúc 14:00, turnaround = 2h
- **When** Scheduler cố allocate slot 15:00 (chỉ 1h sau)
- **Then** hệ thống cảnh báo "Vi phạm turnaround time tối thiểu (2 giờ)"

**AC3: Suggest alternatives**
- **Given** Conflict được phát hiện
- **When** hệ thống hiển thị cảnh báo
- **Then** kèm theo gợi ý 1-3 slot thay thế phù hợp

### INVEST Validation
- **I**ndependent: Logic thuần backend
- **N**egotiable: Algorithm suggest slot
- **V**aluable: Tránh double-booking
- **E**stimable: Query + compare time ranges
- **S**mall: 1 sprint
- **T**estable: Edge cases rõ ràng

---

## US-SCH-004: Dispatch Barge

**Với tư cách là** Supplier Admin (Scheduler),  
**tôi muốn** dispatch barge khi đến thời điểm giao hàng,  
**để** barge operator nhận lệnh và di chuyển đến vessel.

### Tiêu chí chấp nhận

**AC1: Dispatch thành công**
- **Given** Schedule có status = PLANNED và đã đến/gần giờ slot_start
- **When** Scheduler nhấn Dispatch
- **Then** status chuyển DISPATCHED, Barge Operator nhận thông báo kèm thông tin vessel + location

**AC2: Không dispatch khi chưa đến giờ**
- **Given** Schedule có slot_start còn > 4 giờ nữa
- **When** Scheduler nhấn Dispatch
- **Then** hệ thống hỏi xác nhận "Dispatch sớm hơn lịch. Bạn có chắc chắn?"

**AC3: Update ETA**
- **Given** Barge đã dispatched
- **When** Barge Operator cập nhật ETA
- **Then** Vessel nhận thông báo ETA mới

### INVEST Validation
- **I**ndependent: Phụ thuộc US-SCH-002
- **N**egotiable: ETA update frequency
- **V**aluable: Khởi động delivery process
- **E**stimable: Status update + notification
- **S**mall: 1 sprint
- **T**estable: State transition + notification sent

---

## US-SCH-005: Reschedule

**Với tư cách là** Supplier Admin (Scheduler),  
**tôi muốn** dời lịch delivery khi có thay đổi,  
**để** đáp ứng tình huống thực tế (barge hỏng, thời tiết xấu, vessel delay).

### Tiêu chí chấp nhận

**AC1: Reschedule từ PLANNED**
- **Given** Schedule status = PLANNED
- **When** Scheduler chọn Reschedule và chọn slot mới
- **Then** schedule cũ status = CANCELLED, schedule mới được tạo PLANNED, thông báo các bên

**AC2: Reschedule từ DISPATCHED**
- **Given** Schedule status = DISPATCHED (barge đang di chuyển)
- **When** Scheduler chọn Reschedule
- **Then** barge được recall, schedule cũ CANCELLED, schedule mới tạo, re-dispatch khi sẵn sàng

**AC3: Không reschedule khi IN_PROGRESS**
- **Given** Schedule status = IN_PROGRESS (delivery đang diễn ra)
- **When** Scheduler cố reschedule
- **Then** hệ thống từ chối "Không thể dời lịch khi delivery đang thực hiện"

### INVEST Validation
- **I**ndependent: Phụ thuộc US-SCH-002, US-SCH-004
- **N**egotiable: Recall process cho dispatched barge
- **V**aluable: Flexibility vận hành thực tế
- **E**stimable: Cancel + re-create + notify
- **S**mall: 1 sprint
- **T**estable: State transitions + cascade effects
