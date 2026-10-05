# ACCEPTANCE CRITERIA — HỆ THỐNG QUẢN LÝ RẠP CHIẾU PHIM

> **Nguồn:** `USECASE.md` — baseline Use Case mới nhất của nhóm.
>
> **Baseline áp dụng:** 01 System, 02 Actor nghiệp vụ (`Customer`, `Admin`), 15 Use Case.
>
> **Quy ước AC:**
> - Mỗi Acceptance Criteria mô tả một kết quả nghiệp vụ có thể kiểm tra được.
> - `Given` = điều kiện ban đầu.
> - `When` = hành động/sự kiện.
> - `Then` = kết quả hệ thống phải đảm bảo.
> - AC không mô tả chi tiết UI, API, Controller, Repository hoặc cách triển khai.
> - Các bước nội bộ như Hold ghế, tính tiền, tạo Ticket và sinh `ticketCode` chỉ được kiểm tra thông qua kết quả nghiệp vụ cuối cùng.

---

# 1. CUSTOMER

## CUS-01 — Đăng ký tài khoản

**Mục tiêu:** Customer tạo được tài khoản hợp lệ để sử dụng các chức năng dành cho khách.

### AC-01 — Đăng ký thành công
**Given** Customer chưa có tài khoản hợp lệ và cung cấp đầy đủ thông tin hợp lệ  
**When** Customer xác nhận đăng ký  
**Then** hệ thống tạo tài khoản Customer và thông báo đăng ký thành công.

### AC-02 — Từ chối dữ liệu đăng ký không hợp lệ
**Given** Customer nhập thông tin đăng ký không hợp lệ  
**When** Customer xác nhận đăng ký  
**Then** hệ thống thông báo lỗi, không tạo tài khoản và yêu cầu Customer nhập lại.

### AC-03 — Từ chối thông tin định danh đã tồn tại
**Given** thông tin định danh tài khoản đã tồn tại trong hệ thống  
**When** Customer thực hiện đăng ký  
**Then** hệ thống thông báo thông tin đã tồn tại và không tạo tài khoản mới.

---

## CUS-02 — Đăng nhập

**Mục tiêu:** Người dùng truy cập hệ thống bằng tài khoản hợp lệ và được chuyển đến chức năng phù hợp với vai trò.

### AC-01 — Đăng nhập thành công
**Given** người dùng có tài khoản hợp lệ và tài khoản không bị khóa/vô hiệu hóa  
**When** người dùng nhập đúng thông tin đăng nhập  
**Then** hệ thống xác thực thành công, tạo phiên đăng nhập và chuyển người dùng đến các chức năng phù hợp với vai trò Customer hoặc Admin.

### AC-02 — Từ chối thông tin đăng nhập sai
**Given** người dùng có tài khoản  
**When** người dùng nhập sai thông tin đăng nhập  
**Then** hệ thống thông báo lỗi và không tạo phiên đăng nhập hợp lệ.

### AC-03 — Từ chối tài khoản bị khóa/vô hiệu hóa
**Given** tài khoản đang ở trạng thái bị khóa/vô hiệu hóa  
**When** người dùng nhập đúng thông tin đăng nhập  
**Then** hệ thống vẫn từ chối đăng nhập và không tạo phiên đăng nhập.

---

## CUS-03 — Quản lý thông tin cá nhân

**Mục tiêu:** Customer xem và cập nhật được thông tin cá nhân của chính mình.

### AC-01 — Xem thông tin cá nhân
**Given** Customer đã đăng nhập  
**When** Customer mở thông tin cá nhân  
**Then** hệ thống hiển thị thông tin hiện tại của đúng tài khoản Customer.

### AC-02 — Cập nhật thông tin thành công
**Given** Customer đã đăng nhập và nhập dữ liệu cập nhật hợp lệ  
**When** Customer xác nhận cập nhật  
**Then** hệ thống lưu thông tin mới và thông báo cập nhật thành công.

### AC-03 — Từ chối dữ liệu cập nhật không hợp lệ
**Given** Customer đã đăng nhập nhưng dữ liệu cập nhật không hợp lệ  
**When** Customer xác nhận cập nhật  
**Then** hệ thống thông báo lỗi và không lưu dữ liệu không hợp lệ.

### AC-04 — Xử lý lỗi khi cập nhật
**Given** Customer đã nhập dữ liệu hợp lệ nhưng hệ thống không thể cập nhật thông tin  
**When** Customer xác nhận cập nhật  
**Then** hệ thống thông báo lỗi và không xác nhận cập nhật thành công.

---

## CUS-04 — Xem phim và suất chiếu

**Mục tiêu:** Customer tìm và chọn được phim, sau đó xem và chọn suất chiếu phù hợp.

### AC-01 — Xem danh sách phim
**Given** hệ thống có các phim đang được hiển thị  
**When** Customer truy cập danh sách phim  
**Then** hệ thống hiển thị các phim đang được phép hiển thị cho Customer.

### AC-02 — Tìm kiếm/lọc phim có kết quả
**Given** có phim phù hợp với điều kiện tìm kiếm/lọc  
**When** Customer tìm kiếm hoặc lọc phim  
**Then** hệ thống hiển thị các phim phù hợp.

### AC-03 — Không tìm thấy phim
**Given** không có phim phù hợp với điều kiện tìm kiếm/lọc  
**When** Customer thực hiện tìm kiếm/lọc  
**Then** hệ thống thông báo không có phim phù hợp.

### AC-04 — Xem suất chiếu của phim
**Given** Customer đã chọn một phim đang hiển thị  
**When** Customer xem chi tiết phim  
**Then** hệ thống hiển thị thông tin phim và các suất chiếu đang mở bán của phim.

### AC-05 — Không có suất chiếu phù hợp
**Given** phim được chọn không có suất chiếu phù hợp/đang mở bán  
**When** Customer xem suất chiếu  
**Then** hệ thống thông báo không có suất chiếu phù hợp.

### AC-06 — Chọn suất chiếu
**Given** có suất chiếu đang mở bán  
**When** Customer chọn suất chiếu  
**Then** hệ thống ghi nhận suất chiếu được chọn để tiếp tục đặt vé.

---

## CUS-05 — Đặt vé online

**Mục tiêu:** Customer chọn ghế, tùy chọn Bắp Nước, thanh toán trực tuyến và nhận vé điện tử sau khi thanh toán thành công.

### AC-01 — Chọn ghế còn trống và tạo Booking PENDING
**Given** Customer đã đăng nhập, suất chiếu đang mở bán và ghế ở trạng thái `AVAILABLE`  
**When** Customer chọn ghế hợp lệ  
**Then** hệ thống giữ các ghế đã chọn ở trạng thái `HOLD` trong 5 phút và tạo Booking ở trạng thái `PENDING`.

### AC-02 — Không cho chọn quá số lượng vé cho phép
**Given** Customer đang chọn ghế và số ghế đã chọn đạt giới hạn theo thông số rạp phim  
**When** Customer chọn thêm ghế vượt giới hạn  
**Then** hệ thống thông báo vượt số lượng vé cho phép và yêu cầu Customer điều chỉnh lựa chọn.

### AC-03 — Không bán trùng ghế
**Given** một ghế đã được Customer khác giữ hoặc mua  
**When** Customer cố chọn ghế đó  
**Then** hệ thống từ chối lựa chọn ghế, thông báo ghế không còn khả dụng và yêu cầu chọn ghế khác.

### AC-04 — Chọn Bắp Nước còn bán và còn hàng
**Given** món Bắp Nước đang được bán và còn tồn kho  
**When** Customer chọn món trong quá trình đặt vé  
**Then** hệ thống chấp nhận món đã chọn và tính món đó vào tổng tiền Booking.

### AC-05 — Không cho chọn Bắp Nước hết hàng/ngừng bán
**Given** món Bắp Nước đã hết hàng hoặc ngừng bán  
**When** Customer cố chọn món đó  
**Then** hệ thống thông báo món không khả dụng và không thêm món vào Booking.

### AC-06 — Tính tổng tiền đúng theo cấu hình hiện tại
**Given** Customer đã chọn ghế và tùy chọn Bắp Nước hợp lệ  
**When** hệ thống tính tổng tiền  
**Then** tổng tiền được tính theo giá vé đang áp dụng và các dịch vụ đã chọn.

### AC-07 — Thanh toán thành công
**Given** Booking đang `PENDING`, các ghế vẫn còn `HOLD` và Customer thực hiện thanh toán trực tuyến thành công  
**When** hệ thống xác nhận thanh toán thành công  
**Then** Booking chuyển sang `PAID`, các `ShowtimeSeat` tương ứng chuyển từ `HOLD` sang `SOLD`, Ticket được tạo và mỗi Ticket có `ticketCode` duy nhất.

### AC-08 — Hiển thị thông tin vé sau khi thanh toán
**Given** Booking đã chuyển sang `PAID` và Ticket đã được tạo  
**When** quá trình đặt vé hoàn tất  
**Then** hệ thống hiển thị thông tin đặt vé và `ticketCode` cho Customer.

### AC-09 — Thanh toán thất bại hoặc bị hủy
**Given** Customer đang thanh toán một Booking `PENDING`  
**When** thanh toán thất bại hoặc bị hủy  
**Then** Booking chuyển sang `CANCELLED`, không chuyển sang `PAID` và không tạo Ticket mới.

### AC-10 — Hết thời gian Hold trước khi thanh toán
**Given** Booking đang `PENDING` và các ghế đang `HOLD`  
**When** hết thời gian giữ ghế 5 phút trước khi thanh toán thành công  
**Then** hệ thống giải phóng các ghế về `AVAILABLE`, Booking chuyển sang `EXPIRED` và đặt vé không được hoàn tất.

### AC-11 — Không cho tiếp tục với suất chiếu không còn khả dụng
**Given** Customer đã chọn một suất chiếu nhưng suất chiếu không còn khả dụng  
**When** Customer tiếp tục đặt vé  
**Then** hệ thống không cho tiếp tục đặt vé đối với suất chiếu đó.

### AC-12 — Đảm bảo một ghế không bị giữ/bán đồng thời
**Given** nhiều yêu cầu cùng thao tác trên một `ShowtimeSeat` đang `AVAILABLE`  
**When** các yêu cầu đồng thời cố giữ hoặc mua ghế  
**Then** chỉ một yêu cầu được giữ ghế thành công; các yêu cầu còn lại phải nhận trạng thái ghế không còn khả dụng.

### AC-13 — Không ảnh hưởng Booking đã tạo khi giá thay đổi
**Given** một Booking đã được tạo với giá vé đang áp dụng  
**When** Admin thay đổi cấu hình giá vé sau đó  
**Then** giá của Booking đã tạo không bị thay đổi.

---

## CUS-06 — Quản lý vé điện tử và lịch sử đặt vé

**Mục tiêu:** Customer xem được lịch sử Booking và vé điện tử của các Booking đã thanh toán.

### AC-01 — Chỉ xem Booking của chính Customer
**Given** Customer đã đăng nhập và hệ thống có Booking của nhiều khách hàng  
**When** Customer mở lịch sử đặt vé  
**Then** hệ thống chỉ hiển thị các Booking thuộc tài khoản Customer hiện tại.

### AC-02 — Hiển thị lịch sử đặt vé
**Given** Customer có lịch sử Booking  
**When** Customer mở lịch sử đặt vé  
**Then** hệ thống hiển thị danh sách Booking theo thời gian.

### AC-03 — Không có lịch sử đặt vé
**Given** Customer chưa có Booking  
**When** Customer mở lịch sử đặt vé  
**Then** hệ thống hiển thị trạng thái không có dữ liệu.

### AC-04 — Xem chi tiết Booking
**Given** Customer đã chọn một Booking thuộc tài khoản của mình  
**When** Customer mở chi tiết Booking  
**Then** hệ thống hiển thị phim, suất chiếu, ghế, Bắp Nước, tổng tiền và trạng thái Booking.

### AC-05 — Hiển thị vé điện tử cho Booking PAID
**Given** Booking được chọn có trạng thái `PAID` và có Ticket  
**When** Customer xem chi tiết Booking  
**Then** hệ thống hiển thị vé điện tử và `ticketCode`.

### AC-06 — Không hiển thị vé điện tử cho Booking chưa PAID
**Given** Booking được chọn chưa ở trạng thái `PAID`  
**When** Customer xem chi tiết Booking  
**Then** hệ thống không hiển thị vé điện tử.

---

# 2. ADMIN

> **Điều kiện chung:** Admin đã đăng nhập qua `CUS-02` và có quyền quản trị phù hợp với UC đang thực hiện.

## ADM-01 — Quản lý phim

**Mục tiêu:** Admin xem, tìm kiếm và cập nhật phim bằng các thao tác thêm, sửa hoặc thay đổi trạng thái.

### AC-01 — Hiển thị danh sách phim
**Given** Admin đã đăng nhập  
**When** Admin mở Quản lý phim  
**Then** hệ thống hiển thị danh sách phim.

### AC-02 — Tìm kiếm/lọc phim
**Given** hệ thống có phim phù hợp với điều kiện tìm kiếm/lọc  
**When** Admin tìm kiếm/lọc phim  
**Then** hệ thống hiển thị các phim phù hợp.

### AC-03 — Không tìm thấy phim
**Given** không có phim phù hợp  
**When** Admin tìm kiếm/lọc  
**Then** hệ thống thông báo không tìm thấy phim phù hợp và Admin vẫn có thể thêm phim mới.

### AC-04 — Thêm phim thành công
**Given** Admin nhập thông tin phim hợp lệ  
**When** Admin xác nhận thêm phim  
**Then** hệ thống tạo phim mới và hiển thị phim trong danh sách theo trạng thái phù hợp.

### AC-05 — Sửa phim thành công
**Given** phim tồn tại và Admin có quyền quản lý  
**When** Admin sửa thông tin phim hợp lệ và xác nhận  
**Then** hệ thống lưu thông tin phim mới.

### AC-06 — Thay đổi trạng thái phim
**Given** phim tồn tại  
**When** Admin thay đổi trạng thái phim và xác nhận  
**Then** hệ thống lưu trạng thái mới; phim ở trạng thái không hiển thị/ngừng chiếu không xuất hiện cho Customer.

### AC-07 — Từ chối dữ liệu phim không hợp lệ
**Given** Admin nhập dữ liệu phim không hợp lệ  
**When** Admin xác nhận thao tác  
**Then** hệ thống thông báo lỗi và không lưu thay đổi.

---

## ADM-02 — Quản lý suất chiếu

**Mục tiêu:** Admin xem, tìm kiếm và cập nhật suất chiếu hợp lệ theo phim, phòng, ngày và giờ.

### AC-01 — Hiển thị danh sách suất chiếu
**Given** Admin đã đăng nhập và dữ liệu phim/phòng chiếu phù hợp tồn tại  
**When** Admin mở Quản lý suất chiếu  
**Then** hệ thống hiển thị danh sách suất chiếu.

### AC-02 — Tìm kiếm/lọc suất chiếu
**Given** có suất chiếu phù hợp điều kiện tìm kiếm/lọc  
**When** Admin thực hiện tìm kiếm/lọc  
**Then** hệ thống hiển thị các suất chiếu phù hợp.

### AC-03 — Thêm suất chiếu thành công
**Given** phim và phòng chiếu phù hợp tồn tại, thông tin ngày/giờ hợp lệ và không xung đột lịch phòng  
**When** Admin thêm suất chiếu và xác nhận  
**Then** hệ thống lưu suất chiếu và tạo `ShowtimeSeat` tương ứng với các ghế của phòng ở trạng thái `AVAILABLE`.

### AC-04 — Sửa suất chiếu chưa phát sinh Booking
**Given** suất chiếu chưa phát sinh Booking và dữ liệu mới hợp lệ  
**When** Admin sửa phim/phòng/ngày/giờ của suất chiếu và xác nhận  
**Then** hệ thống lưu thay đổi hợp lệ.

### AC-05 — Không cho sửa thông tin của suất chiếu đã phát sinh Booking
**Given** suất chiếu đã phát sinh Booking  
**When** Admin cố sửa phim, phòng hoặc giờ chiếu  
**Then** hệ thống từ chối thay đổi và chỉ cho phép thay đổi trạng thái.

### AC-06 — Từ chối suất chiếu trùng lịch phòng
**Given** việc tạo hoặc sửa suất chiếu gây xung đột thời gian/phòng  
**When** Admin xác nhận  
**Then** hệ thống từ chối thao tác và yêu cầu điều chỉnh dữ liệu.

### AC-07 — Từ chối dữ liệu suất chiếu không hợp lệ
**Given** thông tin suất chiếu không hợp lệ  
**When** Admin xác nhận thao tác  
**Then** hệ thống thông báo lỗi và không lưu.

### AC-08 — Thay đổi trạng thái suất chiếu
**Given** suất chiếu tồn tại  
**When** Admin thay đổi trạng thái suất chiếu hợp lệ  
**Then** hệ thống lưu trạng thái mới và chỉ hiển thị cho Customer khi suất chiếu ở trạng thái phù hợp để mở bán.

---

## ADM-03 — Quản lý phòng chiếu và sơ đồ chỗ ngồi

**Mục tiêu:** Admin quản lý phòng chiếu và cấu hình sơ đồ ghế mà không làm mất tính toàn vẹn dữ liệu đã được tham chiếu.

### AC-01 — Xem danh sách phòng chiếu
**Given** Admin đã đăng nhập  
**When** Admin mở Quản lý phòng chiếu và sơ đồ chỗ ngồi  
**Then** hệ thống hiển thị danh sách phòng chiếu.

### AC-02 — Tìm kiếm phòng chiếu
**Given** có phòng chiếu phù hợp điều kiện tìm kiếm  
**When** Admin tìm kiếm phòng chiếu  
**Then** hệ thống hiển thị phòng chiếu phù hợp.

### AC-03 — Không tìm thấy phòng chiếu
**Given** không có phòng chiếu phù hợp  
**When** Admin tìm kiếm  
**Then** hệ thống thông báo không tìm thấy phòng chiếu.

### AC-04 — Thêm phòng chiếu
**Given** Admin nhập thông tin phòng chiếu hợp lệ  
**When** Admin xác nhận thêm  
**Then** hệ thống tạo phòng chiếu mới.

### AC-05 — Sửa phòng chiếu hợp lệ
**Given** phòng chiếu tồn tại và thay đổi không vi phạm ràng buộc dữ liệu  
**When** Admin sửa thông tin và xác nhận  
**Then** hệ thống lưu thông tin phòng chiếu mới.

### AC-06 — Ẩn phòng chiếu
**Given** phòng chiếu tồn tại  
**When** Admin chọn ẩn phòng chiếu và xác nhận  
**Then** hệ thống thay đổi trạng thái hiển thị của phòng mà không xóa dữ liệu.

### AC-07 — Xem sơ đồ chỗ ngồi
**Given** Admin chọn một phòng chiếu  
**When** Admin mở sơ đồ chỗ ngồi  
**Then** hệ thống hiển thị danh sách/sơ đồ các ghế của phòng.

### AC-08 — Thiết lập và phân loại ghế
**Given** Admin có quyền cấu hình sơ đồ và dữ liệu ghế hợp lệ  
**When** Admin thiết lập sơ đồ hoặc phân loại ghế và xác nhận  
**Then** hệ thống lưu cấu hình ghế.

### AC-09 — Thay đổi trạng thái ghế vật lý
**Given** ghế tồn tại và thay đổi không làm mất tính toàn vẹn dữ liệu  
**When** Admin thay đổi trạng thái sử dụng của ghế và xác nhận  
**Then** hệ thống lưu trạng thái mới của `Seat`.

### AC-10 — Từ chối mã/vị trí ghế trùng hoặc không hợp lệ
**Given** Admin nhập mã hoặc vị trí ghế bị trùng/không hợp lệ  
**When** Admin xác nhận thay đổi sơ đồ  
**Then** hệ thống thông báo lỗi và không lưu thay đổi.

### AC-11 — Bảo vệ dữ liệu đã được tham chiếu
**Given** phòng hoặc ghế đã được tham chiếu bởi suất chiếu hoặc Booking  
**When** Admin thực hiện thao tác làm mất tính toàn vẹn dữ liệu  
**Then** hệ thống từ chối thao tác đó và chỉ cho phép ẩn/đổi trạng thái phù hợp.

---

## ADM-04 — Quản lý loại vé và giá vé

**Mục tiêu:** Admin quản lý loại vé, cấu hình giá và tra cứu lịch sử thay đổi giá.

### AC-01 — Hiển thị loại vé và giá hiện tại
**Given** Admin đã đăng nhập  
**When** Admin mở Quản lý loại vé và giá vé  
**Then** hệ thống hiển thị danh sách loại vé và cấu hình giá hiện tại.

### AC-02 — Thêm loại vé
**Given** thông tin loại vé hợp lệ  
**When** Admin thêm loại vé và xác nhận  
**Then** hệ thống tạo loại vé mới.

### AC-03 — Sửa loại vé
**Given** loại vé tồn tại và dữ liệu sửa hợp lệ  
**When** Admin sửa loại vé và xác nhận  
**Then** hệ thống lưu thay đổi.

### AC-04 — Thay đổi trạng thái loại vé
**Given** loại vé tồn tại  
**When** Admin thay đổi trạng thái và xác nhận  
**Then** hệ thống lưu trạng thái mới của loại vé.

### AC-05 — Thiết lập đối tượng áp dụng
**Given** Admin nhập cấu hình đối tượng áp dụng hợp lệ  
**When** Admin xác nhận  
**Then** hệ thống lưu cấu hình đối tượng áp dụng cho loại vé.

### AC-06 — Thiết lập/sửa giá vé
**Given** cấu hình giá vé hợp lệ và không xung đột  
**When** Admin thiết lập hoặc sửa giá vé và xác nhận  
**Then** hệ thống lưu giá mới và ghi nhận lịch sử thay đổi giá.

### AC-07 — Tra cứu lịch sử giá
**Given** hệ thống có lịch sử thay đổi giá  
**When** Admin tra cứu lịch sử  
**Then** hệ thống hiển thị các thay đổi giá tương ứng và không thay đổi dữ liệu.

### AC-08 — Từ chối giá hoặc cấu hình không hợp lệ
**Given** dữ liệu giá/cấu hình không hợp lệ hoặc bị trùng/xung đột  
**When** Admin xác nhận  
**Then** hệ thống thông báo lỗi và không lưu thay đổi.

### AC-09 — Không ảnh hưởng Booking đã tạo
**Given** Booking đã được tạo trước khi giá vé thay đổi  
**When** Admin thay đổi giá vé  
**Then** giá của Booking đã tạo không bị thay đổi.

---

## ADM-05 — Quản lý thực phẩm

**Mục tiêu:** Admin quản lý món Bắp Nước và tồn kho.

### AC-01 — Hiển thị món và tồn kho
**Given** Admin đã đăng nhập  
**When** Admin mở Quản lý thực phẩm  
**Then** hệ thống hiển thị danh sách món và số lượng tồn kho.

### AC-02 — Thêm món
**Given** thông tin món hợp lệ  
**When** Admin thêm món và xác nhận  
**Then** hệ thống tạo món mới.

### AC-03 — Sửa món
**Given** món tồn tại và thông tin sửa hợp lệ  
**When** Admin sửa món và xác nhận  
**Then** hệ thống lưu thông tin mới.

### AC-04 — Thay đổi trạng thái món
**Given** món tồn tại  
**When** Admin thay đổi trạng thái món và xác nhận  
**Then** hệ thống lưu trạng thái mới; món ngừng bán không được chọn cho Booking mới.

### AC-05 — Theo dõi/cập nhật tồn kho
**Given** Admin có thông tin tồn kho cần theo dõi hoặc cập nhật  
**When** Admin thực hiện thao tác tồn kho hợp lệ  
**Then** hệ thống lưu số lượng tồn kho mới.

### AC-06 — Từ chối dữ liệu thực phẩm không hợp lệ
**Given** dữ liệu món hoặc tồn kho không hợp lệ  
**When** Admin xác nhận thao tác  
**Then** hệ thống thông báo lỗi và không lưu.

### AC-07 — Đánh dấu hết hàng
**Given** số lượng tồn kho của món bằng 0  
**When** hệ thống cập nhật trạng thái tồn kho  
**Then** hệ thống xác định món hết hàng và Customer không thể chọn món đó khi đặt vé.

---

## ADM-06 — Quản lý đơn hàng

**Mục tiêu:** Admin xem và tra cứu toàn bộ Booking, đồng thời kiểm tra tình trạng thanh toán mà không thay đổi dữ liệu nghiệp vụ.

### AC-01 — Xem toàn bộ Booking
**Given** Admin đã đăng nhập  
**When** Admin mở Quản lý đơn hàng  
**Then** hệ thống hiển thị danh sách toàn bộ Booking.

### AC-02 — Không có Booking
**Given** hệ thống chưa có Booking  
**When** Admin mở Quản lý đơn hàng  
**Then** hệ thống hiển thị trạng thái không có dữ liệu.

### AC-03 — Tìm kiếm/lọc Booking
**Given** hệ thống có Booking phù hợp điều kiện tìm kiếm/lọc  
**When** Admin tìm kiếm/lọc theo trạng thái, khách hàng, phim/suất chiếu, ngày đặt hoặc `ticketCode`  
**Then** hệ thống hiển thị các Booking phù hợp.

### AC-04 — Không tìm thấy Booking
**Given** không có Booking phù hợp điều kiện tìm kiếm  
**When** Admin thực hiện tìm kiếm/lọc  
**Then** hệ thống thông báo không có kết quả.

### AC-05 — Xem chi tiết Booking
**Given** Admin chọn một Booking tồn tại  
**When** Admin mở chi tiết  
**Then** hệ thống hiển thị khách hàng, phim, suất chiếu, ghế, Bắp Nước, tổng tiền, trạng thái Booking, tình trạng thanh toán, Ticket và `ticketCode` nếu có.

### AC-06 — Kiểm tra tình trạng thanh toán
**Given** Admin đang xem một Booking  
**When** Admin kiểm tra tình trạng thanh toán  
**Then** hệ thống cung cấp tình trạng thanh toán tương ứng với Booking.

### AC-07 — Không chỉnh sửa Booking
**Given** Admin đang sử dụng ADM-06  
**When** Admin thực hiện các thao tác trong phạm vi UC  
**Then** hệ thống chỉ cho phép xem/tra cứu và kiểm tra tình trạng thanh toán, không thay đổi dữ liệu nghiệp vụ của Booking.

---

## ADM-07 — Quản lý tài khoản

**Mục tiêu:** Admin quản lý tài khoản Customer và Employee trong cùng một UC.

### AC-01 — Hiển thị danh sách tài khoản
**Given** Admin đã đăng nhập và có quyền quản trị tài khoản  
**When** Admin mở Quản lý tài khoản  
**Then** hệ thống hiển thị danh sách tài khoản.

### AC-02 — Tìm kiếm/lọc tài khoản
**Given** hệ thống có tài khoản Customer hoặc Employee phù hợp  
**When** Admin tìm kiếm/lọc theo loại tài khoản hoặc trạng thái  
**Then** hệ thống hiển thị các tài khoản phù hợp.

### AC-03 — Không tìm thấy tài khoản
**Given** không có tài khoản phù hợp điều kiện tìm kiếm  
**When** Admin tìm kiếm/lọc  
**Then** hệ thống thông báo không tìm thấy tài khoản.

### AC-04 — Thêm tài khoản
**Given** Admin có quyền và nhập dữ liệu tài khoản hợp lệ  
**When** Admin xác nhận thêm tài khoản  
**Then** hệ thống tạo tài khoản Customer hoặc Employee tương ứng.

### AC-05 — Xem chi tiết tài khoản
**Given** tài khoản tồn tại  
**When** Admin chọn xem chi tiết  
**Then** hệ thống hiển thị thông tin chi tiết của tài khoản được chọn.

### AC-06 — Chỉnh sửa thông tin tài khoản
**Given** tài khoản tồn tại và dữ liệu chỉnh sửa hợp lệ  
**When** Admin xác nhận cập nhật  
**Then** hệ thống lưu thông tin mới.

### AC-07 — Khóa/mở tài khoản
**Given** tài khoản tồn tại và Admin có quyền thao tác  
**When** Admin khóa hoặc mở tài khoản và xác nhận  
**Then** hệ thống cập nhật trạng thái tài khoản; tài khoản bị khóa/vô hiệu hóa không thể đăng nhập.

### AC-08 — Khởi tạo lại mật khẩu
**Given** tài khoản tồn tại và Admin có quyền quản trị  
**When** Admin yêu cầu khởi tạo lại mật khẩu  
**Then** hệ thống thực hiện khởi tạo mật khẩu theo cơ chế của hệ thống và cập nhật thông tin tài khoản.

### AC-09 — Chỉnh sửa chức vụ
**Given** tài khoản Employee tồn tại và dữ liệu chức vụ hợp lệ  
**When** Admin thay đổi chức vụ và xác nhận  
**Then** hệ thống cập nhật chức vụ.

### AC-10 — Từ chối dữ liệu tài khoản trùng/không hợp lệ
**Given** thông tin tài khoản bị trùng hoặc không hợp lệ  
**When** Admin xác nhận thao tác  
**Then** hệ thống thông báo lỗi và không lưu thay đổi.

### AC-11 — Từ chối thao tác khi không đủ quyền
**Given** Admin không có đủ quyền đối với thao tác đang thực hiện  
**When** Admin thực hiện thao tác  
**Then** hệ thống từ chối thao tác và không thay đổi dữ liệu.

### AC-12 — Không được làm mất Admin cuối cùng
**Given** hệ thống chỉ còn một tài khoản Admin có quyền quản trị cuối cùng  
**When** Admin cố khóa tài khoản đó hoặc đổi chức vụ làm mất Admin cuối cùng  
**Then** hệ thống từ chối thao tác và giữ nguyên tài khoản Admin cuối cùng.

---

## ADM-08 — Quản lý báo cáo và thống kê

**Mục tiêu:** Admin xem báo cáo doanh thu và mức độ quan tâm theo phim theo kỳ và bộ lọc.

### AC-01 — Hiển thị tổng quan hiện tại
**Given** Admin đã đăng nhập  
**When** Admin mở Báo cáo và Thống kê  
**Then** hệ thống hiển thị tổng quan nhanh của ngày hiện tại, gồm doanh thu và số vé bán.

### AC-02 — Chọn báo cáo doanh thu
**Given** Admin đang ở chức năng Báo cáo và Thống kê  
**When** Admin chọn Báo cáo doanh thu  
**Then** hệ thống cho phép Admin chọn kỳ thống kê và bộ lọc tương ứng.

### AC-03 — Chọn báo cáo mức độ quan tâm theo phim
**Given** Admin đang ở chức năng Báo cáo và Thống kê  
**When** Admin chọn Báo cáo mức độ quan tâm theo phim  
**Then** hệ thống cho phép Admin chọn kỳ thống kê và bộ lọc tương ứng.

### AC-04 — Chọn kỳ thống kê
**Given** Admin chọn loại báo cáo  
**When** Admin chọn ngày, tuần, tháng hoặc khoảng thời gian tùy chọn  
**Then** hệ thống nhận kỳ thống kê và mốc thời gian tương ứng.

### AC-05 — Từ chối khoảng thời gian không hợp lệ
**Given** khoảng thời gian được nhập không hợp lệ, ví dụ ngày bắt đầu sau ngày kết thúc  
**When** Admin yêu cầu tạo báo cáo  
**Then** hệ thống yêu cầu nhập lại khoảng thời gian và không tạo báo cáo theo dữ liệu không hợp lệ.

### AC-06 — Tổng hợp dữ liệu báo cáo
**Given** kỳ thống kê hợp lệ  
**When** Admin yêu cầu xem báo cáo  
**Then** hệ thống tổng hợp dữ liệu từ các Booking theo loại báo cáo, kỳ và bộ lọc đã chọn.

### AC-07 — Chỉ tính Booking PAID vào doanh thu và số vé bán
**Given** trong kỳ có Booking ở nhiều trạng thái  
**When** hệ thống tổng hợp doanh thu và số vé bán  
**Then** chỉ Booking `PAID` được tính vào doanh thu và số vé bán.

### AC-08 — Doanh thu ghi nhận theo ngày thanh toán thành công
**Given** Booking được thanh toán thành công  
**When** hệ thống xác định kỳ doanh thu  
**Then** doanh thu của Booking được ghi nhận theo ngày thanh toán thành công.

### AC-09 — Báo cáo mức độ quan tâm theo phim
**Given** kỳ thống kê hợp lệ và có dữ liệu  
**When** Admin xem báo cáo mức độ quan tâm theo phim  
**Then** hệ thống có thể cung cấp các chỉ số được đặc tả như số vé bán theo phim, số lượt đặt vé, tỷ lệ lấp đầy ghế, tỷ lệ hoàn tất đặt vé, xếp hạng phim và khung giờ/ngày cao điểm theo dữ liệu tương ứng.

### AC-10 — Hiển thị báo cáo không có dữ liệu
**Given** kỳ thống kê hợp lệ nhưng không có dữ liệu  
**When** Admin yêu cầu xem báo cáo  
**Then** hệ thống hiển thị báo cáo rỗng hoặc thông báo không có dữ liệu.

### AC-11 — Hiển thị bảng/biểu đồ và so sánh kỳ trước
**Given** kỳ thống kê có dữ liệu  
**When** hệ thống hoàn tất tổng hợp  
**Then** hệ thống hiển thị các chỉ số, bảng, biểu đồ và phần so sánh với kỳ trước theo phạm vi chức năng hỗ trợ.

### AC-12 — Xem chi tiết từ báo cáo
**Given** báo cáo có dữ liệu chi tiết  
**When** Admin chọn xem chi tiết theo kỳ/ngày  
**Then** hệ thống cho phép đi đến danh sách Booking tương ứng trong `ADM-06`.

### AC-13 — Không thay đổi dữ liệu nghiệp vụ khi xem báo cáo
**Given** Admin đang xem hoặc lọc báo cáo  
**When** hệ thống tổng hợp và hiển thị báo cáo  
**Then** dữ liệu Booking và các dữ liệu nghiệp vụ khác không bị thay đổi.

---

## ADM-09 — Cài đặt hệ thống

**Mục tiêu:** Admin chỉnh sửa thông số rạp phim hoặc thực hiện sao lưu dữ liệu.

### AC-01 — Hiển thị thông số hệ thống
**Given** Admin đã đăng nhập và có quyền cấu hình  
**When** Admin mở Cài đặt hệ thống  
**Then** hệ thống hiển thị các thông số rạp phim hiện tại và tùy chọn sao lưu.

### AC-02 — Cập nhật thông số rạp phim hợp lệ
**Given** Admin nhập thông số hợp lệ  
**When** Admin xác nhận cập nhật  
**Then** hệ thống lưu thông số mới và thông báo kết quả thành công.

### AC-03 — Từ chối thông số không hợp lệ
**Given** Admin nhập giá trị thông số không hợp lệ  
**When** Admin xác nhận cập nhật  
**Then** hệ thống từ chối lưu, thông báo lỗi và giữ giá trị cũ.

### AC-04 — Sao lưu dữ liệu thành công
**Given** Admin có quyền cấu hình và hệ thống sẵn sàng sao lưu  
**When** Admin yêu cầu sao lưu dữ liệu  
**Then** hệ thống thực hiện sao lưu và thông báo kết quả.

### AC-05 — Xử lý sao lưu thất bại
**Given** quá trình sao lưu không thể hoàn tất  
**When** Admin yêu cầu sao lưu  
**Then** hệ thống thông báo sao lưu thất bại.

---

# 3. ACCEPTANCE CRITERIA NGHIỆP VỤ TOÀN HỆ THỐNG

Các AC dưới đây kiểm tra những quy tắc xuyên suốt nhiều UC được `USECASE.md` xác định.

## SYS-AC-01 — Vòng đời ShowtimeSeat
**Given** một ghế của một suất chiếu có trạng thái `AVAILABLE`  
**When** Customer chọn ghế và đặt vé  
**Then** ghế chuyển `AVAILABLE → HOLD`; khi thanh toán thành công chuyển `HOLD → SOLD`; khi Hold hết hạn mà chưa thanh toán thì chuyển `HOLD → AVAILABLE`.

## SYS-AC-02 — Không tự ý thay đổi SOLD/AVAILABLE bởi tác vụ giải phóng Hold
**Given** hệ thống có các `ShowtimeSeat` ở `AVAILABLE`, `HOLD` và `SOLD`  
**When** tác vụ nền xử lý Hold hết hạn  
**Then** chỉ `HOLD` đã hết hạn được giải phóng; `AVAILABLE` và `SOLD` không bị thay đổi bởi tác vụ này.

## SYS-AC-03 — Ticket chỉ tồn tại sau PAID
**Given** Booking đang `PENDING`, `CANCELLED` hoặc `EXPIRED`  
**When** hệ thống xử lý Booking  
**Then** không tạo Ticket cho Booking chưa `PAID`.

## SYS-AC-04 — Ticket không có trạng thái
**Given** Ticket được tạo từ Booking `PAID`  
**When** hệ thống lưu và tra cứu Ticket  
**Then** Ticket được định danh bằng `ticketCode` và không sử dụng trạng thái `VALID/USED`.

## SYS-AC-05 — ticketCode duy nhất
**Given** hệ thống tạo nhiều Ticket  
**When** Ticket được tạo sau các Booking `PAID`  
**Then** mỗi Ticket có một `ticketCode` duy nhất để tra cứu.

## SYS-AC-06 — Không sử dụng QR
**Given** Customer đã có Ticket điện tử  
**When** Customer hoặc Admin tra cứu Ticket  
**Then** hệ thống sử dụng `ticketCode` để định danh/tra cứu và không yêu cầu QR.

## SYS-AC-07 — Không xóa dữ liệu danh mục
**Given** Admin quản lý Phim, Suất chiếu, Phòng chiếu, Loại vé hoặc Bắp Nước  
**When** Admin muốn ngừng sử dụng dữ liệu  
**Then** hệ thống sử dụng thao tác ẩn/đổi trạng thái phù hợp thay vì xóa dữ liệu theo phạm vi BFD.

## SYS-AC-08 — Danh mục không khả dụng không xuất hiện cho Booking mới
**Given** phim/suất chiếu ngừng hiển thị, món Bắp Nước ngừng bán hoặc hết tồn kho  
**When** Customer thực hiện Booking mới  
**Then** Customer không thể chọn dữ liệu không khả dụng đó.

## SYS-AC-09 — Employee không phải Actor nghiệp vụ
**Given** Admin cần quản lý tài khoản Employee  
**When** Admin thực hiện quản lý tài khoản  
**Then** Employee được xử lý như một loại tài khoản trong `ADM-07`, không tạo Actor/Use Case riêng cho Employee.

## SYS-AC-10 — Không tạo UC riêng cho tác vụ nội bộ
**Given** hệ thống cần Hold ghế, tính tiền, tạo Ticket hoặc sinh `ticketCode`  
**When** các xử lý này xảy ra trong quá trình nghiệp vụ  
**Then** chúng được xử lý bên trong UC liên quan và không được coi là Use Case độc lập.

## SYS-AC-11 — Không có Promotion/Membership/QR/soát vé điện tử
**Given** hệ thống đang chạy theo baseline hiện tại  
**When** xây dựng hoặc kiểm thử phạm vi Use Case  
**Then** không yêu cầu các chức năng Promotion, Membership, QR hoặc soát vé điện tử.

---

# 4. TRACEABILITY — USE CASE → ACCEPTANCE CRITERIA

| Use Case | Số AC | Nội dung bao phủ chính |
|---|---:|---|
| CUS-01 | 3 | Đăng ký thành công, dữ liệu sai, thông tin trùng |
| CUS-02 | 3 | Đăng nhập thành công, sai thông tin, tài khoản bị khóa |
| CUS-03 | 4 | Xem, cập nhật, dữ liệu sai, lỗi cập nhật |
| CUS-04 | 6 | Xem/tìm phim, không có phim, xem/chọn suất chiếu |
| CUS-05 | 13 | Ghế, Hold, Booking, Bắp Nước, thanh toán, Ticket, ticketCode, timeout, đồng thời |
| CUS-06 | 6 | Lịch sử, phạm vi dữ liệu Customer, chi tiết Booking, vé điện tử |
| ADM-01 | 7 | Danh sách, tìm kiếm, thêm, sửa, trạng thái, dữ liệu sai |
| ADM-02 | 8 | Danh sách, tìm kiếm, thêm/sửa/trạng thái, xung đột lịch, ShowtimeSeat |
| ADM-03 | 11 | Phòng chiếu, sơ đồ ghế, phân loại, trạng thái, toàn vẹn dữ liệu |
| ADM-04 | 9 | Loại vé, giá vé, đối tượng áp dụng, lịch sử giá, không ảnh hưởng Booking |
| ADM-05 | 7 | Món, trạng thái, tồn kho, hết hàng |
| ADM-06 | 7 | Danh sách, tìm kiếm, chi tiết, thanh toán, chỉ đọc |
| ADM-07 | 12 | Customer/Employee, CRUD nghiệp vụ, khóa/mở, mật khẩu, chức vụ, quyền, Admin cuối |
| ADM-08 | 13 | Doanh thu, mức độ quan tâm, kỳ, bộ lọc, dữ liệu PAID, chi tiết, biểu đồ |
| ADM-09 | 5 | Thông số hệ thống, cập nhật, validation, sao lưu |

**Tổng: 15 Use Case · 114 Acceptance Criteria (103 AC theo UC + 11 AC nghiệp vụ xuyên hệ thống).**

---

# 5. PHẠM VI ĐÃ LOẠI BỎ

Theo baseline `USECASE.md`, không tạo Acceptance Criteria cho các chức năng sau vì không còn thuộc phạm vi:

- Promotion.
- Membership.
- QR Code.
- Soát vé điện tử.
- Actor Employee.
- SystemScheduler như một Actor.
- UC riêng cho In vé.
- UC riêng cho Bán vé tại quầy.
- UC riêng cho Thanh toán tại quầy.
- UC riêng cho Giao Combo.
- UC riêng cho Hủy vé.
- UC riêng cho Tra cứu Booking của Employee.
- Xóa dữ liệu danh mục.
- Trạng thái `Ticket` như `VALID/USED`.

---
