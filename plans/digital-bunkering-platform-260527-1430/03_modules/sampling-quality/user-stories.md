# User Stories — Sampling & Quality (SS 524)

## Epic: SAM — Quản lý mẫu nhiên liệu và chất lượng

Ghi nhận mẫu, liên kết delivery, giám sát retention, tracking lab results.

---

## US-SAM-001: Ghi nhận mẫu nhiên liệu

**Với tư cách là** Barge Operator,  
**tôi muốn** ghi nhận thông tin mẫu nhiên liệu đã lấy sau delivery,  
**để** hệ thống track mẫu và liên kết vào eBDN.

### Tiêu chí chấp nhận

**AC1: Record sample thành công**
- **Given** Delivery vừa complete, Operator đã dán nhãn và niêm phong mẫu
- **When** Operator nhập sample reference, sealed_by, storage_location
- **Then** sample record tạo, tự động link đến delivery, retention_end = +12 months

**AC2: Sample reference unique**
- **Given** Sample reference "SAM-2025-001" đã tồn tại
- **When** Operator nhập cùng reference
- **Then** hệ thống từ chối "Sample reference đã tồn tại"

### INVEST Validation
- **I**ndependent: Post-delivery action
- **N**egotiable: Reference format (barcode scan vs manual)
- **V**aluable: Compliance record + eBDN linkage
- **E**stimable: Form + create + link
- **S**mall: < 1 sprint
- **T**estable: Record created + linked correctly

---

## US-SAM-002: Giám sát Retention Period

**Với tư cách là** Supplier Admin,  
**tôi muốn** được thông báo khi sample sắp hết hạn lưu giữ,  
**để** quản lý kho mẫu và dispose đúng thời điểm.

### Tiêu chí chấp nhận

**AC1: Alert trước hết hạn**
- **Given** Sample retention_end còn 30 ngày
- **When** monitoring job chạy
- **Then** Admin nhận alert "Sample [ref] sắp hết hạn retention — còn 30 ngày"

**AC2: Eligible for disposal**
- **Given** retention_end đã qua
- **When** Admin xem sample list
- **Then** sample hiển thị badge "Eligible for disposal"

### INVEST Validation
- **I**ndependent: Scheduled check
- **N**egotiable: Alert threshold (30 ngày configurable)
- **V**aluable: Inventory management
- **E**stimable: Date compare + alert
- **S**mall: < 1 sprint
- **T**estable: Date-based trigger

---

## US-SAM-003: Tracking kết quả Lab

**Với tư cách là** Supplier Admin,  
**tôi muốn** ghi nhận kết quả phân tích từ phòng thí nghiệm,  
**để** theo dõi chất lượng nhiên liệu đã giao.

### Tiêu chí chấp nhận

**AC1: Update lab result**
- **Given** Sample đã gửi lab và nhận kết quả
- **When** Admin nhập lab_report_reference + result (PASS/FAIL)
- **Then** sample record updated, lab_result_status reflect result

**AC2: Alert on FAIL**
- **Given** Lab result = FAIL cho sample
- **When** Admin submit result
- **Then** hệ thống alert "Quality FAIL — sample [ref] — cần investigation"

### INVEST Validation
- **I**ndependent: Data entry action
- **N**egotiable: Lab integration (manual vs API)
- **V**aluable: Quality assurance
- **E**stimable: Update + alert
- **S**mall: < 1 sprint
- **T**estable: Status update + alert sent
