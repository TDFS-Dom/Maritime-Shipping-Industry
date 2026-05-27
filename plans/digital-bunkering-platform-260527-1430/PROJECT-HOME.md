# Trang điều phối dự án BA (Project Home)

**Dự án:** Digital Bunkering Platform  
**Slug:** digital-bunkering-platform  
**Version:** 260527-1430  
**Chế độ:** hybrid  
**Cập nhật:** 2026-05-27

---

## 1. Tôi Đang Ở Đâu?

| Hạng mục | Trạng thái | Ý nghĩa |
|---|---|---|
| Intake | ✅ Hoàn thành | Đã phân tích tài liệu gốc, lock scope |
| Backbone | ✅ Hoàn thành | Khung yêu cầu đã chốt, 35 FR, 14 NFR, 25 features |
| FRD (12 modules) | ✅ Hoàn thành | Tất cả 12 module đã có FRD |
| User Stories (11 modules) | ✅ Hoàn thành | 53 user stories across 11 modules |
| SRS | ⏳ Chưa bắt đầu | Technical specs chi tiết |
| Wireframes | ⏳ Chưa bắt đầu | UI constraint packs |
| Package | ⏳ Chưa bắt đầu | HTML compilation |

---

## 2. Bước Tiếp Theo Được Khuyến Nghị

**→ SRS cho module `ebdn`** (compliance-critical, nhiều technical detail nhất)

Hoặc nếu muốn wireframes trước: **→ DESIGN.md** (cần chốt design system trước khi tạo wireframe constraints)

---

## 3. Việc Cần User Chốt

| Câu hỏi | Vì sao | Ảnh hưởng |
|---|---|---|
| MPA B2G API schema? | Cần cho SRS module b2g-compliance | Integration design, data model |
| MFM data push vs poll? | Architecture decision | mfm-integration SRS |
| Offline signing cần không? | Mobile architecture | ebdn SRS, mobile design |
| LNG workflow riêng hay chung? | Module complexity | delivery-ops SRS |

---

## 4. Bản Đồ Artifact

| Tên BA nhìn thấy | File kỹ thuật | Khi nào dùng |
|---|---|---|
| Phiếu tiếp nhận | `01_intake/intake.md` | Xem lại yêu cầu gốc |
| Kế hoạch | `01_intake/plan.md` | Check tiến độ |
| Khung yêu cầu | `02_backbone/backbone.md` | Source of truth cho scope |
| Bộ nhớ dự án | `02_backbone/project-memory.md` | Vocabulary, decisions, assumptions |
| FRD module X | `03_modules/{module}/frd.md` | Chi tiết chức năng từng module |
| Stories module X | `03_modules/{module}/user-stories.md` | User stories cho dev team |

---

## 5. Prompt Nhanh Theo Runtime

| Muốn làm gì | Gõ |
|---|---|
| Xem trạng thái | `ba-start status --slug digital-bunkering-platform` |
| Tạo SRS cho module | `ba-start srs --slug digital-bunkering-platform --module ebdn` |
| Đánh giá thay đổi | `Đánh giá thay đổi: [mô tả thay đổi]` |
| Tạo wireframe pack | `ba-start wireframes --slug digital-bunkering-platform --module ebdn` |
| Xuất gói bàn giao | `ba-start package --slug digital-bunkering-platform` |

---

## 6. Từ Điển Tên Gọi Thân Thiện

| Thuật ngữ kỹ thuật | BA nói | Giải thích ngắn |
|---|---|---|
| Backbone | Khung yêu cầu | Tài liệu chốt phạm vi + features |
| FRD | Mô tả chức năng | Chi tiết từng tính năng hoạt động thế nào |
| SRS | Đặc tả kỹ thuật | Specs cho lập trình viên |
| User Stories | Câu chuyện người dùng | Yêu cầu dưới dạng "tôi muốn..." |
| Wireframe Input | Ràng buộc giao diện | Rules cho designer tạo mockup |
| Package | Gói bàn giao | HTML compiled final deliverable |

---

## 7. Tổng kết Module

| Module | FRD | Stories | SRS | Priority |
|---|---|---|---|---|
| nomination | ✅ | ✅ (6) | ⏳ | Must |
| scheduling | ✅ | ✅ (5) | ⏳ | Must |
| delivery-ops | ✅ | ✅ (7) | ⏳ | Must |
| mfm-integration | ✅ | ✅ (4) | ⏳ | Must |
| ebdn | ✅ | ✅ (7) | ⏳ | Must |
| fuel-grades | ✅ | ✅ (4) | ⏳ | Must |
| b2g-compliance | ✅ | ✅ (5) | ⏳ | Must |
| sampling-quality | ✅ | ✅ (3) | ⏳ | Should |
| sanctions-kyc | ✅ | ✅ (5) | ⏳ | Should |
| finance | ✅ | ✅ (4) | ⏳ | Should |
| analytics | ✅ | ✅ (3) | ❌ | Could |
| messaging | ✅ | ❌ | ❌ | Could |
