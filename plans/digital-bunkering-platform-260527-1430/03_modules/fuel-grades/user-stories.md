# User Stories — Fuel Grade Management (SS 709)

## Epic: FUEL — Quản lý mã nhiên liệu theo SS 709

Quản lý registry fuel code, validate consistency xuyên suốt hệ thống.

---

## US-FUEL-001: Xem Fuel Code Registry

**Với tư cách là** Supplier Admin,  
**tôi muốn** xem danh sách fuel codes đang có trong hệ thống,  
**để** biết các loại nhiên liệu đang cung cấp và metadata của chúng.

### Tiêu chí chấp nhận

**AC1: Hiển thị danh sách**
- **Given** Admin mở Fuel Code Registry
- **When** trang tải
- **Then** hiển thị danh sách codes với: code, name, type, category, sulphur range, ISO grade, status

**AC2: Filter theo type**
- **Given** Admin chọn filter type = BIO
- **When** filter applied
- **Then** chỉ hiển thị fuel codes có type = BIO (BIO-FAME, BIO-HVO, etc.)

### INVEST Validation
- **I**ndependent: Read-only, hoạt động khi có data
- **N**egotiable: UI layout, export option
- **V**aluable: Visibility vào fuel catalog
- **E**stimable: List + filter
- **S**mall: < 1 sprint
- **T**estable: Data display accuracy

---

## US-FUEL-002: Thêm/Cập nhật Fuel Code

**Với tư cách là** Supplier Admin,  
**tôi muốn** thêm fuel code mới hoặc cập nhật metadata,  
**để** mở rộng danh mục nhiên liệu cung cấp.

### Tiêu chí chấp nhận

**AC1: Thêm code mới**
- **Given** Admin nhập code, name, type, category, sulphur/viscosity ranges, ISO grade
- **When** submit
- **Then** fuel code được tạo, is_active = true

**AC2: Deactivate code**
- **Given** Fuel code "OLD-FUEL" không còn cung cấp
- **When** Admin set is_active = false
- **Then** code bị deactivate, không thể dùng trong nomination mới, nhưng records cũ vẫn reference được

**AC3: Không xóa code đã dùng**
- **Given** Code "RMG380" đã dùng trong 50 nominations
- **When** Admin cố delete
- **Then** hệ thống từ chối "Không thể xóa — chỉ có thể deactivate"

### INVEST Validation
- **I**ndependent: CRUD operation
- **N**egotiable: Bulk import option
- **V**aluable: Maintain fuel catalog
- **E**stimable: CRUD + soft-delete rule
- **S**mall: 1 sprint
- **T**estable: Create/deactivate/delete-blocked

---

## US-FUEL-003: Validate Fuel Code (API)

**Với tư cách là** hệ thống (nomination/delivery/eBDN module),  
**tôi muốn** validate fuel code qua API,  
**để** đảm bảo chỉ codes hợp lệ được sử dụng.

### Tiêu chí chấp nhận

**AC1: Code valid**
- **Given** Code "RMG380" tồn tại và is_active = true
- **When** validate API called với code = "RMG380"
- **Then** return {valid: true, type: "CONVENTIONAL", category: "RESIDUAL", iso_grade: "RMG 380"}

**AC2: Code not found**
- **Given** Code "INVALID123" không tồn tại
- **When** validate API called
- **Then** return {valid: false, reason: "Code not found in SS 709 registry"}

**AC3: Code inactive**
- **Given** Code "OLD-FUEL" tồn tại nhưng is_active = false
- **When** validate API called
- **Then** return {valid: false, reason: "Code is inactive"}

### INVEST Validation
- **I**ndependent: Stateless API call
- **N**egotiable: Response format, caching strategy
- **V**aluable: Single source of truth cho fuel validation
- **E**stimable: Lookup + return
- **S**mall: < 1 sprint
- **T**estable: 3 scenarios rõ ràng

---

## US-FUEL-004: Fuel Code Consistency Check

**Với tư cách là** hệ thống (System),  
**tôi muốn** kiểm tra fuel code match xuyên nomination → delivery → eBDN,  
**để** phát hiện inconsistency sớm.

### Tiêu chí chấp nhận

**AC1: Match — proceed**
- **Given** Nomination fuel = "RMG380", Delivery fuel = "RMG380"
- **When** eBDN generation checks consistency
- **Then** pass — proceed normally

**AC2: Mismatch — alert**
- **Given** Nomination fuel = "RMG380", nhưng Delivery fuel = "MGO"
- **When** consistency check runs
- **Then** flag mismatch, alert Supplier Admin, block eBDN generation until resolved

### INVEST Validation
- **I**ndependent: Cross-entity check
- **N**egotiable: Resolution workflow
- **V**aluable: Data integrity
- **E**stimable: Compare fields
- **S**mall: < 1 sprint
- **T**estable: Match/mismatch scenarios
