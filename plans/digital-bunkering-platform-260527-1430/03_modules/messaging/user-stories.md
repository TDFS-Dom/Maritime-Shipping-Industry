# User Stories — Integrated Messaging

**Module:** messaging  
**Priority:** Could

---

## Epic: Delivery-linked Communication

### US-MSG-001: Send message tied to nomination

**Với tư cách là** Supplier Admin, **tôi muốn** gửi message gắn với 1 nomination cụ thể **để** buyer nhận được thông tin liên quan ngay trong context của nomination đó.

**AC:**
- Given nomination đang ở bất kỳ status nào
- When Supplier Admin gửi message kèm nomination_id
- Then message hiển thị trong thread của nomination, buyer nhận notification

### US-MSG-002: View message thread for delivery

**Với tư cách là** Barge Operator, **tôi muốn** xem message thread liên quan đến delivery đang active **để** tôi biết instructions hoặc thay đổi kế hoạch.

**AC:**
- Given delivery đang IN_PROGRESS
- When operator mở message thread
- Then thấy tất cả messages liên quan đến delivery/nomination, sorted chronological

### US-MSG-003: Receive notification for new message

**Với tư cách là** Buyer, **tôi muốn** nhận push notification khi có message mới từ supplier **để** tôi không bỏ lỡ thông tin quan trọng.

**AC:**
- Given có message mới cho user
- When message được gửi
- Then user nhận IN_APP notification + push (nếu enabled)
