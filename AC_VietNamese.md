# Tiêu chí nghiệm thu (Acceptance Criteria - AC) — Hệ thống Quản lý Rạp chiếu phim

> **Lưu ý:** Tài liệu AC này đã được cập nhật và đồng bộ 100% theo Baseline 24 Use Case mới nhất (gồm 8 Phân hệ).

## Quy ước chung (Cần nhớ khi code)

- Một đơn đặt vé (`Booking`) chứa tối đa **6 vé** (`Ticket`).
- Một `Booking` chỉ thuộc về đúng một suất chiếu (`Showtime`). Nó có thể bao gồm nhiều `Ticket` (vé) và `Combo` (đồ ăn/uống).
- Một `Booking` chỉ được áp dụng tối đa **1 mã khuyến mãi** (`Promotion`).
- Trạng thái ghế ngồi của từng suất chiếu sẽ do bảng `ShowtimeSeat` quản lý.
- Bảng ghế vật lý (`Seat`) của phòng chiếu chỉ lưu thông tin cấu hình ghế, **KHÔNG** lưu trạng thái `AVAILABLE/HOLD/SOLD` (Trống/Đang giữ/Đã bán).
- Thời gian giữ ghế (Hold) khi đang đặt vé mặc định là **5 phút** (Có thể cấu hình qua ADM-09).
- Mã `Booking.qrCodeString` là mã QR **duy nhất (unique)** dùng để tra cứu thông tin của một đơn đặt vé.
- Trạng thái vé (đã dùng hay chưa) phải được lưu trên từng vé (`Ticket.ticketStatus`), **tuyệt đối không** dùng chung cờ `Booking.isUsed` cho cả đơn.
- Phải dùng hàm `Booking.calculateTotal()` để tính tổng tiền của một đơn đặt vé.

### Luồng chuyển đổi trạng thái (State) chính

```text
Trạng thái ghế của suất chiếu (ShowtimeSeat):
AVAILABLE (Trống) → HOLD (Đang giữ chỗ) → SOLD (Đã bán)
HOLD → AVAILABLE (Khi hết hạn giữ chỗ hoặc bị hủy)

Trạng thái vé (Ticket):
VALID (Hợp lệ/Chưa dùng) → USED (Đã sử dụng để vào rạp)
VALID → CANCELLED (Bị hủy)
```

---

# A. DÙNG CHUNG (SHARED)

## AUTH-01 — Đăng nhập

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Người dùng (Customer/Employee/Admin) có thể nhập thông tin định danh (Email/SĐT hoặc Mã NV) và Mật khẩu. |
| AC-02 | Hệ thống phải xác thực (authenticate) thông tin với Database trước khi tạo phiên đăng nhập (session/token). |
| AC-03 | Sai tài khoản hoặc mật khẩu -> Đăng nhập thất bại, báo lỗi cụ thể. |
| AC-04 | Đăng nhập thất bại thì không được cấp session hay token hợp lệ nào. |
| AC-05 | Hệ thống phải check xem tài khoản có đang bị khóa (deactivated/banned) hay không. Bị khóa -> Cấm đăng nhập. |
| AC-06 | Thông tin đúng + Tài khoản hợp lệ -> Đăng nhập thành công, trả về session/token. |
| AC-07 | **Điều hướng (Routing):** Dựa vào Role (Vai trò), hệ thống phải cấp quyền và điều hướng đúng: Customer vào Web/App khách, Employee vào POS quầy, Admin vào Dashboard quản trị. |
| AC-08 | Token trả về phải chứa thông tin Role để Client sử dụng cho các API tiếp theo. |

---

# B. CUSTOMER (KHÁCH HÀNG)

## CUS-01 — Đăng ký tài khoản

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng có thể truy cập vào màn hình/chức năng Đăng ký. |
| AC-02 | Hệ thống yêu cầu nhập các thông tin: email hoặc số điện thoại, mật khẩu và họ tên. |
| AC-03 | Dữ liệu đầu vào phải được kiểm tra (validate format) kỹ trước khi tạo tài khoản. |
| AC-04 | Không cho phép tạo tài khoản nếu email hoặc số điện thoại đã tồn tại trong hệ thống. |
| AC-05 | Nếu nhập sai định dạng hoặc thiếu thông tin, hệ thống từ chối và hiển thị lỗi rõ ràng. |
| AC-06 | Dữ liệu hợp lệ sẽ tạo ra đúng 1 tài khoản `Customer` mới. |
| AC-07 | Nếu quá trình tạo bị lỗi giữa chừng, phải rollback DB, tuyệt đối không để lại dữ liệu rác. |
| AC-08 | Hiển thị thông báo đăng ký thành công cho Khách hàng. |

## CUS-02 — Quản lý Thông tin cá nhân

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng chỉ được phép xem Profile của chính mình. |
| AC-02 | Màn hình hiển thị đúng thông tin của user đang đăng nhập lấy từ token. |
| AC-03 | Cho phép user sửa những trường thông tin được phép (VD: Tên, SĐT, không cho sửa ID). |
| AC-04 | Validate dữ liệu trước khi lưu vào database. |
| AC-05 | Dữ liệu sai định dạng -> Báo lỗi, không cập nhật. |
| AC-06 | Dữ liệu chuẩn -> Lưu thành công vào database. |
| AC-07 | Cập nhật xong, load lại trang phải ra thông tin mới nhất. |
| AC-08 | **Bảo mật (IDOR):** User A không thể gọi API truyền ID để sửa Profile của User B. |
| AC-09 | Nếu quá trình lưu bị lỗi mạng/DB, dữ liệu không được cập nhật nửa vời. |

## CUS-03 — Xem phim

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng xem được danh sách phim (`Movie`). |
| AC-02 | Chỉ hiển thị các phim có trạng thái được phép (VD: Đang chiếu, Sắp chiếu). |
| AC-03 | Khách hàng có thể tìm kiếm theo từ khóa hoặc lọc theo thể loại/trạng thái. |
| AC-04 | Khách hàng click chọn được một bộ phim cụ thể để vào trang chi tiết. |
| AC-05 | Nếu không có phim nào khớp, hiển thị màn hình Empty State (Không có dữ liệu). |
| AC-06 | Hành động xem phim là Read-only, không làm thay đổi dữ liệu. |

## CUS-04 — Xem suất chiếu

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng có thể tìm suất chiếu (`Showtime`) từ trang Chi tiết Phim, hoặc chọn Rạp/Ngày. |
| AC-02 | Hệ thống chỉ trả về các suất chiếu match với điều kiện tìm kiếm và đang mở bán. |
| AC-03 | Trên kết quả tìm kiếm phải hiện đủ: Tên Rạp, Tên Phòng chiếu, Giờ chiếu và Giá vé. |
| AC-04 | Nếu suất chiếu đã bị hủy hoặc trôi qua giờ chiếu, không trả về hoặc không cho click. |
| AC-05 | Nếu không có suất chiếu phù hợp -> Báo "Không có suất chiếu". |
| AC-06 | Thao tác xem lịch chiếu không làm thay đổi trạng thái ghế. |

## CUS-05 — Đặt vé online (Aggregate Root)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng chọn một suất chiếu hợp lệ. |
| AC-02 | Hệ thống render sơ đồ ghế (`ShowtimeSeat`) của suất chiếu. |
| AC-03 | **Quy tắc chọn:** Chỉ những ghế màu trắng/trống (`AVAILABLE`) mới cho phép user chọn. |
| AC-04 | **Giới hạn:** Mỗi đơn (`Booking`) chỉ được chọn tối đa **8 ghế/vé**. |
| AC-05 | **Lock ghế (Hold):** Khi ấn "Tiếp tục", hệ thống kiểm tra atomic xem ghế có bị tranh mua không. |
| AC-06 | Giữ ghế thành công -> Trạng thái ghế đổi thành `HOLD` (Đang giữ). |
| AC-07 | Lưu thời điểm hết hạn giữ ghế (`holdExpiresAt`) = Thời gian hiện tại + 5 phút. |
| AC-08 | Nếu 1 trong số các ghế user định giữ đã bị lấy mất, request Hold thất bại (báo lỗi "Ghế đã có người chọn"). |
| AC-09 | Request Hold thất bại phải tự động rollback các ghế đã lỡ khóa trong cùng request đó về `AVAILABLE`. |
| AC-10 | **Concurrency:** Nếu 2 người cùng bấm giữ 1 ghế tại cùng mili-giây, chỉ 1 người thành công. |
| AC-11 | Giữ ghế thành công, chuyển qua bước mua Combo/Bắp Nước (Optional). |
| AC-12 | Bắp nước không bị tính vào giới hạn 8 vé. |
| AC-13 | Hàm `Booking.calculateTotal()` tính toán chính xác: Tiền vé + Tiền Combo - Khuyến mãi. |
| AC-14 | Chuyển user sang CUS-06 để thanh toán. |

## CUS-06 — Thanh toán online

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | UI hiển thị chính xác tổng số tiền phải thanh toán cuối cùng. |
| AC-02 | Khách chỉ được chọn các cổng thanh toán online (MoMo, VNPay, Credit Card). |
| AC-03 | Dev áp dụng `PaymentStrategy` để gọi API cổng thanh toán tương ứng. |
| AC-04 | Số tiền đẩy sang cổng thanh toán phải khớp 100% với tổng tiền `Booking`. |
| AC-05 | Hệ thống phải chờ webhook/callback từ cổng thanh toán báo "Thành công" mới chốt đơn. |
| AC-06 | **Thanh toán thành công:** Chuyển `Booking` sang `PAID`. |
| AC-07 | Các ghế (`ShowtimeSeat`) chính thức chuyển `HOLD` -> `SOLD`. |
| AC-08 | Tạo vé (`Ticket`) tương ứng với số ghế, trạng thái vé `VALID`. |
| AC-09 | Sinh chuỗi mã `qrCodeString` duy nhất cho Booking và hiển thị/gửi email. |
| AC-10 | **Thanh toán lỗi/hủy:** Đơn KHÔNG được `PAID`, ghế KHÔNG được `SOLD`. |
| AC-11 | Thanh toán lỗi mà thời gian Hold vẫn còn -> Khách có thể thanh toán lại. |
| AC-12 | Nếu Hold đã hết hạn (quá 5p) mà webhook trả về thành công -> Báo lỗi/hoàn tiền quy trình ngoại lệ, KHÔNG chốt ghế nếu ghế đã bị người khác mua. |

## CUS-07 — Xem lịch sử đặt vé

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách vào được mục Lịch sử vé. |
| AC-02 | Chỉ gọi API lấy những đơn của đúng user đang login (Chống leo thang đặc quyền). |
| AC-03 | Đơn vé hiển thị theo thứ tự thời gian chuẩn (mới nhất xếp trên). |
| AC-04 | Xem chi tiết hiển thị rõ: Số vé, số ghế, rạp, bắp nước, QR. |
| AC-05 | Hiện rõ trạng thái của đơn: Đã thanh toán, Đã hủy, Vé đã dùng... |
| AC-06 | Nếu chưa từng mua vé nào -> Hiện màn hình Empty State. |

---

# C. EMPLOYEE (NHÂN VIÊN)

## EMP-01 — Bán vé tại quầy

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Nhân viên (NV) chọn Phim/Suất chiếu trên POS. |
| AC-02 | Hiện sơ đồ ghế, chỉ chọn được ghế trắng (`AVAILABLE`). |
| AC-03 | Giới hạn tối đa **8 vé/đơn**. |
| AC-04 | Việc NV bấm chọn ghế áp dụng cơ chế Hold atomic chặt chẽ y như khách mua online. |
| AC-05 | NV và Khách mua online đụng ghế -> Ai API tới trước người đó ăn, người kia báo lỗi. |
| AC-06 | NV có thể thêm Bắp/Nước vào đơn. Hệ thống tính tổng tiền. |
| AC-07 | Đẩy qua EMP-02 để tính tiền. |
| AC-08 | Thu tiền xong -> Booking `PAID`, Ticket `VALID`, Ghế `SOLD`, sinh QR. |
| AC-09 | Lỗi/Khách hủy kèo -> Rollback, nhả ghế về trắng, không sinh vé. |

## EMP-02 — Thanh toán tại quầy

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Hỗ trợ Tiền mặt (Cash) và Quẹt thẻ POS tĩnh (Credit Card POS). Không dùng MoMo/VNPay online. |
| AC-02 | Tiền mặt: NV nhập số tiền khách đưa. Nhập thiếu -> Báo lỗi. Nhập đủ/dư -> Tính tiền thối. |
| AC-03 | Quẹt thẻ: Máy POS quẹt xong, NV bấm "Xác nhận đã quẹt" trên app. |
| AC-04 | Xác nhận xong -> Đơn đổi thành `PAID`. |
| AC-05 | Hủy thanh toán -> Đơn không `PAID`. |

## EMP-03 — Soát vé (Check-in)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV dùng máy quét QR dịch thành `qrCodeString`. |
| AC-02 | Backend tìm `Booking`, không thấy hoặc sai -> Báo lỗi từ chối. |
| AC-03 | Load toàn bộ danh sách vé (`Ticket`) của đơn. |
| AC-04 | Hiển thị trạng thái từng vé: Xanh (VALID - chưa dùng), Xám (USED - đã vào), Đỏ (CANCELLED - đã hủy). |
| AC-05 | NV chỉ được tick chọn các vé `VALID`. Cấm tick vé `USED` hoặc `CANCELLED`. |
| AC-06 | NV có thể check-in lẻ (VD chọn 2 trên 5 vé). |
| AC-07 | Xác nhận -> Các vé được chọn chuyển `VALID` -> `USED`. Các vé chưa chọn giữ nguyên `VALID`. |
| AC-08 | Không dùng `Booking.isUsed`. Mã QR có thể quét nhiều lần để check-in nốt các vé chưa tới. |
| AC-09 | Sai suất chiếu (đi nhầm ngày, nhầm giờ, nhầm rạp) -> Báo lỗi "Sai suất chiếu". |

## EMP-04 — Giao Bắp và Nước

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Quét QR lấy thông tin đơn. Sai QR -> Từ chối. |
| AC-02 | Load list Combo khách đã mua. |
| AC-03 | Hiện trạng thái: Đã giao hay chưa. Đã giao rồi cấm giao lại (xin thêm). |
| AC-04 | Bấm "Xác nhận giao" -> Combo đổi thành `DELIVERED`. |
| AC-05 | Không ảnh hưởng đến trạng thái vé xem phim. Độc lập hoàn toàn với EMP-03. |

## EMP-05 — Tra cứu Đặt vé

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV search theo SĐT, Email, Mã Booking, Mã QR. |
| AC-02 | Backend trả đúng đơn. Có nhiều đơn (theo SĐT) -> Hiện list cho NV chọn. |
| AC-03 | Không tìm thấy -> Báo lỗi nhẹ nhàng. |
| AC-04 | Xem chi tiết đủ Vé, Bắp, QR, Trạng thái. Màn hình chỉ là Read-only. |

## EMP-06 — Hủy vé

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV/Quản lý mở đúng Booking. |
| AC-02 | Chỉ được phép thao tác Hủy đối với vé còn `VALID`. Cấm hủy vé `USED` hoặc `CANCELLED`. |
| AC-03 | NV tick chọn các vé khách xin hủy. |
| AC-04 | Bấm xác nhận -> Trạng thái vé đổi `VALID` -> `CANCELLED`. |
| AC-05 | **Bắt buộc:** Ghế tương ứng trong `ShowtimeSeat` phải đổi từ `SOLD` -> `AVAILABLE` để bán lại. |
| AC-06 | Hủy lẻ vé không làm ảnh hưởng đến các vé không hủy trong cùng Booking. |
| AC-07 | Ghi Log: Ai hủy, hủy lúc nào, lý do. |

---

# D. ADMIN (QUẢN TRỊ HỆ THỐNG)

## ADM-01 — Quản lý Phim

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Thêm/Sửa/Danh sách Phim. |
| AC-02 | Validation: Các trường bắt buộc không để trống. Thời lượng > 0. |
| AC-03 | **Toàn vẹn:** Nếu Phim đã lên lịch chiếu hoặc đã bán vé -> CẤM xóa cứng (hard-delete). |
| AC-04 | Chỉ hỗ trợ xóa mềm (Ẩn/Ngừng chiếu) để không làm crash lịch sử hóa đơn cũ. |

## ADM-02 — Quản lý Phòng chiếu

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin tạo/sửa/xem danh sách Phòng chiếu (Room). |
| AC-02 | Nếu phòng đã xếp lịch chiếu hoặc có vé -> Cấm xóa cứng. |
| AC-03 | Sửa đổi phòng không làm thay đổi lịch sử vé đã bán. |

## ADM-03 — Quản lý Sơ đồ chỗ ngồi

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Thêm/Xóa/Sửa cấu hình ghế (`Seat`) vật lý của phòng chiếu. |
| AC-02 | Mã ghế (VD: A1, B2) không trùng nhau trong 1 phòng. |
| AC-03 | Bảng này chỉ chứa cấu hình, tuyệt đối không chèn cờ trạng thái `HOLD/SOLD` vào đây. |
| AC-04 | Cấm xóa ghế nếu ghế đó từng xuất hiện trong lịch sử vé đã bán. |

## ADM-04 — Quản lý Suất chiếu

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin tạo suất chiếu: Chọn Phim, Phòng chiếu, Thời gian. |
| AC-02 | **Check Conflict:** API phải tính xem khung giờ đó phòng có trống không. Bị đè -> Cấm tạo. |
| AC-03 | **Quan trọng:** Tạo thành công -> Tự động copy ghế từ `Seat` sang `ShowtimeSeat` cho suất này với trạng thái `AVAILABLE`. |
| AC-04 | Update giờ/phòng chỉ được thực hiện nếu chưa có khách nào mua vé. Đã có vé -> Cấm sửa/xóa. |

## ADM-05 — Quản lý Giá vé

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Cấu hình Price Rule (Loại ngày, khung giờ, giá). Giá > 0. |
| AC-02 | Không cho 2 luật giá mâu thuẫn đè lên nhau cùng khung giờ. |
| AC-03 | Luật giá hết hạn không tự áp dụng cho suất chiếu mới. |
| AC-04 | Sửa/Tăng luật giá -> Những hóa đơn cũ đã mua giữ nguyên số tiền, không update hồi tố. |

## ADM-06 — Quản lý Bắp và Nước

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Thêm/Sửa/Danh sách F&B. Giá bán > 0. |
| AC-02 | Ngưng bán món -> Dùng cờ `isActive = false`, cấm xóa cứng nếu món đã có trong hóa đơn. |
| AC-03 | Thay đổi giá bắp không làm đổi giá trong hóa đơn cũ. |

## ADM-07 — Quản lý Tài khoản (NV & Khách)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin xem danh sách NV và Khách hàng. Tìm kiếm theo Tên, Email, SĐT, Trạng thái. |
| AC-02 | Admin tạo/sửa tài khoản Nhân viên, cấp quyền Role (VD: Soát vé, Quầy vé). |
| AC-03 | SĐT/Email không được trùng lặp. |
| AC-04 | Khóa (Deactivate) NV/KH. Người dùng bị khóa lập tức mất quyền đăng nhập, token hiện tại bị phế. |
| AC-05 | Cấm xóa cứng tài khoản nếu đã có lịch sử tương tác (mua vé, thao tác POS) để bảo toàn Log dữ liệu. |

## ADM-08 — Quản lý Báo cáo và Thống kê

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin chọn loại báo cáo: Doanh thu, Số vé bán, Tỷ lệ lấp đầy. |
| AC-02 | Bộ lọc thời gian (Từ ngày - Đến ngày) và cấp độ (Theo phim, Theo suất chiếu). |
| AC-03 | Hệ thống chỉ tính toán số liệu dựa trên các Booking có trạng thái `PAID`. (Bỏ qua Đã hủy, Chờ thanh toán). |
| AC-04 | Nếu không có dữ liệu trong khoảng thời gian chọn -> Hiển thị bảng trống (Empty State), không crash UI. |
| AC-05 | Có chức năng xuất báo cáo (Export Excel/PDF) khớp 100% với dữ liệu đang hiển thị trên màn hình. |

## ADM-09 — Cài đặt hệ thống

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin truy cập màn hình cấu hình tham số vận hành chung. |
| AC-02 | Cập nhật được "Thời gian giữ ghế" (Mặc định 5 phút). Input phải là số nguyên dương > 0. |
| AC-03 | Cập nhật "Thời gian dọn rạp/Buffer giữa 2 suất chiếu" (Dùng cho logic ADM-04 check conflict). |
| AC-04 | Lưu thông số thành công -> Áp dụng ngay lập tức cho các giao dịch và logic tính toán mới sinh ra sau đó. |

---

# E. SYSTEM (TÁC VỤ NỀN)

## SYS-01 — Giải phóng Hold hết hạn

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Hàm chạy ngầm (Cronjob) quết DB định kỳ theo cấu hình hệ thống. |
| AC-02 | Scan `ShowtimeSeat` đang có trạng thái `HOLD`. |
| AC-03 | Nếu thời gian hiện tại > `holdExpiresAt` -> Xác định là hết hạn. |
| AC-04 | Tự động update ghế đó: `HOLD` -> `AVAILABLE`. |
| AC-05 | Ghế chưa hết hạn -> Bỏ qua. Ghế `AVAILABLE` hoặc `SOLD` -> Bỏ qua. |
| AC-06 | Scheduler TUYỆT ĐỐI không tự set ghế thành `SOLD`. |
| AC-07 | **Race condition:** Nếu cronjob đang nhả ghế A mà khách khác bấm mua ghế A, database phải lock transaction tốt để không hỏng data. |
