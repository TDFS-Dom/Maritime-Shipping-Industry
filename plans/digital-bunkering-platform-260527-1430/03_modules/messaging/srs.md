# SRS — Integrated Messaging

**Version:** 1.0  
**Module:** messaging  
**Ngày:** 2026-05-28  
**Priority:** Could

---

## §1 Mục đích & Phạm vi

Simple messaging system gắn với context (nomination, delivery). Không phải full chat platform.

**Shared Rules:** PLAT-005, CRUD-001, AUTHN-001, NOTIF-001

## §2 Data Model

```sql
CREATE TABLE messages (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id    UUID NOT NULL REFERENCES workspaces(id),
    thread_type     VARCHAR(20) NOT NULL,  -- NOMINATION, DELIVERY
    thread_id       UUID NOT NULL,         -- nomination_id or delivery_id
    sender_id       UUID NOT NULL REFERENCES users(id),
    content         TEXT NOT NULL CHECK (LENGTH(content) <= 2000),
    attachment_url  VARCHAR(500),
    read_by         UUID[] DEFAULT '{}',   -- Array of user_ids who read
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_messages_thread ON messages(thread_type, thread_id, created_at);
CREATE INDEX idx_messages_workspace ON messages(workspace_id, created_at DESC);
```

## §3 APIs

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | /api/v1/messages?thread_type={type}&thread_id={id} | List messages in thread |
| POST | /api/v1/messages | Send message |
| PATCH | /api/v1/messages/{id}/read | Mark as read |

### POST /api/v1/messages

**Request:**
```json
{
  "thread_type": "NOMINATION",
  "thread_id": "uuid-of-nomination",
  "content": "Barge assigned. ETA 10:30 tomorrow.",
  "attachment_url": null
}
```

**Response (201):** MessageDto

**Side effect:** Notification dispatched to all thread participants (except sender).

## §4 Business Rules

| ID | Rule |
|----|------|
| BR-MSG-001 | Messages linked to nomination/delivery — cannot exist without context |
| BR-MSG-002 | All workspace users with access to the nomination/delivery can read thread |
| BR-MSG-003 | Messages immutable after creation (no edit/delete) |
