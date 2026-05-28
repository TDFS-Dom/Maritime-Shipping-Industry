# Wireframe Input — Scheduling

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Scheduling.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-SCH-01 | Scheduling Calendar | P-SUPP | High | Must |
| SCR-SCH-02 | Schedule Detail | P-SUPP | Medium | Must |
| SCR-SCH-03 | Conflict Warning | P-SUPP | Low | Must |

---

## 2. Screen Descriptions

### SCR-SCH-01: Scheduling Calendar

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Supplier Admin / Scheduling Manager
- **Goal:** Quản lý lịch trình barge bằng Gantt-style timeline, phát hiện conflict, allocate delivery
- **Entry point:** Side nav → "Scheduling" (menu item active)
- **Layout type:** Calendar/Gantt (custom timeline)
- **Content:**
  - **Page Header:** "Scheduling" + View toggle (Day / Week) + Date navigator (← Today →) + Button "Auto-schedule" (Secondary)
  - **Timeline Grid:**
    - **Trục ngang (X):** Thời gian — chia theo giờ (Day view) hoặc theo ngày (Week view)
    - **Trục dọc (Y):** Danh sách barges (mỗi row = 1 barge)
    - **Barge Row Header:** Barge name + fuel type certification badges (pill badges, e.g. "VLSFO" "MGO")
    - **Time Slots:** Blocks hiển thị deliveries đã allocate
      - Color by status: Scheduled (blue/info), In-Progress (green), Completed (gray), Conflict (red border)
      - Content mỗi block: Vessel name (truncated), fuel type icon, quantity
      - Click block → open SCR-SCH-02
    - **Drag & Drop:** Kéo nomination chưa allocate vào slot trống để assign barge + time
  - **Sidebar Panel (right, collapsible):**
    - Unallocated nominations list (pending assignment)
    - Mỗi item: Vessel name, fuel, qty, delivery window
    - Drag từ panel vào calendar grid
  - **Conflict Indicators:**
    - **Overlap (red highlight):** Khi 2 delivery trùng thời gian trên cùng barge → red background + red border
    - **Turnaround violation (yellow marker):** Khi gap giữa 2 delivery < minimum turnaround time → yellow triangle icon giữa 2 blocks
- **Actions available:**
  - Drag & drop allocation
  - Click slot → SCR-SCH-02
  - View toggle (Day/Week)
  - Date navigation
  - Auto-schedule button
- **State variations:**
  - Empty (no schedules): Calendar grid trống + "Kéo nomination vào để lên lịch" message
  - Loading: Skeleton blocks trên grid
  - Conflict detected: Red overlays + SCR-SCH-03 panel auto-opens
  - Full capacity: All barge rows have blocks, unallocated panel highlighted
- **Mobile behavior:** KHÔNG có mobile view — chỉ desktop/tablet landscape. Tablet: sidebar collapsed mặc định, mở bằng toggle button.

---

### SCR-SCH-02: Schedule Detail

- **Portal:** P-SUPP (Desktop — modal hoặc slide-over panel)
- **Actor:** Supplier Admin / Scheduling Manager
- **Goal:** Xem chi tiết 1 schedule entry, liên kết với nomination, quản lý trạng thái
- **Entry point:** Click slot trên SCR-SCH-01 calendar
- **Layout type:** Detail panel (slide-over right hoặc modal medium)
- **Content:**
  - **Header:** Schedule reference + Status badge + Close button
  - **Section 1 — Schedule Info (Card):**
    - Barge: Name + registration
    - Time: Start datetime → End datetime
    - Duration: calculated (e.g. "4h 30m")
    - Fuel type + Quantity
  - **Section 2 — Linked Nomination (Card):**
    - Nomination reference (link → SCR-NOM-02)
    - Vessel name + IMO
    - Buyer name
    - Delivery window requested
  - **Section 3 — Barge Info (Card):**
    - Barge name, capacity (MT)
    - Fuel certifications (badges)
    - Current location / status
    - Last maintenance date
  - **Action Bar (bottom):**
    - "Dispatch" (Primary) — mark as dispatched/in-progress
    - "Reschedule" (Secondary) — opens time picker to change slot
    - "Cancel" (Danger) — confirmation dialog
- **Actions available:**
  - Dispatch → changes status, notifies barge
  - Reschedule → pick new time, re-validate conflicts
  - Cancel → confirmation + reason, frees slot
  - Close panel → back to calendar
- **State variations:**
  - Scheduled: All actions available
  - Dispatched: Only "Cancel" available, show dispatched time
  - Completed: Read-only, no actions
  - Cancelled: Read-only, show cancellation reason
- **Mobile behavior:** N/A (scheduling module desktop-only). On tablet: full-screen modal thay vì slide-over.

---

### SCR-SCH-03: Conflict Warning

- **Portal:** P-SUPP (Desktop — panel/modal overlay)
- **Actor:** Supplier Admin / Scheduling Manager
- **Goal:** Thông báo conflict detected, đề xuất giải pháp
- **Entry point:** Auto-appears khi drag tạo conflict, hoặc click conflict indicator trên calendar
- **Layout type:** Panel (inline, below calendar hoặc slide-over)
- **Content:**
  - **Header:** ⚠️ "Conflict Detected" (warning icon + text) + Close button
  - **Conflict List (mỗi conflict = 1 card):**
    - Conflict type: "Time Overlap" hoặc "Turnaround Violation"
    - Affected barge name
    - Conflicting deliveries: Delivery A (vessel, time) vs Delivery B (vessel, time)
    - Visual: Mini timeline showing overlap (red) hoặc insufficient gap (yellow)
  - **Suggestion Section:**
    - "Đề xuất giải pháp:" 
    - Option A: Move delivery B to [suggested time] (button "Apply")
    - Option B: Assign to [alternative barge] (button "Apply")
    - Option C: "Bỏ qua" (Ghost button) — keep conflict, proceed with warning
- **Actions available:**
  - Apply suggestion A/B → auto-moves block on calendar
  - Ignore → close panel, conflict marker remains (red/yellow)
  - Close → dismiss panel
- **State variations:**
  - Single conflict: 1 card
  - Multiple conflicts: List of cards, scrollable
  - No suggestions available: Show "Không có đề xuất tự động" + manual instructions
- **Mobile behavior:** N/A (desktop-only module)

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Breadcrumb |
|---|---|---|---|
| SCR-SCH-01 | P-SUPP | Side nav → "Scheduling" | — |
| SCR-SCH-02 | P-SUPP | Side nav → "Scheduling" | (panel overlay, no breadcrumb change) |
| SCR-SCH-03 | P-SUPP | Side nav → "Scheduling" | (panel overlay, no breadcrumb change) |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Calendar PHẢI hiển thị fuel type certification badges trên mỗi barge row header | Operator cần biết barge nào chứng nhận loại fuel nào để allocate đúng |
| 2 | Overlap (2 delivery trùng time trên cùng barge) PHẢI có RED highlight ngay lập tức — không cần user action | Safety-critical: tránh double-booking barge |
| 3 | Turnaround violation PHẢI hiển thị yellow warning marker giữa 2 blocks | Quy định vận hành: barge cần turnaround time tối thiểu giữa 2 chuyến |
| 4 | Conflict indicators phải dùng BOTH color + icon (không chỉ color) | DESIGN.md §8: "Don't use color alone to convey status" |
| 5 | Drag & drop PHẢI validate conflict TRƯỚC khi commit (show SCR-SCH-03 nếu conflict) | Ngăn scheduling sai, user phải acknowledge conflict |
| 6 | Barge certification badges dùng pill badge style (§5.4 Status Badges) | Consistency với design system |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Barge name | Title case | Max 20 chars, "..." | Tooltip full name |
| Vessel name (trong slot) | Title case | Max 15 chars, "..." | Tooltip full + IMO |
| Time slot (Day view) | `HH:mm` | — | 24h format |
| Time slot (Week view) | `dd MMM` | — | Day header |
| Quantity | `X,XXX MT` | — | Thousand separator |
| Duration | `Xh Ym` | — | Calculated from start/end |
| Fuel cert badges | Uppercase code (e.g. "VLSFO") | — | Pill badge, color per fuel type |
| Turnaround gap | `Xh Ym` | — | Yellow text nếu < minimum |
| Conflict count | Badge counter trên page header | "99+" nếu >99 | — |
