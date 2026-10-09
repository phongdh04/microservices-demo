# CHƯƠNG TRÌNH ĐÀO TẠO KUBERNETES TỪ ZERO ĐẾN SENIOR PLATFORM ENGINEER

Chào mừng bạn đến với kho tài liệu học tập Kubernetes thực chiến. Toàn bộ tài liệu, giáo trình và bài tập trong thư mục này được thiết kế theo chuẩn vận hành production của Principal Platform Engineer và bám sát kiến thức 3 kỳ thi: **CKA (Administrator)**, **CKAD (Application Developer)**, và **CKS (Security Specialist)**.

---

## 📌 QUY TẮC BẮT BUỘC KHI VIẾT BÀI HỌC (8 NGUYÊN TẮC VÀNG)

Mỗi bài học được tạo ra (lưu tại thư mục `LESSON/`) bắt buộc phải tuân thủ nghiêm ngặt 8 quy tắc sau:

1. **Ngôn ngữ:** Tiếng Việt. Giữ nguyên thuật ngữ tiếng Anh chuyên ngành (Pod, Deployment, Service, ConfigMap...) nhưng **luôn giải thích cặn kẽ ở lần đầu xuất hiện**.
2. **Văn phong & Sư phạm:** Viết như đang hướng dẫn một intern chưa biết gì. Câu ngắn, rõ ràng, trực diện, không giả định người học đã biết trước. **Mỗi khái niệm mới PHẢI có ẨN DỤ đời thường trước, sau đó mới đi vào định nghĩa kỹ thuật.**
3. **Bảng thuật ngữ:** Bắt buộc đặt ngay đầu bài học gồm 3 cột: `Thuật ngữ | Giải thích đơn giản | Ví dụ / Ẩn dụ đời thường`. Liệt kê toàn bộ các từ khóa quan trọng xuất hiện trong bài.
4. **Lệnh & YAML thực chiến:** Toàn bộ lệnh và manifest YAML phải **chạy được thật trên môi trường thực tế** (KinD / Minikube), kèm chú thích giải thích từng dòng quan trọng và kết quả mong đợi (`Expected Output`). Sử dụng phiên bản Kubernetes ổn định (v1.30+).
5. **Không nhảy cóc:** Tuyệt đối không dùng khái niệm chưa được dạy ở các bài trước hoặc chưa được giải thích trong chính bài đó.
6. **Thực tế trước, lý thuyết sau:** Ưu tiên trả lời: *"Tại sao cần cái này?"*, *"Nếu không có nó thì hệ thống production sẽ gặp thảm họa gì lúc nửa đêm?"*.
7. **Góc nhìn Senior:** Mỗi bài bắt buộc có mục phân tích chuyên sâu:
   - Đánh đổi (Trade-offs): Được gì và mất gì?
   - Best practices tại production.
   - Câu hỏi phỏng vấn thực tế cấp Senior/Lead kèm định hướng trả lời.
8. **Quy tắc tạo bài:** Chỉ tạo **MỘT bài học duy nhất** cho mỗi lần yêu cầu. Không viết gộp hoặc tự ý tạo trước các bài sau.

---

## 📂 CẤU TRÚC THƯ MỤC THAM CHIẾU

```text
LEARN-KUBERNETES/
├── README.md               # Tài liệu tổng quan & 8 quy tắc bắt buộc (File này)
├── SYLLABUS.md             # Toàn bộ lộ trình 10 giai đoạn - 56 bài học chi tiết
├── LESSON_TEMPLATE.md      # Template cấu trúc chuẩn 11 phần dùng để prompt tạo bài
└── LESSON/                 # Nơi lưu trữ từng bài học chi tiết
    ├── Bai-01-Linux-Namespaces-Cgroups.md
    ├── Bai-02-Container-Image-Layers-CRI.md
    └── ...
```

---

## 🎯 CÁC MỐC ĐÁNH GIÁ NĂNG LỰC (MILESTONES)

* 🟢 **JUNIOR KUBERNETES DEVELOPER (Sau Giai đoạn 1 – 3: Bài 1 đến Bài 17):**
  - Nắm vững bản chất container, viết YAML chuẩn chỉ.
  - Tự tin đóng gói, deploy, cấu hình biến môi trường, gắn storage cho ứng dụng.
  - Đủ năng lực đạt ~70% bài thi thực hành **CKAD**.

* 🟡 **MID-LEVEL DEVOPS / K8S ADMIN (Sau Giai đoạn 4 – 7: Bài 18 đến Bài 42):**
  - Làm chủ Network, Ingress/Gateway API, NetworkPolicy Zero-Trust.
  - Quản lý tài nguyên, autoscaling (HPA/VPA), lập lịch nâng cao (Affinity, Taints).
  - Tăng cường bảo mật RBAC, Pod Security Standards; làm chủ hệ thống Observability (Prometheus, Grafana, Loki) và phương pháp luận troubleshooting đa tầng.
  - Đủ năng lực thi đỗ **CKA** và **CKAD**; sẵn sàng cho **CKS**.

* 🔴 **SENIOR PLATFORM / SRE ENGINEER (Sau Giai đoạn 8 – 10: Bài 43 đến Bài 56):**
  - Thiết kế kiến trúc HA, Multi-cluster, Disaster Recovery (Backup/Restore etcd), Zero-downtime upgrade.
  - Chuẩn hóa GitOps (ArgoCD), Helm/Kustomize, tối ưu chi phí hạ tầng (FinOps).
  - Mở rộng Kubernetes API: Admission Webhooks (Kyverno), CRD & tự viết Kubernetes Operator bằng Golang. Triển khai Service Mesh.
  - Hoàn thành Capstone Project quy mô Production Enterprise; sẵn sàng thi đỗ **CKS**.

---

## 🚀 CÁCH SỬ DỤNG KHI PROMPT TẠO BÀI HỌC MỚI

Khi bạn muốn bắt đầu một bài học mới, hãy prompt theo mẫu sau:

> *"Dựa trên lộ trình tại `SYLLABUS.md`, quy tắc tại `README.md` và khung cấu trúc chuẩn tại `LESSON_TEMPLATE.md`, hãy viết nội dung chi tiết cho **[Bài X: Tên bài học]** và lưu vào thư mục `LESSON/`."*

