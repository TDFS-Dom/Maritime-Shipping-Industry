# Wireframe Input — Nomination

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Nomination.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-NOM-01 | Nomination List (Supplier) | P-SUPP | Medium | Must |
| SCR-NOM-02 | Nomination Detail | P-SUPP | High | Must |
| SCR-NOM-03 | Nomination Create | P-BUYER | Medium | Must |
| SCR-NOM-04 | Nomination List (Buyer) | P-BUYER | Medium | Must |

---

## 2. Screen Descriptions

### SCR-NOM-01: Nomination List (Supplier)

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Supplier Admin / Operations Manager
- **Goal:** Xem tổng quan tất cả nominations nhận được, lọc theo trạng thái, tìm kiếm nhanh
- **Entry point:** Side nav → "Nominations" (menu item active)
- **Layout type:** Data table với filters
- **Content:**
  - **Page Header:** Title "Nominations" + badge đếm tổng số pending
  - **Filter Bar:** Status dropdown (All, Pending, Confirmed, Rejected, Cancelled), Search input (tìm theo reference/vessel name), Date range picker
  - **Data Table:**
    - Columns: Reference (text link), Vessel Name, Fuel Type (badge), Quantity (MT), Port, Delivery Window (date range), Status (badge), Created (relative time)
    - Sortable columns: Reference, Vessel, Quantity, Created
    - Row click → navigate to SCR-NOM-02
  - **Pagination:** Bottom, showing "1-20 of 156 nominations", page size selector (20/50/100)
- **Actions available:**
  - Row click → Detail view (SCR-NOM-02)
  - Filter/Search → updates table
  - Export button (top-right, secondary style) → download CSV
- **State variations:**
  - Empty: Illustration + "Chưa có nomination nào" + message
  - Loading: Skeleton rows (5 rows)
  - Error: Toast notification "Không thể tải dữ liệu"
  - Filtered empty: "Không tìm thấy kết quả phù hợp" + clear filters button
- **Mobile behavior:** Table → Card list. Mỗi card hiển thị: Reference, Vessel, Status badge, Quantity, Date. Filters → bottom sheet.

---

### SCR-NOM-02: Nomination Detail

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Supplier Admin / Operations Manager
- **Goal:** Xem chi tiết nomination, kiểm tra sanctions status, xác nhận hoặc từ chối
- **Entry point:** Click row từ SCR-NOM-01 hoặc notification link
- **Layout type:** Detail view (multi-section)
- **Content:**
  - **Page Header:** Breadcrumb (Nominations > NOM-2024-00156) + Status badge lớn
  - **Section 1 — Thông tin chính (Card):**
    - Reference number
    - Vessel: Name + IMO number
    - Fuel: Code + tên đầy đủ (e.g. "VLSFO380 — Very Low Sulphur Fuel Oil 380")
    - Quantity: xxx MT
    - Port: Singapore (hoặc port khác)
    - Delivery Window: start date → end date
    - Notes: free text từ buyer
  - **Section 2 — Sanctions Status (Card, PROMINENT):**
    - Badge lớn: CLEAR (green) / POTENTIAL_MATCH (yellow) / CONFIRMED_MATCH (red) / PENDING (gray)
    - Last checked timestamp
    - Provider info (OFAC, EU, UN)
    - Link "Xem chi tiết screening" → SCR-SAN-03
  - **Section 3 — Timeline (Card):**
    - Vertical timeline: Created → Sanctions Check → Confirmed/Rejected
    - Mỗi step: timestamp + actor + action
  - **Section 4 — Action Bar (sticky bottom hoặc header):**
    - Button "Confirm Nomination" (Primary, DISABLED nếu sanctions ≠ CLEAR)
    - Button "Reject" (Danger)
    - Button "Request Change" (Secondary)
- **Actions available:**
  - Confirm (primary) — chỉ enabled khi sanctions = CLEAR
  - Reject (danger) — opens confirmation dialog với reason input
  - Request Change (secondary) — opens form/modal
  - Back → quay lại list
- **State variations:**
  - Sanctions PENDING: Confirm button disabled + tooltip "Đang kiểm tra sanctions"
  - Sanctions POTENTIAL_MATCH: Confirm disabled + warning banner
  - Sanctions CLEAR: Confirm enabled (blue primary)
  - Already Confirmed: Buttons hidden, show confirmed info
  - Already Rejected: Buttons hidden, show rejection reason
- **Mobile behavior:** Sections stacked vertically. Action bar sticky bottom. Sanctions badge remains prominently visible above actions.

---

### SCR-NOM-03: Nomination Create

- **Portal:** P-BUYER (Desktop — side nav layout)
- **Actor:** Buyer (Shipowner/Operator)
- **Goal:** Tạo nomination mới gửi cho supplier
- **Entry point:** Button "New Nomination" từ SCR-NOM-04 hoặc Dashboard
- **Layout type:** Form (single page)
- **Content:**
  - **Page Header:** "Create Nomination" + breadcrumb
  - **Form fields (trong Card):**
    - Vessel IMO Number: Text input với auto-suggest (gõ IMO → dropdown danh sách vessel đã biết, hiển thị "IMO + Vessel Name")
    - Fuel Code: Dropdown, hiển thị "SS 709 Code — Tên đầy đủ" (ví dụ: "VLSFO380 — Very Low Sulphur Fuel Oil 380"). Có search/filter.
    - Quantity (MT): Number input, min 1, helper text "Metric Tonnes"
    - Port: Dropdown (danh sách ports khả dụng)
    - Delivery Window: Date range picker (start + end date), minimum 24h window
    - Notes: Textarea, optional, max 500 ký tự, char counter
  - **Action Bar:**
    - "Submit Nomination" (Primary) — disabled khi form invalid
    - "Save Draft" (Secondary)
    - "Cancel" (Ghost) — confirmation dialog nếu form dirty
- **Actions available:**
  - Submit → create nomination, navigate to detail/list
  - Save Draft → save without submitting
  - Cancel → discard, back to list
- **State variations:**
  - Empty form: All fields blank, Submit disabled
  - Partially filled: Submit disabled, Save Draft enabled
  - Valid form: Submit enabled (blue)
  - Submitting: Button shows loading spinner, all fields disabled
  - Success: Toast "Nomination submitted successfully" + redirect to list
  - Error: Inline field errors (red border + error text below field)
- **Mobile behavior:** Full-width form. Date picker → native device date picker. Dropdowns → bottom sheet with search. Action buttons stacked vertically at bottom.

---

### SCR-NOM-04: Nomination List (Buyer)

- **Portal:** P-BUYER (Desktop — side nav layout)
- **Actor:** Buyer (Shipowner/Operator)
- **Goal:** Xem danh sách nominations đã tạo, theo dõi trạng thái
- **Entry point:** Side nav → "Nominations" (menu item active)
- **Layout type:** Data table với filters
- **Content:**
  - **Page Header:** Title "My Nominations" + Button "New Nomination" (Primary, top-right)
  - **Filter Bar:** Status dropdown, Search input, Date range
  - **Data Table:**
    - Columns: Reference, Vessel Name, Fuel Type (badge), Quantity (MT), Port, Delivery Window, Status (badge), Created
    - Status badge colors: Pending (blue/info), Confirmed (green), Rejected (red), Draft (gray)
  - **Pagination:** Standard pagination bar
- **Actions available:**
  - "New Nomination" button → SCR-NOM-03
  - Row click → Nomination detail (buyer view, read-only)
  - Filter/Search
- **State variations:**
  - Empty: "Bạn chưa tạo nomination nào" + illustration + "Create your first nomination" button
  - Loading: Skeleton rows
  - Has data: Normal table
- **Mobile behavior:** Tương tự SCR-NOM-01 — table → card list.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Breadcrumb |
|---|---|---|---|
| SCR-NOM-01 | P-SUPP | Side nav → "Nominations" | — |
| SCR-NOM-02 | P-SUPP | Side nav → "Nominations" | Nominations > {Reference} |
| SCR-NOM-03 | P-BUYER | Side nav → "Nominations" | Nominations > Create |
| SCR-NOM-04 | P-BUYER | Side nav → "Nominations" | — |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Sanctions status badge PHẢI hiển thị rõ ràng trên SCR-NOM-02 **TRƯỚC** nút Confirm | Quy trình compliance bắt buộc — operator phải thấy trạng thái trước khi xác nhận |
| 2 | Nút Confirm DISABLED (gray, non-clickable) khi sanctions ≠ CLEAR | Business rule: không được confirm nomination cho vessel đang bị sanctions flag |
| 3 | Disabled button PHẢI có tooltip giải thích lý do | DESIGN.md §8 Don'ts: "Don't disable buttons without explaining why" |
| 4 | Fuel code dropdown hiển thị dạng "VLSFO380 — Very Low Sulphur Fuel Oil 380" | DESIGN.md §8 Don'ts: "Don't use technical codes as labels" — phải show code + tên đầy đủ |
| 5 | Vessel IMO input có auto-suggest dropdown (search-as-you-type) | UX requirement: tránh nhập sai IMO, giảm error rate |
| 6 | Status badges sử dụng màu từ DESIGN.md §3.2 + text + dot indicator | DESIGN.md §8 Don'ts: "Don't use color alone to convey status" |
| 7 | Reject action phải có confirmation dialog với reason input | DESIGN.md §8 Do's: "Confirm destructive actions with dialog" |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Reference | `NOM-YYYY-NNNNN` | Không truncate | Luôn hiển thị đầy đủ |
| Vessel Name | Title case | Truncate "..." tại 25 chars | Tooltip full name on hover |
| Fuel Type | Badge (pill) — code only trên table | — | Detail view show full name |
| Quantity | Number + " MT" | — | Thousand separator (1,500 MT) |
| Delivery Window | `dd MMM` – `dd MMM yyyy` | — | Ví dụ: "15 Jun – 20 Jun 2026" |
| Status | Badge pill (dot + text) | — | Colors per DESIGN.md §3.2 |
| Created | Relative time (<3 days), absolute date (≥3 days) | — | Per DESIGN.md §8 Do's |
| Pagination | "Showing 1-20 of 156" | — | Page size options: 20, 50, 100 |
| IMO Number | 7 digits, prefix "IMO" | — | Ví dụ: "IMO 9321483" |
