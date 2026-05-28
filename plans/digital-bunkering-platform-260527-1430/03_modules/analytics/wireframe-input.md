# Wireframe Input — Analytics & BI

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Analytics.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Tất cả screens trong module này thuộc P-SUPP (Supplier Portal, Desktop/Web). Read-only dashboards, không có data input.

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-ANA-01 | Analytics Dashboard | P-SUPP | High | Could |
| SCR-ANA-02 | eBDN Turnaround Report | P-SUPP | Medium | Could |

---

## 2. Screen Descriptions

### SCR-ANA-01: Analytics Dashboard

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin / Management
- **Goal:** Xem tổng quan KPIs kinh doanh bunkering: total deliveries, total MT, avg turnaround, barge utilization
- **Entry point:** Sidebar menu → "Analytics" → "Dashboard"
- **Layout type:** KPI cards + Charts grid (2 columns)
- **Content:**
  - **Page Header:**
    - Title: "Analytics Dashboard" (28px, bold)
    - Date Range Selector (top-right): Quick picks [Last 7 days | Last 30 days | Last 90 days | Custom] — default: Last 30 days
  - **KPI Cards Row (top, 3-4 cards, equal width):**
    - Card 1: "Total Deliveries" — large number (e.g., "142"), subtitle "deliveries completed", trend arrow (↑12% vs previous period)
    - Card 2: "Total Volume" — large number (e.g., "28,540 MT"), subtitle "fuel delivered", trend arrow
    - Card 3: "Avg Turnaround" — large number (e.g., "45 min"), subtitle "delivery→eBDN signed", trend arrow (↓ is good)
    - Card 4 (optional): "Active Barges" — large number (e.g., "8/12"), subtitle "utilization ratio"
  - **Charts Section (below KPIs, 2-column grid):**
    - **Chart 1 — Volume Trend (line chart):**
      - X-axis: time (days/weeks depending on range)
      - Y-axis: MT delivered
      - Line: total volume per period
      - Optional: comparison line (previous period, dashed)
    - **Chart 2 — Fuel Type Breakdown (pie/donut chart):**
      - Segments: mỗi fuel type (VLSFO, LSMGO, MGO, HSFO, LNG...)
      - Legend: fuel type name + % + MT
      - Colors: design system palette
    - **Chart 3 — Barge Utilization (horizontal bar chart):**
      - Y-axis: barge names
      - X-axis: utilization % (0-100%)
      - Bars: colored by threshold (green >70%, yellow 40-70%, red <40%)
      - Target line: 70% (dashed vertical)
  - **Footer:** "Dữ liệu cập nhật: 15 phút trước" (caption, muted)
- **Actions available:**
  - Change date range (quick picks or custom)
  - Hover chart elements → tooltip with detail
  - Click chart segment → drill-down (future)
- **State variations:**
  - Has data: Full dashboard with charts populated
  - No data (new workspace): "Chưa có dữ liệu" + illustration + "Dữ liệu sẽ hiển thị khi có delivery đầu tiên"
  - Loading: Skeleton cards (KPIs) + skeleton chart areas
  - Partial data: Some charts may show "Insufficient data" message
  - Date range changed: Loading overlay on charts, KPIs update
- **Mobile behavior:** Single column layout. KPI cards 2×2 grid. Charts full-width, stacked vertically. Date range selector → bottom sheet.

---

### SCR-ANA-02: eBDN Turnaround Report

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin / Management
- **Goal:** Phân tích chi tiết thời gian turnaround eBDN, identify bottlenecks
- **Entry point:** Sidebar menu → "Analytics" → "eBDN Turnaround" hoặc click KPI card "Avg Turnaround" từ Dashboard
- **Layout type:** Chart + Data table (stacked vertically)
- **Content:**
  - **Page Header:**
    - Title: "eBDN Turnaround Report" (28px, bold)
    - Date Range Selector (top-right): same as Dashboard
  - **Turnaround Trend Chart (line chart, full-width):**
    - X-axis: weeks (e.g., "W1 Jun", "W2 Jun", "W3 Jun"...)
    - Y-axis: average turnaround time (minutes)
    - Line: actual avg turnaround per week
    - Target line: horizontal dashed line at 30 min (labeled "Target: 30 min")
    - Color: green khi dưới target, red khi vượt target
    - Tooltip on hover: "Week 2 Jun 2026: Avg 42 min (14 deliveries)"
  - **Summary Cards (below chart, 3 cards):**
    - "Within Target": count + % of deliveries ≤ 30 min (green)
    - "Exceeded Target": count + % of deliveries > 30 min (red)
    - "Average": overall average turnaround time
  - **Detail Table (below summary):**
    - Title: "Per-Delivery Detail"
    - Columns: Delivery Ref | Vessel | Fuel Type | Delivery Complete | Barge Signed | Vessel Signed | Total Turnaround | Status
    - Total Turnaround: formatted "Xh Ym" hoặc "X min"
    - Status: "Within SLA" (green badge) hoặc "Exceeded" (red badge)
    - Sort: turnaround DESC (slowest first) mặc định
    - Pagination: 20 items/page
  - **Filter options:** vessel, barge, fuel type
- **Actions available:**
  - Change date range
  - Hover chart → tooltip
  - Sort table by any column
  - Filter table
  - Click delivery ref → navigate to eBDN detail (cross-module link)
- **State variations:**
  - Has data: Chart + table populated
  - No data: "Chưa có eBDN hoàn thành trong period này"
  - Loading: Skeleton chart + skeleton table
  - Filtered (no results): "Không tìm thấy kết quả" + "Clear filters"
- **Mobile behavior:** Chart full-width (touch-scrollable horizontally nếu nhiều weeks). Table → card list. Summary cards stacked.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Nav Type |
|---|---|---|---|
| SCR-ANA-01 | P-SUPP | Sidebar → Analytics → Dashboard | Desktop sidebar |
| SCR-ANA-02 | P-SUPP | Sidebar → Analytics → eBDN Turnaround | Desktop sidebar |

**P-SUPP Sidebar → Analytics group:**
- Dashboard
- eBDN Turnaround
- (Future: Fuel Breakdown, Barge Utilization detail)

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Charts PHẢI sử dụng design system color palette | Visual consistency: không dùng random chart colors |
| 2 | Date range mặc định = Last 30 days | UX: meaningful default, không cần user chọn ngay |
| 3 | Dashboard load time < 2 giây | Performance: user expectation cho dashboard |
| 4 | Target line trên turnaround chart PHẢI luôn hiển thị | Business context: benchmark reference luôn cần visible |
| 5 | KPI trend arrows chỉ hiển thị khi có data period trước để so sánh | Data integrity: không show fake trends |
| 6 | Turnaround > SLA target phải highlight rõ ràng (red) | Operational awareness: quick identification of issues |
| 7 | Charts phải responsive — không bị cắt trên viewport 1280px+ | Desktop minimum requirement |
| 8 | Tooltip trên chart PHẢI hiển thị exact value + context | Accessibility: precise data reading |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Total deliveries | Integer, thousand separator | — | e.g., "1,247" |
| Total volume | `XX,XXX MT` | — | Thousand separator, 0 decimal |
| Average turnaround | `X min` hoặc `Xh Ym` | — | < 60 min → minutes, ≥ 60 min → hours + min |
| Utilization % | `XX%` | — | 0 decimal, color-coded |
| Trend arrow | ↑ (green) hoặc ↓ (red/green depending on metric) | — | Green = improvement (more volume OR less time) |
| Fuel type in pie | Name + % + absolute MT | — | Legend below/beside chart |
| Week label (chart) | "WN MMM" (e.g., "W2 Jun") | — | Short format for X-axis |
| Delivery ref | Full reference code | — | Clickable link |
| Turnaround time (table) | `XX min` hoặc `Xh Ym` | — | Bold if exceeded |
| Timestamp (delivery complete) | `dd/MM/yyyy HH:mm` | — | Local timezone |
| "Last updated" | Relative: "X phút trước" | — | Auto-refresh indicator |
| Barge utilization bar | % with colored fill | — | Green >70%, Yellow 40-70%, Red <40% |
