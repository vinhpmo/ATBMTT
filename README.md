
![Đại Nam](Images/dn.jpg)

# Hệ thống chia sẻ file nội bộ an toàn – Đề tài nhóm 10: Truyền dữ liệu y tế có bảo mật

## 🌐 Giới thiệu chung
Đây là một hệ thống bảo mật cho quá trình truyền file báo cáo tài chính giữa hai bên thông qua mạng Internet. Nội dung file được nén để giảm kích thước, sau đó mã hóa bằng thuật toán AES-GCM đảm bảo tính bảo mật và toàn vẹn dữ liệu. Danh tính của người gửi và người nhận được xác thực bằng chữ ký số RSA 1024-bit, đồng thời sử dụng hàm băm SHA-512 để kiểm tra toàn vẹn nội dung file.

Hệ thống mô phỏng theo mô hình thực tế khi một tổ chức cần gửi file dữ liệu tài chính quan trọng qua mạng với yêu cầu đảm bảo bí mật – toàn vẹn – xác thực trong toàn bộ quá trình truyền tải

---

## 🔧 Công nghệ sử dụng

| Thành phần | Giải pháp |
|------------|----------|
| Nén dữ liệu | zlib |
| Mã hóa đối xứng | AES-256 GCM |
| Ký số xác thực | RSA 2048-bit |
| Kiểm tra toàn vẹn | SHA-256 |
| Giao tiếp | Giao diện dòng lệnh (CLI) |
| Quản lý khóa | Bộ khóa công khai/riêng tư (PKI) |

---

## 🚦 Quy trình hoạt động

### Bước 1: Bắt tay (Handshake)
- Sender: "Xin gửi dữ liệu!"
- Receiver: "Sẵn sàng nhận!"

### Bước 2: Chuẩn bị & ký dữ liệu
- Metadata bao gồm tên file, loại file, thời gian gửi.
- Tạo chữ ký số và mã hóa khóa phiên bằng RSA.

### Bước 3: Nén & mã hóa
- Nén file gốc bằng zlib.
- Mã hóa bằng AES-GCM với nonce sinh ngẫu nhiên.
- Sinh mã hash SHA-256 cho gói dữ liệu.

### Bước 4: Gửi gói tin
Gói JSON gửi bao gồm:

```json
{
  "cipher_data": "...",
  "nonce": "...",
  "tag": "...",
  "enc_key": "...",
  "signature": "...",
  "metadata": "...",
  "hash": "..."
}
```

### Bước 5: Nhận & xác thực
- Kiểm tra chữ ký số và mã hash.
- Giải mã khóa phiên & dữ liệu.
- Giải nén và lưu kết quả file.
- Xác minh thời gian gửi để chống lại tấn công phát lại (replay).

---

## ▶️ Hướng dẫn sử dụng

### 1. Cài đặt môi trường Python

```bash
pip install pycryptodome
```

### 2. Tạo khóa RSA

```bash
python create_keys.py
```

### 3. Gửi file (vai trò người gửi)

```bash
python send.py
```

> Đảm bảo file cần gửi nằm trong thư mục `data/input/`.

#### Ảnh minh họa vai trò Người gửi:
![Sender](Images/Sender.jpg)

### 4. Nhận file (vai trò người nhận)

```bash
python receive.py
```

> File giải mã được lưu ở `data/output/`.

#### Ảnh minh họa vai trò Người nhận:
![Receiver](Images/Receiver.jpg)

---

## 🛠 Xử lý lỗi

| Tình huống | Hành động |
|-----------|-----------|
| Sai handshake | Gửi lại yêu cầu kết nối đúng định dạng |
| Sai chữ ký | Kiểm tra khóa công khai |
| Sai hash | Gói tin bị sửa đổi – từ chối nhận |
| Timestamp lỗi | Từ chối nhận – nghi ngờ tấn công phát lại |
| Thành công | File lưu thành công, phản hồi "ACK" |

---

## Ví dụ phản hồi ACK
![ACK](Images/ACK.jpg))

## Ví dụ phản hồi NACK
![NACK](Images/Nack.jpg)


## 💡 Mục tiêu

- An toàn trong truyền dữ liệu nội bộ y tế
- Tránh thất thoát dữ liệu nhạy cảm
- Giảm dung lượng truyền nhờ nén
- Bảo đảm tính toàn vẹn và xác thực

---

## 👨‍💻 Tác giả

- Nguyễn Thế Vinh  
- Nhóm 10  
- Khoa: Công nghệ Thông tin  
- Trường: Đại học Đại Nam  
