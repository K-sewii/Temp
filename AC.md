# Acceptance Criteria (AC) — Cinema Management System

## Quy ước chung

- Một `Booking` tối đa 8 `Ticket`.
- Một `Booking` thuộc đúng một `Showtime`, có thể có nhiều `Ticket` và `Combo`.
- Một `Booking` tối đa một `Promotion`.
- `ShowtimeSeat` quản lý trạng thái ghế theo Showtime.
- `Seat` vật lý không quản lý `AVAILABLE/HOLD/SOLD`.
- Hold mặc định 5 phút.
- `Booking.qrCodeString` là mã QR duy nhất để tra cứu Booking.
- Trạng thái sử dụng vé nằm trên `Ticket.ticketStatus`, không dùng `Booking.isUsed`.
- `Booking.calculateTotal()` tính tổng Booking.

### State chính

```text
ShowtimeSeat:
AVAILABLE → HOLD → SOLD
HOLD → AVAILABLE (hết hạn / release)

Ticket:
VALID → USED
VALID → CANCELLED
```

---

# 1. Customer

## CUS-01 — Đăng ký tài khoản

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer có thể mở chức năng Đăng ký. |
| AC-02 | Hệ thống yêu cầu email/số điện thoại, mật khẩu và họ tên theo UC. |
| AC-03 | Dữ liệu đầu vào phải được validate trước khi tạo tài khoản. |
| AC-04 | Email/số điện thoại đã tồn tại thì không được tạo Customer mới. |
| AC-05 | Dữ liệu không hợp lệ phải bị từ chối và thông báo lỗi. |
| AC-06 | Dữ liệu hợp lệ tạo đúng một Customer. |
| AC-07 | Khi Customer được tạo thành công, hệ thống tạo Membership mặc định. |
| AC-08 | Customer mới phải liên kết đúng Membership mặc định. |
| AC-09 | Nếu tạo thất bại, không được để lại dữ liệu Customer/Membership dở dang. |
| AC-10 | Đăng ký thành công phải được thông báo cho Customer. |

## CUS-02 — Đăng nhập

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer có thể nhập thông tin đăng nhập. |
| AC-02 | Hệ thống xác thực thông tin trước khi tạo session. |
| AC-03 | Sai thông tin xác thực thì đăng nhập thất bại. |
| AC-04 | Đăng nhập thất bại không tạo session hợp lệ. |
| AC-05 | Hệ thống kiểm tra trạng thái tài khoản. |
| AC-06 | Account bị khóa/không hoạt động không được đăng nhập. |
| AC-07 | Credentials đúng và account hợp lệ thì đăng nhập thành công. |
| AC-08 | Đăng nhập thành công tạo session/authentication context hợp lệ. |
| AC-09 | Customer có thể truy cập chức năng yêu cầu authentication sau đăng nhập. |

## CUS-03 — Quản lý Profile

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer chỉ xem Profile của chính mình. |
| AC-02 | Hệ thống hiển thị đúng Profile hiện tại. |
| AC-03 | Customer có thể sửa các thông tin được phép. |
| AC-04 | Hệ thống validate dữ liệu trước khi lưu. |
| AC-05 | Dữ liệu không hợp lệ không được cập nhật. |
| AC-06 | Dữ liệu hợp lệ được lưu thành công. |
| AC-07 | Sau cập nhật, Profile hiển thị dữ liệu mới chính xác. |
| AC-08 | Customer không được sửa Profile của Customer khác. |
| AC-09 | Cập nhật thất bại không được làm thay đổi dữ liệu cũ một phần. |

## CUS-04 — Xem phim

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer có thể xem danh sách phim. |
| AC-02 | Hệ thống hiển thị phim theo trạng thái được phép. |
| AC-03 | Customer có thể chọn một Movie. |
| AC-04 | Hệ thống hiển thị đúng chi tiết Movie được chọn. |
| AC-05 | Không có phim phù hợp thì hiển thị trạng thái không có dữ liệu. |
| AC-06 | UC không làm thay đổi dữ liệu Movie. |

## CUS-05 — Xem suất chiếu

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer có thể tìm Showtime bằng Movie. |
| AC-02 | Customer cũng có thể tìm bằng Cinema/Date theo flow đã đặc tả. |
| AC-03 | Hệ thống chỉ trả về Showtime phù hợp tiêu chí. |
| AC-04 | Kết quả thể hiện Cinema, Room, thời gian và giá liên quan. |
| AC-05 | Customer có thể chọn Showtime. |
| AC-06 | Showtime không tồn tại/không phù hợp không được tiếp tục đặt vé. |
| AC-07 | Không bắt buộc CUS-04 trước CUS-05. |
| AC-08 | UC không thay đổi trạng thái ghế. |

## CUS-06 — Đặt vé online

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer phải chọn Showtime hợp lệ. |
| AC-02 | Hệ thống hiển thị ShowtimeSeat của Showtime. |
| AC-03 | Chỉ `AVAILABLE` mới được chọn. |
| AC-04 | Một Booking không quá 8 ghế/Ticket. |
| AC-05 | Hold phải kiểm tra trạng thái ghế tại thời điểm xử lý. |
| AC-06 | Hold thành công chuyển `AVAILABLE → HOLD`. |
| AC-07 | Hold phải ghi nhận `holdExpiresAt`. |
| AC-08 | Hold mặc định 5 phút. |
| AC-09 | Nếu một ghế yêu cầu Hold không còn AVAILABLE, toàn bộ request Hold thất bại. |
| AC-10 | Request Hold thất bại phải rollback các ghế đã Hold trong cùng request. |
| AC-11 | Hai Customer đồng thời không được cùng Hold thành công một ShowtimeSeat. |
| AC-12 | Customer được tiếp tục xử lý Booking sau Hold thành công. |
| AC-13 | Có thể áp dụng Membership nếu đủ điều kiện. |
| AC-14 | Một Booking tối đa một Promotion. |
| AC-15 | Promotion phải còn hiệu lực và đủ điều kiện. |
| AC-16 | Có thể thêm Combo đang bán. |
| AC-17 | Combo không làm tăng giới hạn 8 Ticket. |
| AC-18 | `Booking.calculateTotal()` tính đúng Ticket + Combo + ưu đãi hợp lệ. |
| AC-19 | Booking chuyển sang CUS-07 để thanh toán online. |
| AC-20 | Payment thành công chuyển Booking thành `PAID`. |
| AC-21 | ShowtimeSeat tương ứng chuyển `HOLD → SOLD`. |
| AC-22 | Hệ thống tạo Ticket tương ứng với ghế mua. |
| AC-23 | Ticket mới có trạng thái `VALID`. |
| AC-24 | Số Ticket bằng số ghế mua thành công. |
| AC-25 | Booking thành công có `qrCodeString`. |
| AC-26 | `qrCodeString` duy nhất cho Booking. |
| AC-27 | Trạng thái sử dụng không nằm ở Booking; được quản lý trên từng Ticket. |
| AC-28 | Payment thất bại không chuyển Booking thành `PAID`. |
| AC-29 | Payment thất bại không chuyển ghế sang `SOLD`. |
| AC-30 | Payment thất bại khi Hold chưa hết hạn thì ghế có thể vẫn `HOLD`. |
| AC-31 | Hold hết hạn không được dùng để hoàn tất Booking. |
| AC-32 | Hold hết hạn được SYS-01 release về `AVAILABLE`. |
| AC-33 | Thành công cuối cùng: Booking `PAID`, Ticket `VALID`, ShowtimeSeat `SOLD`, QR tồn tại. |

## CUS-07 — Thanh toán online

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Hệ thống hiển thị đúng tổng tiền Booking. |
| AC-02 | Customer chỉ chọn phương thức online được hỗ trợ. |
| AC-03 | Phương thức gồm MoMo, VNPay, Credit Card. |
| AC-04 | Hệ thống resolve đúng `PaymentStrategy`. |
| AC-05 | Payment request gắn đúng Booking. |
| AC-06 | Số tiền payment đúng tổng Booking. |
| AC-07 | Kết quả payment phải được xác nhận trước khi PAID. |
| AC-08 | Payment thành công chuyển Booking `PAID`. |
| AC-09 | Ticket tương ứng được tạo/hoàn tất ở `VALID`. |
| AC-10 | ShowtimeSeat chuyển `HOLD → SOLD`. |
| AC-11 | Booking có QR sau giao dịch hoàn tất. |
| AC-12 | Payment thất bại không chuyển `PAID`. |
| AC-13 | Payment bị hủy không chuyển `PAID`. |
| AC-14 | Hold hết hạn trước khi xác nhận payment thì không được hoàn tất bằng Hold cũ. |

## CUS-08 — Xem lịch sử Booking

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer có thể mở lịch sử Booking. |
| AC-02 | Chỉ Booking của Customer hiện tại được trả về. |
| AC-03 | Booking của Customer khác không xuất hiện. |
| AC-04 | Danh sách được hiển thị theo quy ước thời gian của hệ thống. |
| AC-05 | Customer có thể xem chi tiết Booking. |
| AC-06 | Chi tiết hiển thị Ticket. |
| AC-07 | Chi tiết hiển thị Combo nếu có. |
| AC-08 | Chi tiết hiển thị QR nếu đã có. |
| AC-09 | Chi tiết hiển thị trạng thái liên quan. |
| AC-10 | Không có Booking thì hiển thị trạng thái không có dữ liệu. |

## CUS-09 — Quản lý Membership

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Customer xem được Membership hiện tại. |
| AC-02 | Hiển thị đúng Membership tier. |
| AC-03 | Hiển thị thông tin điểm theo Membership. |
| AC-04 | Hiển thị quyền lợi Membership. |
| AC-05 | Có thể xem thông tin/lịch sử Membership theo phạm vi UC. |
| AC-06 | Customer không xem/sửa Membership của Customer khác. |
| AC-07 | Việc xem Membership không tự thay đổi dữ liệu. |

---

# 2. Employee

## EMP-01 — Bán vé tại quầy

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Employee chọn Movie/Showtime cho khách. |
| AC-02 | Hệ thống hiển thị ShowtimeSeat. |
| AC-03 | Chỉ `AVAILABLE` được chọn. |
| AC-04 | Booking tại quầy tối đa 8 Ticket. |
| AC-05 | Hold tại quầy tuân thủ atomic Hold. |
| AC-06 | Hai giao dịch đồng thời không bán thành công cùng ghế. |
| AC-07 | Có thể thêm Combo vào Booking. |
| AC-08 | Hệ thống tính đúng tổng tiền. |
| AC-09 | EMP-02 được dùng để hoàn tất thanh toán. |
| AC-10 | Thanh toán thành công làm Booking `PAID`. |
| AC-11 | Ticket tạo ở trạng thái `VALID`. |
| AC-12 | ShowtimeSeat chuyển `HOLD → SOLD`. |
| AC-13 | Booking có `qrCodeString`. |
| AC-14 | QR dùng được cho tra cứu/soát vé. |
| AC-15 | Payment thất bại không hoàn tất Booking và không SOLD ghế. |
| AC-16 | Hold nhiều ghế thất bại do xung đột phải rollback các ghế đã Hold trong request. |

## EMP-02 — Thanh toán tại quầy

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Employee xem được tổng tiền. |
| AC-02 | Hỗ trợ Cash. |
| AC-03 | Hỗ trợ Credit Card POS. |
| AC-04 | Cash phải nhận số tiền khách đưa. |
| AC-05 | Tiền đưa nhỏ hơn tổng tiền thì từ chối. |
| AC-06 | Tiền đủ thì tính chính xác tiền thừa. |
| AC-07 | Card POS chỉ thành công khi POS trả kết quả thành công. |
| AC-08 | Thanh toán thành công chuyển Booking `PAID`. |
| AC-09 | Thanh toán thất bại không chuyển `PAID`. |
| AC-10 | Phương thức online-only không xử lý như Cash/Card POS. |

## EMP-03 — Soát vé

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Employee scan QR của Booking. |
| AC-02 | Frontend lấy được `qrCodeString`. |
| AC-03 | Backend tìm Booking bằng `qrCodeString`. |
| AC-04 | QR không tồn tại phải bị từ chối. |
| AC-05 | Booking được kiểm tra trạng thái. |
| AC-06 | Hệ thống lấy toàn bộ Ticket của Booking. |
| AC-07 | UI hiển thị từng Ticket và trạng thái. |
| AC-08 | Employee có thể chọn toàn bộ Ticket `VALID`. |
| AC-09 | Employee có thể chọn một số Ticket `VALID`. |
| AC-10 | Chỉ Ticket `VALID` được xác nhận vào rạp. |
| AC-11 | Ticket `USED` không dùng lại được. |
| AC-12 | Ticket `CANCELLED` không dùng được. |
| AC-13 | Chỉ Ticket được chọn chuyển `VALID → USED`. |
| AC-14 | Ticket VALID không chọn vẫn `VALID`. |
| AC-15 | Ví dụ 5 Ticket, chọn 2 thì chỉ 2 Ticket thành `USED`. |
| AC-16 | Cùng QR có thể scan lại để xử lý Ticket VALID còn lại. |
| AC-17 | Không dùng `Booking.isUsed` để đánh dấu toàn bộ Booking. |
| AC-18 | Booking chưa thanh toán hợp lệ không được sử dụng Ticket. |
| AC-19 | Showtime không phù hợp theo business rule phải bị từ chối. |
| AC-20 | Sau xác nhận phải hiển thị Ticket đã được sử dụng. |

## EMP-04 — Giao Combo

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Employee tra cứu Booking để lấy Combo. |
| AC-02 | Booking không tồn tại bị từ chối. |
| AC-03 | Hiển thị các Combo thuộc Booking. |
| AC-04 | Hiển thị trạng thái giao Combo. |
| AC-05 | Chỉ giao Combo thuộc đúng Booking. |
| AC-06 | Combo đã giao không giao lại nếu nghiệp vụ không cho phép. |
| AC-07 | Xác nhận giao chuyển Combo sang `DELIVERED` theo mô hình. |
| AC-08 | Giao Combo không thay đổi `Ticket.ticketStatus`. |
| AC-09 | EMP-04 độc lập với EMP-03. |

## EMP-05 — Tra cứu Booking

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Employee nhập tiêu chí tra cứu được hỗ trợ. |
| AC-02 | Hệ thống trả về Booking phù hợp. |
| AC-03 | Không tìm thấy thì thông báo tương ứng. |
| AC-04 | Nhiều kết quả phải cho Employee chọn đúng Booking. |
| AC-05 | Chi tiết có Booking, Ticket, Combo, Showtime, QR và trạng thái theo phạm vi. |
| AC-06 | Tra cứu chỉ đọc dữ liệu. |
| AC-07 | Tra cứu không tự thay đổi trạng thái. |

## EMP-06 — Hủy vé

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Employee xác định đúng Booking trước khi hủy. |
| AC-02 | Hiển thị Ticket và trạng thái từng Ticket. |
| AC-03 | Chỉ `VALID` được hủy. |
| AC-04 | `USED` không được hủy. |
| AC-05 | `CANCELLED` không được hủy lần nữa. |
| AC-06 | Employee chọn Ticket cần hủy. |
| AC-07 | Xác nhận hủy chuyển `VALID → CANCELLED`. |
| AC-08 | ShowtimeSeat tương ứng chuyển `SOLD → AVAILABLE`. |
| AC-09 | Hủy một Ticket không hủy các Ticket khác. |
| AC-10 | Hệ thống ghi nhận việc hủy theo phạm vi nghiệp vụ. |
| AC-11 | Refund không thuộc phạm vi nếu UC không đặc tả. |

---

# 3. Admin

## ADM-01 — Quản lý Phim

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Movie với dữ liệu hợp lệ. |
| AC-02 | Dữ liệu bắt buộc phải hợp lệ. |
| AC-03 | Admin xem danh sách Movie. |
| AC-04 | Admin xem chi tiết Movie. |
| AC-05 | Admin cập nhật Movie. |
| AC-06 | Dữ liệu không hợp lệ bị từ chối. |
| AC-07 | Movie có Showtime/dependency không hard-delete nếu phá integrity. |
| AC-08 | Khi không thể xóa, hệ thống thông báo hoặc dùng trạng thái phù hợp. |
| AC-09 | Thay đổi Movie không làm mất Booking/Ticket lịch sử. |

## ADM-02 — Quản lý Rạp

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Cinema. |
| AC-02 | Cinema có dữ liệu bắt buộc hợp lệ. |
| AC-03 | Admin xem danh sách Cinema. |
| AC-04 | Admin xem chi tiết Cinema. |
| AC-05 | Admin cập nhật Cinema. |
| AC-06 | Cinema có Room/dependency không hard-delete nếu phá integrity. |
| AC-07 | Không xóa dữ liệu giao dịch lịch sử do thao tác quản lý Cinema. |

## ADM-03 — Quản lý Phòng chiếu

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Room thuộc Cinema tồn tại. |
| AC-02 | Room có dữ liệu hợp lệ. |
| AC-03 | Admin xem danh sách Room. |
| AC-04 | Admin xem chi tiết Room. |
| AC-05 | Admin cập nhật Room. |
| AC-06 | Room có Showtime/Booking dependency không hard-delete nếu phá integrity. |
| AC-07 | Room duy trì quan hệ đúng với Cinema. |

## ADM-04 — Quản lý Ghế

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Seat thuộc Room hợp lệ. |
| AC-02 | Seat code hợp lệ và không trùng theo phạm vi yêu cầu. |
| AC-03 | Position thỏa validation của Room. |
| AC-04 | Admin xem danh sách Seat. |
| AC-05 | Admin cập nhật Seat theo rule. |
| AC-06 | Seat không trực tiếp quản lý `AVAILABLE/HOLD/SOLD`. |
| AC-07 | Trạng thái theo Showtime do `ShowtimeSeat` quản lý. |
| AC-08 | Không thay đổi Seat theo cách phá vỡ ShowtimeSeat/Booking lịch sử. |

## ADM-05 — Quản lý Suất chiếu

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin chọn Movie hợp lệ. |
| AC-02 | Admin chọn Room hợp lệ. |
| AC-03 | Admin nhập thời gian Showtime hợp lệ. |
| AC-04 | Hệ thống kiểm tra overlap trong cùng Room. |
| AC-05 | Showtime conflict không được tạo. |
| AC-06 | Tạo thành công tạo Showtime. |
| AC-07 | Tạo Showtime tự tạo ShowtimeSeat cho toàn bộ Seat của Room. |
| AC-08 | ShowtimeSeat mới có trạng thái `AVAILABLE`. |
| AC-09 | Mỗi Seat có association ShowtimeSeat tương ứng. |
| AC-10 | Tạo thất bại không để lại ShowtimeSeat orphan. |
| AC-11 | Admin xem Showtime. |
| AC-12 | Admin cập nhật Showtime theo ràng buộc. |
| AC-13 | Không sửa/xóa Showtime làm hỏng Booking/Ticket đã tồn tại. |

## ADM-06 — Quản lý Giá vé

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Price Rule. |
| AC-02 | Day Type hợp lệ. |
| AC-03 | Start/end time hợp lệ. |
| AC-04 | Giá vé hợp lệ theo rule hệ thống. |
| AC-05 | Effective period hợp lệ. |
| AC-06 | Price Rule overlap trái quy tắc không được tạo/activate. |
| AC-07 | Rule hết hiệu lực không áp dụng Booking mới. |
| AC-08 | Admin xem Price Rule. |
| AC-09 | Admin cập nhật Price Rule. |
| AC-10 | Rule mới không tự thay đổi tổng tiền Booking đã hoàn tất. |

## ADM-07 — Quản lý Membership

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Membership tier. |
| AC-02 | Điều kiện/quyền lợi hợp lệ. |
| AC-03 | Admin xem danh sách tier. |
| AC-04 | Admin xem chi tiết tier. |
| AC-05 | Admin cập nhật tier. |
| AC-06 | Dữ liệu không hợp lệ bị từ chối. |
| AC-07 | Tier đang được sử dụng không hard-delete nếu phá integrity. |
| AC-08 | Thay đổi tier không làm thay đổi trái phép lịch sử Booking. |

## ADM-08 — Quản lý Promotion

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Promotion. |
| AC-02 | Promotion Code hợp lệ. |
| AC-03 | Thời gian hiệu lực hợp lệ. |
| AC-04 | Điều kiện/discount hợp lệ. |
| AC-05 | Code tuân thủ uniqueness nếu được quy định. |
| AC-06 | Admin xem Promotion. |
| AC-07 | Admin cập nhật Promotion. |
| AC-08 | Promotion expired/inactive không áp dụng Booking mới. |
| AC-09 | Booking tối đa một Promotion. |
| AC-10 | Promotion không đủ điều kiện bị từ chối khi áp dụng. |
| AC-11 | Thay đổi Promotion không làm thay đổi Booking đã hoàn tất. |

## ADM-09 — Quản lý Food & Beverage

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo F&B item. |
| AC-02 | Thông tin bắt buộc hợp lệ. |
| AC-03 | Admin xem danh sách F&B. |
| AC-04 | Admin xem chi tiết F&B. |
| AC-05 | Admin cập nhật F&B. |
| AC-06 | F&B inactive không được thêm vào Booking mới. |
| AC-07 | F&B được tham chiếu bởi Booking lịch sử không nên hard-delete nếu phá integrity. |
| AC-08 | Có thể dùng inactive thay cho hard-delete khi phù hợp. |
| AC-09 | Thay đổi F&B không sửa dữ liệu lịch sử Booking đã hoàn tất. |

## ADM-10 — Quản lý Nhân viên

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Admin tạo Employee. |
| AC-02 | Thông tin định danh hợp lệ. |
| AC-03 | Identity/email/username unique theo rule. |
| AC-04 | Admin xem danh sách Employee. |
| AC-05 | Admin xem chi tiết Employee. |
| AC-06 | Admin cập nhật Employee. |
| AC-07 | Admin activate Employee. |
| AC-08 | Admin deactivate Employee. |
| AC-09 | Employee deactivated không được đăng nhập. |
| AC-10 | Employee deactivated không được dùng quyền Employee. |
| AC-11 | Không xóa Employee theo cách mất khả năng truy vết thao tác lịch sử. |

---

# 4. System

## SYS-01 — Giải phóng Hold hết hạn

| ID | Acceptance Criteria |
|---|---|
| AC-01 | Scheduler chạy định kỳ theo cấu hình. |
| AC-02 | Scheduler tìm ShowtimeSeat có `status = HOLD`. |
| AC-03 | Chỉ Hold có `holdExpiresAt < currentTime` được xem là hết hạn. |
| AC-04 | Hold hết hạn chuyển `HOLD → AVAILABLE`. |
| AC-05 | Hold chưa hết hạn không được release. |
| AC-06 | ShowtimeSeat `AVAILABLE` không bị thay đổi. |
| AC-07 | ShowtimeSeat `SOLD` không bị thay đổi. |
| AC-08 | Không có Hold hết hạn thì không thay đổi dữ liệu. |
| AC-09 | Scheduler không chuyển ShowtimeSeat trực tiếp sang `SOLD`. |
| AC-10 | Sau khi release, Customer phải Hold lại nếu muốn mua ghế. |
| AC-11 | Release phải an toàn khi xảy ra đồng thời với request đặt vé. |
| AC-12 | Scheduler không release một Hold hợp lệ chỉ vì request chạy đồng thời. |

---

# 5. Checklist nghiệm thu toàn hệ thống

Một UC được xem là đạt khi:

- Happy path hoạt động đúng.
- Input không hợp lệ được xử lý đúng.
- Business rule được bảo đảm.
- Trạng thái domain sau thao tác đúng.
- Post-condition đúng.
- Không thay đổi dữ liệu ngoài phạm vi.
- Các UC có transaction/concurrency không tạo trạng thái dữ liệu không hợp lệ.
- Các lỗi quan trọng có kết quả xác định.

## Các business rule quan trọng cần test riêng

### Booking

```text
Max 8 Ticket
1 Showtime / Booking
Nhiều Ticket / Booking
Nhiều Combo / Booking
Max 1 Promotion / Booking
```

### Seat concurrency

```text
AVAILABLE
    ↓
HOLD
    ↓
SOLD

HOLD
    ↓ timeout
AVAILABLE
```

### Ticket

```text
VALID → USED
VALID → CANCELLED
```

### QR

```text
Booking.qrCodeString
        ↓
    Find Booking
        ↓
     Ticket[]
        ↓
Verify từng Ticket
```

### Partial check-in

```text
Booking
├── T01 VALID
├── T02 VALID
├── T03 USED
└── T04 CANCELLED

Employee chọn T01 + T02

→ T01 USED
→ T02 USED
→ T03 USED
→ T04 CANCELLED
```

### Expired Hold

```text
Scheduler
   ↓
Find HOLD + expired
   ↓
HOLD → AVAILABLE
```

