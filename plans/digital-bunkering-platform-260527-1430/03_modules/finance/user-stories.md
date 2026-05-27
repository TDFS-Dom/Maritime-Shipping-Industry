# User Stories — Finance & Settlements

## Epic: FIN — Quản lý tài chính và thanh toán

Tạo invoice, theo dõi payment, báo cáo aging, đối soát.

---

## US-FIN-001: Auto-generate Invoice từ eBDN

**Với tư cách là** hệ thống (System),  
**tôi muốn** tự động tạo invoice khi eBDN fully signed,  
**để** Supplier có hóa đơn gửi Buyer ngay sau delivery.

### Tiêu chí chấp nhận

**AC1: Auto-generate trigger**
- **Given** eBDN vừa FULLY_SIGNED, buyer có agreed price $600/MT, quantity = 500 MT
- **When** hệ thống process event
- **Then** invoice tạo: total = $300,000, due_date = issued + Net 30 (per buyer terms)

**AC2: Invoice number unique**
- **Given** Supplier đã có 200 invoices
- **When** invoice mới tạo
- **Then** invoice_number sequential unique (e.g., "INV-2025-0201")

### INVEST Validation
- **I**ndependent: Triggered by eBDN event
- **N**egotiable: Number format, auto-send to buyer
- **V**aluable: Automates billing
- **E**stimable: Calculate + create record
- **S**mall: 1 sprint
- **T**estable: Calculation accuracy

---

## US-FIN-002: Theo dõi thanh toán

**Với tư cách là** Supplier Admin (Finance),  
**tôi muốn** ghi nhận payment received cho invoice,  
**để** biết invoice nào đã thanh toán và chưa.

### Tiêu chí chấp nhận

**AC1: Record payment**
- **Given** Invoice INV-2025-0201 amount = $300,000
- **When** Finance nhập payment received $300,000 + reference
- **Then** invoice status = PAID, paid_at = now

**AC2: Overdue detection**
- **Given** Invoice due_date = 2025-06-15, today = 2025-06-16
- **When** hệ thống check daily
- **Then** invoice status = OVERDUE, alert Supplier Finance

### INVEST Validation
- **I**ndependent: Post-invoice action
- **N**egotiable: Partial payment, bank integration
- **V**aluable: Cash flow visibility
- **E**stimable: Status update + date check
- **S**mall: < 1 sprint
- **T**estable: Status transitions

---

## US-FIN-003: Aging Report

**Với tư cách là** Supplier Admin (Finance),  
**tôi muốn** xem báo cáo công nợ theo tuổi nợ,  
**để** ưu tiên collection cho invoices quá hạn lâu nhất.

### Tiêu chí chấp nhận

**AC1: Aging buckets**
- **Given** Finance mở Aging Report
- **When** report generates
- **Then** hiển thị: Current, 1-30, 31-60, 61-90, 90+ days với total per bucket

**AC2: Filter by buyer**
- **Given** Finance chọn buyer = "ABC Shipping"
- **When** filter applied
- **Then** chỉ hiển thị invoices của buyer đó

### INVEST Validation
- **I**ndependent: Read-only report
- **N**egotiable: Export format, bucket definitions
- **V**aluable: Collection prioritization
- **E**stimable: Query + group by date range
- **S**mall: 1 sprint
- **T**estable: Bucket calculation accuracy

---

## US-FIN-004: Reconciliation

**Với tư cách là** Supplier Admin (Finance),  
**tôi muốn** đối soát invoice vs payment nhận được,  
**để** phát hiện discrepancies (thiếu/thừa thanh toán).

### Tiêu chí chấp nhận

**AC1: Match check**
- **Given** Invoice $300,000 và payment received $300,000
- **When** reconciliation runs
- **Then** status = MATCHED, no discrepancy

**AC2: Under-payment flag**
- **Given** Invoice $300,000 và payment received $290,000
- **When** reconciliation runs
- **Then** flag "Under-payment: $10,000 short", alert Finance

### INVEST Validation
- **I**ndependent: Comparison logic
- **N**egotiable: Tolerance threshold, resolution workflow
- **V**aluable: Financial accuracy
- **E**stimable: Compare amounts + flag
- **S**mall: 1 sprint
- **T**estable: Match/mismatch scenarios
