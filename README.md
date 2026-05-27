# 🚢 Maritime Shipping Industry — Knowledge Base

Bộ tài liệu domain knowledge về ngành hàng hải và bunkering (tiếp nhiên liệu tàu biển), kèm nghiên cứu phần mềm bunkering phục vụ phát triển sản phẩm.

## Mục đích

- Onboarding nhanh cho team product/engineering làm việc trong lĩnh vực maritime & bunkering
- Tham chiếu domain knowledge khi phân tích yêu cầu, thiết kế hệ thống
- Cung cấp market research làm input cho BA workflow (BA-kit)

## Nội dung

### Domain Knowledge

| Tài liệu | Mô tả | Thời gian |
|---|---|---|
| [Introduction to Fuel Types](./Introduction%20to%20Fuel%20Types.md) | HFO, MGO, VLSFO, LNG, Biofuels, Methanol, Ammonia. ISO 8217 grades. | 30 phút |
| [Introduction to Regulations & Compliance](./Introduction%20to%20Regulations%20%20Compliance.md) | IMO, MARPOL, SOLAS, ECAs, Sanctions, EU ETS, FuelEU Maritime. | 30 phút |
| [MPA & Singapore Bunkering Standards](./MPA%20and%20Singapore%20Bunkering%20Standards%20(SS%20648SS%20600SS%20709).md) | SS 648 (MFM), SS 600, SS 524 (sampling), SS 709 (fuel codes), eBDN, B2G. | 1 giờ |

### Research & Planning

| Tài liệu | Mô tả |
|---|---|
| [Bunkering Software Landscape Research](./Bunkering%20Software%20Landscape%20Research.md) | Phân tích Ofiniti/FuelBoss, competitors, module patterns, workflows, stakeholders. |
| [Learning Path](./Learning%20Path.md) | Thứ tự đọc tài liệu. |

## Learning Path

```
Fuel Types → Regulations & Compliance → MPA & Singapore Standards → Software Research
```

## Quick Glossary

| Thuật ngữ | Nghĩa |
|---|---|
| Bunkering | Tiếp nhiên liệu cho tàu biển |
| MFM | Mass Flow Meter — đo lưu lượng nhiên liệu |
| eBDN | Electronic Bunker Delivery Note |
| B2G | Business-to-Government reporting |
| MPA | Maritime and Port Authority of Singapore |
| ECA | Emission Control Area |
| VLSFO | Very Low Sulphur Fuel Oil (≤0.5%) |
| SS 648/600/709 | Bộ tiêu chuẩn Singapore cho bunkering |
| ISO 8217 | Tiêu chuẩn quốc tế chất lượng nhiên liệu hàng hải |

## Ứng dụng

Bộ tài liệu này là raw input cho việc phân tích và xây dựng **Digital Bunkering Platform** — hệ thống end-to-end quản lý bunkering operations (tương tự [Ofiniti/FuelBoss](https://www.ofiniti.com/)):

- Nomination & Scheduling
- Delivery Operations & eBDN
- MFM Integration & B2G Compliance
- Fuel Grade Management (SS 709)
- Sanctions Screening & Emissions Reporting
- Finance & Analytics

## Nguồn

- Tài liệu gốc: PDF nội bộ, chuyển đổi sang Markdown
- Market research: Web search (05/2026) — DNV, TNW, company websites
