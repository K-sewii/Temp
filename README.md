# ĐẶC TẢ USE CASE — HỆ THỐNG QUẢN LÝ RẠP CHIẾU PHIM

> **Baseline đã chốt**
>
> - 01 System chính: **Hệ thống Quản lý Rạp chiếu phim**
> - 07 Subsystem theo domain nghiệp vụ.
> - 26 Use Case chính thức.
> - Kiến trúc Domain-oriented.
> - `Booking` là Aggregate Root của giao dịch đặt vé.
> - `ShowtimeSeat` quản lý trạng thái ghế theo từng suất chiếu: `AVAILABLE → HOLD → SOLD`.
> - Hold mặc định 5 phút; `SystemScheduler` giải phóng Hold hết hạn.
> - `Booking` có `qrCodeString` duy nhất; `Ticket` quản lý `ticketStatus`.
> - Một Booking tối đa 8 Ticket/ghế.
>
> **Template UC Specific:** ID · Actor chính/liên quan · Quan hệ · Tóm tắt · Tiền điều kiện · Dòng sự kiện chính · Dòng sự kiện phụ · Hậu điều kiện.

---

# 1. SYSTEM VÀ SUBSYSTEM

## 1.1. System chính

### Hệ thống Quản lý Rạp chiếu phim

System cung cấp các nghiệp vụ chính:

- Quản lý phim, rạp, phòng, ghế và suất chiếu.
- Cho phép Customer xem phim và suất chiếu.
- Đặt vé và thanh toán online.
- Bán vé và thanh toán tại quầy.
- Quản lý Booking, Ticket và Combo.
- Soát vé bằng QR.
- Quản lý Membership, Promotion và F&B.
- Quản trị nhân viên.
- Tự động giải phóng ghế Hold hết hạn.

## 1.2. Actor

| Actor | Vai trò |
|---|---|
| Customer | Đăng ký, đăng nhập, xem phim/suất chiếu, đặt vé, thanh toán online, xem Booking và Membership |
| Employee | Bán vé, thanh toán tại quầy, soát vé, giao Combo, tra cứu và hủy vé |
| Admin | Quản lý dữ liệu vận hành và cấu hình hệ thống |
| SystemScheduler | Thực hiện tác vụ nền giải phóng Hold hết hạn |

## 1.3. Subsystem

| # | Subsystem | UC | Actor |
|---|---|---|---|
| 1 | **Booking & Payment** — Đặt vé & Thanh toán | CUS-05, CUS-06, CUS-07, CUS-08, EMP-01, EMP-02, EMP-06 | Customer, Employee |
| 2 | **Cinema Operations** — Vận hành tại rạp | EMP-03, EMP-04, EMP-05 | Employee |
| 3 | **Customer Account** — Tài khoản khách hàng | CUS-01, CUS-02, CUS-03, CUS-09 | Customer |
| 4 | **Catalog & Scheduling** — Danh mục & Lịch chiếu | CUS-04, ADM-01..ADM-06 | Customer, Admin |
| 5 | **Marketing & F&B** — Membership, Promotion & F&B | ADM-07, ADM-08, ADM-09 | Admin |
| 6 | **Staff Administration** — Quản trị nhân viên | ADM-10 | Admin |
| 7 | **System Background** — Tác vụ nền | SYS-01 | SystemScheduler |

### Nguyên tắc chia Subsystem

- Chia theo **domain nghiệp vụ**, không chia theo Actor.
- Một Actor có thể tương tác với nhiều Subsystem.
- Subsystem là nhóm/boundary tổ chức UC, **không phải Use Case**.
- Actor kết nối trực tiếp với UC; `include/extend` chỉ dùng giữa các UC.
- `CUS-04` và `CUS-05` là các UC xem dữ liệu; không biến các bước UI như "Chọn ghế", "Hold ghế", "Tính tiền", "Sinh QR" thành UC độc lập.
- `EMP-03 Soát vé` và `EMP-04 Giao Combo` độc lập dù cùng có thể tra cứu Booking/QR.
- `EMP-05 Tra cứu Booking` là UC dùng chung cho các nghiệp vụ vận hành cần tìm Booking.

---

# 2. DANH SÁCH 26 USE CASE

## Customer

| ID | Use Case |
|---|---|
| CUS-01 | Đăng ký tài khoản |
| CUS-02 | Đăng nhập |
| CUS-03 | Quản lý Profile |
| CUS-04 | Xem phim |
| CUS-05 | Xem suất chiếu |
| CUS-06 | Đặt vé online |
| CUS-07 | Thanh toán online |
| CUS-08 | Xem lịch sử Booking |
| CUS-09 | Quản lý Membership |

## Employee

| ID | Use Case |
|---|---|
| EMP-01 | Bán vé tại quầy |
| EMP-02 | Thanh toán tại quầy |
| EMP-03 | Soát vé |
| EMP-04 | Giao Combo |
| EMP-05 | Tra cứu Booking |
| EMP-06 | Hủy vé |

## Admin

| ID | Use Case |
|---|---|
| ADM-01 | Quản lý Phim |
| ADM-02 | Quản lý Rạp |
| ADM-03 | Quản lý Phòng chiếu |
| ADM-04 | Quản lý Ghế |
| ADM-05 | Quản lý Suất chiếu |
| ADM-06 | Quản lý Giá vé |
| ADM-07 | Quản lý Membership |
| ADM-08 | Quản lý Promotion |
| ADM-09 | Quản lý Food & Beverage |
| ADM-10 | Quản lý Nhân viên |

## System

| ID | Use Case |
|---|---|
| SYS-01 | Giải phóng Hold hết hạn |

---

# 3. USE CASE SPECIFICATION

## A. CUSTOMER

## CUS-01 — Đăng ký tài khoản

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Cho phép người dùng mới tạo tài khoản Customer.  
**Tiền điều kiện:** Người dùng chưa có tài khoản.

### Dòng sự kiện chính
1. Customer chọn "Đăng ký".
2. Customer nhập email/số điện thoại, mật khẩu và họ tên.
3. Hệ thống kiểm tra định dạng và tính duy nhất của email/số điện thoại.
4. Hệ thống tạo tài khoản Customer.
5. Hệ thống khởi tạo Membership hạng mặc định.
6. Hệ thống thông báo đăng ký thành công.

### Dòng sự kiện phụ
- 3.1. Email/số điện thoại đã tồn tại → thông báo tài khoản đã tồn tại → quay lại bước 2.
- 3.2. Dữ liệu không hợp lệ → thông báo lỗi field tương ứng → quay lại bước 2.

### Hậu điều kiện
- **Thành công:** Customer mới và Membership mặc định được tạo.
- **Thất bại:** Không tạo tài khoản.

---

## CUS-02 — Đăng nhập

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Cho phép Customer đăng nhập bằng tài khoản đã đăng ký.  
**Tiền điều kiện:** Customer đã có tài khoản.

### Dòng sự kiện chính
1. Customer nhập email/số điện thoại và mật khẩu.
2. Hệ thống xác thực thông tin.
3. Hệ thống kiểm tra trạng thái tài khoản.
4. Hệ thống tạo phiên đăng nhập.
5. Hệ thống chuyển Customer vào trang chính.

### Dòng sự kiện phụ
- 2.1. Sai tài khoản/mật khẩu → thông báo lỗi → quay lại bước 1.
- 3.1. Tài khoản bị khóa → thông báo tài khoản bị khóa → kết thúc.

### Hậu điều kiện
Customer có phiên đăng nhập hợp lệ.

---

## CUS-03 — Quản lý Profile

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Customer xem và cập nhật thông tin cá nhân.  
**Tiền điều kiện:** Customer đã đăng nhập.

### Dòng sự kiện chính
1. Customer vào "Tài khoản của tôi".
2. Hệ thống hiển thị thông tin hiện tại.
3. Customer chỉnh sửa thông tin.
4. Customer chọn "Lưu".
5. Hệ thống validate dữ liệu.
6. Hệ thống cập nhật thông tin.
7. Hệ thống thông báo thành công.

### Dòng sự kiện phụ
- 5.1. Dữ liệu không hợp lệ → thông báo lỗi → quay lại bước 3.

### Hậu điều kiện
Thông tin Profile được cập nhật.

---

## CUS-04 — Xem phim

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Customer xem danh sách và chi tiết phim.  
**Tiền điều kiện:** Không bắt buộc đăng nhập.

### Dòng sự kiện chính
1. Customer mở trang Phim.
2. Hệ thống hiển thị danh sách phim đang/sắp chiếu.
3. Customer chọn một phim.
4. Hệ thống hiển thị chi tiết phim.

### Dòng sự kiện phụ
- 2.1. Không có phim phù hợp → thông báo.

### Hậu điều kiện
Không thay đổi dữ liệu hệ thống.

---

## CUS-05 — Xem suất chiếu

**Actor chính:** Customer  
**Quan hệ:** `<<include>>` bởi CUS-06 và EMP-01.  
**Tóm tắt:** Customer xem Showtime theo phim/rạp/ngày và chọn một Showtime.  
**Tiền điều kiện:** Có Showtime phù hợp với điều kiện tìm kiếm.

### Dòng sự kiện chính
1. Customer chọn phim hoặc chọn rạp/ngày trực tiếp.
2. Hệ thống tìm và hiển thị Showtime khả dụng.
3. Mỗi Showtime hiển thị rạp, phòng, thời gian và giá.
4. Customer chọn một Showtime.
5. Hệ thống chuyển sang nghiệp vụ tiếp theo.

### Dòng sự kiện phụ
- 2.1. Không có Showtime phù hợp → thông báo "Không có suất chiếu".

### Hậu điều kiện
Customer chọn được Showtime.

---

## CUS-06 — Đặt vé online

**Actor chính:** Customer  
**Actor liên quan:** SystemScheduler (gián tiếp qua timeout)  
**Quan hệ:** `<<include>>` CUS-05, CUS-07; các hành vi Membership/Promotion/Combo là phần mở rộng tùy chọn.  
**Tóm tắt:** Customer chọn Showtime, giữ ghế, thanh toán và nhận Ticket điện tử.  
**Tiền điều kiện:** Customer đã đăng nhập; Showtime đang mở bán.

### Dòng sự kiện chính
1. Customer chọn Showtime.
2. Hệ thống hiển thị sơ đồ `ShowtimeSeat`.
3. Customer chọn tối đa 8 ghế `AVAILABLE`.
4. Hệ thống thực hiện atomic update để chuyển các ghế sang `HOLD`.
5. Hệ thống đặt `holdExpiresAt = now + 5 phút`.
6. Customer có thể áp dụng Membership.
7. Customer có thể áp dụng tối đa một Promotion.
8. Customer có thể thêm Combo.
9. Hệ thống tính tổng tiền từ Booking.
10. Customer thực hiện CUS-07.
11. Thanh toán thành công → Booking `PAID`.
12. Các `ShowtimeSeat` chuyển `HOLD → SOLD`.
13. Hệ thống tạo Ticket tương ứng với các ghế đã bán, Ticket ban đầu `VALID`.
14. Hệ thống sinh `Booking.qrCodeString` duy nhất.
15. Hệ thống hiển thị vé điện tử/QR.

### Dòng sự kiện phụ
- 4.1. Một hoặc nhiều ghế đã bị Hold/Sold bởi request khác → thông báo ghế conflict → rollback toàn bộ ghế đã Hold thành công trong request → quay lại bước 3.
- 10.1. Thanh toán thất bại → thông báo lỗi; các ghế tiếp tục `HOLD` nếu chưa hết hạn.
- 10.2. Hold hết hạn trước khi thanh toán hoàn tất → SYS-01 release về `AVAILABLE`; Customer phải thực hiện lại việc giữ ghế.

### Hậu điều kiện
- **Thành công:** `Booking = PAID`, Ticket = `VALID`, `ShowtimeSeat = SOLD`, QR được sinh.
- **Thất bại:** Booking chưa hoàn tất; Hold được giải phóng khi timeout/cancel theo nghiệp vụ.

---

## CUS-07 — Thanh toán online

**Actor chính:** Customer  
**Quan hệ:** `<<include>>` bởi CUS-06.  
**Phương thức:** MoMo, VNPay, Credit Card. Cash không áp dụng online.  
**Tóm tắt:** Customer thanh toán Booking qua phương thức online.  
**Tiền điều kiện:** Booking có ShowtimeSeat đang `HOLD` và còn trong thời gian giữ.

### Dòng sự kiện chính
1. Hệ thống hiển thị tổng tiền Booking.
2. Customer chọn phương thức thanh toán.
3. Hệ thống chọn PaymentStrategy tương ứng.
4. Hệ thống chuyển sang cổng thanh toán.
5. Cổng thanh toán trả kết quả.
6. Hệ thống xác nhận giao dịch.
7. Booking được cập nhật `PAID`.
8. Ticket được tạo.
9. ShowtimeSeat chuyển sang `SOLD`.
10. QR của Booking được sinh nếu chưa có.

### Dòng sự kiện phụ
- 5.1. Thanh toán thất bại/hủy → thông báo thất bại → Hold tiếp tục nếu còn hạn.
- 5.2. Hold hết hạn trước khi thanh toán hoàn tất → thông báo hết hạn → giao dịch không hoàn tất.

### Hậu điều kiện
- **Thành công:** Booking `PAID`, Ticket `VALID`, ShowtimeSeat `SOLD`.
- **Thất bại:** Booking chưa `PAID`.

---

## CUS-08 — Xem lịch sử Booking

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Customer xem danh sách và chi tiết các Booking của chính mình.  
**Tiền điều kiện:** Customer đã đăng nhập.

### Dòng sự kiện chính
1. Customer mở "Lịch sử đặt vé".
2. Hệ thống chỉ lấy Booking của Customer hiện tại.
3. Hệ thống hiển thị danh sách theo thời gian.
4. Customer chọn một Booking.
5. Hệ thống hiển thị chi tiết Booking, Ticket, Combo, QR và trạng thái.

### Dòng sự kiện phụ
- 2.1. Không có Booking → thông báo chưa có lịch sử.

### Hậu điều kiện
Không thay đổi dữ liệu.

---

## CUS-09 — Quản lý Membership

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Customer xem Membership hiện tại, điểm và quyền lợi.  
**Tiền điều kiện:** Customer đã đăng nhập và có Membership.

### Dòng sự kiện chính
1. Customer mở mục Thành viên.
2. Hệ thống hiển thị hạng hiện tại.
3. Hệ thống hiển thị điểm tích lũy nếu có.
4. Hệ thống hiển thị quyền lợi.
5. Hệ thống hiển thị lịch sử tích điểm nếu có.

### Dòng sự kiện phụ
- 2.1. Đủ điều kiện nâng hạng → hệ thống thông báo trạng thái đủ điều kiện.

### Hậu điều kiện
Không thay đổi dữ liệu Membership chỉ bằng thao tác xem.

---

# B. EMPLOYEE

## EMP-01 — Bán vé tại quầy

**Actor chính:** Employee  
**Quan hệ:** `<<include>>` CUS-05, EMP-02; Combo là hành vi tùy chọn.  
**Tóm tắt:** Employee đặt vé thay khách mua trực tiếp tại quầy.  
**Tiền điều kiện:** Employee đã đăng nhập và đang làm việc tại quầy.

### Dòng sự kiện chính
1. Employee chọn phim/Showtime.
2. Employee chọn tối đa 8 ghế.
3. Hệ thống atomic Hold các ghế.
4. Nếu thành công, các ghế chuyển `HOLD`.
5. Employee có thể thêm Combo.
6. Hệ thống tính tổng tiền.
7. Employee thực hiện EMP-02.
8. Thanh toán thành công → Booking `PAID`.
9. Hệ thống tạo Ticket `VALID`.
10. ShowtimeSeat chuyển `SOLD`.
11. Hệ thống sinh QR.
12. Hệ thống in/hiển thị vé.

### Dòng sự kiện phụ
- 3.1. Ghế bị Hold/Sold đồng thời → thông báo conflict → rollback các ghế đã Hold trong request → quay lại bước 2.
- Thanh toán thất bại → Booking chưa hoàn tất; Hold tiếp tục nếu còn hạn.

### Hậu điều kiện
Booking bán tại quầy hoàn tất; Ticket hợp lệ và ghế tương ứng `SOLD`.

---

## EMP-02 — Thanh toán tại quầy

**Actor chính:** Employee  
**Quan hệ:** `<<include>>` bởi EMP-01.  
**Phương thức:** Cash, Credit Card POS.  
**Tóm tắt:** Employee xử lý thanh toán cho Booking tại quầy.  
**Tiền điều kiện:** Booking đã được tạo và ghế đang `HOLD`.

### Dòng sự kiện chính
1. Hệ thống hiển thị tổng tiền.
2. Employee chọn Cash hoặc Credit Card.
3. Nếu Cash, Employee nhập số tiền khách đưa.
4. Hệ thống tính tiền thừa.
5. Nếu Card, hệ thống gửi giao dịch tới POS.
6. Hệ thống nhận kết quả thanh toán.
7. Booking được cập nhật `PAID`.

### Dòng sự kiện phụ
- 3.1. Tiền khách đưa nhỏ hơn tổng tiền → thông báo không đủ tiền → nhập lại.
- 5.1. Card thất bại → thông báo → cho phép thực hiện lại.
- Phương thức chỉ dành cho online không được dùng ở quầy.

### Hậu điều kiện
Booking được thanh toán thành công hoặc vẫn ở trạng thái chưa thanh toán nếu giao dịch thất bại.

---

## EMP-03 — Soát vé

**Actor chính:** Employee  
**Quan hệ:** `<<include>>` EMP-05 nếu cần tra cứu Booking.  
**Tóm tắt:** Employee dùng QR của Booking để kiểm tra và xác nhận từng Ticket.  
**Tiền điều kiện:** Booking đã thanh toán và có Ticket.

### Dòng sự kiện chính
1. Employee quét QR.
2. Frontend lấy `qrCodeString`.
3. Backend tìm Booking.
4. Hệ thống kiểm tra Booking và Showtime.
5. Hệ thống lấy toàn bộ Ticket của Booking.
6. Hệ thống hiển thị danh sách Ticket và trạng thái.
7. Employee chọn tất cả Ticket `VALID` hoặc chọn từng Ticket.
8. Hệ thống xác nhận các Ticket được chọn.
9. Ticket được chọn chuyển `VALID → USED`.
10. Hệ thống hiển thị kết quả.

### Dòng sự kiện phụ
- 3.1. QR không tồn tại → thông báo QR không hợp lệ.
- 4.1. Booking chưa PAID → không cho soát.
- 4.2. Showtime không phù hợp → không cho soát.
- 7.1. Ticket `USED` hoặc `CANCELLED` → không thể chọn.
- Một số Ticket đã USED nhưng còn Ticket VALID → chỉ cho xử lý Ticket VALID.

### Hậu điều kiện
Chỉ các Ticket được chọn chuyển sang `USED`; các Ticket còn lại không thay đổi.

> **Quyết định quan trọng:** QR thuộc Booking, nhưng trạng thái sử dụng thuộc từng Ticket. Vì vậy cùng một QR có thể được quét lại để xử lý các Ticket VALID còn lại.

---

## EMP-04 — Giao Combo

**Actor chính:** Employee  
**Quan hệ:** `<<include>>` EMP-05 nếu cần tra cứu Booking. Không include EMP-03.  
**Tóm tắt:** Employee xác nhận giao Combo thuộc Booking.  
**Tiền điều kiện:** Booking tồn tại và có Combo.

### Dòng sự kiện chính
1. Employee tra cứu Booking.
2. Hệ thống hiển thị Combo.
3. Employee chọn Combo cần giao.
4. Employee xác nhận giao.
5. Hệ thống cập nhật trạng thái Combo thành `DELIVERED`.
6. Hệ thống thông báo thành công.

### Dòng sự kiện phụ
- Không tìm thấy Booking → thông báo.
- Booking không có Combo → thông báo.
- Combo đã `DELIVERED` → không cho giao lại.

### Hậu điều kiện
Combo được đánh dấu đã giao.

---

## EMP-05 — Tra cứu Booking

**Actor chính:** Employee  
**Tóm tắt:** Employee tìm Booking để hỗ trợ các nghiệp vụ tại rạp.  
**Tiền điều kiện:** Employee đã đăng nhập.

### Dòng sự kiện chính
1. Employee nhập tiêu chí tra cứu được hỗ trợ.
2. Hệ thống tìm Booking.
3. Hệ thống hiển thị Booking phù hợp.
4. Hệ thống hiển thị trạng thái Booking.
5. Hệ thống hiển thị Ticket, Combo, Showtime và QR nếu có.

### Dòng sự kiện phụ
- Không tìm thấy → thông báo.
- Có nhiều kết quả → Employee chọn Booking cần xem.

### Hậu điều kiện
Không thay đổi dữ liệu.

---

## EMP-06 — Hủy vé

**Actor chính:** Employee  
**Quan hệ:** `<<include>>` EMP-05.  
**Tóm tắt:** Employee hủy một hoặc nhiều Ticket hợp lệ theo nghiệp vụ.  
**Tiền điều kiện:** Booking tồn tại và có Ticket `VALID`.

### Dòng sự kiện chính
1. Employee tra cứu Booking.
2. Hệ thống hiển thị Ticket và `ticketStatus`.
3. Employee chọn Ticket cần hủy.
4. Hệ thống yêu cầu xác nhận.
5. Employee xác nhận hủy.
6. Ticket được chọn chuyển `VALID → CANCELLED`.
7. ShowtimeSeat tương ứng chuyển `SOLD → AVAILABLE`.
8. Hệ thống ghi nhận việc hủy.

### Dòng sự kiện phụ
- 3.1. Ticket `USED` → không cho hủy.
- 3.2. Ticket `CANCELLED` → không cho hủy lại.
- 3.3. Không còn Ticket VALID → thông báo Booking không còn vé có thể hủy.
- Chính sách hoàn tiền nằm ngoài phạm vi hiện tại.

### Hậu điều kiện
Ticket được chọn `CANCELLED`; ShowtimeSeat tương ứng `AVAILABLE`.

---

# C. ADMIN

## ADM-01 — Quản lý Phim

**Actor chính:** Admin  
**Tóm tắt:** Thêm/sửa/xóa/xem Movie và trạng thái Movie.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở Quản lý Phim.
2. Hệ thống hiển thị danh sách.
3. Admin chọn Thêm/Sửa/Xóa/Đổi trạng thái.
4. Admin nhập hoặc chỉnh thông tin.
5. Hệ thống validate.
6. Admin xác nhận.
7. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Dữ liệu không hợp lệ → báo lỗi.
- Movie đang có Showtime chưa diễn ra → không xóa cứng; chuyển trạng thái nếu phù hợp.

### Hậu điều kiện
Thông tin Movie được cập nhật.

---

## ADM-02 — Quản lý Rạp

**Actor chính:** Admin  
**Tóm tắt:** Quản lý Cinema.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở danh sách Cinema.
2. Chọn Thêm/Sửa/Xóa.
3. Nhập/chỉnh thông tin.
4. Hệ thống validate.
5. Admin xác nhận.
6. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Cinema đang có Room hoạt động/phụ thuộc → chặn xóa cứng.

### Hậu điều kiện
Danh sách Cinema được cập nhật.

---

## ADM-03 — Quản lý Phòng chiếu

**Actor chính:** Admin  
**Tóm tắt:** Quản lý Room thuộc Cinema.  
**Tiền điều kiện:** Admin đã đăng nhập; Cinema tồn tại.

### Dòng sự kiện chính
1. Admin chọn Cinema.
2. Hệ thống hiển thị Room.
3. Admin Thêm/Sửa/Xóa Room.
4. Hệ thống validate.
5. Admin xác nhận.
6. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Room đang có Showtime chưa diễn ra → chặn xóa.

### Hậu điều kiện
Danh sách Room được cập nhật.

---

## ADM-04 — Quản lý Ghế

**Actor chính:** Admin  
**Tóm tắt:** Quản lý Seat vật lý thuộc Room.  
**Tiền điều kiện:** Admin đã đăng nhập; Room tồn tại.

### Dòng sự kiện chính
1. Admin chọn Room.
2. Hệ thống hiển thị Seat.
3. Admin Thêm/Sửa/Xóa Seat.
4. Hệ thống kiểm tra mã/vị trí ghế.
5. Admin xác nhận.
6. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Mã/vị trí ghế trùng → báo lỗi.
- Seat đang có dữ liệu phụ thuộc → không xóa nếu vi phạm ràng buộc.

### Hậu điều kiện
Cấu hình Seat của Room được cập nhật.

> `Seat` chỉ quản lý cấu hình vật lý. Trạng thái `AVAILABLE/HOLD/SOLD` thuộc `ShowtimeSeat`.

---

## ADM-05 — Quản lý Suất chiếu

**Actor chính:** Admin  
**Tóm tắt:** Quản lý Showtime.  
**Tiền điều kiện:** Admin đã đăng nhập; Movie và Room tồn tại.

### Dòng sự kiện chính
1. Admin chọn Movie, Room và thời gian.
2. Admin xác nhận tạo Showtime.
3. Hệ thống kiểm tra conflict thời gian trong Room.
4. Hệ thống tạo Showtime.
5. Hệ thống tự động tạo `ShowtimeSeat` cho toàn bộ Seat của Room.
6. Các ShowtimeSeat mới có trạng thái `AVAILABLE`.
7. Hệ thống thông báo thành công.

### Dòng sự kiện phụ
- 3.1. Trùng khung giờ → cảnh báo conflict → yêu cầu chọn lại.
- Showtime đã có Booking → không xóa/sửa theo cách phá vỡ Booking hiện hữu.
- Hủy Showtime kèm hoàn tiền nằm ngoài phạm vi.

### Hậu điều kiện
Showtime và toàn bộ ShowtimeSeat tương ứng được tạo/cập nhật hợp lệ.

---

## ADM-06 — Quản lý Giá vé

**Actor chính:** Admin  
**Tóm tắt:** Cấu hình giá vé theo loại ghế/loại phòng/khung giờ/ngày.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở danh sách Price Rule.
2. Admin thêm/sửa/xóa rule.
3. Admin nhập điều kiện áp dụng và giá.
4. Hệ thống validate.
5. Hệ thống kiểm tra conflict/overlap.
6. Admin xác nhận.
7. Hệ thống lưu cấu hình.

### Dòng sự kiện phụ
- Giá không hợp lệ → báo lỗi.
- Rule overlap không được phép → báo conflict.
- Rule hết hiệu lực → không áp dụng cho Booking mới.

### Hậu điều kiện
Cấu hình giá vé được cập nhật.

---

## ADM-07 — Quản lý Membership

**Actor chính:** Admin  
**Tóm tắt:** Cấu hình các hạng Membership.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở Membership.
2. Hệ thống hiển thị danh sách tier.
3. Admin thêm/sửa/xóa tier.
4. Admin nhập điều kiện và quyền lợi.
5. Hệ thống validate.
6. Admin xác nhận.
7. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Điều kiện không hợp lệ → báo lỗi.
- Tier đang được sử dụng → không xóa nếu vi phạm ràng buộc.

### Hậu điều kiện
Cấu hình Membership được cập nhật.

---

## ADM-08 — Quản lý Promotion

**Actor chính:** Admin  
**Tóm tắt:** Quản lý Promotion theo mã, mức giảm, điều kiện và thời hạn.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở Promotion.
2. Admin thêm/sửa/xóa Promotion.
3. Admin nhập mã, mức giảm, điều kiện và thời hạn.
4. Hệ thống validate.
5. Admin xác nhận.
6. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Mã Promotion trùng → báo lỗi.
- Thời hạn không hợp lệ → báo lỗi.
- Promotion hết hạn → không áp dụng cho Booking mới.
- Promotion đã áp dụng cho Booking cũ → không thay đổi Booking cũ.

### Hậu điều kiện
Danh sách Promotion được cập nhật.

---

## ADM-09 — Quản lý Food & Beverage

**Actor chính:** Admin  
**Tóm tắt:** Quản lý món F&B, giá và trạng thái bán.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở danh sách F&B.
2. Admin thêm/sửa/xóa món.
3. Admin nhập tên, giá, hình ảnh và trạng thái.
4. Hệ thống validate.
5. Admin xác nhận.
6. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Dữ liệu không hợp lệ → báo lỗi.
- Món không còn bán → chuyển trạng thái ngừng bán thay vì cho thêm vào Booking mới.

### Hậu điều kiện
Danh mục F&B được cập nhật.

---

## ADM-10 — Quản lý Nhân viên

**Actor chính:** Admin  
**Tóm tắt:** Quản lý thông tin và trạng thái Employee.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính
1. Admin mở danh sách Employee.
2. Admin chọn Thêm/Sửa/Vô hiệu hóa.
3. Admin nhập/chỉnh thông tin.
4. Hệ thống validate.
5. Admin xác nhận.
6. Hệ thống cập nhật.

### Dòng sự kiện phụ
- Tài khoản/thông tin định danh bị trùng → báo lỗi.
- Employee bị vô hiệu hóa → không được đăng nhập/sử dụng quyền Employee.

### Hậu điều kiện
Thông tin hoặc trạng thái Employee được cập nhật.

---

# D. SYSTEM

## SYS-01 — Giải phóng Hold hết hạn

**Actor chính:** SystemScheduler  
**Tóm tắt:** Background job định kỳ giải phóng ShowtimeSeat đang HOLD quá hạn.  
**Tiền điều kiện:** Hệ thống Scheduler đang hoạt động.

### Dòng sự kiện chính
1. Scheduler chạy theo chu kỳ.
2. Hệ thống tìm `ShowtimeSeat` thỏa:
   `status = HOLD AND holdExpiresAt < currentTime`.
3. Hệ thống release các ghế tìm được.
4. ShowtimeSeat chuyển `HOLD → AVAILABLE`.
5. Kết thúc chu kỳ.

### Dòng sự kiện phụ
- 2.1. Không có Hold hết hạn → không thực hiện cập nhật.

### Hậu điều kiện
Các ShowtimeSeat hết hạn trở thành `AVAILABLE` và có thể được Hold lại.

> SYS-01 không thay đổi `SOLD` hoặc `AVAILABLE`.

---

# 4. QUAN HỆ INCLUDE / EXTEND ĐÃ CHỐT

| UC chính | Quan hệ | UC được gọi/mở rộng |
|---|---|---|
| CUS-06 Đặt vé online | `include` | CUS-05 Xem suất chiếu |
| CUS-06 Đặt vé online | `include` | CUS-07 Thanh toán online |
| EMP-01 Bán vé tại quầy | `include` | CUS-05 Xem suất chiếu |
| EMP-01 Bán vé tại quầy | `include` | EMP-02 Thanh toán tại quầy |
| EMP-03 Soát vé | `include` | EMP-05 Tra cứu Booking |
| EMP-04 Giao Combo | `include` | EMP-05 Tra cứu Booking |
| EMP-06 Hủy vé | `include` | EMP-05 Tra cứu Booking |

### Các hành vi tùy chọn trong Booking

Các hành vi:

- Áp dụng Membership
- Áp dụng Promotion
- Đặt Combo

được xem là **optional behavior của Booking**, không tính thêm vào 26 UC chính thức nếu nhóm không quyết định nâng chúng thành UC độc lập.

> Không tạo UC riêng cho: Chọn ghế, Hold ghế, Tính tiền, Sinh Ticket, Sinh QR. Đây là các bước/business behavior bên trong CUS-06/EMP-01.

---

# 5. CÁC QUY TẮC NGHIỆP VỤ CỐT LÕI

## Booking / Seat

- Một Booking chỉ thuộc **một Showtime**.
- Một Booking tối đa **8 Ticket/ghế**.
- `Booking` là Aggregate Root.
- `ShowtimeSeat` là trạng thái ghế theo từng Showtime.
- `Seat` là cấu hình vật lý độc lập.
- Không cho hai request cùng bán thành công một ShowtimeSeat.
- Hold sử dụng atomic update.
- Hold mặc định 5 phút.
- `HOLD → AVAILABLE` khi timeout/cancel.
- `HOLD → SOLD` khi thanh toán thành công.

## Ticket / QR

- Một Booking có thể có nhiều Ticket.
- `Booking.qrCodeString` là chuỗi UUID duy nhất.
- QR dùng để lookup Booking.
- `Ticket.ticketStatus` quản lý trạng thái sử dụng.
- `VALID → USED` khi soát vé.
- `VALID → CANCELLED` khi hủy vé.
- `USED` không được hủy.
- Một Booking QR có thể được quét nhiều lần để xử lý các Ticket VALID còn lại.

## Payment

### Online
- MoMo
- VNPay
- Credit Card

### Tại quầy
- Cash
- Credit Card POS

Cash không được sử dụng trong CUS-07.

## Combo

- Booking có thể chứa Combo.
- Giao Combo là UC độc lập với Soát vé.
- Combo có trạng thái giao để ngăn giao trùng.

## Scheduler

- Chỉ release `HOLD` đã hết hạn.
- Không thay đổi `SOLD`.
- Không thay đổi `AVAILABLE`.
- Có thể chạy định kỳ, ví dụ mỗi 30 giây.

---

# 6. STATE LIÊN QUAN TRỰC TIẾP ĐẾN USE CASE

## ShowtimeSeat

```text
AVAILABLE
    |
    | hold()
    v
  HOLD
  /   \
 /     \
timeout  paymentSuccess()
 |          |
 v          v
AVAILABLE  SOLD
              |
              | EMP-06 cancel
              v
          AVAILABLE
```

## Ticket

```text
        +------+
        | VALID |
        +--+---+
           |
     +-----+-----+
     |           |
 verify       cancel
     |           |
     v           v
   USED      CANCELLED
```

---

# 7. KẾT CẤU TÀI LIỆU CHÍNH THỨC

Tài liệu UC của project được tổ chức theo thứ tự:

```text
SYSTEM
 ├── Subsystem 1: Booking & Payment
 │    ├── CUS-05
 │    ├── CUS-06
 │    ├── CUS-07
 │    ├── CUS-08
 │    ├── EMP-01
 │    ├── EMP-02
 │    └── EMP-06
 │
 ├── Subsystem 2: Cinema Operations
 │    ├── EMP-03
 │    ├── EMP-04
 │    └── EMP-05
 │
 ├── Subsystem 3: Customer Account
 │    ├── CUS-01
 │    ├── CUS-02
 │    ├── CUS-03
 │    └── CUS-09
 │
 ├── Subsystem 4: Catalog & Scheduling
 │    ├── CUS-04
 │    └── ADM-01 → ADM-06
 │
 ├── Subsystem 5: Marketing & F&B
 │    ├── ADM-07
 │    ├── ADM-08
 │    └── ADM-09
 │
 ├── Subsystem 6: Staff Administration
 │    └── ADM-10
 │
 └── Subsystem 7: System Background
      └── SYS-01
```

**Tổng: 1 System → 7 Subsystem → 26 UC.**
