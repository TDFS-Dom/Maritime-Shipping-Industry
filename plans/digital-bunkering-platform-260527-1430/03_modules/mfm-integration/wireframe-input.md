# Wireframe Input — MFM Integration

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module MFM Integration.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Module này quản lý MFM devices và session history. LIVE MFM monitor screen đã được định nghĩa trong `delivery-ops/wireframe-input.md` (SCR-DEL-03).

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-MFM-01 | MFM Device Registry | P-SUPP | Medium | Must |
| SCR-MFM-02 | MFM Session History | P-SUPP | Medium | Should |

---

## 2. Screen Descriptions

### SCR-MFM-01: MFM Device Registry

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Quản lý danh sách MFM devices đã đăng ký: theo dõi calibration, trạng thái hoạt động
- **Entry point:** Sidebar menu → "Fleet" → "MFM Devices"
- **Layout type:** Data table + action bar
- **Content:**
  - **Page Header:**
    - Title: "MFM Device Registry" (28px, bold)
    - Subtitle: "Quản lý Mass Flow Meters đã đăng ký"
    - Action button (top-right): "Register Device" (Primary button + icon plus)
  - **Alert Banner (conditional, top of page):**
    - Hiển thị nếu có device(s) calibration expired: "⚠️ X device(s) có calibration hết hạn — cần hiệu chuẩn lại" (yellow/red banner)
  - **Data Table:**
    - Columns: Meter Serial | Barge Name | Manufacturer | Model | Calibration Date | Calibration Expiry | Status | Actions
    - Meter Serial: monospace font (e.g., "MFM-2024-0156")
    - Barge Name: linked text → navigate to barge detail
    - Calibration Date: `dd/MM/yyyy`
    - Calibration Expiry: `dd/MM/yyyy` + **color-coded:**
      - Expired (past date): red text + ⚠️ icon + "Expired" badge (red)
      - Expiring within 30 days: yellow text + "Expiring soon" badge (yellow)
      - OK (>30 days): normal text, no badge
    - Status: ACTIVE (green badge), INACTIVE (gray badge), CALIBRATION_EXPIRED (red badge)
    - Actions column: icon buttons [Edit] [Deactivate/Activate]
  - **Filters:** Status dropdown (All / Active / Inactive / Calibration Expired)
  - **Pagination:** Bottom, 20 items/page
- **Actions available:**
  - Register Device → modal form (serial, barge assignment, manufacturer, model, calibration date/expiry)
  - Edit device info
  - Deactivate device → confirm dialog
  - Activate (re-enable) device
  - Filter by status
  - Sort by calibration expiry (ascending — soon-to-expire first)
- **State variations:**
  - Has data: Table with device rows, alert banner if expired devices exist
  - No expired: No alert banner
  - Has expired: Alert banner visible (yellow/red depending on severity)
  - Empty: "Chưa có MFM device nào" + illustration + "Register Device" CTA
  - Loading: Skeleton rows
  - Register/Edit modal open: Form overlay
- **Mobile behavior:** Card list. Each card: serial (bold) + barge name + calibration expiry (color-coded). Alert banner sticky top.

---

### SCR-MFM-02: MFM Session History

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Xem lịch sử các MFM sessions (phiên đo lưu lượng) đã hoàn thành — verify data, investigate anomalies
- **Entry point:** Sidebar menu → "Fleet" → "MFM Sessions" hoặc click từ delivery detail
- **Layout type:** Data table + expandable detail
- **Content:**
  - **Page Header:**
    - Title: "MFM Session History" (28px, bold)
    - Subtitle: "Lịch sử phiên đo lưu lượng"
  - **Filter Bar:**
    - Barge: dropdown (filter by barge)
    - Date Range: date picker (from – to)
    - Anomaly: dropdown (All / Has Anomaly / No Anomaly)
    - "Apply" + "Clear"
  - **Data Table:**
    - Columns: Delivery Ref | Barge | Start Time | End Time | Total Quantity (MT) | Reading Count | Anomaly Count | Actions
    - Delivery Ref: linked text → navigate to delivery detail
    - Total Quantity: `XXX.XXX MT` (3 decimal places)
    - Anomaly Count: 
      - 0: plain "0" (green text)
      - > 0: red text + warning icon (e.g., "3 ⚠️")
    - Actions: "View Detail" icon button
  - **Expandable Row Detail (click "View Detail" hoặc expand row):**
    - **Session Summary:** Duration, avg flow rate, min/max temperature
    - **Readings Chart (mini line chart in expanded area):**
      - X-axis: time (session duration)
      - Y-axis: flow rate (m³/h)
      - Anomaly markers: red dots on chart where anomaly detected
    - **Anomaly List (if any):** timestamp + type + description
  - **Pagination:** Bottom, 20 items/page
- **Actions available:**
  - Filter sessions
  - Sort by column
  - Expand row → view session detail + chart
  - Click delivery ref → navigate to delivery
  - Click anomaly → detail popup
- **State variations:**
  - Has data: Table populated
  - No data (no sessions yet): "Chưa có session nào" + illustration
  - Expanded row: Detail section visible with chart
  - Filtered (no results): "Không tìm thấy session phù hợp"
  - Loading: Skeleton rows
- **Mobile behavior:** Card list (delivery ref + barge + quantity per card). Tap card → full detail page with chart. Chart horizontally scrollable.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Nav Type |
|---|---|---|---|
| SCR-MFM-01 | P-SUPP | Sidebar → Fleet → MFM Devices | Desktop sidebar |
| SCR-MFM-02 | P-SUPP | Sidebar → Fleet → MFM Sessions | Desktop sidebar |

**P-SUPP Sidebar → Fleet group:**
- Barges
- Vessels
- MFM Devices
- MFM Sessions

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Calibration expiry highlight: RED nếu expired, YELLOW nếu ≤ 30 days | Safety: MFM chưa hiệu chuẩn không đáng tin cậy, cần visual alert rõ ràng |
| 2 | Device serial PHẢI unique trong workspace | Data integrity: mỗi physical device chỉ register 1 lần |
| 3 | Alert banner hiển thị tự động khi có device expired | Proactive: admin biết ngay khi mở page, không cần tìm |
| 4 | Anomaly count > 0 PHẢI highlight rõ (red + icon) | Operational: anomaly cần attention, dễ spot trong table |
| 5 | Session readings chart PHẢI hiển thị anomaly markers | Investigation: overlay anomaly points trên flow rate chart |
| 6 | Deactivate device phải confirm dialog | Safety: deactivate ảnh hưởng scheduling |
| 7 | LIVE MFM monitor screen KHÔNG nằm trong module này | Architecture: live monitor thuộc delivery-ops (SCR-DEL-03) |
| 8 | Calibration expiry sort ascending mặc định (sắp hết hạn lên đầu) | Priority: devices cần attention first |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Meter serial | Monospace font | — | e.g., "MFM-2024-0156" |
| Barge name | Regular text, linked (blue) | Max 25 chars + "..." | Click → barge detail |
| Manufacturer | Regular text | Max 30 chars | e.g., "Emerson", "Endress+Hauser" |
| Model | Regular text | Max 30 chars | e.g., "Coriolis CMF400" |
| Calibration date | `dd/MM/yyyy` | — | Past date |
| Calibration expiry | `dd/MM/yyyy` + color | — | Red if expired, yellow if ≤30 days, normal otherwise |
| Status (device) | Badge: ACTIVE (green), INACTIVE (gray), CALIBRATION_EXPIRED (red) | — | Auto-set to CALIBRATION_EXPIRED khi expiry passed |
| Delivery ref | Linked text | — | Full ref code, clickable |
| Session start/end time | `dd/MM/yyyy HH:mm` | — | Local timezone |
| Total quantity | `XXX.XXX MT` | — | 3 decimal places, thousand separator if ≥1000 |
| Reading count | Integer | — | e.g., "847" |
| Anomaly count | Integer + color | — | 0=green, >0=red + ⚠️ icon |
| Flow rate (chart Y-axis) | `XX.X m³/h` | — | 1 decimal |
| Duration | `Xh Ym` | — | Calculated from start–end |
