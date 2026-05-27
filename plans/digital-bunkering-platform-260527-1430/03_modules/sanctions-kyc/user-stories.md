# User Stories — Sanctions & KYC Screening

## Epic: SAN — Kiểm tra trừng phạt và KYC

Screening tự động, quản lý vessel profiles, xử lý false positives.

---

## US-SAN-001: Auto-screen khi tạo Nomination

**Với tư cách là** hệ thống (System),  
**tôi muốn** tự động screen vessel khi nomination submitted,  
**để** phát hiện rủi ro sanctions trước khi Supplier xử lý.

### Tiêu chí chấp nhận

**AC1: Trigger on nomination submit**
- **Given** Buyer submit nomination cho vessel IMO 9123456
- **When** nomination status → PENDING
- **Then** hệ thống auto-trigger screening (vessel IMO + beneficial owner + flag state)

**AC2: CLEAR result**
- **Given** Screening hoàn tất, không match nào
- **When** result processed
- **Then** screening result = CLEAR, nomination unblocked cho Supplier review

**AC3: FLAGGED result**
- **Given** Vessel IMO match OFAC list
- **When** result processed
- **Then** result = FLAGGED, nomination blocked, Compliance Officer nhận escalation

### INVEST Validation
- **I**ndependent: Event-triggered
- **N**egotiable: Which lists to check, timeout handling
- **V**aluable: Compliance before transaction
- **E**stimable: API calls + store result + notify
- **S**mall: 1 sprint
- **T**estable: Mock sanctions data

---

## US-SAN-002: Re-screen khi bắt đầu Delivery

**Với tư cách là** hệ thống (System),  
**tôi muốn** screen lại vessel khi delivery sắp bắt đầu,  
**để** phát hiện thay đổi sanctions status kể từ nomination.

### Tiêu chí chấp nhận

**AC1: Re-screen trigger**
- **Given** Delivery about to start (barge arrived)
- **When** hệ thống prepare start delivery
- **Then** auto re-screen vessel IMO

**AC2: Still CLEAR**
- **Given** Re-screen result = CLEAR (unchanged)
- **When** processed
- **Then** delivery proceeds normally

**AC3: Now FLAGGED**
- **Given** Re-screen result = FLAGGED (new sanctions hit since nomination)
- **When** processed
- **Then** BLOCK delivery start, escalate to Compliance Officer

### INVEST Validation
- **I**ndependent: Phụ thuộc delivery event
- **N**egotiable: Grace period nếu screening slow
- **V**aluable: Catch status changes between nomination and delivery
- **E**stimable: Same screening logic, different trigger
- **S**mall: < 1 sprint
- **T**estable: Status change scenarios

---

## US-SAN-003: Compliance Officer review

**Với tư cách là** Compliance Officer,  
**tôi muốn** review FLAGGED screening results và đưa ra quyết định,  
**để** xác nhận true positive hoặc resolve false positive.

### Tiêu chí chấp nhận

**AC1: View flagged details**
- **Given** Officer nhận escalation
- **When** mở screening detail
- **Then** hiển thị: vessel info, matched lists, match details, nomination context

**AC2: Mark true positive**
- **Given** Officer xác nhận đúng là sanctions hit
- **When** Officer chọn TRUE_POSITIVE + nhập notes
- **Then** transaction remains BLOCKED, log decision

**AC3: Mark false positive**
- **Given** Officer xác nhận là false positive (tên trùng, entity khác)
- **When** Officer chọn FALSE_POSITIVE + nhập reason
- **Then** transaction UNBLOCKED, proceed normally, log decision

### INVEST Validation
- **I**ndependent: Manual review action
- **N**egotiable: Review UI, decision categories
- **V**aluable: Human judgment for sanctions decisions
- **E**stimable: Review form + decision logic
- **S**mall: 1 sprint
- **T**estable: Decision → unblock/block

---

## US-SAN-004: Vessel Profile Management

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem vessel profiles kèm screening history,  
**để** nắm bắt risk profile của vessels giao dịch.

### Tiêu chí chấp nhận

**AC1: View profile**
- **Given** Admin mở vessel profile IMO 9123456
- **When** profile loads
- **Then** hiển thị: vessel name, flag state, owner, last screening result, history

**AC2: Screening history**
- **Given** Vessel đã được screen 5 lần
- **When** Admin xem history tab
- **Then** hiển thị chronological: date, trigger, result, reviewer (if flagged)

### INVEST Validation
- **I**ndependent: Read-only view
- **N**egotiable: Detail level, export option
- **V**aluable: Risk assessment context
- **E**stimable: Profile page + history list
- **S**mall: 1 sprint
- **T**estable: Data accuracy

---

## US-SAN-005: False Positive Learning

**Với tư cách là** hệ thống (System),  
**tôi muốn** ghi nhận false positives để giảm alert fatigue,  
**để** vessel đã confirmed false positive không bị flag lặp lại cho cùng match.

### Tiêu chí chấp nhận

**AC1: Remember false positive**
- **Given** Vessel IMO 9123456 đã marked false positive cho "Entity X on OFAC"
- **When** vessel screened lại và match "Entity X on OFAC" lần nữa
- **Then** auto-mark as likely false positive, lower priority alert (không auto-block)

**AC2: New match vẫn flag**
- **Given** Vessel có false positive history cho "Entity X"
- **When** vessel match "Entity Y" (different match)
- **Then** treat as new FLAGGED — full block + escalation

### INVEST Validation
- **I**ndependent: Enhancement on screening logic
- **N**egotiable: Auto-resolve vs lower-priority alert
- **V**aluable: Reduce alert fatigue, faster processing
- **E**stimable: Match comparison logic
- **S**mall: 1 sprint
- **T**estable: Same vs different match scenarios
