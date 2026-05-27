# Bộ nhớ dự án (Project Memory) — Compact

**Dự án:** Digital Bunkering Platform  
**Slug:** digital-bunkering-platform  
**Ngày:** 260527-1430  
**Mode:** compact  
**Activation Level:** Modular  
**Last Refresh Source:** backbone

## Canonical Vocabulary

| Memory ID | Loại | Thuật ngữ chuẩn | Diễn giải | Nguồn | Trạng thái |
|---|---|---|---|---|---|
| VOC-01 | Entity | Nomination | Đơn đặt mua nhiên liệu từ buyer | Intake | Locked |
| VOC-02 | Entity | eBDN | Electronic Bunker Delivery Note — phiếu giao nhận số | SS 648 | Locked |
| VOC-03 | Entity | Delivery | Một lần giao nhiên liệu (bunkering operation) | SS 648 | Locked |
| VOC-04 | System | MFM | Mass Flow Meter — thiết bị đo lưu lượng | SS 648 | Locked |
| VOC-05 | System | B2G | Business-to-Government submission platform (MPA) | MPA | Locked |
| VOC-06 | Standard | SS 648 | Code of Practice for Bunkering (Mass Flow Metering) | MPA | Locked |
| VOC-07 | Standard | SS 709 | Fuel Type and Grade Codes | MPA | Locked |
| VOC-08 | Standard | SS 524 | Sampling of Marine Fuel Oils | MPA | Locked |
| VOC-09 | Standard | SS 600 | Code of Practice for Bunkering (General) | MPA | Locked |
| VOC-10 | Actor | Barge Operator | Crew trên bunker barge thực hiện delivery | Intake | Locked |
| VOC-11 | Actor | Chief Engineer | Vessel crew nhận fuel và ký eBDN | Intake | Locked |
| VOC-12 | Entity | Fuel Type Code | Mã nhiên liệu theo SS 709 (vd: VLSFO380, BIO-MGO) | SS 709 | Locked |
| VOC-13 | Process | Signing | Quy trình ký eBDN bởi 2 bên (dual-party) | SS 648 | Locked |
| VOC-14 | Entity | MARPOL Sample | Mẫu nhiên liệu lưu giữ theo MARPOL (drip method) | SS 524 | Locked |
| VOC-15 | Regulation | EU ETS | Emissions Trading System — maritime từ 2024 | EU | Locked |

## Approved Decisions

| Decision ID | Chủ đề | Quyết định | Nguồn xác nhận | Ảnh hưởng artifact |
|---|---|---|---|---|
| DEC-01 | Platform type | Operations-focused, NOT trading/ETRM | Scope lock | All modules |
| DEC-02 | Region | Singapore-first, global expansion later | Scope lock | Compliance modules |
| DEC-03 | Multi-tenant | Each supplier = separate tenant | Architecture | All modules |
| DEC-04 | Fuel scope | All fuel types: conventional + LNG + methanol + biofuels + ammonia | Scope lock | fuel-grades, delivery-ops |
| DEC-05 | MFM authority | MFM reading is authoritative, cannot be overridden | SS 648 | mfm-integration, ebdn |
| DEC-06 | eBDN source | eBDN auto-generated from MFM data, not manually entered | SS 648 | ebdn |
| DEC-07 | Signing | Dual-party signing required before barge departure | SS 648 | ebdn |
| DEC-08 | Engagement mode | Hybrid (backbone + FRD + stories + selective SRS) | Plan | Workflow |
| DEC-09 | Delivery method | Vessel (ship-to-ship) enforced for SS 648/600 operations | SS 648 | delivery-ops |
| DEC-10 | UI baseline | Shadcn UI components | Plan | Wireframes |

## Accepted Assumptions

| Assumption ID | Giả định | Điều kiện hiệu lực | Nguồn | Rà lại khi |
|---|---|---|---|---|
| A1 | MFM hardware pre-installed | Licensed barges in Singapore | Intake | Integration design |
| A2 | 4G/satellite on barge | May be intermittent | Intake | Mobile architecture |
| A3 | Vessel crew has smartphone/tablet | For signing workflow | Intake | UX design |
| A4 | MPA B2G API available | Singapore operations | Intake | Integration sprint |
| A5 | Suppliers have MPA licence | Before platform onboarding | Intake | Onboarding flow |
| A6 | Singapore launch first | Before global expansion | Intake | Business decision |

## Rejected Assumptions

| Reject ID | Không được suy diễn lại | Lý do | Nguồn | Ghi chú |
|---|---|---|---|---|
| REJ-01 | Platform is a trading system | Explicitly out of scope — operations only | DEC-01 | Scope boundary |
| REJ-02 | Manual BDN entry allowed | MFM is authoritative source | DEC-05, SS 648 | Compliance |
| REJ-03 | Single-party signing sufficient | SS 648 requires both parties | DEC-07 | Compliance |

## Accepted Corrections

(None yet)

## Push-back Triggers

| Trigger ID | Dấu hiệu dừng | Câu hỏi / hành động bắt buộc |
|---|---|---|
| PB-01 | Any mention of "trading", "hedging", "position" in requirements | Confirm out of scope per DEC-01 |
| PB-02 | Manual override of MFM quantity | Reject — violates SS 648 and DEC-05 |
| PB-03 | Single-party signing | Reject — violates SS 648 and DEC-07 |
| PB-04 | Paper BDN as primary record | Reject — eBDN is mandatory per MPA |
| PB-05 | Fuel code not in SS 709 registry | Flag — validate against registry before proceeding |
