# Wireframe Input — Fuel Grades

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Fuel Grades.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Module quản lý fuel code registry theo SS 709. System codes (is_system=true) là immutable — chỉ custom codes mới có thể edit/delete.

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-FUEL-01 | Fuel Code Registry | P-SUPP | Medium | Must |
| SCR-FUEL-02 | Add/Edit Fuel Code (Modal) | P-SUPP | Low | Must |

---

## 2. Screen Descriptions

### SCR-FUEL-01: Fuel Code Registry

- **Portal:** P-SUPP (Desktop web — sidebar navigation)
- **Actor:** Supplier Admin
- **Goal:** Xem và quản lý danh sách fuel codes (SS 709 system codes + custom codes)
- **Entry point:** Sidebar menu → "Configuration" → "Fuel Codes"
- **Layout type:** Data table + filter bar
- **Content:**
  - **Page Header:**
    - Title: "Fuel Code Registry" (28px, bold)
    - Subtitle: "Danh mục nhiên liệu theo SS 709 và custom codes"
    - Action button (top-right): "Add Custom Code" (Primary button + icon plus)
  - **Filter Bar (above table):**
    - Category: dropdown (All / DISTILLATE / RESIDUAL / BIOFUEL / GAS / ALTERNATIVE)
    - Fuel Family: dropdown (All / MGO / MDO / VLSFO / HSFO / LNG / METHANOL / FAME...)
    - Status: dropdown (All / ACTIVE / DEPRECATED)
    - Search: text input "Tìm theo code hoặc tên..."
  - **Data Table:**
    - Columns: Code | Name | Category (badge) | Fuel Family | ISO 8217 Grade | Sulphur Limit (%) | Emission Factor | Status | Actions
    - Code column: uppercase monospace (e.g., "VLSFO-001")
    - Category column: pill badge (color-coded per category — DISTILLATE=blue, RESIDUAL=orange, GAS=green, BIOFUEL=teal, ALTERNATIVE=purple)
    - Sulphur Limit: `X.XX%` (e.g., "0.50%")
    - Emission Factor: `X.XXX` (e.g., "3.114")
    - Status: ACTIVE (green badge), DEPRECATED (gray badge)
    - **System code indicator:** 🔒 lock icon trước code name cho rows có is_system=true
    - Actions column:
      - System codes (is_system=true): NO action buttons — chỉ hiển thị lock icon + tooltip "System code — không thể chỉnh sửa"
      - Custom codes (is_system=false): icon buttons [Edit] [Delete]
  - **Pagination:** Bottom, 20 items/page
  - **Legend/Info:** Small info text dưới table: "🔒 = System code (SS 709) — không thể chỉnh sửa hoặc xóa"
- **Actions available:**
  - Add Custom Code → open modal SCR-FUEL-02
  - Edit custom code → open modal SCR-FUEL-02 (pre-filled)
  - Delete custom code → confirm dialog
  - Filter by category/family/status
  - Search
  - Sort by column
- **State variations:**
  - Has data: Table with mix of system + custom codes
  - Loading: Skeleton rows
  - No results (after filter): "Không tìm thấy fuel code phù hợp" + "Clear filters"
  - Delete confirm: Dialog "Xóa fuel code 'XXX'? Action này không thể hoàn tác."
- **Mobile behavior:** Card list. Mỗi card: code (bold) + name + category badge + lock icon (nếu system). Tap → detail view. Filters → collapsible panel.

---

### SCR-FUEL-02: Add/Edit Fuel Code (Modal)

- **Portal:** P-SUPP (Desktop web — modal overlay)
- **Actor:** Supplier Admin
- **Goal:** Tạo mới hoặc chỉnh sửa custom fuel code
- **Entry point:** Click "Add Custom Code" button (SCR-FUEL-01) hoặc Edit icon trên custom code row
- **Layout type:** Modal form (centered overlay, max-width 560px)
- **Content:**
  - **Modal Header:**
    - Title: "Add Custom Fuel Code" (create) hoặc "Edit Fuel Code" (edit)
    - Close button (X icon, top-right)
  - **Form Fields:**
    - Code: text input, uppercase auto-transform, max 20 chars, pattern: `[A-Z0-9\-]+`
      - Placeholder: "e.g., BIOFUEL-B30"
      - Helper text: "Uppercase, max 20 ký tự, chỉ chữ/số/dấu gạch"
    - Name: text input, max 255 chars
      - Placeholder: "e.g., Biofuel Blend B30"
    - Category: dropdown (DISTILLATE / RESIDUAL / BIOFUEL / GAS / ALTERNATIVE)
    - Fuel Family: dropdown (filtered by selected category)
    - ISO 8217 Grade: text input (optional)
      - Placeholder: "e.g., DMA, RMG 380"
    - Sulphur Limit (%): number input, 0.00 – 5.00, step 0.01
    - Emission Factor: number input, 0.000 – 5.000, step 0.001
    - Description: textarea (optional), max 500 chars
  - **Modal Footer:**
    - "Cancel" button (secondary/ghost)
    - "Save" button (Primary) — disabled khi form invalid
  - **Note (for edit mode):** "Chỉ custom codes có thể chỉnh sửa. System codes (SS 709) là read-only."
- **Actions available:**
  - Fill form fields
  - Select category → fuel family dropdown updates (cascading)
  - Save → validate + create/update
  - Cancel → close modal (confirm nếu dirty)
- **State variations:**
  - Create mode: Empty form, title "Add Custom Fuel Code"
  - Edit mode: Pre-filled with existing data, title "Edit Fuel Code"
  - Validation error: Inline errors per field (red text below field)
  - Code duplicate: "Fuel code already exists" inline error
  - Saving: "Save" button loading, fields disabled
  - Dirty close: Confirm dialog "Bạn có thay đổi chưa lưu. Thoát?"
- **Mobile behavior:** Modal → full-screen page. Form vertical layout. Keyboard-friendly.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Nav Type |
|---|---|---|---|
| SCR-FUEL-01 | P-SUPP | Sidebar → Configuration → Fuel Codes | Desktop sidebar |
| SCR-FUEL-02 | P-SUPP | (Modal overlay trên SCR-FUEL-01) | Modal |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | System codes (is_system=true) KHÔNG THỂ edit hoặc delete — hiển thị lock icon + "System" badge | Data integrity: SS 709 codes là chuẩn quốc gia, không cho phép thay đổi |
| 2 | Fuel code format: UPPERCASE, max 20 chars, chỉ chấp nhận [A-Z0-9\-] | Consistency: fuel codes là identifiers, phải standardized |
| 3 | Category → Fuel Family cascading: chỉ hiển thị families thuộc category đã chọn | UX: giảm confusion, data integrity |
| 4 | Lock icon PHẢI visible rõ ràng trên system code rows | UX: user phải nhận biết ngay đâu là system code (non-editable) |
| 5 | Delete custom code phải confirm dialog | Safety: tránh xóa nhầm, action irreversible |
| 6 | Sulphur limit phải validate ≤ 0.50% cho codes trong ECA zones | Compliance: IMO 2020 regulation |
| 7 | Table PHẢI sort system codes trước, custom codes sau (mặc định) | UX: SS 709 standard codes là reference chính |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Fuel code | UPPERCASE, monospace font | Max 20 chars | e.g., "VLSFO-001" |
| Fuel name | Title case | Max 40 chars + "..." | Full name on hover |
| Category | Pill badge, color-coded | — | DISTILLATE=blue, RESIDUAL=orange, GAS=green, BIOFUEL=teal, ALTERNATIVE=purple |
| Fuel family | Plain text | — | e.g., "VLSFO", "MGO" |
| ISO 8217 Grade | Plain text | — | e.g., "DMA", "RMG 380" |
| Sulphur limit | `X.XX%` | — | 2 decimal places |
| Emission factor | `X.XXX` | — | 3 decimal places |
| Status | Badge: ACTIVE (green), DEPRECATED (gray) | — | — |
| System indicator | 🔒 icon + tooltip "System code" | — | Inline trước code text |
| Description | Plain text | Max 80 chars + "..." in table | Full in modal |
