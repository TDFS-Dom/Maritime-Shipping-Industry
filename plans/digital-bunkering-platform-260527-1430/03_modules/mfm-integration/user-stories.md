# User Stories — MFM Data Integration

## Epic: MFM — Tích hợp dữ liệu Mass Flow Meter

Nhận, validate, lưu trữ dữ liệu MFM từ barge và cung cấp cho các module downstream.

---

## US-MFM-001: Đăng ký MFM Meter

**Với tư cách là** Supplier Admin,  
**tôi muốn** đăng ký MFM meter mới vào hệ thống cho barge,  
**để** hệ thống nhận diện và validate data từ meter đó.

### Tiêu chí chấp nhận

**AC1: Đăng ký thành công**
- **Given** Supplier Admin nhập serial, manufacturer, model, MPA approval number, calibration date
- **When** submit registration
- **Then** meter được đăng ký, gắn với barge, status = ACTIVE

**AC2: MPA approval bắt buộc**
- **Given** Admin bỏ trống MPA approval number
- **When** submit
- **Then** hệ thống từ chối "MFM phải có số phê duyệt MPA"

**AC3: Cảnh báo calibration**
- **Given** Meter có next_calibration_due trong vòng 30 ngày
- **When** Admin xem danh sách meters
- **Then** hiển thị cảnh báo "Calibration sắp hết hạn"

### INVEST Validation
- **I**ndependent: Setup task, không phụ thuộc flow khác
- **N**egotiable: Calibration warning threshold (30 ngày configurable)
- **V**aluable: Precondition cho MFM data flow
- **E**stimable: CRUD + validation
- **S**mall: < 1 sprint
- **T**estable: Registration + warning logic

---

## US-MFM-002: Nhận MFM Data Stream

**Với tư cách là** hệ thống (System),  
**tôi muốn** nhận data stream từ MFM device trong suốt quá trình bơm,  
**để** có dữ liệu real-time cho monitoring và final reading.

### Tiêu chí chấp nhận

**AC1: Nhận reading thành công**
- **Given** MFM device gửi reading (serial, flow_rate, totalizer, temperature, density)
- **When** API nhận request
- **Then** validate, store, broadcast to delivery-ops module

**AC2: Reject meter chưa đăng ký**
- **Given** Reading từ meter serial không tồn tại trong registry
- **When** API nhận request
- **Then** reject với error "Meter serial không được đăng ký", log alert

**AC3: Serial không match barge**
- **Given** Meter serial ABC đăng ký cho Barge X, nhưng delivery hiện tại dùng Barge Y
- **When** reading từ ABC đến
- **Then** reject "Meter serial không match barge đang active", flag for review

### INVEST Validation
- **I**ndependent: Phụ thuộc US-MFM-001 (meter registered)
- **N**egotiable: Data format, transport protocol (MQTT, HTTP)
- **V**aluable: Core data pipeline cho toàn hệ thống
- **E**stimable: API endpoint + validation + store + broadcast
- **S**mall: 1 sprint
- **T**estable: Valid/invalid serial scenarios

---

## US-MFM-003: Phát hiện bất thường (Anomaly Detection)

**Với tư cách là** Barge Operator,  
**tôi muốn** được cảnh báo khi MFM data có dấu hiệu bất thường,  
**để** kiểm tra và xử lý kịp thời.

### Tiêu chí chấp nhận

**AC1: Flow spike detection**
- **Given** Reading trước có flow_rate = 100 m³/h
- **When** reading mới có flow_rate = 180 m³/h (spike > 50%)
- **Then** reading flagged is_anomaly = true, anomaly_type = "FLOW_SPIKE", alert Operator

**AC2: Flow drop detection**
- **Given** Reading trước có flow_rate = 100 m³/h
- **When** reading mới có flow_rate = 30 m³/h (drop > 50%)
- **Then** reading flagged is_anomaly = true, anomaly_type = "FLOW_DROP", alert Operator

**AC3: Data vẫn được lưu**
- **Given** Reading bị flag anomaly
- **When** processing
- **Then** reading vẫn stored (không discard), kèm anomaly flag

### INVEST Validation
- **I**ndependent: Enhancement trên US-MFM-002
- **N**egotiable: Threshold percentages (configurable)
- **V**aluable: Early warning cho equipment issues
- **E**stimable: Compare consecutive readings + alert
- **S**mall: < 1 sprint
- **T**estable: Threshold-based scenarios

---

## US-MFM-004: Cung cấp Final Reading cho eBDN

**Với tư cách là** hệ thống (ebdn module),  
**tôi muốn** query MFM final reading khi delivery hoàn tất,  
**để** auto-populate quantity trong eBDN.

### Tiêu chí chấp nhận

**AC1: Return final reading**
- **Given** MFM session đã completed cho delivery X
- **When** eBDN module query final reading
- **Then** trả về: quantity_delivered, start_totalizer, end_totalizer, meter_serial, session timestamps

**AC2: Session chưa complete**
- **Given** MFM session vẫn ACTIVE (delivery chưa xong)
- **When** eBDN module query
- **Then** return error "MFM session chưa hoàn tất"

**AC3: Data integrity**
- **Given** Final reading returned
- **When** eBDN uses quantity
- **Then** quantity = end_totalizer - start_totalizer, đúng 3 decimal places (MT)

### INVEST Validation
- **I**ndependent: API query, phụ thuộc session completed
- **N**egotiable: Response format
- **V**aluable: Source of truth cho eBDN quantity
- **E**stimable: Query + calculate
- **S**mall: < 1 sprint
- **T**estable: Calculation accuracy
