<!--
GHI CHÚ QUAN TRỌNG KHI VIẾT BÀI:
- Độ dài bài viết: khoảng 1500 - 2500 từ (không tính khối mã nguồn/YAML/command output).
- Bắt buộc tuân thủ 8 quy tắc tại README.md và bối cảnh máy (Windows/PowerShell, WSL 4GB RAM, ổ D, kind 1 control-plane + 1 worker).
- Tất cả lệnh thực thi dùng cú pháp PowerShell trên Windows, đường dẫn dấu \.
-->

# Bài NN: [Tên bài học]

## 1. Thông tin bài học
<!-- 
Ghi rõ:
- Tên bài: Bài NN: [Tên bài học]
- Mục tiêu học: 1-2 câu ngắn gọn, cụ thể người học làm được gì sau bài.
- Thời lượng: ước tính phút/giờ (bao gồm đọc và gõ lab).
- Kiến thức cần có: các bài trước đó hoặc kiến thức nền tối thiểu.
-->
* **Tên bài:** Bài NN: ...
* **Mục tiêu học:** ...
* **Thời lượng ước tính:** ... phút
* **Kiến thức cần có trước:** ...
* **Liên quan kỳ thi:** CKA / CKAD / CKS (nếu có)

---

## 2. Bảng thuật ngữ
<!-- 
Đặt ngay đầu bài, liệt kê mọi thuật ngữ mới, từ khó sẽ gặp trong bài.
Bắt buộc có 3 cột: Thuật ngữ | Giải thích đơn giản | Ví dụ/ẩn dụ.
Giải thích đơn giản như nói với intern, ví dụ đời thường dễ liên tưởng.
-->

| Thuật ngữ | Giải thích đơn giản | Ví dụ / Ẩn dụ đời thường |
| :--- | :--- | :--- |
| **[Thuật ngữ 1]** | [Giải thích ngắn gọn] | [Ví dụ / Ẩn dụ đời thường] |
| **[Thuật ngữ 2]** | [Giải thích ngắn gọn] | [Ví dụ / Ẩn dụ đời thường] |
| **[Thuật ngữ 3]** | [Giải thích ngắn gọn] | [Ví dụ / Ẩn dụ đời thường] |

---

## 3. Bức tranh lớn: Vấn đề thực tế và ẩn dụ đời thường
<!-- 
Mục này gồm 3 ý chính:
1. Nhắc lại bài trước: 2-3 dòng tóm tắt bài trước đã giải quyết điều gì và bài này nối tiếp ra sao. (Bài 01 thì nhắc bối cảnh ban đầu).
2. Vấn đề thực tế: Tại sao cần cái này? Nếu không có nó thì hệ thống production sẽ gặp tai họa gì lúc nửa đêm?
3. Ẩn dụ đời thường: Một câu chuyện ví von đời sống (nhà hàng, giao thông, căn hộ chung cư...) giải thích trực quan cách thức hoạt động.
-->

### Nhắc lại bài trước
...

### Tại sao cần cái này ở production? (Vấn đề thực tế)
...

### Ẩn dụ đời thường
...

---

## 4. Giải thích khái niệm theo từng bước
<!-- 
- Giải thích kỹ thuật chính xác nhưng không dùng thuật ngữ chưa học (không nhảy cóc).
- Chia nhỏ thành từng bước logic.
- Sử dụng sơ đồ Mermaid hoặc ASCII mô tả luồng đi của request, kiến trúc hoặc tiến trình.
- Giải thích cú pháp cấu hình hoặc lệnh quan trọng: giải thích cặn kẽ từng dòng, ý nghĩa các trường.
-->

### Cơ chế hoạt động
...

```mermaid
flowchart TD
    %% Sơ đồ trực quan hóa khái niệm
```

### Phân tích chi tiết
...

---

## 5. Thực hành (Lab)
<!-- 
Mọi lab phải chạy được thật trên cụm kind (1 control-plane + 1 worker, file k8s/kind-config.yaml).
- BẮT BUỘC ghi rõ mức RAM cần (đảm bảo không vượt quá 4GB WSL).
- Sử dụng service trong Google Online Boutique (microservices-demo) khi phù hợp.
- Lệnh PowerShell chuẩn trên Windows (dùng dấu \ cho đường dẫn).
- Cung cấp file YAML đầy đủ có comment giải thích từng dòng quan trọng.
- Cung cấp kết quả mong đợi (Expected Output) thật.
- Bắt buộc có bước dọn dẹp (Cleanup) giải phóng RAM sau khi xong lab.
-->

* **Môi trường:** Cluster kind (`k8s\kind-config.yaml`)
* **Mức RAM ước tính:** ~... MB (trong giới hạn 4GB WSL)

### Bước 1: Chuẩn bị file cấu hình
```yaml
# Nội dung file YAML kèm comment giải thích từng dòng quan trọng
```

### Bước 2: Thực thi câu lệnh
```powershell
# Lệnh PowerShell thực thi
```

### Bước 3: Kết quả mong đợi (Expected Output)
```text
# Output chính xác trả về từ terminal
```

### Bước 4: Kiểm chứng tính năng
```powershell
# Câu lệnh kiểm tra xác nhận tính năng chạy đúng
```

### Bước 5: Dọn dẹp tài nguyên (Cleanup)
```powershell
# Lệnh xóa tài nguyên để tránh đầy RAM
```

---

## 6. Lỗi thường gặp và cách debug
<!-- 
Nêu 2-3 lỗi kinh điển mà người mới hay gặp phải ở phần này.
Mỗi lỗi gồm:
- Tên lỗi / Dấu hiệu nhận biết (output lỗi khi describe/logs)
- Nguyên nhân gốc rễ
- Lệnh chẩn đoán và cách sửa dứt điểm
-->

### Lỗi 1: [Tên lỗi hoặc triệu chứng]
* **Dấu hiệu:** ...
* **Nguyên nhân:** ...
* **Cách debug và sửa:** ...

### Lỗi 2: [Tên lỗi hoặc triệu chứng]
* **Dấu hiệu:** ...
* **Nguyên nhân:** ...
* **Cách debug và sửa:** ...

---

## 7. Góc nhìn Senior
<!-- 
Mục tiêu: Đưa tư duy người học lên tầm Senior/Platform Engineer.
Gồm 3 phần:
1. Đánh đổi (Trade-offs): Được gì và mất gì? Khi nào nên dùng, khi nào không nên dùng?
2. Best practice production: Quy chuẩn thực tế (tài nguyên, bảo mật, vận hành tải lớn).
3. Câu hỏi phỏng vấn: 1-2 câu hỏi phỏng vấn cấp Senior kèm câu trả lời chuẩn.
-->

### 1. Sự đánh đổi (Trade-offs)
...

### 2. Best practices tại production
...

### 3. Câu hỏi phỏng vấn Senior
* **Câu hỏi:** ...
* **Gợi ý trả lời chuẩn:** ...

---

## 8. Tóm tắt bài học
<!-- 
Đúng 5 gạch đầu dòng cô đọng nhất để ghi nhớ lâu, không viết lan man.
-->
* 📌 **1.** ...
* 📌 **2.** ...
* 📌 **3.** ...
* 📌 **4.** ...
* 📌 **5.** ...

---

## 9. Bài tập tự làm
<!-- 
3 mức độ rõ ràng:
- Mức Dễ: Củng cố cú pháp và thao tác cơ bản.
- Mức Vừa: Áp dụng vào kịch bản thực tế có thêm tham số.
- Mức Khó: Tình huống edge-case, xử lý sự cố hoặc tối ưu.
LƯU Ý: Đáp án gợi ý KHÔNG đặt ở đây mà đặt ở cuối bài trong mục riêng bên dưới.
-->

* 🟢 **Mức Dễ:** ...
* 🟡 **Mức Vừa:** ...
* 🔴 **Mức Khó:** ...

---

## 10. Câu hỏi tự kiểm tra
<!-- 
5 đến 8 câu hỏi kiểm tra độ hiểu sâu (trắc nghiệm hoặc câu hỏi ngắn).
Đáp án và giải thích ngắn gọn đặt trong thẻ <details> ngay sau các câu hỏi.
-->

1. ...
2. ...
3. ...
4. ...
5. ...

<details>
<summary>👉 Bấm vào đây để xem đáp án câu hỏi tự kiểm tra</summary>

* **Câu 1:** ...
* **Câu 2:** ...
* **Câu 3:** ...
* **Câu 4:** ...
* **Câu 5:** ...
</details>

---

## 11. Tài liệu đọc thêm và bài tiếp theo
<!-- 
- Tài liệu đọc thêm: Ưu tiên link chính thức kubernetes.io.
- Tên bài tiếp theo: CHỈ ghi tên bài, KHÔNG viết nội dung.
-->

### Tài liệu tham khảo
* [Tài liệu chính thức Kubernetes Docs](https://kubernetes.io/docs/)
* ...

### Bài tiếp theo
👉 **Bài NN+1: [Tên bài học tiếp theo]**

---

## PHỤ LỤC: ĐÁP ÁN GỢI Ý BÀI TẬP TỰ LÀM (MỤC 9)
<!-- 
Đặt riêng ở cuối bài học để người học tự làm trước khi xem đáp án.
-->
### Đáp án Mức Dễ
...

### Đáp án Mức Vừa
...

### Đáp án Mức Khó
...
