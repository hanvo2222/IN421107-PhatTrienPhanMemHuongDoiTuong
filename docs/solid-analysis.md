# Phân tích vi phạm SOLID trong ClubManager.cs (Bài tập Ch02)

- Môn: Phát triển phần mềm hướng đối tượng (IN4211), lớp ĐHCNTT24A-IT (IN421107)
- Sinh viên: Sơn Lê Lam - 0024418698
- Số dòng tính theo file starter gốc `ClubManager.cs` (tính cả khối comment đầu file; `class ClubManager` ở dòng 28).

## 1. Các vi phạm (dòng nào, nguyên lý nào, hậu quả gì)

| # | Dòng | Nguyên lý | Hậu quả |
|---|------|-----------|---------|
| 1 | 28–117 (class `ClubManager`), 35–64 (`CreateClub`) | SRP | God class làm hết: quản lý CLB, thành viên, sự kiện, báo cáo, ghi log, gửi thông báo. `CreateClub` riêng đã có 4 việc (validate, lưu, log, thông báo), nên sửa một việc dễ làm hỏng việc khác. |
| 2 | 52–63 (chuỗi if/else gmail/yahoo/generic) | OCP | Muốn thêm kênh SMS hoặc Slack phải sửa lại method cũ, dễ phát sinh lỗi. Việc nhận diện bằng `Contains` cũng dễ sai. |
| 3 | 48, 79, 94, 111 (`File.AppendAllText`); 54–62, 82–83, 97, 108 (`Console.WriteLine`) | DIP | Nghiệp vụ phụ thuộc trực tiếp vào chi tiết cụ thể (file, console). Không unit test được mà không ghi file thật, không đổi được sang DB hay email thật; đường dẫn log bị gắn cứng (dòng 32). |
| 4 | 44, 75, 93 (`new` trong method); 30–31 (`List` làm kho dữ liệu) | DIP | Class tự tạo đối tượng và tự giữ kho lưu trữ, nên không thay được bằng database và không mock được khi test. |
| 5 | 103–113 (`PrintClubReport`) | SRP + OCP | Báo cáo trộn với nghiệp vụ, in cứng ra Console, còn ghi log ngay trong vòng lặp. Muốn xuất CSV phải sửa method này. |
| 6 | 88–99 (`CreateEvent`) | SRP + OCP | Tạo sự kiện nhưng lại tự gửi mail cho từng thành viên (dòng 96–97). Thêm kênh thông báo mới phải sửa lại đây. |
| 7 | 67–84 (`AddMemberToClub`) | SRP + OCP | Vừa xử lý nghiệp vụ (kiểm tra CLB đầy, tạo thành viên) vừa ghi log, vừa gửi email chào và Slack gắn cứng. |
| 8 | 116 (`GetAllClubs`) | Đóng gói / DIP | Trả ra chính `List` nội bộ, nên bên ngoài `Add` hoặc `Clear` được mà không qua nghiệp vụ. |
| 9 | 40, 72 (số 100 và 50) | Dễ đọc, dễ bảo trì | Luật nghiệp vụ (tên tối đa 100 ký tự, CLB tối đa 50 thành viên) nằm lẫn trong code dưới dạng số cứng. |

## 2. Cách sửa (refactor: đổi cấu trúc, chức năng giữ nguyên)

1. **Vi phạm 1 (SRP): tách class theo trách nhiệm.** `ClubService` lo nghiệp vụ CLB và thành viên, `EventService` lo sự kiện, `ReportService` lo báo cáo. Luật validate đưa vào chính entity: `Club.Create(name, description)`.
2. **Vi phạm 2 (OCP): Factory.** `INotificationService` có các cài đặt `EmailNotificationService`, `SmsNotificationService`, `SlackNotificationService`; `NotificationServiceFactory.Create(NotificationChannel)` chọn đúng cài đặt. Thêm kênh mới chỉ cần thêm một class và một nhánh trong factory, không sửa `ClubService`.
3. **Vi phạm 3 (DIP): phụ thuộc vào abstraction.** `IAppLogger` (`ConsoleLogger`, `FileLogger`) được tiêm qua constructor. Đổi từ log console sang log file chỉ cần sửa một dòng ở `Program.cs`.
4. **Vi phạm 4 (DIP): Repository và Dependency Injection.** `IClubRepository`, `IMemberRepository`, `IEventRepository` cùng các bản `InMemory...Repository`. `Program.cs` là nơi duy nhất dùng `new` cho class cụ thể. Bọc thêm `LoggingClubRepository` (Decorator) để ghi log mỗi lần truy cập mà không sửa repository gốc.
5. **Vi phạm 5 (SRP + OCP): Strategy.** `IReportStrategy` với `ConsoleReportStrategy` và `CsvReportStrategy`, gọi bằng `ReportService.Generate(strategy)`.
6. **Vi phạm 6 (SRP + OCP): Observer.** `EventService` chỉ gọi `OnEventCreated` cho các `IEventObserver` đã đăng ký (`LogNotifier`, `EmailNotifier`), không còn tự gửi thông báo.
7. **Vi phạm 7:** nghiệp vụ nằm trong `ClubService.AddMemberToClub`, kiểm tra đầy bằng `Club.HasRoom(...)`; log và thông báo đi qua `IAppLogger` và factory.
8. **Vi phạm 8:** `GetAllClubs` trả về `IReadOnlyList<Club>`, bên ngoài không sửa được danh sách gốc.
9. **Vi phạm 9:** đưa số cứng thành hằng số có tên `Club.MaxMembers` và `Club.MaxNameLength`.

## 3. Kết quả sau khi refactor

- `ClubManager` được tách thành các thành phần nhỏ: `Domain`, `Logging`, `Notifications`, `Repositories`, `Services`, `Reports`, `Observers`.
- Bốn pattern đã áp dụng: Factory (thông báo), Decorator (log repository), Strategy (báo cáo), Observer (sự kiện).
- Có 12 test xUnit theo mẫu AAA cho lớp Domain (`Club`, `Event`) và `ClubService`, tất cả đều qua.
- Chương trình demo (`Program.cs`) vẫn chạy: tạo CLB, thêm thành viên, tạo sự kiện, in báo cáo.

## 4. Điểm lưu ý khi trao đổi với lớp

- Trước refactor: thêm kênh SMS phải sửa `CreateClub` và `AddMemberToClub`; muốn test `CreateClub` thì phải ghi file và in console thật.
- Sau refactor: thêm kênh chỉ cần thêm một class và một nhánh trong factory; muốn test chỉ cần tiêm bản giả của `IAppLogger` hoặc repository.
