# Wireframe Input — Finance

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Finance.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-FIN-01 | Finance Overview | P-SUPP | High | Must |
| SCR-FIN-02 | Invoice List | P-SUPP | Medium | Must |
| SCR-FIN-03 | Invoice Detail | P-SUPP | High | Must |
| SCR-FIN-04 | Record Payment | P-SUPP | Low | Must |

---

## 2. Screen Descriptions

### SCR-FIN-01: Finance Overview

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Finance Officer / Supplier Admin
- **Goal:** Tổng quan tình hình tài chính — outstanding, overdue, paid, aging analysis
- **Entry point:** Side nav → "Finance" (menu item active)
- **Layout type:** Dashboard (KPIs + chart + list)
- **Content:**
  - **Page Header:** "Finance Overview" + Currency selector (nếu multi-currency) + Date range indicator
  - **KPI Cards Row (top, 3 cards ngang):**
    - Total Outstanding: Amount (USD $XXX,XXX.XX) — blue/info
    - Overdue Amount: Amount (USD $XXX,XXX.XX) — red/error, bold
    - Paid This Month: Amount (USD $XXX,XXX.XX) — green/success
  - **Aging Chart Section (Card):**
    - Title: "Aging Analysis"
    - Bar chart (vertical bars):
      - X-axis: 0-30 days, 31-60 days, 61-90 days, 90+ days
      - Y-axis: Amount (USD)
      - Bar colors: Status colors (green → yellow → orange → red) theo aging bracket
    - Total per bracket shown above/inside bar
  - **Recent Invoices Section (Card):**
    - Title: "Recent Invoices" + "View All →" link (→ SCR-FIN-02)
    - Mini table (5 most recent):
      - Columns: Reference, Buyer, Amount, Due Date, Status badge
    - Row click → SCR-FIN-03
- **Actions available:**
  - Click KPI → filter invoice list
  - Click aging bar → filter by aging bracket
  - "View All" → SCR-FIN-02
  - Click recent invoice → SCR-FIN-03
- **State variations:**
  - Healthy (no overdue): Overdue KPI = $0, green text "All on track"
  - Has overdue: Overdue KPI red, aging chart shows distribution
  - Empty (no invoices): "Chưa có hóa đơn nào" + illustration
  - Loading: Skeleton KPIs + chart + table
- **Mobile behavior:** KPI cards → stacked vertically (full-width each). Aging chart → horizontal bars. Recent invoices → card list.

---

### SCR-FIN-02: Invoice List

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Finance Officer / Supplier Admin
- **Goal:** Quản lý toàn bộ invoices, lọc theo trạng thái, buyer, ngày
- **Entry point:** Side nav → "Finance" → sub "Invoices", hoặc "View All" từ dashboard
- **Layout type:** Data table với filters
- **Content:**
  - **Page Header:** "Invoices" + Button "Create Invoice" (Primary, top-right)
  - **Filter Bar:** Status dropdown (All, Draft, Issued, Paid, Partially Paid, Overdue, Cancelled), Buyer dropdown, Date range picker
  - **Data Table:**
    - Columns: Invoice Ref (link), Buyer Name, Amount (right-aligned), Due Date, Status (badge), Overdue Days (red text nếu >0)
    - **Overdue highlighting:**
      - Overdue rows: Red text cho "Overdue Days" column
      - Overdue badge: "Overdue" red pill + "X days" suffix
    - Sortable: Ref, Buyer, Amount, Due Date, Overdue Days
    - Row click → SCR-FIN-03
  - **Pagination:** Standard (20/50/100)
  - **Summary Row (bottom of table, optional):**
    - "Total Outstanding: $XXX,XXX.XX | Overdue: $XX,XXX.XX"
- **Actions available:**
  - "Create Invoice" → invoice creation flow
  - Row click → Detail (SCR-FIN-03)
  - Filter/Search
  - Export (CSV/PDF)
  - Bulk actions: Select multiple → "Mark as Sent"
- **State variations:**
  - Empty: "Chưa có invoice nào" + "Create your first invoice" button
  - Loading: Skeleton rows
  - Filtered empty: "Không có kết quả" + clear filters
  - Has overdue: Overdue rows visually distinct (red text + badge)
- **Mobile behavior:** Table → Card list. Card: Ref, Buyer, Amount (large), Status badge, Due date. Overdue cards red left-border.

---

### SCR-FIN-03: Invoice Detail

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Finance Officer / Supplier Admin
- **Goal:** Xem chi tiết invoice, line items, payment history, ghi nhận thanh toán
- **Entry point:** Click row từ SCR-FIN-02 hoặc dashboard
- **Layout type:** Detail (document-style + side panels)
- **Content:**
  - **Page Header:** Breadcrumb (Invoices > INV-2024-00789) + Status badge + Actions dropdown
  - **Section 1 — Invoice Header (Card, document-style):**
    - From (Supplier): Company name, address, registration
    - To (Buyer): Company name, address, contact
    - Invoice Reference: INV-YYYY-NNNNN
    - Issue Date + Due Date
    - Payment Terms (e.g. "Net 30")
  - **Section 2 — Line Items (Table trong Card):**
    - Columns: # (row number), Description, Fuel Type, Quantity (MT), Unit Price ($/MT), Total
    - Example row: "1 | VLSFO380 delivery — Vessel MV Pacific Star | VLSFO380 | 500.00 | $520.00 | $260,000.00"
    - Footer: Subtotal, Tax (if applicable), **Grand Total** (bold, large)
    - All amounts: 2 decimal places + currency symbol
  - **Section 3 — Payment History (Card):**
    - Title: "Payment History"
    - Table: Date, Amount, Method, Reference, Recorded By
    - Running balance: "Remaining: $XX,XXX.XX"
    - If no payments: "No payments recorded yet"
  - **Section 4 — Reconciliation Status (Card):**
    - Status: Unreconciled / Partially Reconciled / Fully Reconciled
    - Linked deliveries/eBDNs
  - **Action Bar:**
    - "Record Payment" (Primary) → opens SCR-FIN-04
    - "Issue Invoice" (Secondary) — nếu status = Draft
    - "Download PDF" (Ghost)
    - "Send to Buyer" (Secondary)
- **Actions available:**
  - Record Payment → SCR-FIN-04 modal
  - Issue (if draft)
  - Download PDF
  - Send (email/notification to buyer)
  - Edit (if draft only)
- **State variations:**
  - Draft: "Issue" + "Edit" available, no payments
  - Issued: "Record Payment" available
  - Partially Paid: Payment history shown, remaining balance highlighted
  - Fully Paid: Green "PAID" badge, no "Record Payment" button
  - Overdue: Red banner top "⚠️ Overdue by X days"
  - Cancelled: Gray overlay "CANCELLED", no actions
- **Mobile behavior:** Sections stacked. Line items table scrollable horizontally. Action buttons sticky bottom. Amounts always fully visible (no truncation).

---

### SCR-FIN-04: Record Payment

- **Portal:** P-SUPP (Desktop — Modal overlay)
- **Actor:** Finance Officer
- **Goal:** Ghi nhận thanh toán nhận được cho invoice
- **Entry point:** "Record Payment" button từ SCR-FIN-03
- **Layout type:** Form (modal small/medium)
- **Content:**
  - **Modal Header:** "Record Payment" + Close button (X)
  - **Context Info (top, read-only):**
    - Invoice: INV-YYYY-NNNNN
    - Total: $XXX,XXX.XX
    - **Remaining Balance: $XX,XXX.XX** (bold, prominent)
  - **Form Fields:**
    - Amount (USD): Number input
      - Placeholder: "0.00"
      - VALIDATION: Cannot exceed remaining balance
      - If exceeded: inline error "Số tiền không được vượt quá số dư còn lại ($XX,XXX.XX)"
      - Helper text: "Remaining after payment: $XX,XXX.XX" (live calculated)
    - Payment Date: Date picker (default: today)
    - Payment Method: Dropdown (Bank Transfer, Wire, Cash, Check, Other)
    - Reference Number: Text input (e.g. bank transaction ref), optional
  - **Action Bar (modal footer):**
    - "Cancel" (Ghost, left)
    - "Record Payment" (Primary, right) — disabled if amount=0 or amount > remaining
- **Actions available:**
  - Enter payment details
  - Submit → records payment, updates invoice status, closes modal
  - Cancel → close modal, no changes
- **State variations:**
  - Empty: All fields blank, Submit disabled
  - Valid: Amount ≤ remaining, Submit enabled
  - Amount exceeds balance: Red inline error, Submit disabled
  - Submitting: Loading button, fields disabled
  - Success: Modal closes + toast "Payment of $X,XXX.XX recorded successfully" + invoice detail refreshes
- **Mobile behavior:** Modal → full-screen sheet (bottom sheet). Amount field large (48px height). Date picker native. Method dropdown → bottom sheet selector.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Breadcrumb |
|---|---|---|---|
| SCR-FIN-01 | P-SUPP | Side nav → "Finance" | — |
| SCR-FIN-02 | P-SUPP | Side nav → "Finance" | Finance > Invoices |
| SCR-FIN-03 | P-SUPP | Side nav → "Finance" | Finance > Invoices > {Reference} |
| SCR-FIN-04 | P-SUPP | Side nav → "Finance" | (modal overlay, no breadcrumb change) |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Invoices overdue PHẢI highlighted (red text + badge "Overdue X days") | Finance officer cần nhận diện ngay invoices quá hạn |
| 2 | Payment amount CANNOT exceed remaining balance — inline validation real-time | Business rule: tránh overpayment, lỗi kế toán |
| 3 | Aging chart PHẢI dùng consistent status colors từ design system | Visual consistency, quick comprehension |
| 4 | Tất cả invoice amounts hiển thị 2 decimal places + currency symbol | Chuẩn tài chính quốc tế, tránh nhầm lẫn |
| 5 | Grand Total trong invoice detail phải là số LỚN NHẤT, NỔI BẬT NHẤT | Key information — finance officer cần thấy ngay |
| 6 | "Record Payment" disabled khi amount = 0 hoặc > remaining + tooltip giải thích | DESIGN.md: "Don't disable buttons without explaining why" |
| 7 | Amounts right-aligned trong tables | Chuẩn hiển thị số liệu tài chính — dễ scan vertically |
| 8 | Aging chart brackets PHẢI là 0-30, 31-60, 61-90, 90+ (industry standard) | Standard accounting aging brackets |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Invoice Reference | `INV-YYYY-NNNNN` | Không truncate | Link style |
| Amount | `$XXX,XXX.XX` | — | 2 decimals, thousand separator, currency symbol prefix |
| Buyer Name | Title case | Max 30 chars trên table | Tooltip full name |
| Due Date | `dd MMM yyyy` | — | Red text nếu overdue |
| Overdue Days | `X days` | — | Red text, chỉ hiện khi >0 |
| Status | Badge pill (dot + text) | — | Draft(gray), Issued(blue), Paid(green), Overdue(red), Partial(yellow) |
| Payment Date | `dd MMM yyyy` | — | In payment history |
| Payment Method | Title case | — | "Bank Transfer", "Wire", etc. |
| Unit Price | `$XXX.XX/MT` | — | Per metric tonne |
| Quantity | `XXX.XX MT` | — | 2 decimals + unit |
| Aging brackets | "0-30", "31-60", "61-90", "90+" | — | Labels trên chart |
| Currency | ISO code prefix (USD, SGD) hoặc symbol ($, S$) | — | Consistent throughout |
| Remaining Balance | Bold, larger font (16px) | — | Highlighted in Record Payment modal |
