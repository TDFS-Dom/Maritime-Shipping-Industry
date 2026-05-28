# Wireframe Input — Sampling & Quality

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Sampling & Quality.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Module quản lý fuel samples theo SS 524. Retention period tracking là chức năng chính — samples expiring soon cần visual alerting rõ ràng.

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-SAM-01 | Sample List | P-SUPP | Medium | Should |
| SCR-SAM-02 | Retention Report | P-SUPP | Medium | Should |

---

## 2. Screen Descriptions

### SCR-SAM-01: Sample List

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Quản lý danh sách fuel samples: theo dõi retention status, identify samples sắp hết hạn lưu giữ
- **Entry point:** Sidebar menu → "Quality" → "Samples"
- **Layout type:** Data table + filter bar
- **Content:**
  - **Page Header:**
    - Title: "Fuel Samples" (28px, bold)
    - Subtitle: "Quản lý mẫu nhiên liệu và retention period (SS 524)"
  - **Filter Bar (above table):**
    - Status: dropdown (All / IN_RETENTION / EXPIRING_SOON / EXPIRED / DISPOSED)
    - Date Range: date picker (collected date from – to)
    - Search: text input "Tìm theo sample ref hoặc delivery ref..."
  - **Data Table:**
    - Columns: Sample Ref | Delivery Ref | Fuel Type | Volume (ml) | Method | Collected Date | Retention Expiry | Status
    - Sample Ref: monospace (e.g., "SAM-2026-0142")
    - Delivery Ref: linked text → navigate to delivery detail
    - Fuel Type: text (e.g., "VLSFO")
    - Volume: `XXX ml` (e.g., "750 ml")
    - Method: text (e.g., "Continuous Drip", "Manual Spot")
    - Collected Date: `dd/MM/yyyy`
    - Retention Expiry: `dd/MM/yyyy` + color-coded:
      - OK (>30 days): normal text
      - Expiring within 30 days: yellow text + "Expiring" badge
      - Expiring within 7 days: orange text + "Urgent" badge
      - Expired (past date): red text + "Expired" badge
    - Status column: pill badge color-coded:
      - IN_RETENTION: green
      - EXPIRING_SOON: yellow
      - EXPIRED: red
      - DISPOSED: gray
  - **Default Sort:** Retention Expiry ASC (sắp hết hạn lên đầu)
  - **Pagination:** Bottom, 20 items/page
  - **Info footer:** "Minimum volume theo SS 524: 750 ml. Volume hiển thị nhưng không chỉnh sửa."
- **Actions available:**
  - Filter by status, date range
  - Search by sample ref / delivery ref
  - Sort by column (default: expiry ascending)
  - Click delivery ref → navigate to delivery detail
  - Click sample row → sample detail (future)
- **State variations:**
  - Has data: Table populated with sample rows
  - Empty: "Chưa có sample nào" + illustration
  - Loading: Skeleton rows
  - Filtered (no results): "Không tìm thấy sample phù hợp" + "Clear filters"
  - Many expiring: Multiple yellow/orange/red rows (visual urgency)
- **Mobile behavior:** Card list. Each card: sample ref + fuel type + status badge + expiry date (color-coded). Sorted by expiry. Filters → collapsible panel.

---

### SCR-SAM-02: Retention Report

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Dashboard overview cho retention status: quickly identify expired/expiring samples cần action
- **Entry point:** Sidebar menu → "Quality" → "Retention Report" hoặc link từ dashboard alerts
- **Layout type:** Summary cards + data table
- **Content:**
  - **Page Header:**
    - Title: "Retention Report" (28px, bold)
    - Subtitle: "Tổng quan tình trạng lưu giữ mẫu nhiên liệu"
  - **Summary Cards Row (top, 3 cards):**
    - **Card 1 — "Expiring ≤ 30 days" (YELLOW border/accent):**
      - Large number (count of samples)
      - Subtitle: "samples hết hạn trong 30 ngày"
      - Icon: ⏰ hoặc warning icon (yellow)
    - **Card 2 — "Expiring ≤ 7 days" (ORANGE border/accent):**
      - Large number (count)
      - Subtitle: "samples hết hạn trong 7 ngày — cần action"
      - Icon: ⚠️ (orange)
    - **Card 3 — "Expired & Undisposed" (RED border/accent):**
      - Large number (count)
      - Subtitle: "samples đã hết hạn, chưa được dispose"
      - Icon: 🚨 (red)
  - **Action Bar (below cards):**
    - "View All Expiring" → filter SCR-SAM-01 với status=EXPIRING_SOON
    - "Export Report" → download CSV (optional)
  - **Detail Table (below cards) — consolidated list of all attention-needed samples:**
    - Title: "Samples cần xử lý"
    - Columns: Sample Ref | Delivery Ref | Fuel Type | Retention Expiry | Days Remaining | Status | Action
    - Days Remaining:
      - Positive (e.g., "12 days"): yellow/orange text
      - Zero/Negative (e.g., "-5 days" = overdue): red text + bold
    - Status badges: same color as SCR-SAM-01
    - Action column: "Mark Disposed" button (for EXPIRED samples)
    - **Sort:** Days Remaining ASC (most urgent first)
  - **Empty state (all samples OK):**
    - "✅ Tất cả samples đều trong retention period" + green checkmark illustration
    - "Không có sample nào cần xử lý"
- **Actions available:**
  - View summary cards (at a glance)
  - Click card → filter table below
  - Mark sample as DISPOSED
  - Navigate to sample detail / delivery detail
  - Export report (CSV)
- **State variations:**
  - Has attention items: Cards show counts, table populated
  - All OK: Green empty state, cards show "0"
  - Loading: Skeleton cards + skeleton table
  - After marking disposed: Count updates, row disappears from table
- **Mobile behavior:** Cards stacked vertically (1 column). Table → card list. "Mark Disposed" via swipe action.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Nav Type |
|---|---|---|---|
| SCR-SAM-01 | P-SUPP | Sidebar → Quality → Samples | Desktop sidebar |
| SCR-SAM-02 | P-SUPP | Sidebar → Quality → Retention Report | Desktop sidebar |

**P-SUPP Sidebar → Quality group:**
- Samples
- Retention Report
- (Future: Lab Results, Quality Certificates)

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Status badge colors cố định: IN_RETENTION=green, EXPIRING_SOON=yellow, EXPIRED=red, DISPOSED=gray | UX: consistent color language cho urgency levels |
| 2 | Minimum volume (750 ml per SS 524) hiển thị nhưng KHÔNG cho phép edit | Compliance: SS 524 quy định minimum, chỉ reference không edit |
| 3 | Default sort = Retention Expiry ASC (sắp hết hạn lên đầu) | Priority: samples cần action hiện trước |
| 4 | Expired undisposed samples PHẢI hiển thị rõ ràng (red, count on report) | Compliance: samples expired phải được xử lý, không bỏ sót |
| 5 | "Mark Disposed" chỉ khả dụng cho samples có status EXPIRED | Business rule: chỉ dispose khi đã hết retention period |
| 6 | Days remaining negative = overdue, hiển thị red bold | Urgency: quá hạn cần attention ngay |
| 7 | Retention Report PHẢI load nhanh (<2s) vì là monitoring tool | Performance: daily check tool, cần responsive |
| 8 | Volume hiển thị đơn vị "ml" — consistent toàn bộ module | Standards: SS 524 dùng ml cho sample volumes |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Sample reference | Monospace font | — | e.g., "SAM-2026-0142" |
| Delivery reference | Linked text (blue) | — | Click → delivery detail |
| Fuel type | Plain text | — | e.g., "VLSFO", "LSMGO" |
| Volume | `XXX ml` | — | Integer, no decimal. Min 750 ml per SS 524 |
| Method | Plain text | Max 25 chars | e.g., "Continuous Drip", "Manual Spot" |
| Collected date | `dd/MM/yyyy` | — | — |
| Retention expiry | `dd/MM/yyyy` + color | — | Color: normal (>30d), yellow (≤30d), orange (≤7d), red (expired) |
| Days remaining | `X days` (positive) hoặc `-X days` (overdue) | — | Yellow/orange/red depending on urgency |
| Status badge | Pill badge + color | — | IN_RETENTION=green, EXPIRING_SOON=yellow, EXPIRED=red, DISPOSED=gray |
| Summary card count | Large integer (32px+) | — | Bold, color matches card accent |
| Card subtitle | Muted text, 14px | — | Describes what count represents |
| "All OK" state | Green checkmark + message | — | Only when 0 attention items |
