# Bài 1: Xử lý cảnh báo đăng nhập giả mạo GitHub

## 1. Phát hiện dấu hiệu giả mạo

Đường link trong thông báo là `https://githúb.com/login`, không phải địa chỉ đăng nhập chính thức `https://github.com/login`.

Điểm bất thường nằm ở chữ **`ú`** trong `githúb.com`. Đây là ký tự Unicode có dấu (U+00FA), trong khi tên miền thật dùng chữ **`u`** bình thường. Hai địa chỉ trông khá giống nhau nên khi đọc lướt, nhất là lúc thấy thông báo “Security Alert” có vẻ khẩn cấp, người dùng rất dễ nhầm lẫn.

Đây là một dạng **Homograph Attack**: kẻ tấn công sử dụng ký tự có hình dáng gần giống ký tự quen thuộc để tạo tên miền giả, từ đó dụ người dùng nhập thông tin đăng nhập.

## 2. Cách kiểm tra URL trước khi đăng nhập

Nếu nhận được cảnh báo như trên, em sẽ làm theo các bước sau:

1. **Không nhấp ngay vào liên kết** trong email hoặc tin nhắn, dù nội dung nói tài khoản đang gặp nguy hiểm.
2. **Nhìn kỹ tên miền** trên thanh địa chỉ, chú ý chữ có dấu, ký tự lạ, chữ bị thay thế hoặc phần đuôi tên miền. Ví dụ: `githúb.com` khác hoàn toàn `github.com`.
3. **Tự gõ `https://github.com`** hoặc mở GitHub từ bookmark đã lưu, rồi kiểm tra thông báo bảo mật trực tiếp trong tài khoản.
4. **Dùng công cụ hỗ trợ khi còn nghi ngờ:** có thể sao chép URL dưới dạng văn bản để kiểm tra bằng Google Safe Browsing, VirusTotal hoặc công cụ phân tích Unicode/IDN. Không nhập mật khẩu vào những trang đang kiểm tra.
5. **Chỉ đăng nhập sau khi đã xác minh đúng trang.** Nếu đã lỡ nhập mật khẩu ở trang giả, cần đổi mật khẩu trên trang GitHub thật, kiểm tra phiên đăng nhập và bật xác thực hai yếu tố (2FA).

**Lưu ý:** Biểu tượng ổ khóa hoặc `https://` chỉ cho biết kết nối được mã hóa, không chứng minh trang đó là GitHub chính thức. **Master Password của trình quản lý mật khẩu không được nhập vào trang đăng nhập GitHub**, kể cả trang thật; GitHub chỉ nên nhận phương thức xác thực dành riêng cho tài khoản GitHub.

## 3. Bổ sung cho đoạn mã kiểm tra URL

Nếu chỉ kiểm tra URL có chứa chữ `github` hoặc kết thúc bằng `/login` thì rất dễ bỏ sót trang giả. Nên lấy đúng **tên miền (hostname)** và so sánh với địa chỉ được tin cậy.

```python
from urllib.parse import urlsplit

url = "https://githúb.com/login"
parts = urlsplit(url)

if parts.scheme == "https" and parts.hostname == "github.com":
    print("Đúng tên miền GitHub")
else:
    print("Cảnh báo: URL không thuộc tên miền GitHub chính thức")
```

**Kết quả:** `Cảnh báo: URL không thuộc tên miền GitHub chính thức`.

