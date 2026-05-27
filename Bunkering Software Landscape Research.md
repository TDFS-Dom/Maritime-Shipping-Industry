# Bunkering Software Landscape — Market Research

> Nghiên cứu ngày 27/05/2026. Nguồn: web search, company websites, press releases.

## 1. Tổng quan thị trường

Ngành bunkering đang chuyển đổi số mạnh mẽ, driven by:

- **MPA Singapore** bắt buộc eBDN + MFM cho mọi delivery từ licensed barges
- **IMO 2020** sulphur cap → đa dạng hóa fuel types → cần quản lý phức tạp hơn
- **EU ETS + FuelEU Maritime** → cần tracking carbon emissions per delivery
- **Alternative fuels** (LNG, methanol, biofuels, ammonia) → quy trình an toàn khác biệt
- **Manual processes** vẫn chiếm ưu thế: clipboard, spreadsheet, paper BDN, fax

Thị trường phần mềm bunkering có thể chia thành 3 segments:

| Segment | Mô tả | Players |
|---|---|---|
| **Operational platforms** | Scheduling, delivery coordination, eBDN | Ofiniti, ZeroNorth, Bunker Suite |
| **Trading & Risk (ETRM)** | Position management, hedging, credit | Inatech Bunkertech, Engine.online |
| **Procurement (buyer-side)** | Fleet fuel optimization, procurement | Veson IMOS, Inatech Shiptech, StormGeo |

---

## 2. Ofiniti (formerly FuelBoss)

### Company Profile

| Field | Value |
|---|---|
| Tên hiện tại | Ofiniti |
| Tên cũ | FuelBoss (khi còn trong DNV) |
| HQ | Oslo, Norway |
| Founded | ~2020 (trong DNV), spin-out tháng 9/2024 |
| Total funding | $9M (tính đến 2026) |
| Latest round | $6.8M growth round (2026) — led by Verb Ventures |
| Investors | Verb Ventures, ShipsFocus, DNV Ventures, Nysnø Climate Investments |
| Volume (2025) | 25,000+ bunker operations, 500,000 MT alternative fuels |
| LNG deliveries | 3,000+ since 2021 |
| Acquisitions | Angsana Technology (Singapore eBDN, MPA-whitelisted) — Mar 2025; Teqplay (maritime intelligence, digital twins) — 2026 |
| Expansion | Singapore → ARA (Amsterdam-Rotterdam-Antwerp), West Africa, Scandinavia |
| Tagline | "Future-Proof Bunkering with eBDN & Singapore Compliance" |

Sources: [DNV spin-out announcement](https://www.dnv.com/news/2024/digital-bunkering-platform-successfully-spun-out-from-dnv/), [TNW](https://thenextweb.com/news/ofiniti-raises-6-8m-to-push-its-maritime-fuel-software-into-global-shipping-hubs), [ofiniti.com](https://www.ofiniti.com/)

### Platform Features (FuelBoss)

| Module | Chức năng | Users |
|---|---|---|
| **Nomination & Order** | Tạo đơn mua nhiên liệu trong term contracts, format chuẩn | Shipowners, operators |
| **Scheduling** | Lên lịch giao nhận, phối hợp barge-vessel | Suppliers, operators |
| **Spot Inquiries** | Tendering, contracting cho spot purchases | Traders, procurement |
| **Operational Coordination** | Real-time delivery management | Barge crew, vessel crew |
| **Safety Checklists** | Digital pre-delivery checklists | Operations |
| **eBDN Generation & Signing** | Tạo + ký eBDN điện tử, archive | All parties |
| **B2G Compliance** | Submit data lên MPA Singapore platform | Suppliers |
| **AIS Asset Tracking** | Map-based overview of LNG vessels & barges | Fleet managers |
| **Integrated Messaging** | Chat gắn với từng bunkering operation | All parties |
| **Business Intelligence** | Market intelligence từ BDN + AIS data | Management, BD |
| **BASiL Calculations** | Compatibility checks cho LNG (via SGMF partnership) | Technical ops |

**Fuel types supported:** Conventional (HSFO, VLSFO, MGO), LNG, Methanol, Biofuels, Ammonia

**Key differentiator:** End-to-end platform covering ALL fuel types, not just conventional. MPA-whitelisted for Singapore eBDN submission.

---

## 3. Competitors

### 3.1 Bunker Suite

| Field | Value |
|---|---|
| HQ | Dubai, UAE (IFZA Business Park) |
| Focus | Brokers, Traders, Physical Suppliers |
| Architecture | Modular SaaS, responsive (mobile/tablet/desktop) |
| Built by | Solvexus |

**Modules:**

| Module | Mô tả |
|---|---|
| Organisation & Accounts | Users, roles, permissions |
| CRM | Accounts, contacts, reminders, activity timelines |
| Requests (RFQs) | Create RFQs, compare quotes, track results |
| Transactions | Confirmations, reconciliation, audits, status controls |
| Email & Documents | Generate, store, send with secure workflows |
| Credit Management | Limits, exposure, holds/releases, approval routing |
| Risk & Compliance | Counterparty screening, policy checks, evidence |
| Analytics & Data | Live dashboards: volumes, margins, positions, risk |
| Finance | Invoicing, AP/AR, settlements; export to ERP |
| Integrations | APIs for ERPs, accounting, data providers |
| Electronic Documents (E-BDN) | Create, exchange, sign E-BDNs with traceability |

**Role-based workflows:**
- **Broker:** RFQ → compare quotes → confirmation → commission tracking
- **Trader:** Live market context → credit enforcement → P&L tracking → audit trail
- **Physical Supplier:** Barge planning → inventory matching → dispatch → BDN/E-BDN → sign-off

Source: [bunkersuite.com](https://www.bunkersuite.com/)

### 3.2 Inatech Bunkertech

| Field | Value |
|---|---|
| HQ | London, UK |
| Focus | Marine fuels trading & supply management (ETRM) |
| Architecture | Cloud-based, built-in ERP |
| Customers | Arte Bunkering, Bergen Bunkers, Trefoil |

**Key capabilities:**
- Trading workflow: deal capture → pricing → execution
- Inventory management: real-time positions, negative inventory, virtual inventory
- Risk management: mark-to-market, VaR, exposure
- Credit management: multi-entity exposure, complex hierarchies
- Cost of sales: flexible costing variations
- Analytics: real-time P&L, position reports
- Compliance: IMO 2020, alternative fuels support
- ERP: integrated finance, no separate system needed

Source: [inatech.com/bunkertech](https://www.inatech.com/bunkertech/)

### 3.3 Veson IMOS Bunkering

| Field | Value |
|---|---|
| Focus | Bunker procurement (buyer-side) |
| Users | Bunker management desks, fleet operators |

**Key capabilities:**
- Repricing open voyage exposure
- Tracking fuel stems across fleet
- Negotiating against purchase history
- Purpose-built workspace for speed

Source: [veson.com/products/imos/bunkering](https://veson.com/products/imos/bunkering/)

### 3.4 ZeroNorth

| Field | Value |
|---|---|
| Focus | eBDN + voyage optimization |
| Key feature | MFM integration, fuel quantity verification |

**Capabilities:**
- Digital eBDN transformation
- Quick and secure fuel quantity verification
- Real-time data transmission
- Transparency and efficiency

Source: [zeronorth.com/ebdn](https://zeronorth.com/ebdn)

### 3.5 Other Players

| Platform | Focus |
|---|---|
| **Engine.online (EngineX)** | Complete bunker management: supplier CRM, credit, operations |
| **StormGeo s-Insight** | Fuel planning, procurement, compliance centralization |
| **Linbis** | General shipping logistics with bunkering module |
| **Vortex Development Group** | Digital BDN/BDR — legally compliant e-documentation |

---

## 4. Common Module Pattern

Dựa trên phân tích cross-platform, một bunkering software đầy đủ bao gồm:

### 4.1 Commercial Modules

| Module | Mô tả | Stakeholders |
|---|---|---|
| **Nomination/Order Management** | Tạo, track, confirm đơn đặt nhiên liệu | Buyers, suppliers |
| **RFQ/Spot Inquiries** | Request for quotation, compare, award | Procurement, traders |
| **Contract Management** | Term contracts, pricing formulas, terms | Commercial |
| **Pricing & Valuation** | Market prices, mark-to-market, hedging | Traders, risk |
| **Credit Management** | Credit limits, exposure, approval workflows | Finance, risk |

### 4.2 Operational Modules

| Module | Mô tả | Stakeholders |
|---|---|---|
| **Scheduling & Dispatch** | Barge planning, time slots, logistics | Operations |
| **Inventory Management** | Tank levels, stock positions, movements | Operations, finance |
| **Delivery Coordination** | Real-time bunkering operation management | Barge crew, vessel crew |
| **Safety Checklists** | Pre-delivery safety checks (digital) | Operations, safety |
| **Sampling & Quality** | Sample tracking, lab results, disputes | Quality, compliance |

### 4.3 Documentation & Compliance

| Module | Mô tả | Stakeholders |
|---|---|---|
| **eBDN Management** | Generate, sign, store electronic BDN | All parties |
| **MFM Integration** | Real-time metering data from Mass Flow Meters | Operations, compliance |
| **Fuel Grade Management** | SS 709 codes, ISO 8217 mapping | Operations, reporting |
| **B2G Reporting** | Submit to MPA Singapore, regulatory bodies | Compliance |
| **Sanctions Screening** | KYC, vessel/owner/flag checks (OFAC, EU, UN) | Compliance, legal |
| **Emissions Reporting** | EU ETS allowances, CII tracking, FuelEU | Compliance, sustainability |

### 4.4 Finance & Analytics

| Module | Mô tả | Stakeholders |
|---|---|---|
| **Invoicing & Settlements** | AP/AR, payment tracking | Finance |
| **P&L Tracking** | Real-time profit & loss per deal/operation | Management |
| **Analytics & BI** | Dashboards, KPIs, market intelligence | Management |
| **Audit Trail** | Complete history of all actions | Compliance, legal |

### 4.5 Platform/Infrastructure

| Module | Mô tả | Stakeholders |
|---|---|---|
| **User & Role Management** | Organizations, users, permissions | IT, admin |
| **AIS Integration** | Vessel tracking via AIS feed | Operations, BD |
| **API & Integrations** | Connect ERPs, accounting, data providers | IT |
| **Messaging/Chat** | Communication tied to operations | All |
| **Mobile Access** | Responsive or native mobile apps | Field operations |

---

## 5. Technology Trends

| Trend | Mô tả | Adoption |
|---|---|---|
| **Digital BDN/eBDN** | Thay thế paper BDN, digital signatures | Singapore mandatory, EU/Rotterdam encouraging |
| **Mass Flow Metering (MFM)** | Real-time quantity measurement, feed into eBDN | Singapore mandatory (SS 648) |
| **B2G Digital Reporting** | Automated submission to regulators | Singapore (MPA), expanding |
| **Multi-fuel support** | Platform phải handle LNG, methanol, biofuels, ammonia | Critical for future-proofing |
| **AIS + Digital Twins** | Real-time vessel/cargo intelligence | Ofiniti (via Teqplay acquisition) |
| **Carbon accounting** | Per-delivery emissions calculation | Driven by EU ETS, FuelEU |
| **Blockchain/DLT** | Immutable records for BDN verification | Pilot stage |
| **AI/ML** | Demand forecasting, anomaly detection, optimization | Early adoption |

---

## 6. Mapping: Domain Knowledge → Software Modules

| Tài liệu Knowledge Base | Software Modules liên quan |
|---|---|
| **Introduction to Fuel Types** | Fuel Grade Management, Inventory (grade tracking), Nomination (fuel selection), SS 709 code mapping |
| **Introduction to Regulations & Compliance** | Sanctions Screening, Emissions Reporting (EU ETS, CII), Compliance dashboards, Fuel selection logic (ECA rules) |
| **MPA and Singapore Bunkering Standards** | eBDN Management, MFM Integration, B2G Reporting, Sampling & Quality, Safety Checklists, Fuel Grade Codes |

---

## 7. Key Business Workflows (End-to-End)

### Workflow 1: Conventional Fuel Delivery (Singapore)

```
1. Nomination     → Buyer creates order (fuel type, grade, quantity, port, date)
2. Confirmation   → Supplier confirms, assigns barge
3. Scheduling     → Barge slot allocated, ETA communicated
4. Pre-delivery   → Safety checklists completed digitally
5. Delivery       → MFM measures fuel flow in real-time
6. Sampling       → Continuous drip sample from MFM point (SS 524)
7. eBDN           → Generated from MFM data, signed by both parties
8. B2G Submit     → eBDN transmitted to MPA within required timeframe
9. Invoicing      → Invoice generated based on BDN quantity
10. Settlement    → Payment processed, reconciled
```

### Workflow 2: LNG Bunkering

```
1. Nomination     → Order within term contract (volume, window)
2. Compatibility  → BASiL check (fuel compatibility between source & receiver)
3. Scheduling     → LNG bunker vessel (LBV) allocated, berth/anchorage coordinated
4. Pre-delivery   → IGF Code safety checklists, crew certification verified
5. Delivery       → Cryogenic transfer, real-time monitoring
6. eBDN           → Digital BDN generated, signed
7. Documentation  → Archived with all supporting checklists
8. Reporting      → Emissions data, compliance records
```

### Workflow 3: Spot Purchase (Broker-mediated)

```
1. Inquiry        → Buyer requests quote (port, date, grade, quantity)
2. RFQ            → Broker sends to multiple suppliers
3. Quotes         → Suppliers respond with price, availability
4. Comparison     → Broker compares, recommends
5. Award          → Buyer confirms, broker issues confirmation
6. Execution      → Follows Workflow 1 or 2 depending on fuel type
7. Commission     → Broker commission calculated and settled
```

---

## 8. Stakeholder Map

| Stakeholder | Role trong platform | Pain points hiện tại |
|---|---|---|
| **Shipowner/Operator** | Buyer — nominate fuel, receive delivery, sign eBDN | Manual scheduling, paper BDN, no visibility |
| **Bunker Supplier** | Seller — confirm order, dispatch barge, deliver, submit B2G | Spreadsheet ops, compliance burden, multi-system |
| **Bunker Broker** | Intermediary — facilitate trades, earn commission | Re-typing data, no audit trail, margin tracking |
| **Bunker Trader** | Buy/sell positions, manage risk | Position tracking across systems, credit risk |
| **Barge Crew** | Execute delivery, complete checklists, sign eBDN | Paper processes, delayed reporting |
| **Vessel Chief Engineer** | Receive fuel, verify quantity, sign eBDN | No advance scheduling info, paper signing |
| **Port Authority (MPA)** | Regulate, monitor, enforce | Data quality from suppliers, late submissions |
| **Surveyor** | Independent quantity/quality verification | Manual measurement, report generation |
| **Lab/Testing** | Analyze fuel samples, issue CoQ | Sample tracking disconnected from delivery |
| **Finance/Accounting** | Invoice, settle, reconcile | Manual matching BDN to invoice |
| **Compliance Officer** | Sanctions check, emissions report, audit | Multiple screening systems, manual checks |

---

## 9. Kết luận cho BA-kit Intake

Dự án "Digital Bunkering Platform" (Option A) có thể define scope như sau:

**Product vision:** End-to-end digital platform cho bunkering operations — từ nomination đến settlement — covering all fuel types, compliant với Singapore MPA standards (SS 648, SS 600, SS 709) và international regulations (MARPOL, EU ETS).

**Target users:** Bunker suppliers (physical), shipowners/operators, barge crew, vessel engineers

**Core differentiators to explore:**
- Multi-fuel native (conventional + LNG + methanol + biofuels + ammonia)
- Singapore-first compliance (eBDN, MFM, B2G)
- Real-time operational coordination
- Integrated emissions tracking (EU ETS, CII, FuelEU)

**Modules in scope (recommended for BA-kit):**
1. Nomination & Order Management
2. Scheduling & Dispatch
3. Delivery Operations & Safety Checklists
4. MFM Integration & eBDN
5. Fuel Grade Management (SS 709)
6. B2G Compliance Reporting
7. Sampling & Quality
8. Sanctions & KYC Screening
9. Finance & Settlements
10. Analytics & Business Intelligence

**Out of scope (first phase):**
- Trading/risk management (ETRM functionality)
- Hedging & position management
- Brokerage workflows
- AIS integration
- Carbon credit trading
