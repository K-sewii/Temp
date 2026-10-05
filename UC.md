# ĐẶC TẢ USE CASE — HỆ THỐNG QUẢN LÝ RẠP CHIẾU PHIM

> **Baseline đã chốt**
>
> - 01 System chính: **Hệ thống quản lý rạp chiếu phim**.
> - Chỉ có **02 Actor nghiệp vụ: Customer và Admin**.
> - Tài liệu **bám theo BFD (Sơ đồ phân rã chức năng)** gồm 10 chức năng chính; mục 1.3 có bảng ánh xạ BFD → Use Case.
> - Hệ thống thiết kế theo hướng **online-first**.
> - **Vé điện tử** = `Ticket` kèm `ticketCode`, được tạo sau khi thanh toán thành công. Không dùng QR. **Không quản lý trạng thái `Ticket`** (không có `VALID/USED`); `ticketCode` là mã định danh để tra cứu vé.
> - `Booking.status` được quản lý để xác định trạng thái đặt vé và thanh toán.
> - `ShowtimeSeat` quản lý trạng thái ghế theo từng suất chiếu: `AVAILABLE → HOLD → SOLD`. Hold mặc định **5 phút**.
> - Việc giải phóng Hold hết hạn là xử lý nội bộ của hệ thống, không tạo Actor riêng (không có `SystemScheduler`).
> - Admin quản lý tài khoản của **Customer và Employee** trong cùng UC `ADM-07`; Employee không phải Actor trong sơ đồ UC.
> - BFD **không có chức năng xóa**: dữ liệu danh mục được thêm, sửa, ẩn hoặc đổi trạng thái.
> - Không sử dụng Promotion, Membership, QR hoặc soát vé điện tử trong baseline hiện tại.
>
> **Template UC Specific:** ID · Actor chính/liên quan · Quan hệ · Tóm tắt · Tiền điều kiện · Dòng sự kiện chính · Dòng sự kiện phụ · Hậu điều kiện · Sơ đồ Use Case (PlantUML).

---

# 1. SYSTEM VÀ PHÂN HỆ

## 1.1. System chính

### Hệ thống quản lý rạp chiếu phim

System cung cấp các nghiệp vụ chính:

- Cho phép Customer đăng ký, đăng nhập, xem và cập nhật thông tin cá nhân.
- Cho phép Customer tìm phim, tìm và chọn suất chiếu, chọn ghế, chọn dịch vụ đi kèm và thanh toán trực tuyến.
- Cho phép Customer quản lý vé điện tử và xem lại các đơn đặt vé.
- Cho phép Admin quản lý phim, suất chiếu, phòng chiếu, sơ đồ chỗ ngồi, loại vé, giá vé và Bắp Nước.
- Cho phép Admin quản lý vé bán ra (Booking): xem danh sách toàn bộ đơn và kiểm tra tình trạng thanh toán.
- Cho phép Admin quản lý tài khoản Customer và Employee.
- Cho phép Admin xem báo cáo doanh thu, báo cáo mức độ quan tâm theo phim theo ngày, tuần, tháng.
- Cho phép Admin chỉnh sửa thông số rạp phim và sao lưu dữ liệu.
- Tự động xử lý các Hold ghế hết hạn ở phía hệ thống.

## 1.2. Actor

| Actor | Vai trò |
|---|---|
| Customer | Đăng ký, đăng nhập, quản lý thông tin cá nhân, tìm phim/suất chiếu, đặt vé online, quản lý vé điện tử và xem lịch sử đặt vé |
| Admin | Đăng nhập; quản lý phim, suất chiếu, phòng chiếu, sơ đồ chỗ ngồi, loại vé, giá vé, Bắp Nước, vé bán ra, tài khoản Customer/Employee; xem báo cáo/thống kê; cài đặt hệ thống |

### Tài khoản Employee

Employee **không phải Actor trong sơ đồ UC**. Tài khoản Employee được Admin quản lý thông qua `ADM-07 — Quản lý tài khoản`.

## 1.3. Ánh xạ BFD → Phân hệ → Use Case

| # | Chức năng BFD (phân hệ) | Chức năng con trong BFD | UC |
|---|---|---|---|
| 1 | Đăng nhập và đăng ký | Đăng nhập | CUS-02 |
| | | Đăng ký tài khoản | CUS-01 |
| 2 | Quản lý phim | Cập nhật phim (thêm, sửa, thay đổi trạng thái); xem danh sách; tìm kiếm | ADM-01 |
| 3 | Quản lý suất chiếu | Cập nhật suất chiếu (thêm, sửa, thay đổi trạng thái); tìm kiếm; xem danh sách | ADM-02 |
| 4 | Quản lý phòng chiếu và sơ đồ chỗ ngồi | Cập nhật phòng chiếu (thêm, sửa, ẩn); cập nhật sơ đồ (thiết lập, phân loại ghế, đổi trạng thái ghế, xem); tìm kiếm phòng | ADM-03 |
| 5 | Quản lý loại vé và giá vé | Quản lý giá vé (thiết lập, sửa, tra cứu lịch sử); quản lý loại vé (thêm, sửa, đổi trạng thái, thiết lập đối tượng áp dụng) | ADM-04 |
| 6 | Quản lý đặt vé và dịch vụ | Quản lý thực phẩm (thêm, sửa, đổi trạng thái món, theo dõi tồn kho) | ADM-05 |
| | | Quản lý đơn hàng (xem danh sách toàn bộ đơn; kiểm tra tình trạng thanh toán của khách) | ADM-06 |
| 7 | Chức năng khách hàng | Đặt vé trực tuyến: tìm kiếm và chọn phim; tìm kiếm và chọn suất chiếu | CUS-04 |
| | | Đặt vé trực tuyến: chọn sơ đồ ghế; chọn dịch vụ đi kèm; thanh toán trực tuyến | CUS-05 |
| | | Đặt vé trực tuyến: quản lý vé điện tử | CUS-06 |
| | | Quản lý thông tin cá nhân (xem, cập nhật) | CUS-03 |
| 8 | Quản lý tài khoản | Thêm tài khoản; khóa/mở; chỉnh sửa thông tin; xem chi tiết; khởi tạo mật khẩu; chỉnh sửa chức vụ; tìm kiếm | ADM-07 |
| 9 | Quản lý báo cáo và thống kê | Báo cáo doanh thu; báo cáo mức độ quan tâm theo phim | ADM-08 |
| 10 | Cài đặt hệ thống | Chỉnh sửa thông số rạp phim; sao lưu dữ liệu | ADM-09 |

### Nguyên tắc chia Phân hệ

- Chia theo **domain nghiệp vụ** theo BFD, không chia theo Actor.
- Chỉ có hai Actor nghiệp vụ là **Customer** và **Admin**; Employee chỉ là đối tượng tài khoản do Admin quản lý.
- Các chức năng con dạng "thêm/sửa/đổi trạng thái/xem/tìm kiếm" của BFD được gộp thành **một UC quản lý** theo nhóm, không tách mỗi thao tác thành một UC; các thao tác này xuất hiện làm chức năng con trong sơ đồ Use Case của từng UC (mục 4.2).
- Không tạo UC riêng cho các bước nội bộ như Chọn ghế, Hold ghế, Tính tiền, Sinh `ticketCode`; thanh toán online là một bước của `CUS-05`.
- Không có UC Promotion, Membership, QR hoặc soát vé điện tử.

---

# 2. DANH SÁCH 15 USE CASE

## Customer

| ID | Use Case |
|---|---|
| CUS-01 | Đăng ký tài khoản |
| CUS-02 | Đăng nhập |
| CUS-03 | Quản lý thông tin cá nhân |
| CUS-04 | Xem phim và suất chiếu |
| CUS-05 | Đặt vé online |
| CUS-06 | Quản lý vé điện tử và lịch sử đặt vé |

## Admin

| ID | Use Case |
|---|---|
| ADM-01 | Quản lý phim |
| ADM-02 | Quản lý suất chiếu |
| ADM-03 | Quản lý phòng chiếu và sơ đồ chỗ ngồi |
| ADM-04 | Quản lý loại vé và giá vé |
| ADM-05 | Quản lý thực phẩm |
| ADM-06 | Quản lý đơn hàng |
| ADM-07 | Quản lý tài khoản |
| ADM-08 | Quản lý Báo cáo và Thống kê |
| ADM-09 | Cài đặt hệ thống |

**Tổng: 15 Use Case — 02 Actor.**

---

# 3. ĐẶC TẢ USE CASE

# A. CUSTOMER

## CUS-01 — Đăng ký tài khoản

**Actor chính:** Customer  
**Quan hệ:** —  
**Tóm tắt:** Tạo tài khoản khách hàng để sử dụng các chức năng dành cho khách.  
**Tiền điều kiện:** Customer chưa có tài khoản hợp lệ.

### Dòng sự kiện chính

1. Customer chọn chức năng Đăng ký.
2. Hệ thống yêu cầu thông tin đăng ký.
3. Customer nhập thông tin.
4. Hệ thống kiểm tra dữ liệu và tính duy nhất của thông tin định danh tài khoản.
5. Hệ thống tạo tài khoản Customer.
6. Hệ thống thông báo đăng ký thành công.

### Dòng sự kiện phụ

- 4.1. Thông tin không hợp lệ → hệ thống yêu cầu nhập lại → quay lại bước 3.
- 4.2. Tài khoản/thông tin định danh đã tồn tại → hệ thống thông báo và không tạo tài khoản.

### Hậu điều kiện

- **Thành công:** Tài khoản Customer được tạo.
- **Thất bại:** Không tạo tài khoản.

---

## CUS-02 — Đăng nhập

**Actor chính:** Customer  
**Actor liên quan:** Admin (đăng nhập qua cùng chức năng)  
**Quan hệ:** —  
**Tóm tắt:** Truy cập hệ thống bằng tài khoản đã được tạo.  
**Tiền điều kiện:** Người dùng đã có tài khoản.

### Dòng sự kiện chính

1. Người dùng chọn Đăng nhập.
2. Người dùng nhập thông tin đăng nhập.
3. Hệ thống xác thực thông tin.
4. Hệ thống kiểm tra trạng thái tài khoản.
5. Hệ thống tạo phiên đăng nhập.
6. Hệ thống chuyển người dùng đến các chức năng phù hợp với vai trò (Customer hoặc Admin).

### Dòng sự kiện phụ

- 3.1. Thông tin đăng nhập sai → hệ thống thông báo lỗi → quay lại bước 2.
- 4.1. Tài khoản bị khóa/vô hiệu hóa → hệ thống từ chối đăng nhập → kết thúc.

### Hậu điều kiện

Người dùng có phiên đăng nhập hợp lệ.

---

## CUS-03 — Quản lý thông tin cá nhân

**Actor chính:** Customer  
**Quan hệ:** `<<include>>` Xem thông tin · `<<extend>>` Cập nhật thông tin . `<<extend>>` CUS-06 Quản lý vé điện tử.
**Tóm tắt:** Xem và cập nhật thông tin cá nhân của tài khoản.  
**Tiền điều kiện:** Customer đã đăng nhập.

### Dòng sự kiện chính

1. Customer mở thông tin cá nhân.
2. Hệ thống hiển thị thông tin hiện tại.
3. Customer chọn nhóm thao tác:
   - **Cập nhật thông tin:** thay đổi các trường thông tin cá nhân (tên, số điện thoại, v.v.).
   - **CUS-06 Quản lý vé điện tử:** 
4. Customer nhập hoặc chỉnh sửa thông tin (bỏ qua bước này nếu chỉ xem lịch sử đặt vé).
5. Hệ thống kiểm tra/truy xuất dữ liệu.
6. Hệ thống lưu/hiển thị thông tin mới.
7. Hệ thống thông báo cập nhật thành công(nếu Customer chọn thao tác cập nhật thông tin).

### Dòng sự kiện phụ
- 5.1. Dữ liệu không hợp lệ → hệ thống yêu cầu chỉnh sửa → quay lại bước 3.
- 6.1. Thông tin không thể cập nhật → hệ thống thông báo lỗi.

### Hậu điều kiện

Thông tin cá nhân được cập nhật.
Không thay đổi dữ liệu nghiệp vụ.

---

## CUS-04 — Xem phim và suất chiếu

**Actor chính:** Customer  
**Quan hệ:** `<<include>>` Tìm kiếm và chọn phim; Tìm kiếm và chọn suất chiếu  
**Tóm tắt:** Tìm kiếm và chọn phim, tìm kiếm và chọn suất chiếu phù hợp.  
**Tiền điều kiện:** Không bắt buộc đăng nhập.

### Dòng sự kiện chính

1. Customer truy cập danh sách phim.
2. Hệ thống hiển thị các phim đang hiển thị.
3. Customer tìm kiếm hoặc lọc phim.
4. Customer chọn một phim.
5. Hệ thống hiển thị thông tin chi tiết phim và các suất chiếu đang mở bán.
6. Customer xem thông tin suất chiếu và phòng chiếu.
7. Customer chọn một suất chiếu để tiếp tục đặt vé.

### Dòng sự kiện phụ

- 3.1. Không có phim phù hợp → hệ thống thông báo.
- 5.1. Không có suất chiếu phù hợp → hệ thống thông báo không có suất chiếu.

### Hậu điều kiện

Customer chọn được phim/suất chiếu nếu có dữ liệu phù hợp.


---

## CUS-05 — Đặt vé trực tuyến

**Actor chính:** Customer  
**Quan hệ:** `<<include>>` CUS-04 Xem phim và suất chiếu; Chọn sơ đồ ghế ngồi trực tuyến; Thanh toán trực tuyến · `<<extend>>` Chọn dịch vụ đi kèm  
**Tóm tắt:** Customer chọn suất chiếu, chọn sơ đồ ghế, chọn dịch vụ đi kèm (Bắp Nước), thanh toán trực tuyến và hoàn tất đặt vé.  
**Tiền điều kiện:**
- Customer đã đăng nhập.
- Phim và suất chiếu còn khả dụng (đang mở bán).

### Dòng sự kiện chính

1. Customer chọn phim và suất chiếu.
2. Hệ thống hiển thị sơ đồ ghế và trạng thái ghế theo `ShowtimeSeat`.
3. Customer chọn một hoặc nhiều ghế còn `AVAILABLE`.
4. Hệ thống kiểm tra tính khả dụng, giữ các ghế đã chọn trong 5 phút (`AVAILABLE → HOLD`) và tạo Booking ở trạng thái `PENDING`.
5. Customer có thể chọn dịch vụ đi kèm (Bắp Nước đang bán và còn hàng).
6. Hệ thống tính tổng tiền theo giá vé đang áp dụng và các dịch vụ đã chọn.
7. Customer thực hiện thanh toán trực tuyến.
8. Hệ thống xác nhận thanh toán thành công.
9. Hệ thống chuyển `Booking` sang `PAID`.
10. Hệ thống chuyển các `ShowtimeSeat` đã mua sang `SOLD` (`HOLD → SOLD`).
11. Hệ thống tạo Ticket (vé điện tử) và sinh `ticketCode`.
12. Hệ thống hiển thị thông tin đặt vé và `ticketCode`.

### Dòng sự kiện phụ

- 1.1. Suất chiếu không còn khả dụng → hệ thống không cho tiếp tục đặt vé.
- 3.1. Customer chọn quá số lượng vé cho phép → hệ thống thông báo và yêu cầu điều chỉnh → quay lại bước 3.
- 4.1. Ghế vừa được khách khác giữ/mua → hệ thống thông báo và yêu cầu chọn ghế khác → quay lại bước 3.
- 5.1. Món Bắp Nước đã hết hàng/ngừng bán → hệ thống thông báo và không cho chọn món đó.
- 7.1. Thanh toán thất bại/bị hủy → Booking chuyển `CANCELLED`, không chuyển sang `PAID`; ghế được xử lý theo cơ chế giữ ghế và timeout.
- 7.2. Hết thời gian giữ ghế 5 phút trước khi thanh toán thành công → hệ thống giải phóng ghế về `AVAILABLE`, Booking chuyển `EXPIRED`; đặt vé không được hoàn tất; Customer phải chọn lại ghế.

### Hậu điều kiện

**Thành công:**
- Booking ở trạng thái `PAID`.
- Các ghế đã mua ở trạng thái `SOLD`.
- Ticket được tạo và có `ticketCode`.

**Thất bại:**
- Booking không ở trạng thái `PAID` (`CANCELLED` hoặc `EXPIRED`).
- Các ghế Hold được giải phóng về `AVAILABLE` khi hết thời gian giữ.

> Customer xem lại vé điện tử và `ticketCode` ở `CUS-06`; Admin tra cứu Booking ở `ADM-06`.

## CUS-06 — Quản lý vé điện tử

**Actor chính:** Customer  
**Quan hệ:** `<<include>>` Xem lịch sử đặt vé · `<<extend>>` Xem vé điện tử  
**Tóm tắt:** Xem các đơn đặt vé của tài khoản và quản lý (xem/tra cứu) vé điện tử của các Booking đã thanh toán.  
**Tiền điều kiện:** Customer đã đăng nhập.

### Dòng sự kiện chính

1. Customer mở lịch sử đặt vé / vé điện tử.
2. Hệ thống chỉ lấy các Booking thuộc Customer hiện tại.
3. Hệ thống hiển thị danh sách Booking theo thời gian.
4. Customer chọn một Booking để xem chi tiết.
5. Hệ thống hiển thị chi tiết Booking (phim, suất chiếu, ghế, Bắp Nước, tổng tiền, trạng thái).
6. Nếu Booking đã `PAID`, hệ thống hiển thị vé điện tử gồm Ticket và `ticketCode`.

### Dòng sự kiện phụ

- 2.1. Không có lịch sử đặt vé → hệ thống hiển thị trạng thái không có dữ liệu.
- 6.1. Booking chưa `PAID` → hệ thống không hiển thị vé điện tử.

### Hậu điều kiện

Customer xem được lịch sử và vé điện tử. Không thay đổi dữ liệu nghiệp vụ.

# B. ADMIN

> Tiền điều kiện chung của các UC Admin: Admin đã đăng nhập (qua `CUS-02`) và có quyền quản trị.

## ADM-01 — Quản lý phim

**Actor chính:** Admin  
**Quan hệ:** `<<include>>` Xem danh sách phim · `<<extend>>` Tìm kiếm phim; Cập nhật phim (Thêm phim / Sửa phim / Thay đổi trạng thái phim)  
**Tóm tắt:** Cập nhật phim (thêm, sửa, thay đổi trạng thái), xem danh sách và tìm kiếm phim.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính

1. Admin mở Quản lý phim.
2. Hệ thống hiển thị danh sách phim.
3. Admin tìm kiếm/lọc phim.
4. Admin chọn Thêm phim, Sửa phim hoặc Thay đổi trạng thái phim.
5. Admin nhập hoặc chỉnh sửa thông tin.
6. Hệ thống kiểm tra dữ liệu.
7. Admin xác nhận.
8. Hệ thống lưu thay đổi.

### Dòng sự kiện phụ

- 3.1. Không tìm thấy phim phù hợp → hệ thống thông báo; Admin vẫn có thể thêm phim mới.
- 6.1. Dữ liệu không hợp lệ → hệ thống thông báo lỗi và không lưu.

### Hậu điều kiện

Thông括/trạng thái phim được cập nhật. Phim ở trạng thái không hiển thị/ngừng chiếu sẽ không xuất hiện cho Customer.

## ADM-02 — Quản lý suất chiếu

**Actor chính:** Admin  
**Quan hệ:** `<<include>>` Xem danh sách suất chiếu · `<<extend>>` Tìm kiếm suất chiếu; Cập nhật suất chiếu (Thêm suất chiếu / Sửa suất chiếu / Thay đổi trạng thái suất chiếu)  
**Tóm tắt:** Cập nhật suất chiếu (thêm, sửa, thay đổi trạng thái), tìm kiếm và xem danh sách suất chiếu.  
**Tiền điều kiện:** Admin đã đăng nhập; phim và phòng chiếu phù hợp đã tồn tại.

### Dòng sự kiện chính

1. Admin mở Quản lý suất chiếu.
2. Hệ thống hiển thị danh sách suất chiếu.
3. Admin tìm kiếm/lọc suất chiếu.
4. Admin chọn Thêm suất chiếu, Sửa suất chiếu hoặc Thay đổi trạng thái suất chiếu.
5. Admin chọn phim, phòng chiếu, ngày và giờ chiếu.
6. Hệ thống kiểm tra dữ liệu và xung đột thời gian/phòng.
7. Admin xác nhận.
8. Hệ thống lưu suất chiếu.
9. Khi tạo suất chiếu mới, hệ thống tạo `ShowtimeSeat` tương ứng với các ghế của phòng, trạng thái `AVAILABLE`.

### Dòng sự kiện phụ

- 3.1. Không tìm thấy suất chiếu phù hợp → hệ thống thông báo.
- 4.1. Sửa phim/phòng/giờ chiếu của suất đã phát sinh Booking → hệ thống từ chối; chỉ cho phép thay đổi trạng thái.
- 6.1. Dữ liệu không hợp lệ hoặc trùng lịch phòng → hệ thống từ chối và yêu cầu điều chỉnh.

### Hậu điều kiện

Lịch chiếu và `ShowtimeSeat` được cập nhật hợp lệ.

## ADM-03 — Quản lý phòng chiếu và sơ đồ chỗ ngồi

**Actor chính:** Admin  
**Quan hệ:** `<<extend>>` Tìm kiếm phòng chiếu; Cập nhật phòng chiếu (Thêm phòng chiếu / Sửa phòng chiếu / Ẩn phòng chiếu); Cập nhật sơ đồ chỗ ngồi (Thiết lập sơ đồ chỗ ngồi / Phân loại ghế / Thay đổi trạng thái ghế), gồm `<<include>>` Xem danh sách sơ đồ chỗ ngồi  
**Tóm tắt:** Cập nhật phòng chiếu (thêm, sửa, ẩn), tìm kiếm phòng và cập nhật sơ đồ chỗ ngồi (thiết lập sơ đồ, phân loại ghế, thay đổi trạng thái ghế, xem sơ đồ).  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính

1. Admin mở Quản lý phòng chiếu và sơ đồ chỗ ngồi.
2. Hệ thống hiển thị danh sách phòng chiếu.
3. Admin tìm kiếm phòng chiếu.
4. Admin chọn một trong hai nhóm thao tác:
   - **Phòng chiếu:** Thêm phòng, Sửa phòng hoặc Ẩn phòng chiếu.
   - **Sơ đồ chỗ ngồi:** chọn một phòng; hệ thống hiển thị sơ đồ ghế; Admin thiết lập sơ đồ, phân loại ghế hoặc thay đổi trạng thái ghế.
5. Hệ thống kiểm tra dữ liệu và các ràng buộc (mã/vị trí ghế, tham chiếu từ suất chiếu).
6. Admin xác nhận.
7. Hệ thống lưu thay đổi.

### Dòng sự kiện phụ

- 3.1. Không tìm thấy phòng chiếu phù hợp → hệ thống thông báo.
- 5.1. Mã hoặc vị trí ghế bị trùng/không hợp lệ → hệ thống thông báo lỗi và không lưu.
- 5.2. Phòng/ghế đã được tham chiếu bởi suất chiếu hoặc Booking → không cho thay đổi làm mất tính toàn vẹn dữ liệu; chỉ cho ẩn/đổi trạng thái.

### Hậu điều kiện

Thông tin phòng chiếu và sơ đồ chỗ ngồi được cập nhật.

> `Seat` là cấu hình ghế vật lý (có trạng thái riêng như đang dùng/ngừng dùng). Trạng thái `AVAILABLE/HOLD/SOLD` thuộc `ShowtimeSeat`, theo từng suất chiếu.

## ADM-04 — Quản lý loại vé và giá vé

**Actor chính:** Admin  
**Quan hệ:** `<<extend>>` Quản lý giá vé (Thiết lập giá vé / Sửa giá vé / Tra cứu lịch sử thay đổi giá vé); Quản lý loại vé (Thêm loại vé / Sửa loại vé / Thay đổi trạng thái loại vé / Thiết lập đối tượng áp dụng)  
**Tóm tắt:** Quản lý giá vé (thiết lập, sửa, tra cứu lịch sử thay đổi) và quản lý loại vé (thêm, sửa, thay đổi trạng thái, thiết lập đối tượng áp dụng).  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính

1. Admin mở Quản lý loại vé và giá vé.
2. Hệ thống hiển thị danh sách loại vé và cấu hình giá hiện tại.
3. Admin chọn nhóm thao tác:
   - **Loại vé:** thêm loại vé, sửa loại vé, thay đổi trạng thái loại vé, thiết lập đối tượng áp dụng.
   - **Giá vé:** thiết lập giá vé, sửa giá vé hoặc tra cứu lịch sử thay đổi giá vé.
4. Admin nhập hoặc chỉnh sửa thông tin (bỏ qua bước này nếu chỉ tra cứu lịch sử).
5. Hệ thống kiểm tra dữ liệu và xung đột cấu hình.
6. Admin xác nhận.
7. Hệ thống lưu cấu hình và ghi nhận lịch sử thay đổi giá (nếu có).

### Dòng sự kiện phụ

- 3.1. Admin tra cứu lịch sử thay đổi giá vé → hệ thống hiển thị lịch sử → kết thúc (không thay đổi dữ liệu).
- 5.1. Dữ liệu/giá không hợp lệ → hệ thống thông báo lỗi và không lưu.
- 5.2. Cấu hình bị trùng/xung đột → hệ thống thông báo lỗi và không lưu.

### Hậu điều kiện

Danh mục loại vé và cấu hình giá vé được cập nhật. Booking đã tạo không bị ảnh hưởng bởi thay đổi giá.

## ADM-05 — Quản lý thực phẩm

**Actor chính:** Admin  
**Quan hệ:** `<<extend>>` Thêm món; Sửa món; Thay đổi trạng thái món; Theo dõi số lượng tồn kho  
**Tóm tắt:** Quản lý thực phẩm: thêm món, sửa món, thay đổi trạng thái món và theo dõi số lượng tồn kho.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính

1. Admin mở Quản lý thực phẩm.
2. Hệ thống hiển thị danh sách món và số lượng tồn kho.
3. Admin chọn Thêm món, Sửa món, Thay đổi trạng thái món hoặc Theo dõi/cập nhật tồn kho.
4. Admin nhập thông tin (tên, giá, trạng thái hoặc số lượng tồn).
5. Hệ thống kiểm tra dữ liệu.
6. Admin xác nhận.
7. Hệ thống lưu thay đổi.

### Dòng sự kiện phụ

- 5.1. Dữ liệu không hợp lệ → hệ thống thông báo lỗi và không lưu.
- 7.1. Số lượng tồn kho bằng 0 → hệ thống đánh dấu hết hàng và không cho Customer chọn món đó khi đặt vé.

### Hậu điều kiện

Danh mục, trạng thái và tồn kho Bắp và Nước được cập nhật.

## ADM-06 — Quản lý đơn hàng

**Actor chính:** Admin  
**Quan hệ:** `<<include>>` Xem danh sách toàn bộ đơn · `<<extend>>` Kiểm tra tình trạng thanh toán của khách  
**Tóm tắt:** Xem danh sách toàn bộ đơn (Booking) và kiểm tra tình trạng thanh toán của khách.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính

1. Admin mở Quản lý đơn hàng.
2. Hệ thống hiển thị danh sách toàn bộ Booking.
3. Admin tìm kiếm/lọc Booking theo trạng thái, khách hàng, phim/suất chiếu, ngày đặt hoặc `ticketCode`.
4. Admin chọn một Booking.
5. Hệ thống hiển thị chi tiết: khách hàng, phim, suất chiếu, ghế, Bắp Nước, tổng tiền, trạng thái Booking, tình trạng thanh toán, Ticket và `ticketCode`.
6. Admin kiểm tra tình trạng thanh toán của khách.

### Dòng sự kiện phụ

- 2.1. Chưa có Booking nào → hệ thống hiển thị trạng thái không có dữ liệu.
- 3.1. Không tìm thấy Booking phù hợp → hệ thống thông báo không có kết quả.

### Hậu điều kiện

Admin nắm được thông tin và tình trạng thanh toán của Booking. Không thay đổi dữ liệu nghiệp vụ.


## ADM-07 — Quản lý tài khoản

**Actor chính:** Admin  
**Quan hệ:** `<<extend>>` Thêm tài khoản; Khóa/Mở tài khoản; Chỉnh sửa thông tin; Xem chi tiết tài khoản; Khởi tạo mật khẩu; Chỉnh sửa chức vụ; Tìm kiếm tài khoản  
**Tóm tắt:** Quản lý tài khoản Customer và Employee trong cùng một UC: thêm, khóa/mở, chỉnh sửa thông tin, xem chi tiết, khởi tạo mật khẩu, chỉnh sửa chức vụ, tìm kiếm.  
**Tiền điều kiện:** Admin đã đăng nhập và có quyền quản trị tài khoản.

### Dòng sự kiện chính

1. Admin mở Quản lý tài khoản.
2. Hệ thống hiển thị danh sách tài khoản.
3. Admin tìm kiếm/lọc tài khoản (theo loại Customer/Employee, trạng thái).
4. Admin chọn tài khoản hoặc chọn Thêm tài khoản.
5. Admin thực hiện một thao tác: xem chi tiết; thêm tài khoản; chỉnh sửa thông tin; khóa/mở khóa; khởi tạo lại mật khẩu; chỉnh sửa chức vụ.
6. Hệ thống kiểm tra quyền và dữ liệu.
7. Admin xác nhận.
8. Hệ thống lưu thay đổi.

### Dòng sự kiện phụ

- 3.1. Không tìm thấy tài khoản → hệ thống thông báo.
- 6.1. Thông tin tài khoản bị trùng/không hợp lệ → hệ thống thông báo lỗi và không lưu.
- 6.2. Admin không đủ quyền → hệ thống từ chối thao tác.
- 6.3. Thao tác làm mất Admin cuối cùng của hệ thống (khóa, đổi chức vụ) → hệ thống từ chối.

### Hậu điều kiện

Tài khoản Customer/Employee được thêm hoặc cập nhật thông tin, trạng thái, chức vụ, mật khẩu theo thao tác. Tài khoản bị khóa không thể đăng nhập.

> Không tạo UC riêng cho Quản lý nhân viên; Employee là một loại tài khoản được quản lý trong UC này.

## ADM-08 — Quản lý báo cáo và thống kê

**Actor chính:** Admin  
**Quan hệ:** `<<extend>>` Báo cáo doanh thu; Báo cáo mức độ quan tâm theo phim  
**Tóm tắt:** Xem báo cáo doanh thu và báo cáo mức độ quan tâm theo phim, thống kê theo ngày, tuần, tháng (và khoảng thời gian tùy chọn) để phục vụ điều hành rạp.  
**Tiền điều kiện:** Admin đã đăng nhập.

### Dòng sự kiện chính

1. Admin mở chức năng báo cáo/thống kê.
2. Hệ thống hiển thị tổng quan nhanh của ngày hiện tại (doanh thu, số vé bán).
3. Admin chọn loại báo cáo: **Báo cáo doanh thu** hoặc **Báo cáo mức độ quan tâm theo phim**.
4. Admin chọn kỳ thống kê: theo **ngày**, **tuần**, **tháng** hoặc khoảng thời gian tùy chọn, và chọn mốc thời gian cụ thể.
5. Admin chọn bộ lọc (phim, phòng chiếu, loại vé) nếu cần.
6. Hệ thống kiểm tra tính hợp lệ của kỳ/khoảng thời gian.
7. Hệ thống tổng hợp số liệu từ dữ liệu Booking.
8. Hệ thống hiển thị chỉ số, bảng và biểu đồ, kèm so sánh với kỳ trước.
9. Admin xem chi tiết (đi từ kỳ → ngày → danh sách Booking ở `ADM-06`) hoặc xuất báo cáo nếu chức năng được hỗ trợ.

### Dòng sự kiện phụ

- 6.1. Khoảng thời gian không hợp lệ (ví dụ ngày bắt đầu sau ngày kết thúc) → hệ thống yêu cầu nhập lại → quay lại bước 4.
- 7.1. Không có dữ liệu trong kỳ → hệ thống hiển thị báo cáo rỗng/thông báo không có dữ liệu.

### Hậu điều kiện

Admin có được số liệu thống kê theo loại báo cáo, kỳ và bộ lọc đã chọn. Không thay đổi dữ liệu nghiệp vụ.

### Danh mục chỉ số báo cáo *(đề xuất — chỉnh theo nhu cầu của nhóm)*

**Báo cáo doanh thu**

| Chỉ số | Ý nghĩa / công thức | Dùng để |
|---|---|---|
| Tổng doanh thu | Tổng tiền các Booking `PAID` trong kỳ | Nắm tình hình kinh doanh |
| Doanh thu vé | Phần tiền vé trong Booking `PAID` | Tách nguồn thu |
| Doanh thu Bắp Nước | Phần tiền dịch vụ đi kèm trong Booking `PAID` | Đánh giá doanh thu dịch vụ |
| Số Booking thành công | Số Booking `PAID` | Đo lượng giao dịch |
| Số vé bán | Tổng số Ticket của Booking `PAID` | Đo sản lượng |
| Giá trị trung bình mỗi Booking | Tổng doanh thu ÷ số Booking `PAID` | Đánh giá giá trị đơn |
| Tăng/giảm so với kỳ trước | (Kỳ này − kỳ trước) ÷ kỳ trước | Thấy xu hướng |

Phân tích theo: thời gian (ngày/tuần/tháng — biểu đồ xu hướng), phim, phòng chiếu, loại vé.

**Báo cáo mức độ quan tâm theo phim**

| Chỉ số | Ý nghĩa / công thức | Dùng để |
|---|---|---|
| Số vé bán theo phim | Tổng Ticket của Booking `PAID` theo phim | Đo độ ăn khách |
| Số lượt đặt vé | Số Booking `PAID` theo phim | Đo mức quan tâm |
| Tỷ lệ lấp đầy ghế | Số ghế `SOLD` ÷ tổng ghế của các suất chiếu trong kỳ | Đánh giá hiệu quả suất chiếu |
| Tỷ lệ hoàn tất đặt vé | Booking `PAID` ÷ tổng Booking được tạo | Phát hiện phim/suất có nhiều đơn bị bỏ ngang |
| Xếp hạng phim | Top N phim cao nhất và thấp nhất theo số vé bán | Quyết định tăng/giảm suất chiếu |
| Khung giờ/ngày cao điểm | Số vé bán theo khung giờ chiếu và thứ trong tuần | Bố trí lịch chiếu |

## ADM-09 — Cài đặt hệ thống

**Actor chính:** Admin  
**Quan hệ:** `<<extend>>` Chỉnh sửa thông số rạp phim; Sao lưu dữ liệu  
**Tóm tắt:** Chỉnh sửa thông số rạp phim và sao lưu dữ liệu hệ thống.  
**Tiền điều kiện:** Admin đã đăng nhập và có quyền cấu hình.

### Dòng sự kiện chính

1. Admin mở Cài đặt hệ thống.
2. Hệ thống hiển thị thông số rạp phim hiện tại (ví dụ: thông tin rạp, thời gian giữ ghế, số vé tối đa mỗi lần đặt) và tùy chọn sao lưu.
3. Admin chọn Chỉnh sửa thông số rạp phim hoặc Sao lưu dữ liệu.
4. Nếu chỉnh sửa thông số: Admin cập nhật giá trị, hệ thống kiểm tra, Admin xác nhận và hệ thống lưu.
5. Nếu sao lưu dữ liệu: Admin yêu cầu sao lưu và hệ thống thực hiện sao lưu.
6. Hệ thống thông báo kết quả.

### Dòng sự kiện phụ

- 4.1. Giá trị không hợp lệ → hệ thống từ chối lưu và giữ giá trị cũ.
- 5.1. Sao lưu thất bại → hệ thống thông báo lỗi.

### Hậu điều kiện

Thông số rạp phim được cập nhật và/hoặc bản sao lưu dữ liệu được tạo.

# 4. QUAN HỆ INCLUDE / EXTEND / GENERALIZATION

## 4.1. Sơ đồ tổng quan (mục 7)

Chỉ vẽ 15 UC và một quan hệ giữa các UC:

| UC chính | Quan hệ | UC liên quan |
|---|---|---|
| CUS-05 Đặt vé trực tuyến | `<<include>>` | CUS-04 Xem phim và suất chiếu |

## 4.2. Sơ đồ Use Case của từng UC (mục 3)

Mỗi UC có một sơ đồ Use Case riêng, phân rã UC thành các **chức năng con theo BFD**:

| Quan hệ | Ký hiệu | Ý nghĩa | Ví dụ |
|---|---|---|---|
| Include | `UC ..> con : <<include>>` | Chức năng con luôn được thực hiện khi chạy UC | `ADM-01` include Xem danh sách phim |
| Extend | `con ..> UC : <<extend>>` | Chức năng con tùy chọn, Admin/Customer chọn hoặc xảy ra theo điều kiện | `ADM-01` extend bởi Tìm kiếm phim, Cập nhật phim |
| Generalization | Mũi tên rỗng từ chức năng con đến nhóm cha | Các thao tác cùng một nhóm "Cập nhật…" của BFD | Thêm phim, Sửa phim, Thay đổi trạng thái phim kế thừa Cập nhật phim |

Cú pháp PlantUML: include `UC ..> con : <<include>>`, extend `con ..> UC : <<extend>>`, generalization `con --|> nhóm`.

### Giải thích

- Chức năng con **không có đặc tả riêng**; chúng được mô tả trong dòng sự kiện chính/phụ của UC cha.
- Các bước nội bộ (giữ ghế, tính tiền, tạo Ticket, sinh `ticketCode`) **không vẽ** thành Use Case.
- `CUS-02 Đăng nhập` là điều kiện chung; Admin liên kết với UC này, các UC Admin không vẽ `<<include>>` đến Đăng nhập để tránh rối.

---

# 5. QUY TẮC NGHIỆP VỤ CỐT LÕI

## 5.1. Đặt vé và ghế

- Một Booking thuộc một suất chiếu và được giới hạn số vé theo thông số rạp phim (`ADM-09`).
- `Booking` chỉ được xem là hoàn tất khi thanh toán thành công và chuyển sang `PAID`.
- `Seat` là cấu hình ghế vật lý của phòng; `ShowtimeSeat` là trạng thái ghế theo từng suất chiếu.
- `AVAILABLE → HOLD` khi khách chọn ghế; việc giữ ghế phải đảm bảo nguyên tử (không để hai request cùng giữ/bán một ghế).
- `HOLD` có thời hạn 5 phút.
- `HOLD → SOLD` khi thanh toán thành công; `HOLD → AVAILABLE` khi hết hạn mà chưa thanh toán.

## 5.2. Vé điện tử

- Một Booking có thể có nhiều Ticket; Ticket được tạo sau khi Booking `PAID`.
- Mỗi Ticket có `ticketCode` duy nhất để tra cứu.
- Không dùng trạng thái cho Ticket (không có `VALID/USED`); không sử dụng QR.

## 5.3. Thanh toán

- Thanh toán là một bước trong `CUS-05`; chỉ hỗ trợ thanh toán trực tuyến.
- Booking chỉ chuyển `PAID` khi hệ thống xác nhận thanh toán thành công.

## 5.4. Dữ liệu danh mục

- BFD không có chức năng xóa: Phim, Suất chiếu, Phòng chiếu, Loại vé, món Bắp Nước được thêm, sửa, ẩn hoặc đổi trạng thái.
- Phim/suất chiếu ngừng hiển thị không xuất hiện cho Customer. Món ngừng bán hoặc hết tồn kho không được chọn cho Booking mới.
- Thay đổi giá vé không ảnh hưởng Booking đã tạo; lịch sử thay đổi giá được lưu để tra cứu.

## 5.5. Vé bán ra và Thống kê

- Admin chỉ xem/tra cứu Booking trong `ADM-06`, không chỉnh sửa.
- Chỉ Booking `PAID` được tính vào doanh thu và số vé bán; doanh thu ghi nhận theo ngày thanh toán thành công.
- Tỷ lệ lấp đầy ghế tính theo suất chiếu trong kỳ (theo ngày chiếu); Booking `EXPIRED/CANCELLED` chỉ dùng để tính tỷ lệ hoàn tất đặt vé.
- Tuần thống kê tính từ thứ Hai đến Chủ nhật *(giả định, chỉnh theo nhóm)*.

## 5.6. Tài khoản

- Customer tự đăng ký; Admin thêm/quản lý tài khoản Customer và Employee trong `ADM-07`.
- Tài khoản bị khóa/vô hiệu hóa không đăng nhập được. Employee không phải Actor trong sơ đồ UC.
- Không được làm mất Admin cuối cùng của hệ thống.

## 5.7. Tác vụ nền

- Hệ thống xử lý nền để giải phóng `ShowtimeSeat` đang `HOLD` đã hết hạn; đây là hành vi nội bộ, không phải Actor/UC.
- Tác vụ nền chỉ release `HOLD`, không thay đổi `SOLD` hoặc `AVAILABLE`.

## 5.8. Ngoài phạm vi

- Không có Promotion, Membership, QR hoặc soát vé điện tử.

---

# 6. STATE LIÊN QUAN TRỰC TIẾP

## 6.1. ShowtimeSeat

```text
AVAILABLE
    |
    | hold()
    v
  HOLD
  /    /     timeout  paymentSuccess()
 |          |
 v          v
AVAILABLE  SOLD
```

## 6.2. Booking *(đề xuất — chỉnh theo ERD của nhóm)*

```text
PENDING ──paymentSuccess()──► PAID
   │
   ├──hết Hold──► EXPIRED
   └──thanh toán thất bại/hủy──► CANCELLED
```

- `PENDING`: đã giữ ghế, đang chờ thanh toán.
- `PAID`: đã thanh toán thành công — điều kiện để tạo Ticket, sinh `ticketCode` và tính vào báo cáo.
- `EXPIRED`/`CANCELLED`: không tạo Ticket, không tính doanh thu.

## 6.3. Ticket

- Ticket **không có trạng thái**. Ticket được định danh và tra cứu bằng `ticketCode`.

---

# 7. KẾT CẤU TÀI LIỆU CHÍNH THỨC

```text
SYSTEM
│
├── Actor: Customer
│   ├── CUS-01 Đăng ký tài khoản
│   ├── CUS-02 Đăng nhập (dùng chung với Admin)
│   ├── CUS-03 Quản lý thông tin cá nhân
│   ├── CUS-04 Xem phim và suất chiếu
│   ├── CUS-05 Đặt vé trực tuyến
│   └── CUS-06 Quản lý vé điện tử
│
└── Actor: Admin
    ├── ADM-01 Quản lý phim
    ├── ADM-02 Quản lý suất chiếu
    ├── ADM-03 Quản lý phòng chiếu và sơ đồ chỗ ngồi
    ├── ADM-04 Quản lý loại vé và giá vé
    ├── ADM-05 Quản lý thực phẩm
    ├── ADM-06 Quản lý đơn hàng
    ├── ADM-07 Quản lý tài khoản
    ├── ADM-08 Quản lý báo cáo và thống kê
    └── ADM-09 Cài đặt hệ thống
```

**Tổng: 01 System → 02 Actor → 15 Use Case.**
