# Wireframe Input — Sanctions & KYC

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Sanctions Screening & KYC.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý:** Module này xử lý dữ liệu nhạy cảm (sanctions compliance). Visual clarity là ưu tiên hàng đầu.

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-SAN-01 | Sanctions Alerts | P-SUPP | Medium | Must |
| SCR-SAN-02 | Alert Detail & Resolution | P-SUPP | High | Must |
| SCR-SAN-03 | Vessel Profile | P-SUPP | Medium | Must |

---

## 2. Screen Descriptions

### SCR-SAN-01: Sanctions Alerts

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Compliance Officer / Supplier Admin
- **Goal:** Xem tất cả sanctions alerts, phân biệt mức độ nghiêm trọng, xử lý nhanh
- **Entry point:** Side nav → "Compliance" → sub-section "Sanctions" hoặc separate nav item
- **Layout type:** Data table với color-coded rows
- **Content:**
  - **Page Header:** "Sanctions Alerts" + badge count (unresolved)
  - **Filter Bar:** Status dropdown (All, Potential Match, Confirmed Match, False Positive, Resolved), Severity filter, Vessel search
  - **Data Table:**
    - Columns: Vessel Name, IMO, Alert Type (text), Severity (badge), Status (badge), Source List, Created Date, Action (button)
    - **Row color-coding (background tint):**
      - CONFIRMED_MATCH: Red tint background (`#FEF2F2`)
      - POTENTIAL_MATCH: Yellow tint background (`#FFFBEB`)
      - Resolved/False Positive: White (normal) + green status badge
    - Sort: Unresolved CONFIRMED first, then POTENTIAL, then resolved
    - Row click → SCR-SAN-02
  - **Pagination:** Standard
- **Actions available:**
  - Row click → Alert Detail (SCR-SAN-02)
  - Quick action button per row: "Review" → SCR-SAN-02
  - Filter/Search
  - Export alerts (CSV)
- **State variations:**
  - Empty: "Không có sanctions alert nào" + ✓ green indicator "All Clear"
  - Loading: Skeleton rows
  - Has CONFIRMED: Red rows at top, prominent warning banner
  - All resolved: Green "All resolved" banner
- **Mobile behavior:** Table → Card list. Each card: Vessel name, Status badge (large), Alert type, Date. Color-coded card border (red/yellow/green).

---

### SCR-SAN-02: Alert Detail & Resolution

- **Portal:** P-SUPP (Desktop — detail page)
- **Actor:** Compliance Officer
- **Goal:** Xem chi tiết alert match, review thông tin vessel, resolve (false positive hoặc confirm)
- **Entry point:** Click row từ SCR-SAN-01 hoặc link từ SCR-NOM-02
- **Layout type:** Detail (multi-section, decision-focused)
- **Content:**
  - **Page Header:** Breadcrumb (Sanctions > Alert #XXX) + Status badge (large, color-coded)
  - **Section 1 — Match Details (Card, PROMINENT):**
    - Source List: OFAC / EU / UN (badge)
    - Matched Entity Name: Tên entity trong sanctions list
    - Confidence Score: percentage + visual bar (e.g. "87%" với progress bar)
    - Match Type: Exact / Partial / Fuzzy
    - Match Fields: Những fields nào match (vessel name, IMO, owner name...)
    - Last Updated: timestamp từ sanctions provider
  - **Section 2 — Vessel Profile Summary (Card):**
    - IMO Number
    - Vessel Name + Flag
    - Owner: Company name
    - Operator: Company name
    - Classification society
    - Link "Xem full profile" → SCR-SAN-03
  - **Section 3 — Resolution Actions (Card, decision area):**
    - **Option A: Mark False Positive**
      - Button "Mark as False Positive" (Secondary)
      - Khi click → expand form:
        - Reason textarea (REQUIRED, min 10 characters)
        - Character counter: "X/500"
        - Submit button: "Confirm False Positive"
      - Validation: Submit disabled nếu reason < 10 chars
    - **Option B: Confirm Match (Block)**
      - Button "Confirm Match — Block Vessel" (Danger)
      - Khi click → Confirmation dialog:
        - Title: "⚠️ Xác nhận Block Vessel"
        - Message: "Hành động này sẽ chặn tất cả nominations cho vessel [Name] (IMO [number]). Không thể hoàn tác tự động."
        - Buttons: "Hủy" (Ghost) | "Xác nhận Block" (Danger)
  - **Section 4 — Screening History (table trong card):**
    - History of past checks for this vessel
    - Columns: Date, Provider (OFAC/EU/UN), Result (Clear/Match), Resolved By
- **Actions available:**
  - Mark False Positive (with reason) → resolves alert, unblocks nomination
  - Confirm Match → blocks vessel, linked nominations auto-rejected
  - View full vessel profile → SCR-SAN-03
  - Back to list
- **State variations:**
  - Pending review: Both action options available
  - Resolving (False Positive): Form shown, Submit loading
  - Resolved (False Positive): "Resolved as False Positive by [name] — [reason]" green banner
  - Confirmed Match: "BLOCKED" red banner, vessel blocked indicator
  - Already resolved: Read-only, no action buttons
- **Mobile behavior:** Sections stacked. Action buttons sticky bottom. Confidence score bar visible. Confirmation dialog full-screen on mobile.

---

### SCR-SAN-03: Vessel Profile

- **Portal:** P-SUPP (Desktop — detail page)
- **Actor:** Compliance Officer
- **Goal:** Xem toàn bộ KYC data của vessel, screening history, risk assessment
- **Entry point:** Link "Xem full profile" từ SCR-SAN-02, hoặc search vessel
- **Layout type:** Profile detail (multi-section)
- **Content:**
  - **Page Header:** Vessel name + IMO badge + Risk Score indicator (visual gauge/badge)
  - **Section 1 — Basic Info (Card):**
    - IMO Number
    - Vessel Name
    - Flag State (+ flag icon nếu có)
    - Vessel Type
    - Built Year
    - Gross Tonnage
    - DWT (Deadweight)
  - **Section 2 — Ownership & Operation (Card):**
    - Registered Owner: Company name + country
    - Beneficial Owner (if different)
    - Operator/Manager: Company name + country
    - Classification Society
    - P&I Club
  - **Section 3 — Risk Score (Card):**
    - Visual indicator: Gauge hoặc colored badge
    - Score: numeric (0-100) + level (Low/Medium/High/Critical)
    - Factors contributing to score (list)
    - Last assessment date
  - **Section 4 — Screening History (Card, table):**
    - Table: Date, Provider Source (OFAC/EU/UN badge), Check Type (Auto/Manual), Result (Clear ✓ / Match ⚠️), Resolved Status
    - Sorted: newest first
    - Pagination nếu >10 entries
  - **Section 5 — Related Nominations (Card, optional):**
    - Recent nominations involving this vessel
    - Link to each nomination
- **Actions available:**
  - Trigger manual screening ("Run Screening Now" button, Secondary)
  - Back to alerts list
  - View linked nominations
- **State variations:**
  - Normal (no alerts): Green risk indicator, clean profile
  - Active alert: Warning banner top "⚠️ Vessel has active sanctions alert"
  - Blocked: Red banner "🚫 Vessel BLOCKED — Confirmed sanctions match"
  - Loading: Skeleton sections
- **Mobile behavior:** Sections stacked, scrollable. Screening history table → card list on mobile.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Breadcrumb |
|---|---|---|---|
| SCR-SAN-01 | P-SUPP | Side nav → "Compliance" (or dedicated "Sanctions") | — |
| SCR-SAN-02 | P-SUPP | Side nav → "Compliance" | Sanctions > Alert #{ID} |
| SCR-SAN-03 | P-SUPP | Side nav → "Compliance" | Sanctions > Vessel {Name} |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | POTENTIAL_MATCH (yellow) và CONFIRMED_MATCH (red) PHẢI visually RẤT KHÁC BIỆT | Quyết định sai có hậu quả pháp lý nghiêm trọng — officer không được nhầm lẫn 2 trạng thái |
| 2 | Yellow = `#FFFBEB` bg + `#D97706` text/border. Red = `#FEF2F2` bg + `#DC2626` text/border | Cụ thể hóa constraint #1 bằng exact colors |
| 3 | False Positive resolution BẮT BUỘC nhập reason text (min 10 ký tự) | Audit trail requirement: mọi quyết định phải có lý do documented |
| 4 | "Confirm Match — Block" action PHẢI có confirmation dialog với warning message | Destructive action (DESIGN.md §8), hậu quả không thể hoàn tác tự động |
| 5 | Screening history PHẢI hiển thị provider source (OFAC/EU/UN) cho mỗi check | Compliance audit: cần biết check từ nguồn nào |
| 6 | Confidence score phải có visual representation (bar/gauge) + numeric % | Quick assessment: officer nhìn biết ngay mức độ chính xác của match |
| 7 | Row color-coding trong table PHẢI kết hợp color + text + icon | DESIGN.md §8: "Don't use color alone" |
| 8 | Blocked vessel phải có PERSISTENT visual indicator (banner, not toast) | Không được miss thông tin critical này |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| IMO Number | "IMO " + 7 digits | Không truncate | Luôn hiển thị đầy đủ |
| Vessel Name | Title case | Max 30 chars trên table | Tooltip full name |
| Alert Type | Title case text | — | e.g. "Exact Name Match" |
| Severity | Badge pill (dot + text) | — | CRITICAL(red), WARNING(yellow), INFO(blue) |
| Status | Badge pill | — | Color-coded per state |
| Confidence Score | `XX%` + visual bar | — | Bar width proportional to score |
| Source List | Badge (pill per source) | — | "OFAC" "EU" "UN" as separate badges |
| Risk Score | `XX/100` + level text + color | — | 0-25 Low(green), 26-50 Medium(yellow), 51-75 High(orange), 76-100 Critical(red) |
| Screening Date | `dd MMM yyyy, HH:mm` | — | 24h format |
| Reason text | Full text display (detail), truncate 50 chars (table) | Tooltip full | — |
| Country | Name + flag emoji (optional) | — | ISO country names |
| Created Date | Relative (<3 days), absolute (≥3 days) | — | Per DESIGN.md |
