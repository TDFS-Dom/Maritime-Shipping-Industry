# User Stories — B2G Compliance Reporting

## Epic: B2G — Nộp báo cáo và tuân thủ MPA

Tự động nộp eBDN, giám sát deadline, tính toán emissions, đảm bảo compliance.

---

## US-B2G-001: Auto-submit eBDN to MPA

**Với tư cách là** hệ thống (System),  
**tôi muốn** tự động nộp eBDN cho MPA khi đã fully signed,  
**để** đảm bảo tuân thủ deadline mà không cần thao tác thủ công.

### Tiêu chí chấp nhận

**AC1: Auto-submit trigger**
- **Given** eBDN vừa chuyển status = FULLY_SIGNED
- **When** hệ thống nhận event
- **Then** tự động gửi data đến MPA B2G API, submission status = SUBMITTED

**AC2: Data integrity**
- **Given** eBDN có quantity = 500.123 MT, fuel = "RMG380"
- **When** submit to MPA
- **Then** data gửi đi match CHÍNH XÁC eBDN — không làm tròn, không thay đổi

**AC3: Auto-retry on failure**
- **Given** MPA API trả timeout error
- **When** retry mechanism kích hoạt
- **Then** retry với exponential backoff (1m → 5m → 15m → 60m), max 5 retries

### INVEST Validation
- **I**ndependent: Triggered by eBDN event
- **N**egotiable: Retry intervals, max retries
- **V**aluable: Automation compliance
- **E**stimable: API call + retry logic
- **S**mall: 1 sprint
- **T**estable: Mock MPA API responses

---

## US-B2G-002: Theo dõi trạng thái Submission

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem trạng thái mỗi submission gửi MPA,  
**để** biết eBDN nào đã accepted, rejected, hay đang pending.

### Tiêu chí chấp nhận

**AC1: View status list**
- **Given** Admin mở B2G Submissions
- **When** trang tải
- **Then** hiển thị danh sách submissions: eBDN ref, submitted date, status, MPA reference

**AC2: Rejection detail**
- **Given** Submission status = REJECTED
- **When** Admin click vào submission
- **Then** hiển thị rejection reason từ MPA + link đến eBDN resubmit flow

### INVEST Validation
- **I**ndependent: Read-only view
- **N**egotiable: UI layout
- **V**aluable: Visibility vào compliance status
- **E**stimable: List + detail
- **S**mall: < 1 sprint
- **T**estable: Status display accuracy

---

## US-B2G-003: Cảnh báo Deadline

**Với tư cách là** Supplier Admin,  
**tôi muốn** nhận cảnh báo khi sắp hết hạn nộp eBDN,  
**để** can thiệp kịp thời nếu có vấn đề.

### Tiêu chí chấp nhận

**AC1: Alert trước deadline**
- **Given** eBDN chưa submit, deadline còn < 4 giờ
- **When** monitoring job chạy
- **Then** Admin nhận alert "eBDN [ref] sắp hết hạn nộp — còn [X] giờ"

**AC2: Overdue highlight**
- **Given** eBDN đã quá deadline mà chưa ACCEPTED
- **When** Admin xem dashboard
- **Then** hiển thị highlight đỏ "OVERDUE" trên record

### INVEST Validation
- **I**ndependent: Scheduled monitoring
- **N**egotiable: Threshold time, alert channel
- **V**aluable: Prevent compliance violation
- **E**stimable: Timer + notification
- **S**mall: < 1 sprint
- **T**estable: Time-based trigger

---

## US-B2G-004: Tính toán CO₂ Emissions

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem CO₂ emissions tính từ mỗi delivery,  
**để** báo cáo và theo dõi environmental impact.

### Tiêu chí chấp nhận

**AC1: Auto-calculate**
- **Given** eBDN ACCEPTED, fuel = "RMG380" (emission factor = 3.114), quantity = 500 MT
- **When** hệ thống tính
- **Then** CO₂ = 500 × 3.114 = 1557.0 tonnes, lưu vào submission record

**AC2: Aggregate reporting**
- **Given** Admin xem compliance dashboard
- **When** chọn period = tháng 6/2025
- **Then** hiển thị tổng CO₂ tonnes cho period đó

### INVEST Validation
- **I**ndependent: Calculation logic
- **N**egotiable: Emission factors source, display format
- **V**aluable: Environmental compliance reporting
- **E**stimable: Multiply + aggregate
- **S**mall: < 1 sprint
- **T**estable: Math verification

---

## US-B2G-005: Compliance Dashboard

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem tổng quan compliance status trên dashboard,  
**để** nắm bắt nhanh tình hình tuân thủ.

### Tiêu chí chấp nhận

**AC1: KPI cards**
- **Given** Admin mở Compliance Dashboard
- **When** trang tải
- **Then** hiển thị: Total submissions (month), Acceptance rate %, Pending count, Overdue count

**AC2: Trend chart**
- **Given** Admin chọn view = 6 months
- **When** chart renders
- **Then** hiển thị trend line: submissions per month, acceptance rate per month

### INVEST Validation
- **I**ndependent: Aggregate data view
- **N**egotiable: Chart types, KPI selection
- **V**aluable: Executive visibility
- **E**stimable: Queries + charts
- **S**mall: 1 sprint
- **T**estable: Data aggregation accuracy
