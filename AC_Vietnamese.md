# Tiêu chí nghiệm thu (Acceptance Criteria - AC) — Hệ thống Quản lý Rạp chiếu phim

## Quy ước chung (Cần nhớ khi code)

- Một đơn đặt vé (`Booking`) chứa tối đa **8 vé** (`Ticket`).
- Một `Booking` chỉ thuộc về đúng một suất chiếu (`Showtime`). Nó có thể bao gồm nhiều `Ticket` (vé) và `Combo` (đồ ăn/uống).
- Một `Booking` chỉ được áp dụng tối đa **1 mã khuyến mãi** (`Promotion`).
- Trạng thái ghế ngồi của từng suất chiếu sẽ do bảng `ShowtimeSeat` quản lý.
- Bảng ghế vật lý (`Seat`) của phòng chiếu chỉ lưu thông tin cấu hình ghế, **KHÔNG** lưu trạng thái `AVAILABLE/HOLD/SOLD` (Trống/Đang giữ/Đã bán).
- Thời gian giữ ghế (Hold) khi đang đặt vé mặc định là **5 phút**.
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

# 1. Customer (Khách hàng)

## CUS-01 — Đăng ký tài khoản

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng có thể truy cập vào màn hình/chức năng Đăng ký. |
| AC-02 | Hệ thống yêu cầu nhập các thông tin: email hoặc số điện thoại, mật khẩu và họ tên (theo đúng Use Case). |
| AC-03 | Dữ liệu đầu vào phải được kiểm tra (validate) kỹ trước khi tạo tài khoản. |
| AC-04 | Không cho phép tạo tài khoản nếu email hoặc số điện thoại đã tồn tại trong hệ thống. |
| AC-05 | Nếu nhập sai định dạng hoặc thiếu thông tin, hệ thống phải từ chối và hiển thị thông báo lỗi rõ ràng. |
| AC-06 | Dữ liệu hợp lệ sẽ tạo ra đúng 1 tài khoản `Customer` mới. |
| AC-07 | Khi tạo `Customer` thành công, hệ thống phải tự động cấp hạng thành viên (`Membership`) mặc định. |
| AC-08 | Tài khoản mới tạo phải được liên kết đúng với cái `Membership` mặc định đó. |
| AC-09 | Nếu quá trình tạo bị lỗi giữa chừng (fail), phải rollback, tuyệt đối không để lại dữ liệu rác (tài khoản không có membership...). |
| AC-10 | Hiển thị thông báo đăng ký thành công cho Khách hàng. |

## CUS-02 — Đăng nhập

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng có thể nhập thông tin để đăng nhập. |
| AC-02 | Hệ thống phải xác thực (authenticate) thông tin trước khi tạo phiên đăng nhập (session/token). |
| AC-03 | Sai tài khoản hoặc mật khẩu -> Đăng nhập thất bại. |
| AC-04 | Đăng nhập thất bại thì không được cấp session hay token hợp lệ nào. |
| AC-05 | Hệ thống phải check xem tài khoản có đang bị khóa hay không. |
| AC-06 | Tài khoản đang bị khóa (banned/inactive) thì không cho đăng nhập. |
| AC-07 | Thông tin đúng + Tài khoản không bị khóa -> Đăng nhập thành công. |
| AC-08 | Đăng nhập thành công trả về session/token hợp lệ cho Client. |
| AC-09 | Khách hàng dùng token này để gọi được các API yêu cầu quyền đăng nhập. |

## CUS-03 — Quản lý Profile (Hồ sơ cá nhân)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng chỉ được phép xem Profile của chính mình. |
| AC-02 | Màn hình hiển thị đúng thông tin của user đang đăng nhập. |
| AC-03 | Cho phép user sửa những trường thông tin được phép (ví dụ: Tên, SDT, không cho sửa ID...). |
| AC-04 | Validate dữ liệu trước khi lưu vào database. |
| AC-05 | Dữ liệu sai định dạng -> Báo lỗi, không cập nhật. |
| AC-06 | Dữ liệu chuẩn -> Lưu thành công vào database. |
| AC-07 | Cập nhật xong, load lại trang phải ra thông tin mới nhất. |
| AC-08 | **Bảo mật:** User A không thể gọi API để sửa Profile của User B. |
| AC-09 | Nếu quá trình lưu bị lỗi (ví dụ rớt mạng DB), không được cập nhật nửa vời (chỉ sửa tên mà không sửa ảnh...). |

## CUS-04 — Xem danh sách phim

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng xem được danh sách phim (`Movie`). |
| AC-02 | Chỉ hiển thị các phim có trạng thái được phép hiển thị (VD: Đang chiếu, Sắp chiếu). |
| AC-03 | Khách hàng click chọn được một bộ phim cụ thể. |
| AC-04 | Vào trang chi tiết, hiển thị đúng các thông tin của phim đó (Tên, mô tả, trailer...). |
| AC-05 | Nếu không có phim nào, phải hiển thị màn hình Empty State (Không có dữ liệu). |
| AC-06 | Hành động xem phim chỉ là thao tác đọc (Read-only), không làm thay đổi bất kỳ dữ liệu nào. |

## CUS-05 — Xem lịch chiếu (Suất chiếu)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách hàng có thể tìm suất chiếu (`Showtime`) từ trang Chi tiết Phim. |
| AC-02 | Có thể tìm suất chiếu thông qua bộ lọc: Cụm rạp (Cinema) và Ngày chiếu (Date). |
| AC-03 | Hệ thống chỉ trả về các suất chiếu match với bộ lọc. |
| AC-04 | Trên kết quả tìm kiếm phải hiện đủ: Tên Rạp, Tên Phòng chiếu, Giờ chiếu và Giá vé cơ bản. |
| AC-05 | Khách hàng click chọn 1 suất chiếu để tiến hành đặt vé. |
| AC-06 | Nếu suất chiếu đã bị hủy hoặc trôi qua giờ chiếu, không cho phép bấm đặt vé. |
| AC-07 | User có thể vào thẳng URL suất chiếu mà không cần phải qua bước Xem Phim (CUS-04). |
| AC-08 | Thao tác xem lịch chiếu không làm thay đổi bất kỳ trạng thái ghế nào. |

## CUS-06 — Đặt vé online (Flow cực kỳ quan trọng)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Phải chọn một suất chiếu hợp lệ (chưa chiếu, chưa bị hủy). |
| AC-02 | Hệ thống render ra sơ đồ ghế (`ShowtimeSeat`) của suất chiếu đó. |
| AC-03 | **Quy tắc chọn:** Chỉ những ghế màu trắng/trống (`AVAILABLE`) mới cho phép user click chọn. |
| AC-04 | **Giới hạn:** Mỗi đơn (`Booking`) chỉ được chọn tối đa 8 ghế/vé. |
| AC-05 | **Lock ghế (Hold):** Khi ấn "Tiếp tục", hệ thống phải kiểm tra xem các ghế đang chọn có bị người khác lấy mất chưa (Real-time check). |
| AC-06 | Giữ ghế thành công -> Trạng thái ghế đổi thành `HOLD` (Đang giữ). |
| AC-07 | Phải lưu lại thời điểm hết hạn giữ ghế (`holdExpiresAt`) vào DB/Redis. |
| AC-08 | Thời gian hết hạn đếm ngược là **5 phút**. |
| AC-09 | Nếu 1 trong số các ghế user định giữ đã bị mua mất, toàn bộ request giữ chỗ đó sẽ thất bại (báo lỗi "Ghế đã có người chọn"). |
| AC-10 | Khi request Hold thất bại, phải tự động nhả (rollback) lại các ghế đã lỡ khóa trong cùng request đó về `AVAILABLE`. |
| AC-11 | **Concurrency (Xử lý đồng thời):** Nếu 2 người cùng bấm giữ 1 ghế tại cùng 1 mili-giây, hệ thống chỉ cho 1 người thành công, người kia phải nhận thông báo lỗi. |
| AC-12 | Giữ ghế thành công, chuyển user sang bước mua bắp nước / chọn khuyến mãi. |
| AC-13 | Nếu là thành viên VIP/Gold, hệ thống tự động hiển thị/áp dụng giá ưu đãi. |
| AC-14 | Chỉ được nhập tối đa 1 mã `Promotion` cho 1 đơn. |
| AC-15 | Mã khuyến mãi phải được validate: còn hạn, chưa hết lượt và đủ điều kiện áp dụng cho đơn này. |
| AC-16 | Cho phép user mua thêm Bắp Nước (`Combo`) đang có sẵn. |
| AC-17 | Combo thoải mái, không bị tính vào giới hạn tối đa 8 vé. |
| AC-18 | Hàm `Booking.calculateTotal()` phải tính toán chính xác số tiền: [Tiền vé + Tiền Combo - Khuyến mãi]. |
| AC-19 | Đẩy user sang bước CUS-07 để Thanh toán online. |
| AC-20 | **Thanh toán XONG:** Chuyển trạng thái Booking sang `PAID` (Đã thanh toán). |
| AC-21 | Ghế (`ShowtimeSeat`) chính thức chuyển từ `HOLD` -> `SOLD` (Đã bán - hiện màu đỏ trên map). |
| AC-22 | Hệ thống sinh ra các bản ghi Vé (`Ticket`) tương ứng với số ghế đã mua. |
| AC-23 | Vé mới tạo có trạng thái là `VALID` (Hợp lệ). |
| AC-24 | Đặt 5 ghế thì sinh ra đúng 5 Ticket. |
| AC-25 | Tạo ra 1 chuỗi mã `qrCodeString` duy nhất cho toàn bộ Booking này. |
| AC-26 | Mã QR này không được trùng lặp với bất kỳ Booking nào khác. |
| AC-27 | Việc soát vé (vào rạp) quản lý theo từng `Ticket`, chứ không đổi trạng thái của cả `Booking`. |
| AC-28 | **Thanh toán LỖI:** Không được chuyển đơn sang `PAID`. |
| AC-29 | Thanh toán lỗi thì ghế KHÔNG được chuyển thành `SOLD`. |
| AC-30 | Nếu thanh toán lỗi mà thời gian 5 phút vẫn còn, thì ghế đó vẫn ở trạng thái `HOLD` cho user đó làm lại. |
| AC-31 | Nếu 5 phút đã hết mà user mới thanh toán, hệ thống **bắt buộc phải từ chối giao dịch**. |
| AC-32 | Quá 5 phút, tác vụ nền (SYS-01) sẽ tự động nhả ghế từ `HOLD` về lại `AVAILABLE`. |
| AC-33 | **End-goal (Thành công mỹ mãn):** Booking=`PAID`, Ticket=`VALID`, Ghế=`SOLD`, sinh mã QR. |

## CUS-07 — Thanh toán trực tuyến (Payment)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | UI hiển thị chính xác tổng số tiền phải thanh toán cuối cùng. |
| AC-02 | Khách chỉ được chọn các cổng thanh toán online đang mở. |
| AC-03 | Hỗ trợ: MoMo, VNPay, Thẻ tín dụng. |
| AC-04 | Dev áp dụng đúng pattern `PaymentStrategy` trong code để gọi API bên thứ 3 tương ứng. |
| AC-05 | Payload gửi sang cổng thanh toán phải đính kèm đúng ID của `Booking`. |
| AC-06 | Số tiền đẩy sang cổng thanh toán phải khớp 100% với tổng tiền `Booking`. |
| AC-07 | Hệ thống phải chờ webhook/callback từ cổng thanh toán báo "Thành công" thì mới tính là giao dịch hoàn tất (chứ không tin client). |
| AC-08 | Nhận kết quả thành công -> Update đơn thành `PAID`. |
| AC-09 | Các vé (Ticket) được xác nhận là `VALID`. |
| AC-10 | Chốt cứng ghế thành `SOLD`. |
| AC-11 | Gửi email/thông báo kèm mã QR cho khách. |
| AC-12 | Cổng thanh toán báo lỗi (Thiếu tiền, rớt mạng...) -> Đơn không được `PAID`. |
| AC-13 | Khách bấm "Hủy giao dịch" trên MoMo/VNPay -> Đơn về trạng thái hủy, không `PAID`. |
| AC-14 | Nhắc lại: Nếu khách ngâm trang thanh toán quá thời gian Hold ghế, khi webhook trả về thành công thì phải hoàn tiền hoặc báo lỗi, chứ KHÔNG được chốt đơn. |

## CUS-08 — Xem lịch sử đặt vé

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách vào được mục Lịch sử vé. |
| AC-02 | Chỉ gọi API lấy những đơn của đúng user đang login. |
| AC-03 | Chống leo thang đặc quyền: Không truyền ID ảo để xem vé người khác. |
| AC-04 | Đơn vé hiển thị theo thứ tự thời gian chuẩn (mới nhất xếp trên). |
| AC-05 | Bấm vào 1 đơn để xem chi tiết. |
| AC-06 | Trong chi tiết hiển thị rõ từng vé (`Ticket`) ghế nào, rạp nào. |
| AC-07 | Hiển thị cả các món bắp nước đã mua (nếu có). |
| AC-08 | Hiển thị mã QR to rõ ràng để đi quét tại rạp (nếu đơn đã PAID). |
| AC-09 | Hiện rõ trạng thái của đơn: Đã thanh toán, Đã hủy, Vé đã dùng... |
| AC-10 | Nếu chưa từng mua vé nào -> Hiện màn hình "Bạn chưa có giao dịch nào". |

## CUS-09 — Quản lý thẻ thành viên (Membership)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Khách xem được thẻ thành viên của mình. |
| AC-02 | Hiển thị đúng Hạng (Tier): Standard, VIP, VVIP... |
| AC-03 | Hiển thị số điểm tích lũy hiện tại. |
| AC-04 | Hiển thị danh sách các quyền lợi của Hạng đó (VD: giảm 10% bắp nước). |
| AC-05 | Xem được lịch sử lên/xuống hạng hoặc lịch sử cộng/trừ điểm. |
| AC-06 | Bảo mật: Không xem hoặc hack sửa điểm của user khác. |
| AC-07 | Thao tác xem này là Read-only, không sửa đổi dữ liệu DB. |

---

# 2. Employee (Nhân viên tại rạp)

## EMP-01 — Bán vé trực tiếp tại quầy

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Nhân viên (NV) chọn Phim và Suất chiếu trên máy POS theo yêu cầu của khách. |
| AC-02 | Hiện sơ đồ ghế của suất chiếu. |
| AC-03 | Chỉ chọn được ghế trắng (`AVAILABLE`). |
| AC-04 | Giới hạn 1 lần bán tại quầy tối đa 8 vé. |
| AC-05 | Việc nhân viên bấm chọn ghế cũng phải áp dụng cơ chế Giữ ghế (Hold) chặt chẽ (để tránh đụng với khách đang mua online). |
| AC-06 | **Concurrency:** NV và Khách mua online bấm cùng lúc -> Chỉ 1 người ăn, người kia báo lỗi đổi ghế. |
| AC-07 | NV có thể upsell, add thêm Bắp/Nước vào đơn. |
| AC-08 | App của NV tự cộng tiền chính xác. |
| AC-09 | Đẩy qua chức năng EMP-02 để tính tiền. |
| AC-10 | Thu tiền xong bấm "Xác nhận" -> Booking đổi thành `PAID`. |
| AC-11 | Tạo các Ticket trạng thái `VALID`. |
| AC-12 | Ghế chuyển từ Đang giữ (`HOLD`) sang Đã bán (`SOLD`). |
| AC-13 | Sinh mã QR và In biên lai cho khách. |
| AC-14 | Khách cầm biên lai có QR này để soát vé bình thường như mua online. |
| AC-15 | Nếu khách đổi ý không mua/thẻ quẹt lỗi -> Hủy lệnh, nhả ghế về trắng, không sinh vé. |
| AC-16 | Nếu trong lúc thao tác bị lỗi đồng bộ ghế, phải tự động nhả (rollback) toàn bộ các ghế đang giữ của khách này. |

## EMP-02 — Thanh toán tại quầy

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV nhìn thấy tổng bill cần thu. |
| AC-02 | Chấp nhận hình thức thu Tiền mặt (Cash). |
| AC-03 | Chấp nhận hình thức quẹt thẻ máy POS (Credit Card POS). |
| AC-04 | Trả bằng tiền mặt: App NV có chỗ nhập số tiền khách đưa. |
| AC-05 | Nếu nhập khách đưa ít hơn tổng bill -> App báo đỏ, không cho qua. |
| AC-06 | Đưa dư -> App tính ra số tiền cần thối lại (tiền thừa). |
| AC-07 | Quẹt thẻ: NV thao tác quẹt, khi nào máy POS in bill thành công thì NV mới bấm "Xác nhận đã quẹt" trên app. |
| AC-08 | Chốt thanh toán xong -> Đơn đổi thành `PAID`. |
| AC-09 | Hủy thanh toán -> Đơn không `PAID`. |
| AC-10 | App NV không hiển thị các cổng MoMo/VNPay online (vì rạp thu bằng máy POS tĩnh hoặc tiền mặt). |

## EMP-03 — Soát vé vào rạp (Check-in)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV dùng máy quét QR của khách (từ điện thoại hoặc giấy). |
| AC-02 | App dịch được mã QR thành `qrCodeString`. |
| AC-03 | Backend tra cứu `Booking` bằng chuỗi QR này. |
| AC-04 | Quét QR tự chế/hết hạn/sai -> Báo lỗi "Mã không hợp lệ", màn hình nháy đỏ. |
| AC-05 | Quét đúng -> Hiện thông tin đơn vé. |
| AC-06 | Load toàn bộ danh sách ghế (`Ticket`) của đơn này lên màn hình NV. |
| AC-07 | Hiển thị rõ trạng thái từng vé: Vé nào xanh (chưa dùng), vé nào xám (đã vào rạp). |
| AC-08 | Khách đi đủ người: NV bấm "Check-in tất cả" các vé `VALID`. |
| AC-09 | Khách đi lẻ (ví dụ 3 người đến trước 2 người đến sau): NV chỉ tick chọn check-in 3 vé của 3 người đến trước. |
| AC-10 | App chỉ cho phép tick chọn các vé đang `VALID`. |
| AC-11 | Vé `USED` (đã vào rồi) -> Không thể check-in lại (Chống vé lậu). |
| AC-12 | Vé `CANCELLED` (bị hủy) -> Không cho check-in. |
| AC-13 | Các vé được NV tick chọn sẽ chuyển trạng thái `VALID` -> `USED`. |
| AC-14 | Các vé chưa đến (không được tick) vẫn giữ nguyên trạng thái `VALID`. |
| AC-15 | VD: Đơn 5 vé, check-in 2 vé -> Trong DB có 2 `USED` và 3 `VALID`. |
| AC-16 | Lát sau 3 người kia tới, đưa mã QR cũ quét lại -> App hiện 3 vé `VALID` còn lại cho NV check-in nốt. |
| AC-17 | **Nhắc lại:** Tuyệt đối không dùng biến `Booking.isUsed` để đánh dấu cho toàn bộ đơn, check-in phải đếm trên từng đầu vé. |
| AC-18 | Nếu đơn vé chưa thanh toán (hack/lỗi) -> Cấm check-in. |
| AC-19 | Nếu khách đi nhầm rạp, sai ngày, sai giờ chiếu -> App báo lỗi to đùng "Sai suất chiếu". |
| AC-20 | Bấm Check-in xong, màn hình cập nhật real-time các vé đó thành "Đã sử dụng". |

## EMP-04 — Giao Bắp Nước (Combo)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Tại quầy bắp nước, NV cũng quét mã QR để lấy thông tin. |
| AC-02 | Mã QR sai -> Từ chối. |
| AC-03 | Load danh sách Combo khách đã mua trong đơn. |
| AC-04 | Hiển thị trạng thái: Đã giao hay chưa giao. |
| AC-05 | Không hiển thị lộn Combo của đơn khác. |
| AC-06 | Nếu Combo ghi là "Đã giao" -> Khách đang xin lại lần 2 -> NV từ chối. |
| AC-07 | NV giao xong bấm nút "Xác nhận giao" -> Combo đó đổi thành `DELIVERED` (Đã giao). |
| AC-08 | Thao tác giao bắp nước hoàn toàn không ảnh hưởng gì đến trạng thái soát vé (`ticketStatus`) của khách. |
| AC-09 | Module Bắp nước (EMP-04) và Soát vé (EMP-03) hoạt động hoàn toàn độc lập, khách làm cái nào trước cũng được. |

## EMP-05 — Tìm kiếm & Tra cứu đơn vé

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV có thanh search: tìm theo SĐT khách, Mã QR, Mã Đơn, Email... |
| AC-02 | Backend trả về đúng đơn cần tìm. |
| AC-03 | Không ra kết quả -> Báo "Không tìm thấy". |
| AC-04 | Nếu search theo SĐT ra 3 đơn của khách đó -> Hiện list cho NV hỏi khách đang muốn xử lý đơn nào. |
| AC-05 | Bấm vào xem chi tiết phải có đủ: Vé, Bắp, Ngày chiếu, QR và Trạng thái thanh toán. |
| AC-06 | Màn hình tra cứu chỉ là chế độ Xem (Read-only). |
| AC-07 | Thao tác tìm kiếm không tự động làm thay đổi bất cứ trạng thái nào của DB. |

## EMP-06 — Hủy vé (Dành cho Quản lý / NV có quyền)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | NV mở đúng mã Booking cần xử lý. |
| AC-02 | Hiển thị list các vé và trạng thái từng vé. |
| AC-03 | Chỉ được phép thao tác Hủy đối với những vé còn mới (`VALID`). |
| AC-04 | Khách đã vào rạp, vé thành `USED` -> Cấm hủy. |
| AC-05 | Vé đã `CANCELLED` trước đó -> Cấm hủy lại. |
| AC-06 | NV tick chọn những vé khách muốn hoàn/hủy. |
| AC-07 | Bấm "Xác nhận hủy" -> DB đổi trạng thái các vé đó từ `VALID` -> `CANCELLED`. |
| AC-08 | **Quan trọng:** Ghế tương ứng với các vé này trong `ShowtimeSeat` phải đổi từ `SOLD` (đã bán) về lại `AVAILABLE` (ghế trống) để người khác mua được. |
| AC-09 | Đơn có 3 vé, khách chỉ xin hủy 1 vé, thì 2 vé kia vẫn giữ bình thường, không hủy lây. |
| AC-10 | Lưu lịch sử "Ai hủy, hủy lúc nào, lý do" vào Log. |
| AC-11 | *(Về luồng hoàn tiền - Refund, team làm theo spec riêng nếu có yêu cầu, tạm thời AC này không đề cập chi tiết cách hoàn tiền).* |

---

# 3. Admin (Quản trị hệ thống)

## ADM-01 — Quản lý Phim (Movie)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin thêm phim mới bằng form nhập liệu. |
| AC-02 | Các trường bắt buộc (Tên phim, Độ dài, Thể loại...) không được để trống. |
| AC-03 | Admin xem được list phim. |
| AC-04 | Bấm vào xem chi tiết phim. |
| AC-05 | Admin có quyền sửa thông tin phim. |
| AC-06 | Nếu sửa thành dữ liệu sai (VD: thời lượng -10 phút) -> Báo lỗi. |
| AC-07 | **Toàn vẹn dữ liệu:** Không được cho phép xóa cứng (hard-delete) một bộ phim nếu phim đó ĐÃ CÓ suất chiếu hoặc ĐÃ CÓ người mua vé. (Xóa đi hệ thống sẽ bị lỗi khóa ngoại - crash). |
| AC-08 | Thay vì xóa cứng, hệ thống nên hiển thị cảnh báo, hoặc cung cấp chức năng Xóa mềm (chuyển trạng thái phim thành Ẩn/Ngừng chiếu). |
| AC-09 | Việc update thông tin phim không được làm sai lệch lịch sử hiển thị của những vé cũ đã bán. |

## ADM-02 — Quản lý Cụm Rạp (Cinema)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin tạo Rạp chiếu mới (VD: CGV Landmark). |
| AC-02 | Điền đủ tên, địa chỉ... hợp lệ. |
| AC-03 | Xem list Rạp. |
| AC-04 | Xem chi tiết Rạp. |
| AC-05 | Cập nhật thông tin Rạp. |
| AC-06 | Tương tự phim, nếu Rạp đã có phòng chiếu, có doanh thu -> KHÔNG ĐƯỢC xóa cứng (hard-delete). |
| AC-07 | Việc sửa đổi Rạp không được làm bay màu lịch sử hóa đơn cũ. |

## ADM-03 — Quản lý Phòng chiếu (Room)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin thêm Phòng chiếu mới (VD: Rạp 1) vào trong một Cụm rạp đã có sẵn. |
| AC-02 | Thông tin Phòng hợp lệ (số lượng hàng ghế...). |
| AC-03 | Xem danh sách Phòng. |
| AC-04 | Xem chi tiết Phòng. |
| AC-05 | Sửa Phòng. |
| AC-06 | Nếu phòng đã xếp lịch chiếu hoặc có người mua vé -> Cấm xóa cứng. |
| AC-07 | Dữ liệu Phòng phải luôn map đúng với ID của Rạp chứa nó. |

## ADM-04 — Quản lý Cấu hình Ghế (Seat)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin thêm/xóa/sửa Ghế vật lý trong một Phòng chiếu. |
| AC-02 | Mã ghế (VD: A1, B2) không được trùng nhau trong cùng 1 phòng. |
| AC-03 | Tọa độ (Vị trí hàng/cột) phải hợp lệ với ma trận phòng. |
| AC-04 | Xem danh sách sơ đồ ghế. |
| AC-05 | Admin có thể cập nhật loại ghế (Ghế thường, VIP, Sweetbox...). |
| AC-06 | **Lưu ý Code:** DB bảng `Seat` này chỉ quản lý Cấu hình ghế, tuyệt đối không chèn cờ `AVAILABLE/HOLD` vào bảng này. |
| AC-07 | Trạng thái ghế cho người dùng mua là thuộc bảng `ShowtimeSeat` riêng biệt. |
| AC-08 | Không cho phép Admin xóa ghế A1 nếu ghế A1 đó đã từng được bán trong bất kỳ lịch sử suất chiếu nào (sẽ làm hỏng vé cũ của khách). |

## ADM-05 — Lên lịch Suất chiếu (Showtime)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin chọn 1 phim. |
| AC-02 | Chọn 1 phòng chiếu. |
| AC-03 | Nhập thời gian bắt đầu và kết thúc suất chiếu. |
| AC-04 | **Check đụng độ (Conflict):** Backend phải tính toán xem giờ này phòng chiếu đó có đang chiếu phim nào khác không. |
| AC-05 | Nếu giờ bị đè lên nhau (overlap) -> Cấm tạo, báo lỗi. |
| AC-06 | Nếu khung giờ trống rảnh -> Tạo thành công. |
| AC-07 | **Quan trọng:** Ngay khi tạo thành công `Showtime`, hệ thống tự động copy toàn bộ cấu hình ghế từ `Seat` sang bảng `ShowtimeSeat` cho suất chiếu đó. |
| AC-08 | Toàn bộ ghế mới sinh ra phải có trạng thái mặc định là Trống (`AVAILABLE`). |
| AC-09 | Mỗi ghế `ShowtimeSeat` phải ánh xạ đúng ID với ghế vật lý `Seat`. |
| AC-10 | Nếu tạo gặp lỗi giữa chừng, phải rollback, không để lại mớ ghế `ShowtimeSeat` rác trong DB. |
| AC-11 | Xem lịch chiếu. |
| AC-12 | Cập nhật giờ chiếu/Phòng chiếu (Chỉ được sửa nếu chưa ai mua vé). |
| AC-13 | Nếu đã có khách mua vé, cấm đổi/xóa suất chiếu (nếu muốn phải có quy trình báo khách, đổi vé/hoàn tiền riêng). |

## ADM-06 — Quản lý Giá vé (Price Rule)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin cấu hình bảng giá vé tự động (Price Rule). |
| AC-02 | Thiết lập được loại ngày (Ngày thường, Cuối tuần, Lễ...). |
| AC-03 | Thiết lập được khung giờ (Suất chiếu sớm, chiếu khuya...). |
| AC-04 | Giá tiền quy định phải số dương, hợp lý. |
| AC-05 | Có quy định ngày bắt đầu và ngày kết thúc áp dụng. |
| AC-06 | Không cho phép 2 luật giá mâu thuẫn/đè lên nhau cùng 1 khung giờ. |
| AC-07 | Khi luật giá hết hạn, không tự động áp dụng cho các suất chiếu mới tạo. |
| AC-08 | Xem danh sách luật giá. |
| AC-09 | Cập nhật luật giá. |
| AC-10 | Khi sửa luật giá (VD tăng giá từ 100k -> 120k), những hóa đơn ngày xưa khách đã mua 100k **phải giữ nguyên số tiền**, không được tự update lên 120k. |

## ADM-07 — Quản lý Hạng thành viên (Membership)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Tạo hạng thành viên mới (Bronze, Silver, Gold...). |
| AC-02 | Cấu hình mức điểm để đạt hạng, tỉ lệ giảm giá. |
| AC-03 | Xem list hạng. |
| AC-04 | Xem chi tiết. |
| AC-05 | Sửa/Cập nhật hạng. |
| AC-06 | Nhập vớ vẩn chữ vào ô số -> Báo lỗi. |
| AC-07 | Hạng đang có khách hàng sử dụng -> Cấm xóa cứng (xóa đi khách mất hạng). |
| AC-08 | Sửa tỉ lệ chiết khấu của Hạng không được làm thay đổi tiền của các đơn hàng trong quá khứ. |

## ADM-08 — Quản lý Mã khuyến mãi (Promotion)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Tạo mã giảm giá (Voucher/Coupon). |
| AC-02 | Tên mã viết liền không dấu hợp lệ (VD: TET2024). |
| AC-03 | Set được thời hạn bắt đầu/kết thúc. |
| AC-04 | Set được điều kiện (Đơn tối thiểu bao nhiêu) và Mức giảm (Giảm bao nhiêu % hoặc bao nhiêu tiền). |
| AC-05 | Mã code không được trùng lặp. |
| AC-06 | Xem mã. |
| AC-07 | Cập nhật mã. |
| AC-08 | Mã hết hạn / bị khóa -> Khách không nhập được trên app nữa. |
| AC-09 | Khách chỉ xài 1 mã/đơn. |
| AC-10 | Khách nhập mã nhưng mua chưa đủ điều kiện -> Báo "Đơn chưa đủ điều kiện áp dụng". |
| AC-11 | Việc sửa nội dung mã không làm sai lệch số tiền của các hóa đơn cũ. |

## ADM-09 — Quản lý Menu Đồ ăn/Thức uống (F&B)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Thêm Bắp, Nước, Combo vào Menu. |
| AC-02 | Tên, giá bán, hình ảnh... hợp lệ. |
| AC-03 | Xem Menu. |
| AC-04 | Xem chi tiết món. |
| AC-05 | Sửa/Cập nhật món. |
| AC-06 | Tạm ngưng bán món (Inactive) -> Trên app khách không thấy để mua nữa. |
| AC-07 | Món đã từng có người mua -> Cấm xóa cứng. |
| AC-08 | Dev nên dùng cờ `isActive = false` (xóa mềm) thay cho lệnh DELETE SQL. |
| AC-09 | Tăng giá bắp không làm đổi hóa đơn cũ của khách. |

## ADM-10 — Quản lý Tài khoản Nhân viên

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Admin cấp tài khoản cho Nhân viên mới. |
| AC-02 | Tên, SĐT, Chức vụ hợp lệ. |
| AC-03 | Email/Tên đăng nhập không được trùng với ai trong hệ thống. |
| AC-04 | Danh sách NV. |
| AC-05 | Chi tiết NV. |
| AC-06 | Sửa thông tin NV. |
| AC-07 | Admin có thể mở khóa (Activate) tài khoản NV. |
| AC-08 | Khi NV nghỉ việc, Admin khóa tài khoản (Deactivate). |
| AC-09 | NV bị khóa không thể Login vào app rạp. |
| AC-10 | Nếu NV đang login mà bị khóa, token của họ bị phế, gọi API sẽ trả về báo lỗi không có quyền. |
| AC-11 | Không được xóa cứng tài khoản NV vì sẽ làm mất dữ liệu Log (không biết hóa đơn nào do NV nào thao tác). |

---

# 4. System (Hệ thống chạy ngầm)

## SYS-01 — Cronjob tự động nhả ghế (Giải phóng Hold)

| ID | Tiêu chí nghiệm thu (AC) |
|---|---|
| AC-01 | Hàm chạy ngầm (Scheduler/Cronjob) sẽ quết DB định kỳ mỗi 1 phút hoặc vài giây (tùy cấu hình). |
| AC-02 | Hệ thống scan toàn bộ bảng `ShowtimeSeat` đang có trạng thái `HOLD`. |
| AC-03 | Check thời gian `holdExpiresAt`: Ai bé hơn thời gian hiện tại (`currentTime`) thì xác định là HẾT HẠN. |
| AC-04 | Tự động update ghế đó từ `HOLD` về lại `AVAILABLE`. |
| AC-05 | Ghế nào đang Hold mà chưa hết thời gian (mới 3 phút) thì BỎ QUA, không đụng vào. |
| AC-06 | Các ghế trắng (`AVAILABLE`) hoàn toàn không bị ảnh hưởng. |
| AC-07 | Các ghế đã bán (`SOLD`) hoàn toàn không bị ảnh hưởng. |
| AC-08 | Nếu hệ thống không có ghế nào bị kẹt, cronjob chạy xong tự tắt êm đẹp, không gây lỗi. |
| AC-09 | Scheduler TUYỆT ĐỐI không bao giờ được tự set ghế thành `SOLD`. |
| AC-10 | Sau khi ghế bị nhả, nếu user kia quay lại bấm Thanh toán, API báo lỗi yêu cầu Hold lại từ đầu. |
| AC-11 | **Race condition:** Nếu cronjob đang chạy nhả ghế A, mà đúng lúc đó có người khác vào mua ghế A, database phải lock transaction cẩn thận để dữ liệu không bị hỏng. |
| AC-12 | Job không được release nhầm ghế đang Hold hoàn toàn hợp lệ chỉ vì chạy chéo luồng. |

---

# 5. Checklist nghiệm thu chung cho mọi tính năng

QA/Dev tự check, một API/Chức năng được xem là "XONG - DONE" khi:

- **Happy path:** Luồng thao tác suôn sẻ từ A-Z không xuất hiện bug.
- **Bad path:** Nhập dữ liệu đểu, hack API... hệ thống phải bắt lỗi chuẩn chỉ, không được báo lỗi hệ thống chung chung (Internal Server Error 500) mà phải trả về mã lỗi cụ thể (400 Bad Request, 403...).
- **Business rule:** Các quy tắc nghiệp vụ không bị phá vỡ.
- **Trạng thái DB:** Data lưu vào DB chuẩn xác, đúng bảng, đúng field.
- **Post-condition:** Sau khi thao tác xong, các module khác bị ảnh hưởng phải được update đúng.
- **Isolation:** Update/xóa cái này không làm bốc hơi sai trái cái khác.
- **Concurrency:** Có xử lý khoá luồng, 100 người mua cùng 1 ghế thì hệ thống không bị crash và data không bị double.

## Túm lại các Rule "Chết cũng không được quên" khi Code & Test:

### Quy tắc đơn vé (Booking)

```text
- Tối đa 8 vé (Ticket)
- Chỉ đặt được 1 Suất chiếu (Showtime) / 1 Đơn
- Có thể mua n vé + n Combo / 1 Đơn
- Chỉ Add được 1 mã Giảm giá (Promotion) / 1 Đơn
```

### Xử lý tranh chấp Ghế (Concurrency Seat)

```text
Ghế trắng (AVAILABLE)
    ↓ (User chọn -> Hold thành công)
Ghế vàng (HOLD)
    ↓ (User thanh toán thành công)
Ghế đỏ (SOLD)

Nhưng:
Ghế vàng (HOLD)
    ↓ (Quá 5 phút không trả tiền)
Ghế trắng (AVAILABLE)
```

### Vòng đời của Vé (Ticket)

```text
Hợp lệ đi xem (VALID) → Quét QR vào rạp → (USED)
Hợp lệ đi xem (VALID) → Đòi lại tiền/Admin hủy → (CANCELLED)
```

### Cách soát vé chuẩn xác

```text
Mã QR của Đơn hàng (Booking.qrCodeString)
        ↓
    Tìm ra đơn hàng đó trong DB
        ↓
     Móc ra danh sách toàn bộ các vé (Ticket[])
        ↓
Kiểm tra trạng thái của TỪNG VÉ một
```

### Ví dụ về Check-in đi lẻ

```text
1 Đơn hàng (Booking) gồm 4 vé:
├── Vé 1: VALID
├── Vé 2: VALID
├── Vé 3: USED (Đã check-in lúc nãy)
└── Vé 4: CANCELLED (Khách 4 bận nên xin hủy 1 vé)

Bây giờ: NV tick vào màn hình chọn Check-in cho Vé 1 và Vé 2.
Kết quả sau khi bấm:
→ Vé 1 thành USED
→ Vé 2 thành USED
→ Vé 3 giữ nguyên USED (Không check in đè)
→ Vé 4 giữ nguyên CANCELLED (Bị hủy cấm check)
```

### Sứ mệnh của Job Nhả ghế

```text
Scheduler (Cứ 1 phút chạy 1 lần)
   ↓
Tìm những ghế đang ở trạng thái HOLD + (thời gian hiện tại > holdExpiresAt)
   ↓
Update các ghế đó về AVAILABLE (Xong nhiệm vụ)