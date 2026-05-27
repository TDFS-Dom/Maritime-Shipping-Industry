# User Stories — Analytics & BI

## Epic: ANA — Phân tích và báo cáo kinh doanh

Dashboards và metrics cho ra quyết định kinh doanh.

---

## US-ANA-001: Delivery Volume Dashboard

**Với tư cách là** Supplier Admin / Management,  
**tôi muốn** xem tổng lượng nhiên liệu đã giao theo thời gian,  
**để** đánh giá performance kinh doanh.

### Tiêu chí chấp nhận

**AC1: Volume chart**
- **Given** User mở Delivery Volume Dashboard
- **When** chọn period = tháng 6/2025
- **Then** hiển thị bar chart: total MT per day, tổng tháng ở header

**AC2: Filter by fuel type**
- **Given** User chọn fuel type = LNG
- **When** filter applied
- **Then** chart chỉ hiển thị deliveries LNG

### INVEST Validation
- **I**ndependent: Read-only aggregate
- **V**aluable: Business visibility
- **S**mall: 1 sprint
- **T**estable: Aggregation accuracy

---

## US-ANA-002: eBDN Turnaround Metrics

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem thời gian trung bình từ delivery complete đến eBDN fully signed,  
**để** cải thiện operational efficiency.

### Tiêu chí chấp nhận

**AC1: Average turnaround**
- **Given** User mở eBDN Metrics
- **When** dashboard loads
- **Then** hiển thị: avg time delivery→barge sign, avg time barge sign→vessel sign, total avg

**AC2: SLA highlight**
- **Given** eBDN mất > 2h để fully sign
- **When** displayed in list
- **Then** highlight đỏ "Exceeded 2h SLA"

### INVEST Validation
- **I**ndependent: Aggregate from eBDN timestamps
- **V**aluable: Operational improvement
- **S**mall: 1 sprint
- **T**estable: Time calculations

---

## US-ANA-003: Barge Utilization

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem tỷ lệ sử dụng (utilization %) của mỗi barge,  
**để** optimize fleet allocation.

### Tiêu chí chấp nhận

**AC1: Utilization per barge**
- **Given** User mở Barge Utilization
- **When** chọn period = tháng 6/2025
- **Then** hiển thị per barge: scheduled hours / total available hours = utilization %

**AC2: Identify idle barges**
- **Given** Barge ABC có utilization < 30%
- **When** displayed
- **Then** flag "Underutilized" để Scheduler review

### INVEST Validation
- **I**ndependent: Schedule data aggregate
- **V**aluable: Fleet optimization
- **S**mall: 1 sprint
- **T**estable: % calculation
