# Wireframe Input — Messaging

> **Mục đích:** Tài liệu ràng buộc UI cho designer tạo wireframe/mockup module Messaging.
> **Design System:** Tham chiếu `/designs/digital-bunkering-platform/DESIGN.md`
> **Lưu ý đặc biệt:** Messaging KHÔNG phải standalone page. Nó là tab/section nhúng trong nomination detail hoặc delivery detail. Chat-style, contextual communication.

---

## 1. Screen Inventory

| Screen ID | Screen Name | Portal | Complexity | Priority |
|---|---|---|---|---|
| SCR-MSG-01 | Message Thread | P-SUPP / P-OPS / P-BUYER | Medium | Should |

---

## 2. Screen Descriptions

### SCR-MSG-01: Message Thread

- **Portal:** P-SUPP / P-OPS / P-BUYER (hiển thị trong context của nomination hoặc delivery detail)
- **Actor:** Bất kỳ user liên quan đến nomination/delivery (Supplier Admin, Barge Operator, Buyer)
- **Goal:** Trao đổi thông tin liên quan đến nomination hoặc delivery cụ thể (clarification, updates, issues)
- **Entry point:** Tab "Messages" trong Nomination Detail page HOẶC Delivery Detail page
- **Layout type:** Chat-style thread (embedded tab/section, KHÔNG phải standalone page)
- **Content:**
  - **Thread Header (tab area):**
    - Tab label: "Messages" + unread count badge (nếu có)
    - Context line (optional): "Thread for Nomination #NOM-2026-0142" (caption, muted)
  - **Message List (scrollable area, chiếm phần lớn không gian):**
    - Messages hiển thị chronological (cũ nhất trên, mới nhất dưới — standard chat order)
    - **Day Separator:** "15 Jun 2026" (centered, muted text + line)
    - **Mỗi message:**
      - Left: Avatar circle (initials hoặc photo, 32px)
      - Right:
        - Sender name (bold, 14px) + timestamp (muted, 12px, e.g., "14:35")
        - Message content (16px, normal weight)
        - Attachment link (nếu có): icon + file name (clickable) — e.g., "📎 survey-report.pdf"
    - **Own messages:** Aligned right (hoặc khác background nhẹ) để phân biệt
    - **Auto-scroll:** Scroll to bottom khi mở thread hoặc khi new message arrives
  - **Input Area (bottom, fixed):**
    - Text input field: placeholder "Nhập tin nhắn..." — multi-line support (expandable, max 4 lines visible)
    - Character counter: "0/2000" (hiển thị khi typing, muted)
    - Attachment button (optional): icon 📎 bên trái input — tooltip "Đính kèm link"
    - Send button: icon → (hoặc "Gửi") bên phải input — disabled khi input trống
  - **Empty state (no messages yet):**
    - Illustration + "Chưa có tin nhắn nào"
    - "Bắt đầu trao đổi về nomination/delivery này"
- **Actions available:**
  - Type + send message
  - Attach link (text URL, not file upload in v1)
  - Scroll through message history
  - Click attachment link → open in new tab
- **State variations:**
  - No messages: Empty state illustration
  - Has messages: Chronological list
  - Typing: Input expanded, char counter visible
  - Max length reached (2000 chars): Counter red, input stops accepting
  - Sending: Send button loading, input disabled briefly
  - Sent: Message appears in thread, input clears
  - New message from others: Appears at bottom with subtle animation
  - Offline (P-OPS mobile): "Không có kết nối. Tin nhắn sẽ gửi khi có mạng."
- **Mobile behavior (P-OPS):** Full-height tab content. Keyboard pushes input up. Messages scrollable. Avatar smaller (24px). Input area sticky bottom.

---

## 3. Navigation Context

| Screen | Portal | Nav Active Item | Nav Type |
|---|---|---|---|
| SCR-MSG-01 | P-SUPP | (Embedded tab in Nomination/Delivery detail) | Tab within detail page |
| SCR-MSG-01 | P-OPS | (Embedded tab in Delivery detail) | Tab within detail page |
| SCR-MSG-01 | P-BUYER | (Embedded tab in Nomination detail) | Tab within detail page |

**Context:** Message thread KHÔNG có sidebar/bottom nav active item riêng. Nó inherits navigation state từ parent page (Nomination Detail hoặc Delivery Detail).

---

## 4. Non-Negotiable Constraints

| # | Constraint | Lý do |
|---|---|---|
| 1 | Max 2000 ký tự per message | Performance + UX: messages ngắn gọn, tránh spam |
| 2 | Attachment = link only (không file upload trong v1) | MVP scope: file upload phức tạp (storage, virus scan), defer to v2 |
| 3 | Read receipts KHÔNG hiển thị trong v1 | Simplicity: tránh complexity UI + privacy concerns |
| 4 | Messages là immutable — KHÔNG cho phép edit/delete sau khi gửi | Audit trail: trao đổi liên quan delivery phải giữ nguyên cho compliance |
| 5 | Thread chỉ accessible cho users liên quan đến nomination/delivery đó | Security: không phải ai cũng xem được mọi thread |
| 6 | Messages grouped by day separator | UX: dễ scan timeline |
| 7 | KHÔNG phải standalone page — luôn embedded trong context | UX: messages liên quan trực tiếp đến business entity, không cần inbox riêng |

---

## 5. Data Display Rules

| Data | Format | Truncation | Notes |
|---|---|---|---|
| Sender name | Bold, 14px | Max 25 chars + "..." | First name + Last name |
| Timestamp (message) | `HH:mm` (within same day) | — | 24h format |
| Day separator | `dd MMM yyyy` (e.g., "15 Jun 2026") | — | Centered, muted |
| Message content | Normal text, 16px | No truncation (full display) | Preserve line breaks |
| Character counter | `N/2000` | — | Muted, hiển thị khi typing. Red khi ≥1900 |
| Attachment | Icon 📎 + filename (link) | Max 40 chars filename + "..." | Clickable, opens new tab |
| Unread badge (tab) | Exact count ≤ 9, "9+" for > 9 | — | Small badge on tab label |
| Avatar | Initials circle (32px desktop, 24px mobile) | — | Color generated from user name hash |
| Thread context | "Thread for {entity_type} #{ref}" | — | Caption, muted |
