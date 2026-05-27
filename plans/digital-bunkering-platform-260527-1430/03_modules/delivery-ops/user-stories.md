# User Stories — Delivery Operations & Safety Checklists

## Epic: DEL — Vận hành giao nhận nhiên liệu

Quản lý quy trình bunkering thực tế, đảm bảo an toàn qua checklist, giám sát bơm, và ghi nhận dữ liệu MFM.

---

## US-DEL-001: Hoàn thành Pre-delivery Checklist

**Với tư cách là** Barge Operator,  
**tôi muốn** hoàn thành safety checklist trước khi bơm nhiên liệu,  
**để** đảm bảo tất cả điều kiện an toàn được thỏa mãn trước khi bắt đầu.

### Tiêu chí chấp nhận

**AC1: Load checklist theo fuel type**
- **Given** Delivery session đã initiated cho fuel type = LNG
- **When** hệ thống load checklist
- **Then** hiển thị standard checklist + IGF Code items bổ sung

**AC2: Tất cả items pass**
- **Given** Operator đã trả lời tất cả checklist items = PASS
- **When** Operator submit checklist
- **Then** hệ thống cho phép bắt đầu bơm (nút Start Pumping enabled)

**AC3: Có items fail**
- **Given** Operator đánh dấu 1 item = FAIL
- **When** Operator submit checklist
- **Then** hệ thống BLOCK bơm, hiển thị "Không thể bắt đầu bơm — checklist chưa hoàn thành"

**AC4: Redo items fail**
- **Given** Checklist có items FAIL
- **When** Operator fix issue và đánh dấu lại item = PASS
- **Then** hệ thống re-evaluate, nếu tất cả PASS → cho phép bơm

### INVEST Validation
- **I**ndependent: Phụ thuộc delivery session initiated
- **N**egotiable: UX checklist (toggle, checkbox, photo evidence)
- **V**aluable: Đảm bảo safety compliance
- **E**stimable: Form + validation logic
- **S**mall: 1 sprint
- **T**estable: Pass/fail scenarios rõ ràng

---

## US-DEL-002: Bắt đầu Delivery (Start Pumping)

**Với tư cách là** Barge Operator,  
**tôi muốn** bắt đầu bơm nhiên liệu sau khi checklist pass,  
**để** tiến hành giao hàng.

### Tiêu chí chấp nhận

**AC1: Start thành công**
- **Given** Checklist all_passed = true
- **When** Barge Operator nhấn Start Pumping
- **Then** status = PUMPING, MFM start reading ghi nhận, thời gian bắt đầu lưu lại

**AC2: Block nếu checklist chưa pass**
- **Given** Checklist chưa hoàn thành hoặc có items fail
- **When** Operator cố nhấn Start Pumping
- **Then** nút bị disable, hiển thị "Hoàn thành checklist trước khi bơm"

**AC3: Record location**
- **Given** Operator nhấn Start Pumping
- **When** hệ thống xử lý start
- **Then** GPS location (lat/long) được ghi nhận tự động

### INVEST Validation
- **I**ndependent: Phụ thuộc US-DEL-001
- **N**egotiable: Auto GPS vs manual entry
- **V**aluable: Chính thức bắt đầu delivery
- **E**stimable: Status update + MFM read + GPS
- **S**mall: < 1 sprint
- **T**estable: Precondition check + data recorded

---

## US-DEL-003: Giám sát quá trình bơm

**Với tư cách là** Barge Operator,  
**tôi muốn** theo dõi real-time flow rate và totalizer từ MFM,  
**để** biết tiến trình giao hàng và dừng đúng lúc.

### Tiêu chí chấp nhận

**AC1: Hiển thị real-time data**
- **Given** Delivery status = PUMPING
- **When** MFM gửi data stream
- **Then** UI hiển thị flow rate (m³/h), totalizer (MT), temperature, density cập nhật liên tục

**AC2: Progress indicator**
- **Given** Nomination quantity = 500 MT, MFM totalizer hiện tại = 400 MT
- **When** Operator xem monitoring screen
- **Then** hiển thị progress bar 80% và "Còn ~100 MT"

**AC3: Cảnh báo gần target**
- **Given** MFM totalizer đạt 95% target quantity
- **When** threshold triggered
- **Then** hiển thị cảnh báo "Sắp đạt target — chuẩn bị dừng bơm"

### INVEST Validation
- **I**ndependent: Phụ thuộc MFM data stream
- **N**egotiable: Refresh rate, UI layout
- **V**aluable: Operational visibility
- **E**stimable: WebSocket/SSE + render
- **S**mall: 1 sprint
- **T**estable: Data accuracy + alert threshold

---

## US-DEL-004: Hoàn tất Delivery

**Với tư cách là** Barge Operator,  
**tôi muốn** dừng bơm và ghi nhận MFM final reading,  
**để** kết thúc delivery và trigger tạo eBDN.

### Tiêu chí chấp nhận

**AC1: Complete thành công**
- **Given** Delivery status = PUMPING
- **When** Operator nhấn Stop Pumping và confirm MFM final reading
- **Then** status = COMPLETED, quantity_delivered tính từ MFM, GPS end location ghi nhận

**AC2: MFM final bắt buộc**
- **Given** Operator nhấn Complete
- **When** MFM final reading chưa được confirm
- **Then** hệ thống yêu cầu "Vui lòng xác nhận MFM final reading trước khi hoàn tất"

**AC3: Trigger eBDN**
- **Given** Delivery status vừa chuyển COMPLETED
- **When** hệ thống xử lý completion
- **Then** tự động trigger eBDN generation với delivery data

### INVEST Validation
- **I**ndependent: Phụ thuộc US-DEL-002
- **N**egotiable: MFM confirm flow (auto vs manual)
- **V**aluable: Kết thúc delivery, khởi tạo eBDN
- **E**stimable: Status update + calculation + event publish
- **S**mall: 1 sprint
- **T**estable: Data integrity + event triggered

---

## US-DEL-005: Checklist LNG với IGF Code

**Với tư cách là** Barge Operator giao LNG,  
**tôi muốn** có checklist bổ sung theo IGF Code,  
**để** đảm bảo tuân thủ quy định an toàn cho nhiên liệu LNG.

### Tiêu chí chấp nhận

**AC1: Template chứa IGF items**
- **Given** Delivery fuel type = LNG
- **When** checklist loaded
- **Then** bao gồm standard items + IGF Code items (gas detection, ventilation, ESD test...)

**AC2: IGF items cũng bắt buộc**
- **Given** Standard items all PASS nhưng IGF item = FAIL
- **When** submit checklist
- **Then** BLOCK pumping — IGF items có cùng enforcement level

### INVEST Validation
- **I**ndependent: Phụ thuộc US-DEL-001 framework
- **N**egotiable: Specific IGF items list (configurable)
- **V**aluable: LNG safety compliance
- **E**stimable: Template config + existing checklist engine
- **S**mall: < 1 sprint
- **T**estable: Correct template loaded per fuel type

---

## US-DEL-006: Checklist Ammonia với Toxicity Monitoring

**Với tư cách là** Barge Operator giao ammonia,  
**tôi muốn** có checklist bổ sung về giám sát độc tính,  
**để** đảm bảo an toàn cho nhân viên khi xử lý ammonia.

### Tiêu chí chấp nhận

**AC1: Toxicity items included**
- **Given** Delivery fuel type = Ammonia
- **When** checklist loaded
- **Then** bao gồm standard + toxicity monitoring items (PPE check, gas detector calibration, emergency shower verify...)

**AC2: Monitoring ongoing**
- **Given** Pumping đang diễn ra cho ammonia delivery
- **When** hệ thống active
- **Then** nhắc nhở kiểm tra toxicity levels theo interval (configurable)

### INVEST Validation
- **I**ndependent: Phụ thuộc US-DEL-001 framework
- **N**egotiable: Interval reminder, specific toxicity items
- **V**aluable: Crew safety cho ammonia handling
- **E**stimable: Template + reminder logic
- **S**mall: < 1 sprint
- **T**estable: Template correct + reminders fire

---

## US-DEL-007: Delivery Method Enforcement

**Với tư cách là** hệ thống (System),  
**tôi muốn** enforce delivery method = SHIP_TO_SHIP khi SS 648/600 applicable,  
**để** đảm bảo tuân thủ MPA regulations.

### Tiêu chí chấp nhận

**AC1: Auto-set delivery method**
- **Given** Delivery tại Singapore port, SS 648/600 applicable
- **When** delivery session initiated
- **Then** delivery_method tự động set = SHIP_TO_SHIP, không cho phép thay đổi

**AC2: Display method rõ ràng**
- **Given** Delivery method đã set
- **When** Operator xem delivery screen
- **Then** hiển thị rõ "Delivery Method: Ship-to-Ship (SS 648/600)"

### INVEST Validation
- **I**ndependent: Logic enforcement thuần system
- **N**egotiable: Khi nào SS 648/600 "applicable" (port-based config)
- **V**aluable: Regulatory compliance
- **E**stimable: Rule lookup + enforce
- **S**mall: < 1 sprint
- **T**estable: Correct method per port/regulation
