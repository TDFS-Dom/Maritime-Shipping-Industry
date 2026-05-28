# Wireframe Input — B2G Compliance

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module B2G Compliance (Business-to-Government).
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-B2G-01 | Compliance Dashboard | P-SUPP | High | Must |
| SCR-B2G-02 | Submission List | P-SUPP | Medium | Must |
| SCR-B2G-03 | Alert Detail | P-SUPP | Medium | Must |

---

## 2. Screen Descriptions

### SCR-B2G-01: Compliance Dashboard

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Compliance Officer / Supplier Admin
- **Goal:** Tổng quan nhanh tình trạng compliance B2G, phát hiện issues cần xử lý
- **Entry point:** Side nav → "Compliance" (menu item active)
- **Layout type:** Dashboard (KPIs + chart + alert list)
- **Content:**
  - **Page Header:** "B2G Compliance" + last refresh timestamp
  - **KPI Cards Row (top, 5 cards ngang):**
    - Pending Submission: count (blue/info)
    - Submitted: count (blue)
    - Acknowledged: count (green/success)
    - Rejected: count (red/error)
    - Approaching Deadline: count (yellow/warning) + countdown nếu có urgent
  - **Chart Section (Card, middle):**
    - Submission Volume Trend (Line/Area chart)
    - X-axis: Last 30 days (daily) hoặc last 12 weeks (weekly)
    - Y-axis: Number of submissions
    - Lines: Submitted vs Acknowledged vs Rejected
    - Toggle: Daily / Weekly view
  - **Alert List Section (Card, bottom):**
    - Title: "Unresolved Alerts" + badge count
    - Table/List format:
      - Columns: Severity (icon + color), Type, eBDN Reference, Description, Time Created, Action
      - Sort: CRITICAL first (top), then by time DESC
      - CRITICAL alerts highlighted: red left-border hoặc red background tint
    - Click row → SCR-B2G-03
  - **Deadline Countdown (inline trong KPI card hoặc alert):**
    - Nếu có eBDN approaching deadline: show countdown timer
    - Red text nếu <1h remaining
    - Yellow text nếu <4h remaining
- **Actions available:**
  - Click KPI card → filter submission list (SCR-B2G-02)
  - Click alert row → SCR-B2G-03
  - Chart toggle (Daily/Weekly)
  - Refresh data
- **State variations:**
  - All clear: All KPIs green/zero issues, alert list empty with "✓ Không có alerts"
  - Has critical: CRITICAL alerts at top, KPI "Rejected" highlighted red
  - Loading: Skeleton KPIs + chart + list
  - Error: Toast "Không thể tải dữ liệu compliance"
- **Mobile behavior:** KPI cards → 2×3 grid (scroll horizontal). Chart → full-width, scrollable. Alert list → card list.

---

### SCR-B2G-02: Submission List

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Compliance Officer / Supplier Admin
- **Goal:** Xem chi tiết danh sách submissions đến MPA, theo dõi trạng thái từng eBDN
- **Entry point:** Click KPI card từ Dashboard, hoặc sub-nav "Submissions"
- **Layout type:** Data table với filters
- **Content:**
  - **Page Header:** "Submissions" + total count badge
  - **Filter Bar:** Status dropdown (All, Pending, Submitted, Acknowledged, Rejected, Failed), Date range picker
  - **Data Table:**
    - Columns: eBDN Reference (link), Submission Date, Status (badge), MPA Reference (text hoặc "—"), Retry Count, Last Attempt
    - Sortable: Date, Status, Retry Count
    - Row click → SCR-EBDN-02 (eBDN detail with submission section)
  - **Pagination:** Standard
- **Actions available:**
  - Row click → eBDN detail
  - Filter by status/date
  - Bulk retry (select multiple FAILED → "Retry Selected" button)
- **State variations:**
  - Empty: "Chưa có submission nào"
  - Loading: Skeleton rows
  - Has failed: Failed rows highlighted with red left-border
  - All acknowledged: Green "All compliant" indicator top
- **Mobile behavior:** Table → Card list. Card: Reference, Status badge, Date, MPA Ref.

---

### SCR-B2G-03: Alert Detail

- **Portal:** P-SUPP (Desktop — modal hoặc detail page)
- **Actor:** Compliance Officer
- **Goal:** Xem chi tiết alert, hiểu vấn đề, thực hiện action (acknowledge, retry)
- **Entry point:** Click alert row từ SCR-B2G-01 dashboard
- **Layout type:** Detail (modal medium hoặc page)
- **Content:**
  - **Header:** Alert severity icon + "Alert Detail" + Close button
  - **Section 1 — Alert Info (Card):**
    - Type: e.g. "Submission Failed", "Deadline Approaching", "Rejected by MPA"
    - Severity: CRITICAL (red badge) / WARNING (yellow) / INFO (blue)
    - Description: Human-readable explanation
    - Created: Timestamp
    - Status: Unresolved / Acknowledged / Resolved
  - **Section 2 — Linked eBDN (Card):**
    - eBDN Reference (link → SCR-EBDN-02)
    - Vessel name
    - Delivery date
    - Current eBDN status
  - **Section 3 — Error Details (conditional, if rejection/failure):**
    - MPA rejection reason (verbatim from API)
    - Suggested fix (system recommendation)
  - **Action Bar:**
    - "Acknowledge" (Secondary) — marks alert as seen
    - "Retry Submission" (Primary) — only for FAILED/EXHAUSTED
    - "Dismiss" (Ghost) — close detail
- **Actions available:**
  - Acknowledge → changes alert status, removes from unresolved count
  - Retry → re-submits to MPA, shows progress
  - View linked eBDN → navigate
  - Close/Dismiss
- **State variations:**
  - Unresolved: Actions available
  - Acknowledged: "Acknowledged by [name] at [time]" shown, Retry still available if applicable
  - Resolved (auto): "Resolved — submission successful" green indicator
  - Retry in progress: Loading state on button
  - Retry failed: Error toast + update retry count
- **Mobile behavior:** Full-screen modal. Actions sticky bottom.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Breadcrumb |
|---|---|---|---|
| SCR-B2G-01 | P-SUPP | Side nav → "Compliance" | — |
| SCR-B2G-02 | P-SUPP | Side nav → "Compliance" | Compliance > Submissions |
| SCR-B2G-03 | P-SUPP | Side nav → "Compliance" | (modal overlay, no breadcrumb change) |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Deadline approaching: PHẢI hiển thị countdown timer — RED nếu <1h, YELLOW nếu <4h | MPA quy định deadline nộp BDN, vi phạm = phạt |
| 2 | CRITICAL alerts PHẢI ở đầu dashboard, unacknowledged, nổi bật | Compliance officer cần xử lý ngay items quan trọng nhất |
| 3 | Retry button CHỈ hiển thị cho submissions FAILED hoặc EXHAUSTED | Chỉ submissions thất bại mới cần retry |
| 4 | Countdown timer phải live-updating (không static) | Officer cần biết chính xác thời gian còn lại |
| 5 | KPI cards phải dùng status colors consistent (§3.2) | Nhận diện nhanh tình trạng bằng color coding |
| 6 | Alert severity phải dùng BOTH icon + color + text | DESIGN.md §8: "Don't use color alone" |
| 7 | Failed submissions highlighted với visual indicator (red left-border) | Quick scanning trong table dài |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| eBDN Reference | `EBDN-YYYY-NNNNN` | Không truncate | Link style (clickable) |
| Submission Date | `dd MMM yyyy, HH:mm` | — | 24h format |
| MPA Reference | `MPA-BDN-YYYY-NNNNNN` | Không truncate | "—" nếu chưa có |
| Retry Count | `X/Y` (current/max) | — | Red text nếu exhausted |
| Status Badge | Pill (dot + text) | — | Colors per §3.2 |
| Countdown Timer | `Xh Ym` hoặc `Xm Xs` (nếu <1h) | — | Live updating, color-coded |
| KPI Numbers | Integer, no decimal | — | Thousand separator nếu >999 |
| Alert Severity | Icon + text + background tint | — | CRITICAL=red, WARNING=yellow, INFO=blue |
| Chart dates | `dd/MM` (daily), `Wxx` (weekly) | — | X-axis labels |
| Last Attempt | Relative time (<3 days), absolute (≥3 days) | — | Per DESIGN.md Do's |
