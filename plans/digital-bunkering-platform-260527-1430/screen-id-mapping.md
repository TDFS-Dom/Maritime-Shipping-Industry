# Screen ID Mapping

> Mapping giữa backbone Screen IDs (SCR-NN) và module-level wireframe Screen IDs (SCR-XXX-NN).

| Backbone ID | Module ID | Screen Name | Portal |
|---|---|---|---|
| SCR-01 | — | Supplier Dashboard | P-SUPP |
| SCR-02 | SCR-NOM-01 | Nomination List (Supplier) | P-SUPP |
| SCR-03 | SCR-NOM-02 | Nomination Detail | P-SUPP |
| SCR-04 | SCR-SCH-01 | Scheduling Calendar | P-SUPP |
| SCR-05 | — | Delivery List | P-SUPP |
| SCR-06 | — | Delivery Detail | P-SUPP |
| SCR-07 | SCR-EBDN-01 | eBDN List | P-SUPP |
| SCR-08 | SCR-EBDN-02 | eBDN Detail | P-SUPP |
| SCR-09 | SCR-B2G-01 | Compliance Dashboard | P-SUPP |
| SCR-10 | SCR-FIN-01 | Finance Overview | P-SUPP |
| SCR-11 | SCR-DEL-01 | Active Deliveries (mobile) | P-OPS |
| SCR-12 | SCR-DEL-02 | Pre-delivery Checklist (mobile) | P-OPS |
| SCR-13 | SCR-DEL-03 | MFM Monitor (mobile) | P-OPS |
| SCR-14 | SCR-EBDN-03 | eBDN Sign — Barge (mobile) | P-OPS |
| SCR-15 | — | Upcoming Deliveries (mobile) | P-VESSEL |
| SCR-16 | SCR-EBDN-04 | eBDN Review & Sign (mobile) | P-VESSEL |
| SCR-17 | SCR-NOM-03 | Nomination Create | P-BUYER |
| SCR-18 | — | Delivery Tracking | P-BUYER |

## Module-level screens NOT in backbone (added during FRD/wireframe phase)

| Module ID | Screen Name | Portal | Lý do bổ sung |
|---|---|---|---|
| SCR-NOM-04 | Nomination List (Buyer) | P-BUYER | Buyer cần track own nominations |
| SCR-SCH-02 | Schedule Detail | P-SUPP | Panel/modal on calendar click |
| SCR-SCH-03 | Conflict Warning | P-SUPP | Modal when conflict detected |
| SCR-DEL-04 | Complete Delivery | P-OPS | Confirmation step after pumping |
| SCR-EBDN-05 | eBDN Submission Status | P-SUPP | Inline section in eBDN detail |
| SCR-B2G-02 | Submission List | P-SUPP | Drill-down from dashboard |
| SCR-B2G-03 | Alert Detail | P-SUPP | Click alert row |
| SCR-SAN-01 | Sanctions Alerts | P-SUPP | Compliance sub-section |
| SCR-SAN-02 | Alert Resolution | P-SUPP | Review + resolve match |
| SCR-SAN-03 | Vessel Profile | P-SUPP | Full KYC detail |
| SCR-FIN-02 | Invoice List | P-SUPP | All invoices |
| SCR-FIN-03 | Invoice Detail | P-SUPP | Single invoice |
| SCR-FIN-04 | Record Payment | P-SUPP | Modal overlay |
