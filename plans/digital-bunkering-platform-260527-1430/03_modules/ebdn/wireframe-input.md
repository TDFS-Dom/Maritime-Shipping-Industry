# Wireframe Input — eBDN (Electronic Bunker Delivery Note)

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module eBDN.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý:** Module này span nhiều portals — P-SUPP (web), P-OPS (mobile), P-VESSEL (mobile).

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-EBDN-01 | eBDN List | P-SUPP | Medium | Must |
| SCR-EBDN-02 | eBDN Detail | P-SUPP | High | Must |
| SCR-EBDN-03 | eBDN Sign (Barge) | P-OPS | Medium | Must |
| SCR-EBDN-04 | eBDN Review & Sign (Vessel) | P-VESSEL | Medium | Must |
| SCR-EBDN-05 | eBDN Submission Status | P-SUPP | Low | Should |

---

## 2. Screen Descriptions

### SCR-EBDN-01: eBDN List

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Supplier Admin / Operations Manager
- **Goal:** Quản lý tất cả eBDN, theo dõi trạng thái ký, lọc và tìm kiếm
- **Entry point:** Side nav → "eBDN" (menu item active)
- **Layout type:** Data table với filters
- **Content:**
  - **Page Header:** "eBDN Management" + badge count (unsigned/pending)
  - **Filter Bar:** Status dropdown (All, Generated, Barge Signed, Fully Signed, Submitted, Acknowledged, Rejected), Date range picker, Vessel search input
  - **Data Table:**
    - Columns: Reference (link), Vessel Name, Barge Name, Fuel Type (badge), Quantity (MT), Status (badge), Barge Signed (date/time or "—"), Vessel Signed (date/time or "—")
    - Sortable: Reference, Vessel, Status, Dates
    - Row click → SCR-EBDN-02
  - **Pagination:** Standard (20/50/100)
- **Actions available:**
  - Row click → Detail
  - Filters/Search
  - Export (CSV)
- **State variations:**
  - Empty: "Chưa có eBDN nào" + illustration
  - Loading: Skeleton rows
  - Filtered empty: "Không có kết quả" + clear filters
- **Mobile behavior:** Table → Card list. Card shows: Reference, Vessel, Status badge, Signed dates.

---

### SCR-EBDN-02: eBDN Detail

- **Portal:** P-SUPP (Desktop — side nav layout)
- **Actor:** Supplier Admin / Compliance Officer
- **Goal:** Xem nội dung eBDN đầy đủ, theo dõi signatures, quản lý submission
- **Entry point:** Click row từ SCR-EBDN-01
- **Layout type:** Detail (document-style + metadata panels)
- **Content:**
  - **Page Header:** Breadcrumb (eBDN > EBDN-2024-00456) + Status badge
  - **Section 1 — eBDN Document Content (Card, document-style):**
    - Header: Supplier info, Vessel info, Barge info
    - Delivery details: Fuel type, Grade, Quantity delivered, Start/End time
    - MFM readings: Totalizer start/end, Temperature, Density
    - Remarks field
    - *Hiển thị như document chính thức — format tương tự BDN giấy*
  - **Section 2 — Signatures (Card):**
    - Barge Operator: Name + Signed date/time + ✓ indicator (hoặc "Chờ ký")
    - Vessel Chief Engineer: Name + Signed date/time + ✓ indicator (hoặc "Chờ ký")
    - Visual: 2 signature slots side-by-side
  - **Section 3 — Status Timeline (Card):**
    - Generated → Barge Signed → Vessel Signed → Submitted to MPA → Acknowledged
    - Mỗi step: timestamp + status
  - **Section 4 — B2G Submission Status (Card, SCR-EBDN-05 content inline):**
    - Submission status badge (Pending/Submitted/Acknowledged/Rejected)
    - MPA Reference Number (hiển thị sau ACKNOWLEDGED)
    - Retry count (nếu có failures)
    - Last attempt timestamp
  - **Timeout Warning Banner (conditional):**
    - Hiển thị nếu >1h kể từ generation mà chưa có signature
    - Yellow warning banner top: "⚠️ eBDN chưa được ký sau 1 giờ. Vui lòng liên hệ barge/vessel."
  - **Action Bar:**
    - "Resubmit to MPA" (Secondary) — chỉ hiện nếu status = REJECTED
    - "Download PDF" (Ghost)
    - Nếu FULLY_SIGNED: KHÔNG hiển thị edit button
- **Actions available:**
  - Resubmit (if rejected)
  - Download PDF
  - View linked nomination
  - Back to list
- **State variations:**
  - Generated (no signatures): Waiting indicators, timeout banner possible
  - Barge Signed: First signature shown, waiting for vessel
  - Fully Signed: Both signatures ✓, content READ-ONLY
  - Submitted: Submission status shown
  - Acknowledged: MPA reference number displayed
  - Rejected: Rejection reason + Resubmit button
- **Mobile behavior:** Sections stacked. Document content scrollable. Signatures section prominent.

---

### SCR-EBDN-03: eBDN Sign (Barge Crew)

- **Portal:** P-OPS (Mobile — full screen, signing flow)
- **Actor:** Barge Operator
- **Goal:** Xem eBDN summary và ký xác nhận từ phía barge
- **Entry point:** Bottom tab "eBDN" trên P-OPS, hoặc notification push, hoặc auto-navigate sau Complete Delivery
- **Layout type:** Summary + Large action button
- **Content:**
  - **App Bar:** "Sign eBDN" + eBDN reference + Back arrow
  - **eBDN Summary (Card, scrollable):**
    - Vessel: Name + IMO
    - Fuel: Type + Grade
    - Quantity Delivered: XXX.XX MT
    - Start Time → End Time
    - Duration
    - MFM Totalizer: Start → End
    - Temperature: Range
  - **Legal Notice (text block):**
    - "Bằng việc ký, bạn xác nhận thông tin trên là chính xác và đầy đủ."
    - Compact text, visible before Sign button
  - **Sign Button (LARGEST element on screen):**
    - Full-width, tall (56px height minimum)
    - Primary color (blue)
    - Text: "Ký xác nhận" (bold, 18px)
    - Icon: pen/signature icon left of text
  - **Confirmation Dialog (after tap Sign):**
    - Title: "Xác nhận ký eBDN"
    - Message: "Bạn xác nhận rằng thông tin delivery ở trên là chính xác?"
    - Buttons: "Hủy" (Ghost) | "Ký" (Primary)
- **Actions available:**
  - Sign → confirmation → submit signature
  - Back → return to eBDN list
  - Scroll to review content
- **State variations:**
  - Ready to sign: Summary shown, Sign button enabled
  - Signing in progress: Button loading state
  - Signed successfully: ✓ Success screen "Đã ký thành công" + auto-return
  - Already signed: Show "Bạn đã ký eBDN này" info, no button
  - Offline: Banner "Không có kết nối" + Sign button disabled
- **Mobile behavior:** Optimized cho signing experience. Content scrollable nhưng Sign button luôn visible (sticky bottom). Confirmation dialog centered.

---

### SCR-EBDN-04: eBDN Review & Sign (Vessel CE)

- **Portal:** P-VESSEL (Mobile — full screen, signing flow)
- **Actor:** Chief Engineer (Vessel)
- **Goal:** Review full eBDN content và ký xác nhận từ phía vessel
- **Entry point:** Bottom tab "Sign" trên P-VESSEL, hoặc notification push
- **Layout type:** Document review + Large action button
- **Content:**
  - **App Bar:** "Review & Sign" + eBDN reference + Back arrow
  - **Barge Signed Indicator (top banner):**
    - Green banner: "✓ Barge đã ký — [Operator Name], [Date/Time]"
    - Cho vessel CE biết barge side đã xác nhận
  - **Full eBDN Content (scrollable, Card):**
    - Hiển thị ĐẦY ĐỦ nội dung eBDN (không phải summary):
      - Supplier info, Vessel info, Barge info
      - Delivery details (fuel, quantity, times)
      - MFM readings (totalizer, temp, density)
      - Remarks
  - **Legal Notice:** Tương tự SCR-EBDN-03
  - **Sign Button (LARGEST element):**
    - Tương tự SCR-EBDN-03: full-width, 56px height, Primary
    - Text: "Ký xác nhận"
  - **Confirmation Dialog:** Tương tự SCR-EBDN-03
- **Actions available:**
  - Review full content (scroll)
  - Sign → confirmation → submit
  - Back → return to list
- **State variations:**
  - Barge not yet signed: Show info "Chờ barge ký trước" + Sign button disabled
  - Ready to sign (barge signed): Full content + Sign enabled
  - Signing: Loading
  - Signed: Success + "eBDN hoàn tất ✓ Đang gửi đến MPA"
  - Already signed: "Bạn đã ký" info
- **Mobile behavior:** Document content đọc được trên mobile (font 16px body). Sign button sticky bottom. Barge signed indicator always visible at top.

---

### SCR-EBDN-05: eBDN Submission Status

- **Portal:** P-SUPP (Desktop — embedded trong SCR-EBDN-02)
- **Actor:** Supplier Admin / Compliance Officer
- **Goal:** Theo dõi trạng thái nộp eBDN cho MPA (B2G submission)
- **Entry point:** Section trong SCR-EBDN-02 detail view
- **Layout type:** Card/Badge (compact status display)
- **Content:**
  - **Status Badge:** PENDING / SUBMITTED / ACKNOWLEDGED / REJECTED / FAILED
  - **MPA Reference Number:** Hiển thị sau status = ACKNOWLEDGED (e.g. "MPA-BDN-2024-123456")
  - **Submission Details:**
    - Last submission attempt: timestamp
    - Retry count: "Attempt 2/3"
    - Error message (if REJECTED): e.g. "Invalid vessel IMO"
  - **Actions (conditional):**
    - "Retry" button — chỉ cho FAILED/REJECTED status
- **Actions available:**
  - Retry submission
  - View error details
- **State variations:**
  - PENDING: Gray badge, no reference
  - SUBMITTED: Blue badge, waiting
  - ACKNOWLEDGED: Green badge + MPA reference number
  - REJECTED: Red badge + error reason + Retry button
  - FAILED (exhausted): Red badge + "Đã hết lượt retry" + manual action required
- **Mobile behavior:** N/A — embedded trong desktop detail view.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Tab Bar Visible |
|---|---|---|---|
| SCR-EBDN-01 | P-SUPP | Side nav → "eBDN" | N/A (desktop) |
| SCR-EBDN-02 | P-SUPP | Side nav → "eBDN" | N/A (desktop) |
| SCR-EBDN-03 | P-OPS | Bottom tab → "eBDN" | ✅ (list) / ❌ (signing flow) |
| SCR-EBDN-04 | P-VESSEL | Bottom tab → "Sign" | ✅ (list) / ❌ (signing flow) |
| SCR-EBDN-05 | P-SUPP | Side nav → "eBDN" | N/A (embedded in SCR-EBDN-02) |

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | eBDN content PHẢI read-only sau khi FULLY_SIGNED — không hiển thị edit button | Legal requirement: document đã ký không được sửa đổi |
| 2 | Sign button là PHẦN TỬ LỚN NHẤT trên mobile signing screens (SCR-EBDN-03, 04) | UX critical: operator cần ký nhanh, tay có thể ướt/bẩn |
| 3 | SCR-EBDN-04 PHẢI hiển thị "Barge đã ký ✓" indicator | Vessel CE cần biết barge đã confirm trước khi mình ký |
| 4 | Timeout warning banner hiển thị nếu >1h kể từ generation mà chưa có chữ ký | Compliance SLA: eBDN phải được ký trong thời gian quy định |
| 5 | MPA reference number CHỈ hiển thị sau status ACKNOWLEDGED | Business rule: reference chỉ có sau khi MPA xác nhận |
| 6 | Confirmation dialog BẮT BUỘC trước khi submit signature | Legal: user phải explicitly acknowledge |
| 7 | Sign button disabled khi offline (không thể ký mà không có kết nối) | Technical: signature cần server confirmation |
| 8 | eBDN content trên P-VESSEL phải FULL (không summary) | CE cần review toàn bộ trước khi ký |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| eBDN Reference | `EBDN-YYYY-NNNNN` | Không truncate | Luôn hiển thị đầy đủ |
| Vessel Name | Title case | Max 25 chars (table), full (detail) | Tooltip on truncate |
| Barge Name | Title case | Max 20 chars (table) | — |
| Quantity | `XXX.XX MT` | — | 2 decimal places, thousand separator |
| Fuel Type | Badge pill (code) trên table, full name trên detail | — | Per DESIGN.md §8 |
| Status | Badge pill (dot + text) | — | Colors per DESIGN.md §3.2 |
| Signed Date | `HH:mm, dd MMM yyyy` | — | Hoặc "—" nếu chưa ký |
| MPA Reference | `MPA-BDN-YYYY-NNNNNN` | Không truncate | Chỉ hiển thị khi ACKNOWLEDGED |
| MFM Totalizer | `XXXXX.XX` + unit | — | 2 decimals |
| Temperature | `XX.X °C` | — | 1 decimal |
| Timeout counter | "Đã Xh Ym kể từ tạo" | — | Red text nếu >1h |
| Retry count | "Lần X/Y" | — | — |
