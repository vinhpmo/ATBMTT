
![Đại Nam](a0a697ea-ba38-4314-bf3e-84e2a2cd5a6c.png)

# Hệ thống chia sẻ file nội bộ an toàn – Đề tài nhóm 10: Truyền dữ liệu y tế có bảo mật

## 🌐 Giới thiệu chung

Hệ thống này hỗ trợ truyền file dữ liệu y tế giữa các bộ phận trong cùng một bệnh viện một cách **bảo mật – chính xác – toàn vẹn**. File sẽ được nén, mã hóa, và xác thực trước khi gửi qua mạng nội bộ nhằm đảm bảo **không rò rỉ dữ liệu nhạy cảm**.

Mô hình thích hợp cho bệnh viện, phòng khám hay tổ chức y tế yêu cầu chia sẻ dữ liệu bệnh án, xét nghiệm, kết quả chẩn đoán hình ảnh giữa các bộ phận/khoa.

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
![Sender](b7bb31a3-986d-4a0e-869f-bb08c635f9d0.png)

### 4. Nhận file (vai trò người nhận)

```bash
python receive.py
```

> File giải mã được lưu ở `data/output/`.

#### Ảnh minh họa vai trò Người nhận:
![Receiver](34482f58-7c72-4346-a52f-3c80b57f1a4a.png)

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

## Người gửi
![Người gửi](images/sender.png)

## Người nhận
![Người nhận](images/receiver.png)

## Ví dụ phản hồi ACK
![ACK](images/ack.png)

## Ví dụ phản hồi NACK
![NACK](images/nack.png)


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
