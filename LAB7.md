# BÀI THỰC HÀNH SỐ 7: CASE STUDY CÀI ĐẶT KIẾN TRÚC MICROSERVICES TRÊN MÔI TRƯỜNG .NET CORE
## PHẦN 5: LƯU TRỮ VÀ TRUY CẬP DỮ LIỆU HIỆU QUẢ CÙNG MARTEN

**Họ và tên:** Nguyễn Hà Minh Duy  
**Mã sinh viên:** 2424802010312  
**Repository:** [https://github.com/minhduy-nhmd/Microservices_Lab4](https://github.com/minhduy-nhmd/Microservices_Lab4)  
**Nhánh:** `student/Lab7`  
**Collaborator giảng viên:** `caotxuan@gmail.com`  

---

### Task #1: Thêm Marten vào dự án
- Đã cài đặt package `Marten` (phiên bản `7.19.1`) cho `PaymentService.csproj`.
- Cấu hình DocumentStore trong `MartenInstaller.cs`:
  - Thiết lập chuỗi kết nối PostgreSQL (`PgConnection`).
  - Schema: `payment_service`.
  - Thiết lập cấu hình mapping và duplicate index unique cho `PolicyAccount` theo `PolicyNumber`.
  - Tích hợp serializer JSON với `ProtectedSettersContractResolver`.

### Task #2: Bổ sung code cho microservice PaymentService
Đã tích hợp đầy đủ 3 thư mục của microservice `PaymentService`:
1. **PaymentService**:
   - `Controllers`: `PaymentController.cs` (API lấy số dư tài khoản theo số hợp đồng `api/Payment/accounts/{policyNumber}`).
   - `DataAccess/Marten`: `MartenDataStore.cs`, `MartenInstaller.cs`, `MartenPolicyAccountRepository.cs`, `ProtectedSettersContractResolver.cs`.
   - `Domain`: `PolicyAccount.cs`, `AccountingEntry.cs`, `ExpectedPayment.cs`, `InPayment.cs`, `OutPayment.cs`, `BankStatementFile.cs`, `InPaymentRegistrationService.cs`, `PolicyAccountNumberGenerator.cs`.
   - `Jobs`: Xử lý nạp tiền định kỳ trong nền với Hangfire (`InPaymentRegistrationJob.cs`).
   - `Listeners`: Lắng nghe sự kiện từ RabbitMQ (`PolicyCreatedHandler.cs`, `PolicyTerminatedHandler.cs`).
   - `Queries`: `GetAccountBalanceHandler.cs`.
2. **PaymentService.Api**:
   - `Queries`: `GetAccountBalanceQuery.cs`, `GetAccountBalanceQueryResult.cs`.
   - `Dtos`: `PolicyAccountBalanceDto.cs`, `PolicyAccountDto.cs`.
   - `Exceptions`: `PolicyAccountNotFound.cs`, `BankStatementsFileNotFound.cs`, `BankStatementsFileReadingError.cs`.
3. **PaymentService.Test**:
   - `Domain/PolicyAccountTest.cs`: Kiểm thử toàn bộ logic nạp/rút, hạch toán, dự kiến thanh toán và trạng thái tài khoản.
   - Kết quả kiểm thử: **6/6 tests passed**.

### Task #3: Bổ sung code cho microservice ProductService
Đã tích hợp đầy đủ 3 thư mục của microservice `ProductService`:
1. **ProductService**:
   - `Controllers`: `ProductsController.cs` (API danh mục sản phẩm, tìm theo mã sản phẩm, kích hoạt/hủy kích hoạt sản phẩm).
   - `DataAccess/EF`: `ProductDbContext.cs`, `ProductRepository.cs`, cấu hình mapping EF Core trong `Configuration/ProductConfig.cs`.
   - `Domain`: Các Aggregate và Entity gồm `Product.cs`, `Question.cs`, `Cover.cs`, `Choice.cs`.
   - `Commands` & `Queries`: Sử dụng MediatR để xử lý commands (`CreateProductDraftHandler`, `ActivateProductHandler`, `DiscontinueProductHandler`) và queries (`FindAllProductsHandler`, `FindProductByCodeHandler`).
   - `Init`: Tự động nạp dữ liệu mẫu ban đầu qua `DemoProductFactory.cs`.
2. **ProductService.Api**:
   - Chứa các DTOs, Commands, Queries contract phục vụ gọi API và giao tiếp liên dịch vụ.
3. **ProductService.Test**:
   - Kiểm thử unit test cho domain, mappers, handlers và controller.
   - Kết quả kiểm thử: **14/14 tests passed**.

---

### Kết quả Biên dịch (Build) và Kiểm thử
- **Solution:** `DotNetMicroservicesPoc.sln`
- **Tổng số Projects:** 23/23 projects
- **Kết quả Build:** Thành công 100% (**0 Errors**).
- **Kết quả Tests:**
  - `PaymentService.Test`: 6 Passed, 0 Failed.
  - `ProductService.Test`: 14 Passed, 0 Failed.
