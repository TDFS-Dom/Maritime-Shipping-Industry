# Wireframe Input — Delivery Operations

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Delivery Operations.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Tất cả screens trong module này là MOBILE-FIRST (P-OPS portal, touch-optimized, 4G connectivity).

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-DEL-01 | Active Deliveries List | P-OPS | Low | Must |
| SCR-DEL-02 | Pre-delivery Checklist | P-OPS | High | Must |
| SCR-DEL-03 | MFM Live Monitor | P-OPS | High | Must |
| SCR-DEL-04 | Complete Delivery | P-OPS | Medium | Must |

---

## 2. Screen Descriptions

### SCR-DEL-01: Active Deliveries List

- **Portal:** P-OPS (Mobile — bottom tab bar layout)
- **Actor:** Barge Operator
- **Goal:** Xem danh sách deliveries đang active được giao cho mình, chọn delivery để thao tác
- **Entry point:** Bottom tab → "Active" (tab 1, mặc định khi mở app)
- **Layout type:** Card list (scrollable)
- **Content:**
  - **App Bar:** "Active Deliveries" (center title) + notification bell icon (right)
  - **Card List (mỗi card = 1 delivery):**
    - **Left section:** Vessel name (bold, 16px), Fuel type badge (pill)
    - **Right section:** Status badge (e.g. "In Progress", "Awaiting Start")
    - **Bottom row:** Time elapsed (e.g. "2h 15m") hoặc "Starts in 45m", Quantity target (e.g. "500 MT")
    - **Card tap → navigate:** to appropriate screen based on status
      - "Awaiting Start" → SCR-DEL-02 (Checklist)
      - "In Progress" → SCR-DEL-03 (MFM Monitor)
      - "Pumping Complete" → SCR-DEL-04 (Complete)
  - **Pull-to-refresh:** Kéo xuống để refresh list
- **Actions available:**
  - Tap card → navigate to relevant screen
  - Pull-to-refresh
  - Notification bell → notifications list
- **State variations:**
  - Empty: Illustration + "Không có delivery nào đang active" + "Các delivery mới sẽ hiển thị tại đây"
  - Loading: Skeleton cards (3 cards)
  - Has data: Card list with delivery info
  - Offline: Banner top "Không có kết nối mạng" (yellow warning)
- **Mobile behavior:** Native mobile screen. Cards full-width, padding 16px. Touch target mỗi card ≥ 44px height. Scroll vertically.

---

### SCR-DEL-02: Pre-delivery Checklist

- **Portal:** P-OPS (Mobile — full screen, no bottom tab)
- **Actor:** Barge Operator
- **Goal:** Hoàn thành checklist an toàn trước khi bắt đầu bơm nhiên liệu
- **Entry point:** Tap delivery card (status "Awaiting Start") từ SCR-DEL-01
- **Layout type:** Sequential form (stepped)
- **Content:**
  - **App Bar:** "Pre-delivery Check" + Back arrow (left) + Delivery reference (subtitle)
  - **Progress Bar:** Top, full-width, hiển thị % hoàn thành (e.g. "4/12 items")
  - **Checklist Items (sequential, 1 item tại 1 thời điểm hoặc scrollable list):**
    - Mỗi item gồm:
      - Question text (16px, bold) — e.g. "Hose connections inspected and secure?"
      - Pass/Fail toggle: 2 buttons lớn [✓ Pass] [✗ Fail]
      - If Fail selected: Comment textarea appears (bắt buộc) + Photo upload button (camera icon)
      - Photo preview (thumbnail) nếu đã upload
    - Items PHẢI trả lời tuần tự — item tiếp theo chỉ hiện khi item trước đã answered
  - **Bottom Action:**
    - "Submit Checklist" button (Primary, full-width) — chỉ enabled khi ALL items answered
    - Disabled state + text "Hoàn thành tất cả mục để submit"
- **Actions available:**
  - Pass/Fail toggle per item
  - Add comment (if Fail)
  - Upload photo (if Fail)
  - Submit checklist (when all complete)
  - Back → confirmation dialog "Bạn có muốn thoát? Tiến trình sẽ được lưu."
- **State variations:**
  - Fresh: 0 items answered, progress 0%, Submit disabled
  - In-progress: Some items answered, progress partial
  - All Pass: All items ✓, Submit enabled (green)
  - Has Failures: Some items ✗ (shown with red indicator), Submit enabled but with warning "X items failed"
  - Submitting: Button loading state, items disabled
  - Submitted: Success toast + auto-navigate to SCR-DEL-03
- **Mobile behavior:** Full-screen experience (bottom tab bar HIDDEN). Large touch targets cho Pass/Fail buttons (minimum 48×48px). Photo upload dùng native camera. Items scroll vertically, current item highlighted.

---

### SCR-DEL-03: MFM Live Monitor

- **Portal:** P-OPS (Mobile — full screen, no bottom tab)
- **Actor:** Barge Operator
- **Goal:** Giám sát real-time quá trình bơm nhiên liệu (flow rate, totalizer, temperature)
- **Entry point:** Tap delivery card (status "In Progress") từ SCR-DEL-01, hoặc auto-navigate sau checklist submit
- **Layout type:** Dashboard (real-time gauges)
- **Content:**
  - **App Bar:** "MFM Monitor" + "LIVE" indicator (pulsing green dot + text) + Back arrow
  - **Last Update Timestamp:** "Cập nhật: 14:35:22" (caption text, below app bar)
  - **Main Gauge — Flow Rate (top, largest element):**
    - Radial/arc gauge (semicircle)
    - Current value: LARGE number (32px+) + unit "m³/h"
    - Min/Max markers trên gauge
    - Color: Green khi normal range, Yellow khi approaching limit, Red khi abnormal
  - **Totalizer Progress (middle section):**
    - Progress bar (horizontal, full-width)
    - "Delivered: 342 / 500 MT" (current / target)
    - Percentage: "68.4%"
    - Estimated completion time
  - **Secondary Metrics (below, 2-column grid):**
    - Temperature: value + unit (°C)
    - Duration: elapsed time "2h 15m"
    - Pressure: value + unit (optional)
    - Batch number
  - **Bottom Action:**
    - "Stop Pumping" (Danger button, full-width) — emergency stop
    - Confirmation dialog required
- **Actions available:**
  - View real-time data (auto-refreshing)
  - Stop Pumping (emergency)
  - Back → SCR-DEL-01
- **State variations:**
  - Live streaming: Normal display, "LIVE" indicator pulsing
  - Data stale (>30s no update): "LIVE" → "STALE" (yellow), warning banner "Mất kết nối MFM"
  - Connection lost: Red banner "Mất kết nối", last known values shown (grayed)
  - Pumping complete (target reached): Gauge at 100%, auto-prompt "Pumping complete" + navigate to SCR-DEL-04
  - Emergency stopped: Red overlay "STOPPED" + timestamp
- **Mobile behavior:** Optimized cho đọc từ khoảng cách (arm's length). Font sizes lớn hơn thường (flow rate ≥ 32px, totalizer ≥ 24px). Minimal UI chrome, maximize data display. Works on 4G (200ms latency) — data pushed via lightweight protocol.

---

### SCR-DEL-04: Complete Delivery

- **Portal:** P-OPS (Mobile — full screen, no bottom tab)
- **Actor:** Barge Operator
- **Goal:** Xác nhận hoàn thành delivery, xem summary, ghi nhận location
- **Entry point:** Auto-navigate khi MFM session complete, hoặc tap card (status "Pumping Complete")
- **Layout type:** Summary + Confirmation
- **Content:**
  - **App Bar:** "Complete Delivery" + Delivery reference
  - **Summary Card (main content):**
    - Total Quantity Delivered: LARGE number + "MT" (e.g. "500.240 MT")
    - Duration: "3h 45m"
    - Start Time: "14:30, 15 Jun 2026"
    - End Time: "18:15, 15 Jun 2026"
    - Average Flow Rate: "X m³/h"
    - Temperature Range: "Min – Max °C"
  - **Location Section (Card):**
    - Mini-map hiển thị current GPS position (static map image hoặc lightweight map)
    - Coordinates text (lat, lng)
    - Location label: "Singapore Anchorage East"
  - **Confirmation Section:**
    - Checkbox: "I confirm the above information is accurate"
    - "Complete Delivery" button (Primary, full-width) — DISABLED nếu:
      - MFM session chưa complete
      - Checkbox chưa ticked
  - **Important Notice:**
    - Info banner: "Sau khi confirm, eBDN sẽ được tạo tự động"
- **Actions available:**
  - Tick confirmation checkbox
  - "Complete Delivery" → finalizes delivery, triggers eBDN generation
  - Back → SCR-DEL-03 (if MFM still active)
- **State variations:**
  - MFM not complete: "Complete" button disabled + message "MFM session chưa kết thúc"
  - Ready to complete: Checkbox + button available
  - Completing: Button loading, all fields disabled
  - Completed: Success screen → "Delivery hoàn thành ✓" + "eBDN đang được tạo" + navigate to eBDN sign
- **Mobile behavior:** Single scroll page. Mini-map lightweight (static image preferred over interactive map for 4G performance). Large confirm button at bottom (sticky).

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Tab Bar Visible |
|---|---|---|---|
| SCR-DEL-01 | P-OPS | Bottom tab → "Active" | ✅ Yes |
| SCR-DEL-02 | P-OPS | (Full screen flow) | ❌ Hidden |
| SCR-DEL-03 | P-OPS | (Full screen flow) | ❌ Hidden |
| SCR-DEL-04 | P-OPS | (Full screen flow) | ❌ Hidden |

**P-OPS Bottom Tab Bar:** Active | Checklists | MFM | eBDN (4 tabs)

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Checklist items KHÔNG THỂ bỏ qua — phải trả lời tuần tự từng item | Safety compliance: mọi bước kiểm tra phải được thực hiện |
| 2 | MFM monitor PHẢI hiển thị "LIVE" indicator + last-update timestamp | Operator cần biết data có real-time hay không |
| 3 | "Complete Delivery" button DISABLED nếu MFM session chưa complete | Business rule: không thể hoàn thành delivery khi chưa kết thúc bơm |
| 4 | Tất cả screens phải hoạt động trên 4G (200ms latency) — minimal data transfer | Điều kiện thực tế: barge hoạt động ngoài khơi, kết nối yếu |
| 5 | Touch targets ≥ 44×44px cho tất cả interactive elements | DESIGN.md §6.2: mobile touch target minimum |
| 6 | Flow rate gauge phải đọc được ở khoảng cách cánh tay (arm's length) | UX requirement: operator có thể đang thao tác thiết bị khác |
| 7 | MFM data stale (>30s) phải cảnh báo bằng visual indicator rõ ràng | Safety: operator cần biết ngay khi mất kết nối |
| 8 | Photo upload trong checklist dùng native camera API | Performance + UX: nhanh nhất cho field conditions |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Vessel name | Title case, bold | Max 20 chars mobile | Giữ ngắn cho mobile |
| Quantity | `XXX.XXX MT` | — | 2 decimal places, thousand separator nếu ≥1000 |
| Flow rate | `XX.X m³/h` | — | 1 decimal, font ≥ 32px trên MFM |
| Temperature | `XX.X °C` | — | 1 decimal |
| Duration | `Xh Ym` | — | Không hiển thị seconds |
| Time elapsed | `Xh Ym` hoặc "Starts in Xm" | — | Relative, live updating |
| Progress | `XX.X%` + progress bar | — | 1 decimal |
| Timestamp | `HH:mm:ss` (MFM), `HH:mm, dd MMM yyyy` (summary) | — | 24h format |
| GPS coordinates | `X.XXXXX°N, X.XXXXX°E` | — | 5 decimal places |
| Checklist progress | `X/Y items` + percentage bar | — | Cập nhật sau mỗi answer |
| Status badges | Pill badge + dot | — | Mobile size: 12px font, 2px 8px padding |
