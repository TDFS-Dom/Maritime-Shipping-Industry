# Phiếu tiếp nhận yêu cầu (Intake Form)

## Thông tin dự án (Project Information)

| Trường | Giá trị |
|---|---|
| Tên dự án | Digital Bunkering Platform |
| Ngày | 2026-05-27 |
| Người yêu cầu | Product Owner |
| Tài liệu gốc | Introduction to Fuel Types.md, Introduction to Regulations Compliance.md, MPA and Singapore Bunkering Standards.md, Bunkering Software Landscape Research.md |

## Bối cảnh kinh doanh (Business Context)

### Vấn đề cần giải quyết

Ngành bunkering (tiếp nhiên liệu tàu biển) vẫn vận hành chủ yếu bằng quy trình thủ công: paper BDN, spreadsheet scheduling, clipboard checklists, fax/email communication. Điều này dẫn đến:

1. **Chậm trễ documentation** — paper BDN mất thời gian, dễ sai sót, khó truy xuất
2. **Compliance risk** — MPA Singapore yêu cầu eBDN + B2G submission trong timeframe chặt, vi phạm bị phạt
3. **Thiếu visibility** — shipowners không biết delivery status real-time, suppliers quản lý multi-barge trên spreadsheet
4. **Multi-fuel complexity** — LNG, methanol, biofuels, ammonia mỗi loại có quy trình an toàn khác nhau, khó quản lý thủ công
5. **Regulatory pressure tăng** — EU ETS (2024+), FuelEU Maritime (2025+), IMO GHG strategy → cần tracking emissions per delivery

### Mục tiêu kinh doanh

| # | Mục tiêu | Chỉ số đo lường |
|---|---|---|
| G1 | Số hóa toàn bộ bunkering operations từ nomination đến settlement | 100% deliveries có eBDN digital |
| G2 | Đảm bảo compliance MPA Singapore (SS 648, SS 709) | 0 late B2G submissions, 0 fuel code mismatch |
| G3 | Giảm thời gian từ delivery completion đến eBDN signing | < 30 phút (hiện tại: 2-4 giờ) |
| G4 | Support tất cả fuel types trên 1 platform | Conventional + LNG + Methanol + Biofuels + Ammonia |
| G5 | Cung cấp real-time operational visibility | Suppliers thấy toàn bộ pipeline: upcoming, in-progress, completed |
| G6 | Tích hợp emissions tracking | Per-delivery CO₂ calculation, EU ETS reporting ready |

### Các bên liên quan (Stakeholders)

| Stakeholder | Vai trò | Mức ảnh hưởng | Ghi chú |
|---|---|---|---|
| Bunker Supplier (Admin) | Quản lý toàn bộ operations, compliance | Cao | Primary user, decision maker |
| Barge Operator | Thực hiện delivery, hoàn thành checklists, ký eBDN | Cao | Field user, mobile-first |
| Vessel Chief Engineer | Nhận fuel, verify quantity, ký eBDN | Cao | External user, signing only |
| Compliance Officer | Monitoring B2G submissions, sanctions, audit | Trung bình | Internal, dashboard-focused |
| Finance Team | Invoicing, settlements, reconciliation | Trung bình | Downstream consumer |
| MPA Singapore | Regulator, nhận B2G data | Thấp (external) | System integration point |
| Shipowner/Operator | Nominate fuel, track deliveries | Trung bình | Buyer-side user |

## Yêu cầu thô (Raw Requirements)

### Từ domain knowledge documents:

**Fuel Types (ISO 8217, SS 709):**
- Hệ thống phải quản lý fuel type codes theo SS 709: VLSMGO, ULSMGO, VLSFO380, ULSFO180, HSFO380, BIO-VLSFO380, BIO-MGO, MMA
- Map fuel codes với ISO 8217 grades (DMA, DMZ, RMG380, RME180, etc.)
- Phân biệt conventional vs biofuel vs methanol
- Track sulphur content ranges per fuel type
- Track viscosity ranges per fuel type

**Regulations & Compliance:**
- MARPOL Annex VI — sulphur cap enforcement (0.5% global, 0.1% ECA)
- Sanctions screening — OFAC, EU, UN, UK OFSI checks on vessel/owner/flag
- EU ETS — carbon allowance tracking per delivery (40%→70%→100% coverage 2024-2026)
- CII rating tracking per vessel
- FuelEU Maritime — GHG intensity limits

**MPA & Singapore Standards:**
- SS 648: MFM là authoritative measurement, eBDN generated from MFM data
- SS 648: Dual-party signing (barge crew + Chief Engineer) before departure
- SS 648: B2G submission within required timeframe
- SS 600: Operational procedures — connecting, pre-delivery checks, pumping, completion
- SS 524: Continuous drip sampling from MFM point, sample retention 12 months
- SS 709: Correct fuel type code in eBDN, mismatch → compliance issue
- Delivery method: Vessel (ship-to-ship) when SS 648/SS 600 applies

**Software Landscape (competitive analysis):**
- Ofiniti/FuelBoss: nomination → scheduling → coordination → eBDN → B2G
- Bunker Suite: CRM, RFQ, transactions, credit, E-BDN, risk & compliance, finance
- Inatech Bunkertech: trading, inventory, risk, credit, ERP
- Common pattern: modular architecture, role-based access, multi-fuel support

## Màn hình và giao diện

| Portal | Screens dự kiến | Users |
|---|---|---|
| Supplier Portal (Web) | Dashboard, Nominations list, Scheduling calendar, Deliveries, eBDN list, Compliance, Finance, Analytics, Settings | Supplier Admin, Compliance, Finance |
| Operations Portal (Mobile/Tablet) | Active deliveries, Checklists, MFM readings, eBDN generation, Signing | Barge Operator |
| Vessel Portal (Mobile) | Upcoming deliveries, eBDN review & sign, Delivery history | Chief Engineer |
| Buyer Portal (Web) | Nominations, Delivery tracking, eBDN archive, Invoices | Shipowner/Operator |

## Quy trình và luồng công việc

| # | Quy trình | Mô tả |
|---|---|---|
| W1 | Nomination → Confirmation | Buyer tạo order, supplier confirms, assigns barge |
| W2 | Scheduling & Dispatch | Allocate barge time slot, communicate ETA |
| W3 | Pre-delivery Checks | Digital safety checklists completed by barge crew |
| W4 | Delivery & MFM Monitoring | Real-time fuel flow measurement |
| W5 | eBDN Generation & Signing | Auto-generate from MFM, dual-party sign |
| W6 | B2G Submission | Transmit to MPA within timeframe |
| W7 | Sampling & Quality | Track MARPOL sample, link to delivery |
| W8 | Invoicing & Settlement | Generate invoice from BDN quantity, track payment |
| W9 | Sanctions Screening | KYC check on vessel/owner before confirming nomination |
| W10 | Emissions Calculation | Per-delivery CO₂, aggregate for EU ETS reporting |

## Ràng buộc và giả định

### Ràng buộc
- Platform phải comply với MPA Singapore digital bunkering requirements (whitelisted)
- eBDN format phải match MPA B2G submission schema
- MFM data integration via approved data interface (SS 648-2)
- Digital signatures phải legally valid per Singapore Electronic Transactions Act
- Data retention: eBDN records minimum 5 years, MARPOL samples reference 12 months
- Multi-tenancy: mỗi supplier là 1 tenant riêng biệt

### Giả định
- A1: MFM systems đã có sẵn trên bunker barges (hardware provided by barge owner)
- A2: Internet connectivity available trên barge (4G/satellite) cho real-time data
- A3: Vessel Chief Engineer có smartphone/tablet để ký eBDN
- A4: MPA B2G API documentation available cho integration
- A5: Suppliers đã có licence MPA trước khi onboard platform
- A6: Initial launch chỉ Singapore, multi-region sau

## Tuân thủ và quy định

| Regulation | Impact on system |
|---|---|
| MPA Bunkering Licence conditions | Platform must generate compliant eBDN |
| SS 648 (MFM) | MFM data integration, authoritative measurement |
| SS 709 (Fuel codes) | Fuel type code registry, validation |
| SS 524 (Sampling) | Sample record linking to delivery |
| MARPOL Annex VI | Sulphur compliance tracking |
| EU ETS (2024+) | Per-delivery emissions calculation |
| FuelEU Maritime (2025+) | GHG intensity tracking |
| PDPA Singapore | Personal data protection for crew info |
| Electronic Transactions Act | Digital signature validity |

## Câu hỏi mở

| # | Câu hỏi | Impact |
|---|---|---|
| Q1 | MPA B2G API — exact schema and authentication method? | Integration design |
| Q2 | MFM data interface — polling vs push? Frequency? | Architecture |
| Q3 | Vessel signing — offline capability needed? | Mobile architecture |
| Q4 | Multi-fuel: same workflow for LNG vs conventional, or separate flows? | Module design |
| Q5 | Pricing/quotation features in scope or out? | Scope boundary |
| Q6 | Integration với existing ERP systems của suppliers? | Integration points |

## Ghi chú phân tích

- Dự án có độ phức tạp cao do regulatory requirements nghiêm ngặt (SS 648, MPA compliance)
- Multi-fuel support là differentiator nhưng cũng tăng complexity đáng kể (LNG cần IGF Code, ammonia cần toxicity protocols)
- Mobile-first cho field operations (barge/vessel) là critical — crew không ngồi trước desktop
- Singapore-first strategy hợp lý vì MPA requirements rõ ràng nhất, có benchmark cụ thể
- Engagement mode khuyến nghị: `hybrid` — backbone + targeted FRD cho core modules + stories + selective SRS cho compliance-critical modules
