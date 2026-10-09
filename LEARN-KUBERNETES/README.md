# HƯỚNG DẪN HỌC TẬP VÀ VẬN HÀNH KUBERNETES

## 1. Mục tiêu
Học Kubernetes từ con số 0 đến trình độ Senior: thiết kế, vận hành, bảo mật và debug cluster production, đủ nền tảng để thi CKA/CKAD/CKS.

## 2. Bối cảnh máy (mọi lab phải tuân thủ)
- Máy cá nhân Windows, RAM 8GB, 6 nhân/12 luồng. WSL giới hạn `memory=4GB`.
- Docker Desktop (WSL 2), dữ liệu Docker nằm ở ổ D. Ổ C gần đầy: tránh hướng dẫn tải/cài thứ nặng vào ổ C.
- Công cụ: `docker`, `kubectl` v1.36.x, `kind` 0.33.x. Terminal: PowerShell.
- Cluster lab: `kind`, 1 control-plane + 1 worker (sử dụng file `k8s/kind-config.yaml`).
- Dự án thực hành xuyên suốt: Google Online Boutique (thư mục `microservices-demo`), chạy từng phần nhỏ, không chạy đủ 11 service cùng lúc.
- Giai đoạn nặng RAM (monitoring, service mesh) phải có phiên bản rút gọn chạy được trong giới hạn trên.
- Tên file/đường dẫn trong lệnh dùng cú pháp Windows/PowerShell (ví dụ: `.\k8s\kind-config.yaml`, `d:\LEARN\...`).

## 3. Quy tắc bắt buộc cho mọi bài học
1. **Ngôn ngữ:** Tiếng Việt. Giữ nguyên thuật ngữ tiếng Anh (Pod, Deployment...) nhưng giải thích lần đầu xuất hiện.
2. **Văn phong cho intern chưa biết gì:** Câu ngắn, dễ hiểu, không giả định kiến thức trước. Mỗi khái niệm mới có **ẨN DỤ đời thường trước**, rồi mới đến định nghĩa kỹ thuật.
3. **Bảng thuật ngữ:** Mỗi bài PHẢI có "Bảng thuật ngữ" gồm các cột: `Thuật ngữ | Giải thích đơn giản | Ví dụ/ẩn dụ`. Đặt ngay đầu bài, liệt kê mọi từ khó sẽ gặp trong bài.
4. **Lệnh và YAML chạy thật:** Phải chạy được thật, kèm giải thích từng dòng quan trọng và kết quả mong đợi (expected output). Dùng phiên bản Kubernetes ổn định hiện tại; nếu không chắc về API/tính năng mới thì ghi rõ để người học đối chiếu kubernetes.io.
5. **Không nhảy cóc:** Không dùng khái niệm chưa dạy ở bài trước hoặc chưa giải thích trong bài này.
6. **Thực tế trước, lý thuyết sau:** Ưu tiên *"tại sao cần cái này"*, *"nếu không có thì chuyện gì xảy ra"*.
7. **Góc nhìn Senior:** Mỗi bài có phần "Góc nhìn Senior": lỗi phổ biến, đánh đổi (trade-off), best practice production, câu hỏi phỏng vấn.
8. **Viết từng bài:** Mỗi lần chỉ viết MỘT bài, không viết trước các bài sau.

## Quy trình viết bài
Khi tạo bài học mới, thực hiện đúng các bước sau:
1. Đọc kỹ `README.md`, `LESSON_TEMPLATE.md`, và `SYLLABUS.md`.
2. Chọn bài có trạng thái `[ ]` đầu tiên trong `SYLLABUS.md`.
3. Soạn nội dung bài học theo đúng khung chuẩn 11 phần tại `LESSON_TEMPLATE.md`.
4. Lưu bài học vào file: `LESSON\NN-ten-bai-khong-dau.md` (ví dụ: `LESSON\01-linux-namespaces-cgroups.md`).
5. Cập nhật `SYLLABUS.md`: đổi `[ ]` của bài vừa hoàn thành thành `[x]`.
