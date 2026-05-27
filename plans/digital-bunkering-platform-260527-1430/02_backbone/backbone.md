# Xương sống yêu cầu (Requirements Backbone)

**Dự án:** Digital Bunkering Platform  
**Slug:** digital-bunkering-platform  
**Ngày:** 260527-1430  
**Chế độ:** hybrid  
**Chủ sở hữu:** BA Lead

---

## Tóm tắt quyết định phạm vi

- **Vấn đề kinh doanh:** Bunkering operations vẫn chủ yếu thủ công (paper BDN, spreadsheet, clipboard). MPA Singapore bắt buộc eBDN digital nhưng nhiều suppliers chưa có platform phù hợp. Multi-fuel complexity tăng nhanh.
- **Kết quả mong muốn:** Platform end-to-end digitize toàn bộ bunkering operations, đảm bảo compliance, tăng visibility, giảm thời gian documentation.
- **Ngoài phạm vi:** Trading/ETRM (hedging, position management), brokerage workflows, AIS vessel tracking, carbon credit trading, physical infrastructure (MFM hardware).
- **Quyết định đã chốt:**
  - Operations-focused, NOT trading platform
  - Singapore-first, global later
  - Web + Mobile (responsive + native mobile cho field)
  - Multi-tenant SaaS
  - All fuel types on single platform

---

## Mục tiêu kinh doanh và chỉ số thành công

| Goal ID | Mục tiêu | Chỉ số | Chủ sở hữu |
|---|---|---|---|
| G1 | Digitize 100% bunkering operations | % deliveries with digital eBDN | Product |
| G2 | Zero compliance violations | Late B2G submissions = 0, fuel code mismatch = 0 | Compliance |
| G3 | Reduce eBDN turnaround time | Time from delivery complete → fully signed < 30 min | Operations |
| G4 | Support all marine fuel types | Fuel types supported / total SS 709 codes | Engineering |
| G5 | Real-time operational visibility | Supplier can see live pipeline status | Product |
| G6 | Emissions tracking readiness | Per-delivery CO₂ calculation accuracy | Sustainability |

---

## Nhóm người dùng và tác nhân

| Actor ID | Vai trò | Mục tiêu chính | Portal | Ghi chú |
|---|---|---|---|---|
| ACT-01 | Supplier Admin | Quản lý operations, compliance, finance | Supplier Portal (Web) | Primary decision maker |
| ACT-02 | Barge Operator | Execute delivery, complete checklists, sign eBDN | Operations Portal (Mobile) | Field user |
| ACT-03 | Vessel Chief Engineer | Verify quantity, sign eBDN | Vessel Portal (Mobile) | External user, minimal interaction |
| ACT-04 | Compliance Officer | Monitor B2G, sanctions, audit trail | Supplier Portal (Web) | Dashboard-focused |
| ACT-05 | Finance Team | Invoice, settle, reconcile | Supplier Portal (Web) | Downstream consumer |
| ACT-06 | Shipowner/Operator | Nominate fuel, track delivery | Buyer Portal (Web) | Buyer-side |
| ACT-07 | System (MFM) | Push metering data | — | Machine actor |
| ACT-08 | System (MPA B2G) | Receive eBDN submissions | — | External system |

---

## Ma trận portal

| Portal ID | Portal Name | Target Actor | Owned Screen Families | Default Entry |
|---|---|---|---|---|
| P-SUPP | Supplier Portal | ACT-01, ACT-04, ACT-05 | Dashboard, Nominations, Scheduling, Deliveries, eBDN, Compliance, Finance, Analytics, Settings | Dashboard |
| P-OPS | Operations Portal | ACT-02 | Active Deliveries, Checklists, MFM Monitor, eBDN Generate, Sign | Active Deliveries |
| P-VESSEL | Vessel Portal | ACT-03 | Upcoming, eBDN Review & Sign, History | Upcoming |
| P-BUYER | Buyer Portal | ACT-06 | Nominations, Tracking, eBDN Archive, Invoices | Nominations |

---

## Bản đồ tính năng

| Feature ID | Tính năng | Mô tả | Ưu tiên | Module |
|---|---|---|---|---|
| F01 | Create Nomination | Buyer tạo đơn đặt nhiên liệu (fuel type, grade, quantity, port, window) | Must | nomination |
| F02 | Confirm Nomination | Supplier review, accept/reject, assign barge | Must | nomination |
| F03 | Schedule Delivery | Allocate barge time slot, set ETA, notify parties | Must | scheduling |
| F04 | Dispatch Barge | Confirm barge departure, track transit | Must | scheduling |
| F05 | Pre-delivery Checklist | Digital safety checklists per fuel type | Must | delivery-ops |
| F06 | Monitor MFM Data | Real-time fuel flow visualization | Must | mfm-integration |
| F07 | Complete Delivery | Record MFM final reading, trigger eBDN | Must | delivery-ops |
| F08 | Generate eBDN | Auto-populate from MFM data + metadata | Must | ebdn |
| F09 | Dual-party Signing | Barge crew sign + vessel Chief Engineer sign | Must | ebdn |
| F10 | B2G Submission | Submit signed eBDN to MPA platform | Must | b2g-compliance |
| F11 | Fuel Code Registry | Manage SS 709 fuel type codes + ISO 8217 mapping | Must | fuel-grades |
| F12 | Fuel Code Validation | Validate fuel code consistency across nomination → delivery → eBDN | Must | fuel-grades |
| F13 | Compliance Dashboard | Monitor B2G submission status, overdue alerts | Must | b2g-compliance |
| F14 | MARPOL Sample Tracking | Record sample reference, link to delivery, retention tracking | Should | sampling-quality |
| F15 | Sanctions Screening | Check vessel/owner/flag against OFAC, EU, UN lists | Should | sanctions-kyc |
| F16 | KYC Vessel Profile | Maintain vessel KYC data, beneficial ownership | Should | sanctions-kyc |
| F17 | Generate Invoice | Auto-create invoice from eBDN quantity + agreed price | Should | finance |
| F18 | Payment Tracking | Track payment status, aging, reconciliation | Should | finance |
| F19 | Emissions Calculator | Per-delivery CO₂ based on fuel type + quantity | Should | b2g-compliance |
| F20 | Operational Analytics | Volumes, turnaround times, barge utilization dashboards | Could | analytics |
| F21 | Integrated Chat | Messaging tied to specific delivery/nomination | Could | messaging |
| F22 | Multi-fuel Workflow Extension | Fuel-type-specific checklists (LNG: IGF Code, Ammonia: toxicity) | Must | delivery-ops |
| F23 | Delivery Method Enforcement | Enforce SS 648/600 delivery method rules | Must | delivery-ops |
| F24 | Loading Operations | Track barge loading from terminal (no eBDN required) | Should | delivery-ops |
| F25 | Transfer Operations | Track inter-barge transfers (eBDN optional) | Should | delivery-ops |

---

## Backbone yêu cầu chức năng

| FR ID | Yêu cầu | Module | Nguồn |
|---|---|---|---|
| FR-NOM-001 | Buyer có thể tạo nomination với: fuel type (SS 709), quantity, port, delivery window | nomination | SS 709, Workflow |
| FR-NOM-002 | Supplier có thể view, accept, reject nomination; assign barge khi accept | nomination | Workflow |
| FR-NOM-003 | Nomination trigger sanctions check tự động trước khi supplier confirm | nomination | Regulations |
| FR-SCH-001 | Scheduler allocate barge + time slot từ available fleet | scheduling | Workflow |
| FR-SCH-002 | System notify vessel + barge khi schedule confirmed | scheduling | Workflow |
| FR-SCH-003 | Scheduling conflict detection (double-booking) | scheduling | Workflow |
| FR-DEL-001 | Barge operator bắt đầu pre-delivery checklist (fuel-type-specific) | delivery-ops | SS 600, IGF Code |
| FR-DEL-002 | All checklist items must be completed before pumping can start | delivery-ops | SS 600 |
| FR-DEL-003 | System enforce delivery method = Vessel khi standard = SS 648/600 | delivery-ops | SS 648 |
| FR-DEL-004 | Record delivery start time, end time, location (lat/long) | delivery-ops | SS 648 |
| FR-MFM-001 | System receive MFM data (flow rate, totalizer, temperature) real-time | mfm-integration | SS 648-2 |
| FR-MFM-002 | MFM reading is authoritative — cannot be overridden by manual entry | mfm-integration | SS 648 |
| FR-MFM-003 | System validate MFM meter serial matches registered meter for barge | mfm-integration | SS 648 |
| FR-EBDN-001 | Generate eBDN automatically from MFM final data + delivery metadata | ebdn | SS 648 |
| FR-EBDN-002 | eBDN must contain: vessel name/IMO, barge name/IMO, supplier licence, date/time, location, fuel code (SS 709), quantity (MT), MFM serial/reading, reference number | ebdn | SS 648, MPA |
| FR-EBDN-003 | Both barge crew AND vessel Chief Engineer must sign before barge departs | ebdn | SS 648 |
| FR-EBDN-004 | Digital signatures timestamped, non-repudiable | ebdn | Electronic Transactions Act |
| FR-FUEL-001 | System maintain registry of SS 709 fuel type codes with ISO 8217 mapping | fuel-grades | SS 709 |
| FR-FUEL-002 | Validate fuel code consistency: nomination fuel type = delivery fuel type = eBDN fuel type | fuel-grades | SS 709 |
| FR-FUEL-003 | Support biofuel codes (BIO-VLSFO380, BIO-MGO, HVO variants) | fuel-grades | SS 709 |
| FR-FUEL-004 | Support methanol codes (MMA, MMB, MMC) | fuel-grades | SS 709 |
| FR-B2G-001 | Submit signed eBDN to MPA B2G platform within required timeframe | b2g-compliance | SS 648, MPA |
| FR-B2G-002 | Track submission status (pending, submitted, acknowledged, rejected) | b2g-compliance | MPA |
| FR-B2G-003 | Alert when submission approaching deadline | b2g-compliance | MPA |
| FR-B2G-004 | Calculate per-delivery CO₂ emissions based on fuel type + quantity | b2g-compliance | EU ETS |
| FR-SAM-001 | Record MARPOL sample reference linked to delivery record | sampling-quality | SS 524 |
| FR-SAM-002 | Track sample retention period (12 months minimum by supplier) | sampling-quality | SS 524 |
| FR-SAM-003 | Record sampling method (continuous drip from MFM point) | sampling-quality | SS 524 |
| FR-SAN-001 | Screen vessel IMO against sanctions lists before nomination confirmation | sanctions-kyc | OFAC, EU, UN |
| FR-SAN-002 | Screen beneficial owner + flag state | sanctions-kyc | Regulations |
| FR-SAN-003 | Re-screen at delivery start (sanctions change constantly) | sanctions-kyc | Regulations |
| FR-FIN-001 | Generate invoice from eBDN: quantity × agreed price | finance | Workflow |
| FR-FIN-002 | Track payment status per invoice (pending, paid, overdue) | finance | Workflow |
| FR-FIN-003 | Settlement reconciliation (eBDN qty vs invoice qty) | finance | Workflow |
| FR-ANA-001 | Dashboard: delivery volumes by period, fuel type, vessel | analytics | Workflow |
| FR-ANA-002 | Dashboard: eBDN turnaround time trends | analytics | G3 |
| FR-MSG-001 | Send/receive messages tied to specific nomination or delivery | messaging | Competitive |

---

## Backbone yêu cầu phi chức năng

| NFR ID | Danh mục | Yêu cầu | Trigger/Gate |
|---|---|---|---|
| NFR-01 | Performance | eBDN generation < 3 seconds | Load testing |
| NFR-02 | Performance | Dashboard load < 2 seconds with 1000+ records | Load testing |
| NFR-03 | Availability | 99.9% uptime (operations are 24/7) | SLA |
| NFR-04 | Security | All data encrypted at rest (AES-256) and in transit (TLS 1.3) | Security review |
| NFR-05 | Security | Multi-tenant data isolation — zero cross-tenant leakage | Security review |
| NFR-06 | Security | Digital signatures non-repudiable, timestamped | Compliance audit |
| NFR-07 | Compliance | MPA B2G submission format exact match | Integration testing |
| NFR-08 | Compliance | Data retention: eBDN 5 years, audit logs 7 years | Compliance audit |
| NFR-09 | Scalability | Support 100+ concurrent deliveries per supplier tenant | Load testing |
| NFR-10 | Mobile | Operations portal usable on 4G with 200ms latency | Mobile testing |
| NFR-11 | Offline | Signing workflow must work with intermittent connectivity | Architecture |
| NFR-12 | Integration | MFM data interface: support both polling and push (webhook) | Architecture |
| NFR-13 | i18n | UI supports English (default) + locale-specific date/time formats | UI review |
| NFR-14 | Audit | All state changes logged with who/what/when | Compliance audit |

---

## Story Map sơ bộ

| Epic | Capability | Stories (draft) | Priority |
|---|---|---|---|
| E1: Nomination | Create nomination | Buyer creates order, selects fuel type/grade/qty/port/window | Must |
| E1: Nomination | Confirm nomination | Supplier reviews, accepts, assigns barge | Must |
| E1: Nomination | Reject/modify | Supplier rejects or proposes alternative | Must |
| E2: Scheduling | Allocate slot | Scheduler assigns barge + time slot | Must |
| E2: Scheduling | Notify parties | Vessel + barge receive schedule confirmation | Must |
| E2: Scheduling | Conflict detection | System flags double-bookings | Must |
| E3: Delivery | Pre-delivery checklist | Fuel-type-specific safety checklist | Must |
| E3: Delivery | Monitor MFM | Real-time flow visualization | Must |
| E3: Delivery | Complete delivery | Record final MFM reading | Must |
| E4: eBDN | Generate | Auto-populate from MFM + metadata | Must |
| E4: eBDN | Sign (barge) | Barge crew reviews and signs | Must |
| E4: eBDN | Sign (vessel) | Chief Engineer reviews and signs | Must |
| E4: eBDN | Archive | Store signed eBDN with full audit trail | Must |
| E5: Compliance | B2G submit | Transmit to MPA | Must |
| E5: Compliance | Monitor status | Track submission acknowledgement | Must |
| E5: Compliance | Emissions calc | Per-delivery CO₂ | Should |
| E6: Fuel Grades | Code registry | Manage SS 709 codes | Must |
| E6: Fuel Grades | Validation | Cross-check consistency | Must |
| E7: Sampling | Record sample | Link MARPOL sample to delivery | Should |
| E7: Sampling | Retention alert | Warn before 12-month expiry | Should |
| E8: Sanctions | Pre-nomination screen | Check vessel/owner/flag | Should |
| E8: Sanctions | Re-screen at delivery | Verify no new sanctions | Should |
| E9: Finance | Invoice generation | From eBDN quantity × price | Should |
| E9: Finance | Payment tracking | Status, aging, reminders | Should |
| E10: Analytics | Operational dashboards | Volumes, turnaround, utilization | Could |
| E11: Messaging | Delivery chat | Messages tied to operations | Could |

---

## UI và màn hình cần tài liệu

| Screen ID | Portal ID | Màn hình | Phức tạp | Cần wireframe |
|---|---|---|---|---|
| SCR-01 | P-SUPP | Supplier Dashboard | High | Yes |
| SCR-02 | P-SUPP | Nomination List | Medium | Yes |
| SCR-03 | P-SUPP | Nomination Detail | Medium | Yes |
| SCR-04 | P-SUPP | Scheduling Calendar | High | Yes |
| SCR-05 | P-SUPP | Delivery List | Medium | Yes |
| SCR-06 | P-SUPP | Delivery Detail | High | Yes |
| SCR-07 | P-SUPP | eBDN List | Medium | Yes |
| SCR-08 | P-SUPP | eBDN Detail | High | Yes |
| SCR-09 | P-SUPP | Compliance Dashboard | Medium | Yes |
| SCR-10 | P-SUPP | Finance Overview | Medium | Yes |
| SCR-11 | P-OPS | Active Deliveries (mobile) | Medium | Yes |
| SCR-12 | P-OPS | Pre-delivery Checklist (mobile) | Medium | Yes |
| SCR-13 | P-OPS | MFM Monitor (mobile) | High | Yes |
| SCR-14 | P-OPS | eBDN Generate & Sign (mobile) | High | Yes |
| SCR-15 | P-VESSEL | Upcoming Deliveries (mobile) | Low | Yes |
| SCR-16 | P-VESSEL | eBDN Review & Sign (mobile) | Medium | Yes |
| SCR-17 | P-BUYER | Nomination Create | Medium | Yes |
| SCR-18 | P-BUYER | Delivery Tracking | Medium | Yes |

---

## Hướng thiết kế UI cần chốt trước wireframe

- Cần DESIGN.md: **Yes**
- Hướng tham chiếu: Professional B2B SaaS (clean, data-dense), mobile-first cho field portals
- Quyết định cần chốt: Color scheme, typography, component library (Shadcn UI baseline)

---

## Gating quyết định artifact

| Artifact | Decision | Lý do |
|---|---|---|
| FRD | Required — all 12 modules | Complex domain, nhiều business rules |
| User Stories | Required — 11 modules | Development team cần stories cho sprint planning |
| SRS | Required — 10 modules | Compliance-critical, cần technical detail |
| Wireframes | Required — 7 modules (có UI) | Multi-portal, mobile + web |
| Package HTML | Required | Stakeholder handoff |

---

## Assumptions, Risks, Open Questions

### Assumptions
- A1: MFM hardware pre-installed on all licensed barges
- A2: 4G/satellite connectivity on barges (may be intermittent)
- A3: Vessel crew has smartphone/tablet
- A4: MPA B2G API available for integration
- A5: Suppliers already have MPA licence
- A6: Singapore-first launch

### Risks
| Risk ID | Risk | Probability | Impact | Mitigation |
|---|---|---|---|---|
| R1 | MPA B2G API spec changes | Medium | High | Abstraction layer, versioned adapter |
| R2 | MFM data format varies by manufacturer | High | Medium | Generic interface + manufacturer adapters |
| R3 | Offline signing on vessel | Medium | High | Local-first architecture, sync when online |
| R4 | Scope creep into trading | Medium | Medium | Hard scope boundary, explicit "out of scope" |
| R5 | Multi-fuel complexity | High | Medium | Common base + fuel-type extensions |

### Open Questions
- Q1: MPA B2G exact API schema? (blocked for integration design)
- Q2: MFM data push vs poll? (architecture decision)
- Q3: Offline signing capability? (mobile architecture decision)
- Q4: Same workflow for all fuels or fuel-specific flows? (module design)

---

## Next Step

→ **FRD production** cho module `nomination` (first module in dependency chain)
