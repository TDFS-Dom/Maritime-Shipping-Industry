# Work Plan — Digital Bunkering Platform

**Slug:** digital-bunkering-platform  
**Date:** 260527-1430  
**Mode:** hybrid  
**Language:** Vietnamese

## Phạm vi đã chốt

End-to-end digital bunkering operations platform:
- Từ nomination đến settlement
- All fuel types (conventional + LNG + methanol + biofuels + ammonia)
- MPA Singapore compliant (SS 648, SS 600, SS 709, B2G)
- Multi-portal: Supplier (web), Operations (mobile), Vessel (mobile), Buyer (web)

## Modules in scope

| # | Module Slug | Module Name | Priority | FRD | Stories | SRS | Wireframes |
|---|---|---|---|---|---|---|---|
| 1 | nomination | Nomination & Order Management | Must | ✅ | ✅ | ✅ | ✅ |
| 2 | scheduling | Scheduling & Dispatch | Must | ✅ | ✅ | ✅ | ✅ |
| 3 | delivery-ops | Delivery Operations & Safety Checklists | Must | ✅ | ✅ | ✅ | ✅ |
| 4 | mfm-integration | MFM Data Integration | Must | ✅ | ✅ | ✅ | ❌ |
| 5 | ebdn | eBDN Generation & Signing | Must | ✅ | ✅ | ✅ | ✅ |
| 6 | fuel-grades | Fuel Grade Management (SS 709) | Must | ✅ | ✅ | ✅ | ❌ |
| 7 | b2g-compliance | B2G Compliance Reporting | Must | ✅ | ✅ | ✅ | ✅ |
| 8 | sampling-quality | Sampling & Quality (SS 524) | Should | ✅ | ✅ | ✅ | ❌ |
| 9 | sanctions-kyc | Sanctions & KYC Screening | Should | ✅ | ✅ | ✅ | ✅ |
| 10 | finance | Finance & Settlements | Should | ✅ | ✅ | ✅ | ✅ |
| 11 | analytics | Analytics & Business Intelligence | Could | ✅ | ✅ | ❌ | ❌ |
| 12 | messaging | Integrated Messaging | Could | ✅ | ❌ | ❌ | ❌ |

## Execution Plan

### Phase 1: Foundation
- [x] Step 1-4: Intake + Gap Analysis + Scope Lock
- [x] Step 5: Requirements Backbone

### Phase 2: Core Modules (Must)
- [x] Module: nomination — FRD → Stories → SRS
- [x] Module: scheduling — FRD → Stories → SRS
- [x] Module: delivery-ops — FRD → Stories → SRS
- [x] Module: mfm-integration — FRD → Stories → SRS
- [x] Module: ebdn — FRD → Stories → SRS
- [x] Module: fuel-grades — FRD → Stories → SRS
- [x] Module: b2g-compliance — FRD → Stories → SRS

### Phase 3: Supporting Modules (Should)
- [x] Module: sampling-quality — FRD → Stories → SRS
- [x] Module: sanctions-kyc — FRD → Stories → SRS
- [x] Module: finance — FRD → Stories → SRS

### Phase 4: Nice-to-have Modules (Could)
- [x] Module: analytics — FRD → Stories
- [x] Module: messaging — FRD only

### Phase 5: Design & Package
- [x] DESIGN.md (design system)
- [x] Wireframe input packs (7 modules)
- [ ] Package compilation (HTML)

## Estimation

| Artifact type | Count | Avg complexity |
|---|---|---|
| FRDs | 12 | Medium-High |
| User Story sets | 11 | Medium |
| SRS documents | 10 | High |
| Wireframe packs | 7 | Medium |
| Compiled packages | 2 | Low |

## Risks

| Risk | Mitigation |
|---|---|
| MPA B2G API spec unavailable | Design with abstraction layer, mock interface |
| MFM data format varies by manufacturer | Define generic interface, adapter pattern |
| Scope creep from trading features | Strict scope lock — operations only, NOT ETRM |
| Multi-fuel complexity explosion | Common base workflow + fuel-type-specific extensions |
