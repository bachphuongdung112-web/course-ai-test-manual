# Software Requirements Specification (SRS)
## Module: Login (Authentication)
### Hệ thống: Perfex CRM – Anh Tester Demo
### URL: https://crm.anhtester.com/authentication/login

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu này mô tả các yêu cầu chức năng và phi chức năng cho module **Đăng nhập (Login)** của hệ thống Perfex CRM demo tại `crm.anhtester.com`, dựa trên khảo sát thực tế giao diện và hành vi của trang đăng nhập. Tài liệu phục vụ làm cơ sở cho việc thiết kế test case, kiểm thử hồi quy và phát triển các chức năng liên quan đến xác thực người dùng.

### 1.2 Phạm vi
Module Login cho phép người dùng đã có tài khoản (nhân viên/quản trị viên CRM) đăng nhập vào hệ thống bằng **Email** và **Mật khẩu**. Phạm vi tài liệu bao gồm:
- Màn hình đăng nhập (`/authentication/login`)
- Liên kết sang chức năng quên mật khẩu (`/authentication/forgot_password`)
- Không bao gồm: quy trình đặt lại mật khẩu chi tiết, đăng ký tài khoản, đăng nhập bằng mạng xã hội (SSO/OAuth) vì không xuất hiện trên giao diện khảo sát.

### 1.3 Tài khoản khảo sát
- Email: `admin@example.com`
- Password: `12345`
- Kết quả thực tế: hệ thống trả về thông báo lỗi **"Invalid username or password"**, tài khoản này không hợp lệ trên môi trường demo tại thời điểm khảo sát (18/09/2026). Thông báo lỗi là **chung chung**, không tiết lộ email có tồn tại hay không (đúng thông lệ bảo mật).

### 1.4 Đối tượng sử dụng tài liệu
BA, Dev, Tester tham gia thiết kế, phát triển và kiểm thử module Login.

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh sản phẩm
Perfex CRM là hệ thống quản trị khách hàng (CRM). Trang Login là điểm vào bắt buộc để truy cập các chức năng nghiệp vụ (Sales, Support, Projects, Invoices...). Người dùng chưa đăng nhập chỉ có thể xem trang chủ công khai và Knowledge Base.

### 2.2 Đối tượng người dùng
- Quản trị viên hệ thống (Admin/Staff)
- Nhân viên (Staff) được cấp tài khoản nội bộ
- (Ngoài phạm vi khảo sát) Khách hàng (Customer/Client) có thể có cổng đăng nhập riêng.

### 2.3 Môi trường vận hành
- Ứng dụng web, truy cập qua trình duyệt (Chrome, Edge, Firefox, Safari...).
- Giao diện responsive (form đăng nhập hiển thị dạng card căn giữa màn hình).
- Hỗ trợ đa ngôn ngữ (xem mục 3.1.2).

---

## 3. Yêu cầu chức năng

### 3.1 Giao diện màn hình đăng nhập

Màn hình gồm các thành phần:

| # | Thành phần | Loại | Bắt buộc | Ghi chú |
|---|-----------|------|----------|---------|
| 1 | Language (Ngôn ngữ) | Combobox | Không | Danh sách 26 ngôn ngữ: English (mặc định), Chinese, Vietnamese, Indonesia, Bulgarian, Portuguese_br, Swedish, Dutch, Persian, Turkish, Catalan, Spanish, Portuguese, French, Czech, Japanese, Italian, Slovak, Russian, Polish, Greek, Finnish, Norwegian, German, Ukrainian, Romanian |
| 2 | Email Address | Textbox (text) | Có | Nhãn "Email Address" |
| 3 | Password | Textbox (password, ẩn ký tự) | Có | Nhãn "Password" |
| 4 | Remember me | Checkbox | Không | Mặc định không được chọn |
| 5 | Nút Login | Button (submit) | - | Gửi form đăng nhập |
| 6 | Liên kết "Forgot Password?" | Link | - | Điều hướng tới `/authentication/forgot_password` |
| 7 | Liên kết "Knowledge Base" (header) | Link | - | Điều hướng tới trang Knowledge Base công khai |
| 8 | Logo hệ thống | Image/Link | - | Điều hướng về trang chủ |

#### 3.1.2 Đổi ngôn ngữ
Người dùng có thể chọn ngôn ngữ hiển thị trước khi đăng nhập từ combobox "Language".

### 3.2 Chức năng đăng nhập (Login)

**Mô tả:** Người dùng nhập Email và Password, nhấn nút "Login" để xác thực vào hệ thống.

**Luồng chính (Happy path):**
1. Người dùng truy cập `https://crm.anhtester.com/authentication/login`.
2. Người dùng nhập Email hợp lệ đã đăng ký và Password đúng.
3. (Tuỳ chọn) Tick "Remember me" để hệ thống ghi nhớ đăng nhập.
4. Người dùng nhấn nút "Login".
5. Hệ thống xác thực thông tin, chuyển hướng vào trang chủ/dashboard nội bộ (khu vực sau đăng nhập).

**Luồng ngoại lệ đã xác nhận qua khảo sát thực tế:**

| Mã | Điều kiện | Kết quả quan sát được |
|----|-----------|------------------------|
| EX-01 | Bỏ trống Email và Password, nhấn Login | Hiển thị lỗi inline dưới từng field: "The Email Address field is required." và "The Password field is required." Form không được submit. |
| EX-02 | Nhập Email sai định dạng (vd: `notanemail`), Password bất kỳ | Hiển thị lỗi inline: "The Email Address field must contain a valid email address." |
| EX-03 | Nhập Email đúng định dạng nhưng tài khoản/mật khẩu không đúng (vd: `admin@example.com` / `12345`) | Hệ thống submit form (POST), reload lại trang login, hiển thị thông báo dạng **toast/notification** (góc trên bên phải, có icon chuông): **"Invalid username or password"**. Trường Password bị xoá trắng; trường Email được giữ nguyên giá trị đã nhập. Không tiết lộ email tồn tại hay không → tuân thủ nguyên tắc bảo mật (generic error message). |
| EX-04 | Lặp lại đăng nhập sai nhiều lần liên tiếp (đã thử 3 lần) | Chưa quan sát thấy cơ chế khoá tài khoản/giới hạn số lần thử trong phạm vi 3 lần thử trên môi trường demo. **Cần xác nhận thêm với đội phát triển** vì Perfex CRM (bản gốc) có hỗ trợ cấu hình giới hạn số lần đăng nhập sai (xem mục 5 – Giả định & Vấn đề mở). |

**Quy tắc validate (xác nhận qua khảo sát):**
- Email: bắt buộc nhập, phải đúng định dạng email (client-side + server-side, do lỗi hiển thị ngay khi submit mà không cần load lại trang — kiểm tra bằng AJAX/JS validation trước, sau đó có thể có kiểm tra lại phía server).
- Password: bắt buộc nhập, không giới hạn định dạng quan sát được (chấp nhận chuỗi ngắn như "12345" ở bước validate định dạng, lỗi trả về là do sai thông tin đăng nhập chứ không phải sai định dạng).
- Thông báo lỗi đăng nhập sai là **thông báo chung** (không phân biệt "sai email" hay "sai mật khẩu").

### 3.3 Chức năng "Remember me"
Checkbox cho phép ghi nhớ phiên đăng nhập. (Hành vi lưu cookie/token ghi nhớ dài hạn — cần kiểm thử riêng, không quan sát chi tiết cookie trong phạm vi khảo sát này.)

### 3.4 Chức năng "Forgot Password?"
- Liên kết dẫn tới `https://crm.anhtester.com/authentication/forgot_password`.
- Màn hình quên mật khẩu gồm:
  - 1 ô nhập "Email Address" (input type = `email`, có validate định dạng email của trình duyệt - HTML5).
  - Nút "Submit".
- Phạm vi chi tiết luồng đặt lại mật khẩu (gửi email, đặt mật khẩu mới) **không thuộc phạm vi khảo sát của tài liệu này** — đề xuất tách thành SRS riêng cho module "Forgot/Reset Password" nếu cần.

### 3.5 Điều hướng liên quan
- Từ trang Login, có thể vào "Knowledge Base" (trang trợ giúp công khai) mà không cần đăng nhập.
- Nhấn logo hệ thống điều hướng về trang chủ public.

---

## 4. Yêu cầu phi chức năng

| Nhóm | Yêu cầu |
|------|---------|
| Bảo mật | Password được ẩn ký tự (input type="password"). Thông báo lỗi đăng nhập không tiết lộ email có tồn tại trong hệ thống hay không (chống dò tài khoản - user enumeration). |
| Bảo mật | Trường Password bị xoá sau mỗi lần đăng nhập thất bại (không lưu lại giá trị nhạy cảm trên form). |
| Khả dụng (Usability) | Thông báo lỗi hiển thị rõ ràng ngay tại field tương ứng (lỗi validate) hoặc dạng toast nổi bật (lỗi xác thực sai). |
| Đa ngôn ngữ | Hỗ trợ 26 ngôn ngữ hiển thị giao diện. |
| Khả năng tương thích | Giao diện dạng form card, cần kiểm tra hiển thị trên nhiều kích thước màn hình (desktop, tablet, mobile). |
| Hiệu năng | Trang đăng nhập tải nhanh, không có tài nguyên nặng bất thường (cần đo đạc thêm trong kiểm thử hiệu năng riêng). |

---

## 5. Giả định & Vấn đề mở (Assumptions & Open Issues)

1. **Giới hạn số lần đăng nhập sai / khoá tài khoản (Account lockout):** Không quan sát được trong phạm vi 3 lần thử trên demo. Perfex CRM bản chính thức có hỗ trợ cấu hình "Login attempts" trong Settings, nhưng cần xác nhận demo site có bật tính năng này hay không, ngưỡng khoá là bao nhiêu lần, và thời gian khoá là bao lâu.
2. **Trang đích sau đăng nhập thành công:** Chưa xác minh được (không có tài khoản hợp lệ để test luồng thành công). Cần bổ sung tài khoản test hợp lệ để xác nhận URL/màn hình dashboard sau đăng nhập.
3. **Hành vi "Remember me":** Chưa xác minh cụ thể thời gian ghi nhớ phiên, tên cookie/token liên quan.
4. **Luồng quên mật khẩu chi tiết** (gửi email, token reset, thời hạn token, thông báo thành công/thất bại): ngoài phạm vi khảo sát, cần khảo sát riêng.
5. **Đăng nhập cho Customer/Client portal** (nếu có cổng đăng nhập khách hàng riêng biệt với `/authentication/login`): chưa được khảo sát.
6. **Session timeout / đăng xuất tự động:** chưa khảo sát.

---

## 6. Phụ lục – Kết quả khảo sát chi tiết

### 6.1 Cấu trúc form (trích xuất từ DOM thực tế)
```
form
 ├─ select#language (Language) — mặc định "English"
 ├─ input[type=text]  (Email Address)
 ├─ input[type=password] (Password)
 ├─ input[type=checkbox] (Remember me)
 ├─ button[type=submit] "Login"
 └─ a href="/authentication/forgot_password" "Forgot Password?"
```

### 6.2 Thông báo hệ thống ghi nhận được
- `The Email Address field is required.`
- `The Password field is required.`
- `The Email Address field must contain a valid email address.`
- `Invalid username or password` (hiển thị dạng toast notification góc trên bên phải màn hình)

### 6.3 Thông tin môi trường khảo sát
- Ngày khảo sát: 18/09/2026
- URL: https://crm.anhtester.com/authentication/login
- Tài khoản thử nghiệm: `admin@example.com` / `12345` → **Đăng nhập thất bại** (Invalid username or password)

---

*Tài liệu được sinh tự động dựa trên khảo sát thực tế giao diện đăng nhập tại `crm.anhtester.com`. Cần review lại với BA/PO và đối chiếu với tài liệu nghiệp vụ gốc (nếu có) trước khi dùng làm baseline chính thức.*
