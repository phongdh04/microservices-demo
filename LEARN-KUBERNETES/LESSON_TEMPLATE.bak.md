# TEMPLATE CHUẨN SOẠN BÀI HỌC KUBERNETES (11 PHẦN)

> **Ghi chú dành cho AI / Giảng viên:** Mỗi bài học được viết ra bắt buộc phải bám sát 100% khung cấu trúc dưới đây, không được bỏ bớt phần nào và tuân thủ đúng 8 nguyên tắc vàng tại `README.md`.

---

# [TÊN BÀI HỌC IN HOA]: Bài X - ...

## PHẦN 1: THÔNG TIN TỔNG QUAN
* **Tên bài:** Bài X: [Tên bài học]
* **Mục tiêu học tập:** [1–2 câu nêu rõ sau bài này người học tự tay làm được gì và hiểu được bản chất gì]
* **Thời lượng ước tính:** [X giờ / phút (gồm đọc lý thuyết và gõ lab)]
* **Kiến thức cần có trước:** [Liệt kê các bài học tiên quyết hoặc kiến thức nền]
* **Phạm vi kỳ thi:** [CKA / CKAD / CKS / SRE Production]

---

## PHẦN 2: BẢNG THUẬT NGỮ (GLOSSARY)
*(Bắt buộc đặt ngay đầu bài. Liệt kê toàn bộ các thuật ngữ mới, từ khóa tiếng Anh sẽ xuất hiện trong bài)*

| Thuật ngữ | Giải thích đơn giản (Plain Vietnamese) | Ví dụ / Ẩn dụ đời thường |
| :--- | :--- | :--- |
| **[Thuật ngữ 1]** | [Giải thích ngắn gọn, dễ hiểu, không hàn lâm] | [Ví dụ thực tế gần gũi trong đời sống] |
| **[Thuật ngữ 2]** | [Giải thích ngắn gọn, dễ hiểu, không hàn lâm] | [Ví dụ thực tế gần gũi trong đời sống] |
| **[Thuật ngữ 3]** | [Giải thích ngắn gọn, dễ hiểu, không hàn lâm] | [Ví dụ thực tế gần gũi trong đời sống] |

---

## PHẦN 3: BỨC TRANH LỚN (THE BIG PICTURE)
### 1. Nỗi đau thực tế ở Production (Tại sao cần?)
* [Mô tả tình huống thực tế: Nếu không có thành phần/tính năng này thì hệ thống sẽ gặp sự cố gì lúc nửa đêm?]
* [Những khó khăn nếu phải tự làm bằng tay hoặc dùng công cụ truyền thống.]

### 2. Ẩn dụ đời thường (Real-world Analogy)
* [Câu chuyện ví von đời thực (nhà hàng, bến cảng, giao thông, căn hộ, công ty...) giúp một người hoàn toàn mới hình dung trực quan cách đối tượng này hoạt động mà không bị choáng ngợp bởi thuật ngữ.]

---

## PHẦN 4: BẢN CHẤT KỸ THUẬT & CƠ CHẾ HOẠT ĐỘNG
### 1. Định nghĩa kỹ thuật chính xác
* [Định nghĩa chuẩn xác, cơ chế hoạt động bên dưới nắp ca-pô (under the hood).]

### 2. Sơ đồ kiến trúc & Luồng xử lý
```mermaid
[Vẽ sơ đồ luồng dữ liệu, tương tác giữa các thành phần bằng cú pháp Mermaid hoặc ASCII rõ ràng]
```

### 3. Giải phẫu cấu trúc cấu hình (YAML / Specs / Flags)
* [Trình bày cấu trúc khai báo hoặc câu lệnh cốt lõi.]
* [Giải thích chi tiết từng trường quan trọng, kiểu dữ liệu và ý nghĩa thực tế.]

---

## PHẦN 5: THỰC HÀNH LAB (HANDS-ON LAB)
* **Môi trường yêu cầu:** KinD / Minikube (Kubernetes v1.30+).
* **Mục tiêu Lab:** [Mô tả kết quả cụ thể người học sẽ đạt được sau khi chạy xong Lab].

### Bước 1: Chuẩn bị file cấu hình
*(Kèm chú thích giải thích trực tiếp trên các dòng cấu hình quan trọng)*
```yaml
# Ví dụ manifest YAML hoàn chỉnh
apiVersion: ...
kind: ...
metadata:
  name: ...
spec:
  ...
```

### Bước 2: Thực thi câu lệnh
```bash
# Câu lệnh chạy thực tế
kubectl apply -f ...
```

### Bước 3: Kết quả mong đợi (Expected Output)
```text
# Đầu ra chuẩn từ terminal để người học đối chiếu
pod/my-app created
```

### Bước 4: Kiểm chứng tính năng hoạt động
```bash
# Lệnh kiểm tra hành vi hệ thống
kubectl get ...
```

### Bước 5: Dọn dẹp môi trường (Cleanup)
```bash
# Lệnh xóa sạch tài nguyên lab để tránh tốn RAM/CPU cluster
kubectl delete -f ...
```

---

## PHẦN 6: LỖI PHỔ BIẾN & PHÁC ĐỒ CHẨN ĐOÁN
*(Những cái bẫy người mới chắc chắn sẽ gặp phải)*

### 1. Lỗi: [Tên lỗi hoặc triệu chứng thường gặp]
* **Dấu hiệu nhận biết:** [Thông báo lỗi từ terminal / describe / logs]
* **Nguyên nhân gốc rễ:** [Tại sao lỗi xảy ra?]
* **Cách khắc phục dứt điểm:** [Lệnh hoặc thao tác sửa đúng]

### 2. Lỗi: [Tên lỗi hoặc triệu chứng thứ hai]
* **Dấu hiệu nhận biết:** ...
* **Nguyên nhân gốc rễ:** ...
* **Cách khắc phục dứt điểm:** ...

---

## PHẦN 7: GÓC NHÌN SENIOR (PRODUCTION INSIGHTS)
### 1. Sự đánh đổi (Trade-offs)
* [Được gì và mất gì khi sử dụng giải pháp này? Không có giải pháp nào là "viên đạn bạc".]
* [Khi nào NÊN dùng và khi nào TUYỆT ĐỐI KHÔNG NÊN dùng?]

### 2. Best Practices tại Production
* [Quy chuẩn đặt tên (Naming convention), phân bổ tài nguyên, cấu hình HA, bảo mật thực tế.]

### 3. Câu hỏi phỏng vấn thực chiến (Senior/Lead Interview)
* **Câu hỏi:** [Câu hỏi tình huống hóc búa hay gặp trong phỏng vấn]
* **Gợi ý trả lời chuẩn:** [Định hướng cách trả lời thể hiện tư duy thiết kế và kinh nghiệm vận hành]

---

## PHẦN 8: TÓM TẮT BÀI HỌC (KEY TAKEAWAYS)
*(Đúng 5 gạch đầu dòng cô đọng nhất)*
* 📌 **1.** [Ý cốt lõi 1]
* 📌 **2.** [Ý cốt lõi 2]
* 📌 **3.** [Ý cốt lõi 3]
* 📌 **4.** [Ý cốt lõi 4]
* 📌 **5.** [Ý cốt lõi 5]

---

## PHẦN 9: CÂU HỎI TỰ KIỂM TRA (SELF-CHECK QUIZ)
*(5–8 câu hỏi đào sâu bản chất để kiểm tra mức độ tiếp thu)*
1. [Câu hỏi 1?]
2. [Câu hỏi 2?]
3. [Câu hỏi 3?]
4. [Câu hỏi 4?]
5. [Câu hỏi 5?]

<details>
<summary>👉 Bấm vào đây để xem đáp án gợi ý</summary>

* **Đáp án 1:** ...
* **Đáp án 2:** ...
* **Đáp án 3:** ...
* **Đáp án 4:** ...
* **Đáp án 5:** ...
</details>

---

## PHẦN 10: BÀI TẬP TỰ LUYỆN (HANDS-ON CHALLENGES)
* 🟢 **Mức Dễ (Warm-up):** [Bài tập củng cố cú pháp và thao tác cơ bản].
* 🟡 **Mức Vừa (Application):** [Tình huống triển khai có thêm biến số thực tế].
* 🔴 **Mức Khó (Production Edge-case):** [Tình huống khắc phục sự cố hoặc tối ưu nâng cao].

<details>
<summary>👉 Bấm vào đây để xem gợi ý giải pháp bài tập</summary>

* **Gợi ý Mức Dễ:** ...
* **Gợi ý Mức Vừa:** ...
* **Gợi ý Mức Khó:** ...
</details>

---

## PHẦN 11: TÀI LIỆU THAM KHẢO & ĐỌC THÊM
* [Tài liệu chính thức Kubernetes Docs: URL]
* [Mã nguồn mở / KEP / Blog kỹ thuật kiến trúc chuyên sâu: URL]

