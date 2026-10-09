# LỘ TRÌNH ĐÀO TẠO KUBERNETES TOÀN DIỆN (SYLLABUS)
### Từ Con Số 0 Đến Senior Platform / SRE Engineer (Chuẩn CKA, CKAD, CKS)

---

## TỔNG QUAN CÁC GIAI ĐOẠN

```mermaid
flowchart TD
    G1["Giai đoạn 1: Nền tảng Container & Hạ tầng (Bài 1 - 4)"] --> G2["Giai đoạn 2: Kiến trúc & Đối tượng cốt lõi (Bài 5 - 11)"]
    G2 --> G3["Giai đoạn 3: Cấu hình & Lưu trữ dữ liệu (Bài 12 - 17)"]
    G3 -->|Mốc JUNIOR| G4["Giai đoạn 4: Mạng nâng cao trong K8s (Bài 18 - 23)"]
    G4 --> G5["Giai đoạn 5: Lập lịch & Quản lý tài nguyên (Bài 24 - 29)"]
    G5 --> G6["Giai đoạn 6: Bảo mật & Tăng cường hệ thống (Bài 30 - 36)"]
    G6 --> G7["Giai đoạn 7: Giám sát toàn diện & Troubleshooting (Bài 37 - 42)"]
    G7 -->|Mốc MID-LEVEL (CKA/CKAD)| G8["Giai đoạn 8: Vận hành Production & Vòng đời (Bài 43 - 49)"]
    G8 --> G9["Giai đoạn 9: Nâng cao & Platform Engineering (Bài 50 - 55)"]
    G9 --> G10["Giai đoạn 10: Capstone Project Production (Bài 56)"]
    G10 -->|Mốc SENIOR PLATFORM (CKS)| Lead["Production Ready & Team Lead"]
```

---

## GIAI ĐOẠN 1: NỀN TẢNG CONTAINER & HẠ TẦNG (Bài 1 – 4)
*Mục tiêu:* Hiểu bản chất container là tiến trình Linux được cô lập; giải mã lý do Docker đơn lẻ thất bại ở quy mô lớn và sự ra đời của Kubernetes.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 1** | **Linux cơ bản cho Container: Namespace & Cgroups** | Hiểu cách Linux cô lập tiến trình bằng Namespaces và giới hạn tài nguyên bằng Cgroups. Tự tay "chế tạo" một container thô mà không cần Docker. | Dòng lệnh Linux cơ bản (bash, SSH) | Dùng lệnh `unshare`, `cgroups-v2` để tự giam một tiến trình bash vào "nhà tù" riêng. | 2.5 giờ | CKA, CKS |
| **Bài 2** | **Container & Docker từ bản chất: Image, Layer & CRI** | Nắm vững cấu trúc xếp tầng (Union FS) của Container Image và sự khác biệt giữa Docker, containerd và chuẩn CRI. | Bài 1 | Viết Dockerfile tối ưu đa tầng (multi-stage), phân tích image layer và chạy container trực tiếp bằng `nerdctl`/`crictl`. | 3 giờ | CKAD |
| **Bài 3** | **Tại sao cần Kubernetes? Bài toán Orchestration** | Hiểu những đau đớn khi tự quản lý hàng trăm container thủ công (tự hồi phục, rolling update, cân bằng tải). | Bài 2 | Dựng kịch bản mô phỏng 1 container chết lúc nửa đêm và thấy sự bế tắc nếu không có orchestrator. | 1.5 giờ | Nền tảng chung |
| **Bài 4** | **Thiết lập môi trường Lab thực chiến: KinD & `kubectl`** | Dựng cụm Kubernetes nhiều node trên máy cá nhân bằng KinD và thành thạo các câu lệnh `kubectl` đầu tiên. | Bài 2, Bài 3 | Khởi tạo cụm K8s 1 Control Plane + 2 Worker Nodes với KinD, cấu hình autocomplete và alias cho `kubectl`. | 2 giờ | CKA, CKAD |

---

## GIAI ĐOẠN 2: KIẾN TRÚC & ĐỐI TƯỢNG CỐT LÕI (Bài 5 – 11)
*Mục tiêu:* Hiểu cỗ máy bên trong Kubernetes hoạt động ra sao và làm chủ các khối gạch nền móng để chạy một ứng dụng web.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 5** | **Kiến trúc Kubernetes: Control Plane, Worker Node & Vòng lặp hòa giải** | Nắm rõ vai trò của API Server, etcd, Scheduler, Controller Manager, Kubelet và nguyên lý "Desired State vs Actual State". | Bài 4 | Theo dõi luồng dữ liệu từ khi gõ lệnh `kubectl apply` đến khi tiến trình thực sự chạy trên node. | 2.5 giờ | CKA |
| **Bài 6** | **Pod: Đơn vị tính toán nguyên tử** | Hiểu vì sao K8s không chạy container trực tiếp mà bọc trong Pod. Nắm vững vòng đời Pod và mô hình Multi-container (Sidecar pattern). | Bài 5 | Tạo Pod chạy Nginx kèm Sidecar container tự động đồng bộ file log sang thư mục chung. | 2.5 giờ | CKAD, CKA |
| **Bài 7** | **Labels, Selectors & Annotations: Xương sống định tuyến** | Hiểu cách K8s nhóm và gắn nhãn tài nguyên để các bộ điều khiển tìm thấy nhau mà không cần cấu hình cứng. | Bài 6 | Dùng `labels` và `matchLabels` để gán metadata, lọc Pod và điều hướng request có chọn lọc. | 1.5 giờ | CKAD, CKA |
| **Bài 8** | **ReplicaSet: Đảm bảo số lượng bản sao** | Hiểu lý do không bao giờ chạy Pod đơn lẻ ở production và cách ReplicaSet tự bù đắp Pod khi có sự cố. | Bài 7 | Tạo ReplicaSet với 3 replicas, thử "giết" một Pod và quan sát cơ chế tự tái sinh tức thì. | 1.5 giờ | CKAD, CKA |
| **Bài 9** | **Deployment: Nâng cấp và lùi phiên bản không downtime** | Làm chủ chiến lược Rolling Update, Rollback phiên bản khi có lỗi và tạm dừng (pause/resume) quá trình triển khai. | Bài 8 | Nâng cấp ứng dụng từ v1 lên v2 không rớt request nào; mô phỏng deploy v3 bị lỗi và rollback trong 10 giây. | 3 giờ | CKAD, CKA |
| **Bài 10** | **Service: Cầu nối mạng bền vững (ClusterIP, NodePort, LoadBalancer)** | Hiểu vì sao IP của Pod là tạm bợ và cách Service cung cấp một IP tĩnh kèm khả năng cân bằng tải nội bộ. | Bài 9 | Tạo ClusterIP để các microservices gọi nhau, tạo NodePort để truy cập từ máy ngoài vào. | 3 giờ | CKAD, CKA |
| **Bài 11** | **Namespace & ResourceQuota cơ bản: Chia sẻ cluster an toàn** | Phân chia tài nguyên hợp lý giữa các môi trường (dev, staging, prod) và đặt hạn ngạch để tránh "hàng xóm ồn ào". | Bài 10 | Tạo Namespace riêng cho team Dev, giới hạn chỉ được dùng tối đa 2 Pod và 1GB RAM. | 2 giờ | CKAD, CKA |

---

## GIAI ĐOẠN 3: CẤU HÌNH & LƯU TRỮ DỮ LIỆU (Bài 12 – 17)
*Mục tiêu:* Tách biệt mã nguồn khỏi cấu hình theo chuẩn 12-Factor App và làm chủ việc gắn ổ cứng bền vững cho ứng dụng có trạng thái (Stateful).

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 12** | **ConfigMap: Tách rời cấu hình khỏi mã nguồn** | Nạp biến môi trường và file cấu hình động vào Pod mà không cần build lại Docker image. | Bài 9 | Truyền file `nginx.conf` và biến môi trường vào ứng dụng qua ConfigMap bằng Volume mount. | 2 giờ | CKAD, CKA |
| **Bài 13** | **Secret: Quản lý thông tin nhạy cảm** | Lưu trữ mật khẩu, API key, chứng chỉ TLS và nhận diện nguy cơ bảo mật khi Secret chỉ được mã hóa Base64. | Bài 12 | Tạo Secret, nạp vào container dưới dạng file nhạy cảm và kiểm tra quyền truy cập file từ bên trong. | 2 giờ | CKAD, CKA, CKS |
| **Bài 14** | **Ephemeral Volumes: emptyDir & hostPath** | Sử dụng các loại ổ đĩa tạm thời để chia sẻ file giữa các container trong cùng một Pod hoặc đọc file từ host node. | Bài 6, 12 | Tạo Pod dùng `emptyDir` làm bộ nhớ đệm dùng chung giữa app chính và exporter. | 1.5 giờ | CKAD, CKA |
| **Bài 15** | **PersistentVolume (PV) & PersistentVolumeClaim (PVC)** | Hiểu hợp đồng lưu trữ giữa người vận hành hạ tầng (PV) và lập trình viên (PVC) để bảo toàn dữ liệu khi Pod bị xóa. | Bài 14 | Tạo PVC yêu cầu 5GB ổ đĩa, gắn vào cơ sở dữ liệu PostgreSQL, xóa Pod và chứng minh dữ liệu vẫn còn. | 3 giờ | CKA |
| **Bài 16** | **StorageClass & Dynamic Volume Provisioning** | Tự động hóa hoàn toàn việc tạo ổ cứng ảo trên Cloud/Storage backend mà không cần admin tạo tay từng PV. | Bài 15 | Cấu hình Local Path Provisioner / CSI driver để tự động sinh PV ngay khi nộp PVC. | 2.5 giờ | CKA |
| **Bài 17** | **StatefulSet & Headless Service: Chạy cơ sở dữ liệu** | Hiểu điểm khác biệt cốt tử giữa Deployment (phi trạng thái) và StatefulSet (danh tính cố định, thứ tự khởi động, ổ cứng riêng). | Bài 10, 16 | Triển khai cụm Redis hoặc MySQL Master-Slave với định danh mạng và volume riêng biệt cho từng bản sao. | 3.5 giờ | CKAD, CKA |

---

## GIAI ĐOẠN 4: MẠNG NÂNG CAO TRONG KUBERNETES (Bài 18 – 23)
*Mục tiêu:* Xóa bỏ "hộp đen" về mạng; hiểu sâu cách gói tin định tuyến qua CNI và phơi bày ứng dụng ra ngoài an toàn.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 18** | **Mô hình mạng phẳng & CNI (Container Network Interface)** | Hiểu nguyên tắc "Pod nào cũng có IP riêng và nói chuyện trực tiếp với nhau" cùng cách CNI (Calico/Cilium) điều phối bảng định tuyến. | Bài 10 | So sánh bảng định tuyến giữa các node và xem gói tin đi qua veth pair thế nào. | 2.5 giờ | CKA |
| **Bài 19** | **CoreDNS & Service Discovery** | Hiểu cách Kubernetes phân giải tên miền nội bộ (`my-svc.my-ns.svc.cluster.local`) và cấu hình `resolv.conf` trong Pod. | Bài 18 | Debug lỗi Pod không gọi được service do timeout DNS; tùy biến cấu hình CoreDNS hosts. | 2 giờ | CKA, CKAD |
| **Bài 20** | **Ingress & Ingress Controller** | Phơi bày hàng chục dịch vụ web ra ngoài Internet chỉ bằng 1 IP công khai duy nhất thông qua định tuyến Path/Host và SSL Termination. | Bài 10 | Cài Ingress-Nginx, cấu hình routing `api.example.com` và `app.example.com` kèm chứng chỉ TLS tự ký. | 3 giờ | CKA, CKAD |
| **Bài 21** | **Gateway API: Chuẩn mực định tuyến thế hệ mới** | Nắm bắt kiến trúc thay thế Ingress với sự phân quyền rõ ràng giữa Infrastructure Provider, Cluster Operator và Developer. | Bài 20 | Dựng Envoy Gateway, cấu hình `GatewayClass`, `Gateway` và `HTTPRoute` để thực hiện Canary traffic splitting (90/10). | 2.5 giờ | Xu hướng mới |
| **Bài 22** | **NetworkPolicy: Tường lửa cô lập mạng nội bộ** | Triển khai nguyên tắc Zero Trust, chặn đứng nguy cơ hacker chiếm được 1 web-pod rồi quét toàn bộ mạng nội bộ. | Bài 18 | Viết luật NetworkPolicy cô lập Database, chỉ cho phép duy nhất Backend Pod gửi request qua cổng 5432. | 2.5 giờ | CKA, CKAD, CKS |
| **Bài 23** | **Debug sự cố mạng: Phương pháp luận từ Pod đến Node** | Nắm vững quy trình loại trừ lỗi mạng từng lớp: kiểm tra iptables, IPVS, CNI logs và dùng ephermeral debug pod. | Bài 18 – 22 | Giải cứu một cụm K8s đang bị đứt kết nối giữa 2 worker node do lỗi định tuyến CNI. | 3 giờ | CKA, CKS |

---

## GIAI ĐOẠN 5: LẬP LỊCH & QUẢN LÝ TÀI NGUYÊN (Bài 24 – 29)
*Mục tiêu:* Điều khiển chính xác vị trí chạy của Pod; phân bổ CPU/RAM khoa học và tự động co giãn theo tải thực tế.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 24** | **Resource Requests & Limits, QoS Classes** | Hiểu cơ chế cấp tài nguyên thực tế: Request quyết định lập lịch, Limit quyết định bị bóp CPU hoặc dính đòn OOMKilled. | Bài 1, 9 | Cấu hình Pod dính OOMKilled do vượt RAM limit; phân loại và kiểm tra 3 cấp QoS (Guaranteed, Burstable, BestEffort). | 2.5 giờ | CKAD, CKA |
| **Bài 25** | **Kube-Scheduler: Thuật toán lọc và chấm điểm Node** | Hiểu 2 bước Filter (Lọc) và Score (Chấm điểm) của Scheduler khi tìm ngôi nhà tối ưu nhất cho Pod. | Bài 5, 24 | Giả lập tình huống Pod rơi vào trạng thái `Pending` do thiếu tài nguyên và đọc scheduler event để chẩn đoán. | 2 giờ | CKA |
| **Bài 26** | **Node Affinity & Pod Anti-Affinity** | Ép Pod chạy trên nhóm máy có phần cứng đặc biệt (SSD, GPU) hoặc rải đều Pod sang các node khác nhau để đảm bảo High Availability. | Bài 7, 25 | Dùng Pod Anti-Affinity để đảm bảo 3 bản sao của Web Server không bao giờ nằm chung trên một máy vật lý. | 2.5 giờ | CKAD, CKA |
| **Bài 27** | **Taints & Tolerations: Xua đuổi và dung thứ Pod** | Dành riêng nhóm node cho các ứng dụng đặc biệt (như thanh toán, AI training) và ngăn không cho ứng dụng thông thường nhảy vào. | Bài 26 | Đặt Taint lên node và cấu hình Toleration cho một Pod đặc quyền để nó là đối tượng duy nhất được chạy ở đó. | 2 giờ | CKAD, CKA |
| **Bài 28** | **Horizontal Pod Autoscaler (HPA): Tự động tăng giảm bản sao** | Co giãn số lượng Pod linh hoạt dựa trên mức sử dụng CPU/RAM thực tế thông qua Metrics Server. | Bài 9, 24 | Cài Metrics Server, dùng công cụ `hey` bơm tải HTTP để chứng kiến HPA tự động kích hoạt scale từ 2 lên 10 Pods. | 3 giờ | CKAD, CKA |
| **Bài 29** | **Vertical Pod Autoscaler (VPA) & Cluster Autoscaler (CA / Karpenter)** | Tự động điều chỉnh kích cỡ Pod (VPA) và tự gọi Cloud API xin cấp thêm Node khi cụm hết chỗ chứa (Karpenter/CA). | Bài 28 | Cấu hình VPA ở chế độ khuyến nghị (Off mode) để tính toán chuẩn xác lượng CPU/RAM app cần dùng. | 3 giờ | Prod Best Practice |

---

## GIAI ĐOẠN 6: BẢO MẬT & TĂNG CƯỜNG HỆ THỐNG (Bài 30 – 36)
*Mục tiêu:* Khóa chặt cluster theo chuẩn CIS Benchmark; kiểm soát chặt chẽ quyền hạn con người và tiến trình theo nguyên tắc Least Privilege.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 30** | **Authentication & RBAC: Ai được làm gì trên Cluster?** | Làm chủ cơ chế xác thực chứng chỉ X.509 và phân quyền tối thiểu bằng Role, ClusterRole, RoleBinding. | Bài 11 | Tạo tài khoản cho intern tên "Tuấn", chỉ được xem log ở namespace `dev`, không được xóa Pod và không được xem Secret. | 3 giờ | CKA, CKS |
| **Bài 31** | **ServiceAccount & Projected Service Account Tokens** | Cấp danh tính cho chính ứng dụng chạy trong Pod để nó gọi API Server an toàn với token tự động xoay vòng. | Bài 30 | Viết ứng dụng nhỏ dùng ServiceAccount Token giới hạn để tự đọc trạng thái các Pod khác trong cùng namespace. | 2.5 giờ | CKA, CKS |
| **Bài 32** | **Pod Security Standards (PSS) & Admission (PSA)** | Thay thế PodSecurityPolicy cũ bằng 3 cấp độ bảo vệ chuẩn: Privileged, Baseline và Restricted ở cấp Namespace. | Bài 11 | Bật nhãn `enforce=restricted` cho Namespace và chứng kiến K8s từ chối Pod cố tình chạy dưới quyền root. | 2.5 giờ | CKS |
| **Bài 33** | **SecurityContext: Bảo vệ Pod ở cấp Linux Kernel** | Triệt tiêu mọi đặc quyền thừa thãi: cấm chạy bằng root, đặt hệ thống file chỉ đọc (read-only), gỡ bỏ Linux Capabilities. | Bài 1, 32 | Cấu hình Pod chạy với user non-root (UID 10001), `readOnlyRootFilesystem: true`, và loại bỏ mọi Capabilities nguy hiểm. | 3 giờ | CKAD, CKS |
| **Bài 34** | **Quản lý Secret chuẩn Enterprise: HashiCorp Vault & ESO** | Chấm dứt việc lưu plaintext trong Git bằng External Secrets Operator (ESO) đồng bộ bí mật trực tiếp từ Vault/AWS Secrets Manager. | Bài 13 | Dựng External Secrets Operator, tự động kéo mật khẩu từ HashiCorp Vault về tạo thành K8s Secret an toàn. | 3.5 giờ | CKS, Prod |
| **Bài 35** | **Bảo mật chuỗi cung ứng: Image Scanning & Signing** | Quét lỗ hổng CVE trong image bằng Trivy và chỉ cho phép chạy những image đã được ký số bằng Cosign. | Bài 2 | Dùng Trivy chặn build image có lỗ hổng Critical; dùng Cosign ký image và kiểm tra chữ ký trước khi deploy. | 3 giờ | CKS |
| **Bài 36** | **Kiểm toán & Phát hiện mối đe dọa Runtime: Audit Log & Falco** | Ghi nhận mọi hành động tác động lên API Server và dùng Falco phát hiện hacker đang mở shell trái phép trong Pod. | Bài 5, 30 | Cấu hình Kube-APIServer Audit Policy; cài Falco và bắt quả tang cảnh báo khi có ai gõ `kubectl exec` tạo backdoor. | 3.5 giờ | CKS |

---

## GIAI ĐOẠN 7: GIÁM SÁT TOÀN DIỆN & TROUBLESHOOTING (Bài 37 – 42)
*Mục tiêu:* Thấu suốt trạng thái sức khỏe cluster qua 3 trụ cột Observability (Metrics, Logs, Traces) và phản xạ gỡ lỗi đa tầng nhanh chóng.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 37** | **Tập trung hóa Log: stdout/stderr & Logging DaemonSet** | Hiểu cơ chế log rotation của Kubelet và cách thu thập log phân tán về cụm tập trung bằng Vector/FluentBit và Loki. | Bài 6, 26 | Triển khai FluentBit dưới dạng DaemonSet gom toàn bộ container logs đẩy về Grafana Loki để tra cứu. | 3 giờ | CKA, CKAD |
| **Bài 38** | **Thu thập Metrics: Prometheus Operator & ServiceMonitor** | Giám sát toàn bộ hạ tầng và ứng dụng theo chuẩn Prometheus, tự động khám phá mục tiêu qua Custom Resource ServiceMonitor. | Bài 10, 24 | Cài đặt `kube-prometheus-stack`, cấu hình ServiceMonitor để cào metrics từ ứng dụng Golang/NodeJS. | 3.5 giờ | CKA |
| **Bài 39** | **Cảnh báo chuẩn SRE: Alertmanager & 4 Golden Signals** | Thiết lập cảnh báo dựa trên 4 tín hiệu vàng (Latency, Traffic, Errors, Saturation) và bắn thông báo qua Slack/Telegram. | Bài 38 | Viết quy tắc PrometheusRule cảnh báo khi tỷ lệ lỗi HTTP 5xx vượt 1% trong 5 phút và kích hoạt Alertmanager. | 2.5 giờ | Prod Best Practice |
| **Bài 40** | **Distributed Tracing & OpenTelemetry trên K8s** | Theo dõi luồng request xuyên qua nhiều microservice để tìm chính xác điểm nghẽn độ trễ. | Bài 20, 38 | Cài đặt OpenTelemetry Collector, trace một request đi qua 3 dịch vụ và hiển thị waterfall graph trên Jaeger. | 3 giờ | Mid/Senior |
| **Bài 41** | **Phương pháp luận Troubleshooting: Khung chẩn đoán 5 tầng** | Nắm vững quy trình xử lý sự cố có hệ thống: Đi từ Tầng 1 (Infra/Node) -> Tầng 2 (Control Plane) -> Tầng 3 (Network) -> Tầng 4 (Pod/Kubelet) -> Tầng 5 (App). | Toàn bộ GĐ 2 – 5 | Tham gia bài tập tình huống: Cluster bị tê liệt một phần, vận dụng phương pháp 5 tầng để cô lập nguyên nhân trong 15 phút. | 3 giờ | CKA |
| **Bài 42** | **Xử lý các ca bệnh kinh điển: CrashLoop, Pending, Evicted** | Mổ xẻ nguyên nhân gốc rễ và cách fix dứt điểm các lỗi kinh điển nhất mà dân vận hành gặp hàng ngày. | Bài 41 | Thực hành gỡ lỗi 5 kịch bản cố tình tạo hỏng: ConfigMap sai tên, OOMKilled lặp lại, thiếu Storage, CNI treo, Probe sai port. | 3.5 giờ | CKA, CKAD |

---

## GIAI ĐOẠN 8: VẬN HÀNH PRODUCTION & VÒNG ĐỜI (Bài 43 – 49)
*Mục tiêu:* Tự động hóa phân phối ứng dụng bằng GitOps, bảo trì nâng cấp cluster không downtime, backup dữ liệu sống còn và tối ưu chi phí.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 43** | **Helm: Đóng gói ứng dụng dạng biểu mẫu chuẩn** | Quản lý vòng đời ứng dụng phức tạp bằng Helm Chart, tái sử dụng cấu hình qua biến `values.yaml` và quản lý hooks. | Bài 9, 10, 12 | Tự viết một Helm Chart hoàn chỉnh từ số 0 cho ứng dụng Production gồm: Deployment, Service, Ingress, HPA, Secret. | 3 giờ | CKAD |
| **Bài 44** | **Kustomize: Quản lý cấu hình không cần template** | Phân chia cấu hình đa môi trường (dev/staging/prod) dựa trên cơ chế patch và overlay mà không cần sửa file gốc. | Bài 9, 12 | Xây dựng cấu trúc thư mục Kustomize chuẩn: thư mục `base` và ghi đè cấu hình cho `overlays/prod`. | 2.5 giờ | CKAD |
| **Bài 45** | **GitOps thực chiến với ArgoCD: Single Source of Truth** | Đồng bộ trạng thái khai báo từ Git lên Cluster tự động, chống trôi cấu hình (Config Drift) và hủy bỏ hoàn toàn việc gõ `kubectl apply` tay. | Bài 43, 44 | Cài ArgoCD, cấu hình kết nối Git repo; sửa đổi code trên Git và quan sát ArgoCD tự động sync lên cluster. | 3.5 giờ | Prod Standard |
| **Bài 46** | **Trái tim etcd: Sao lưu & Phục hồi thảm họa (Disaster Recovery)** | Nắm vững kiến thức bảo vệ cơ sở dữ liệu cốt lõi của cluster; thực hiện snapshot và restore etcd an toàn. | Bài 5 | Dùng `etcdctl` tạo snapshot, cố tình xóa sạch toàn bộ Deployment trên cluster, sau đó phục hồi lại hoàn toàn từ file snapshot. | 3 giờ | CKA |
| **Bài 47** | **Nâng cấp Cluster không gián đoạn (Zero-Downtime Upgrade)** | Quy trình nâng cấp chuẩn bằng `kubeadm`: cordon, drain node, nâng cấp Control Plane rồi lần lượt nâng cấp Worker Nodes. | Bài 5, 46 | Thực hành nâng cấp một cụm Kubernetes từ v1.30 lên v1.31 trên môi trường máy ảo mà dịch vụ web vẫn phản hồi liên tục. | 3.5 giờ | CKA |
| **Bài 48** | **Kiến trúc High Availability (HA) & Multi-Cluster** | Thiết kế cụm K8s sẵn sàng cao với etcd xếp chồng (stacked) hoặc etcd tách rời; khái niệm phân vùng đa vùng (Multi-AZ) và Multi-Cluster. | Bài 47 | Thiết kế sơ đồ kiến trúc HA 3 Control Plane Nodes đứng sau HAProxy/Keepalived và phân tích điểm nghẽn rủi ro. | 3 giờ | CKA, Senior |
| **Bài 49** | **FinOps & Tối ưu hóa chi phí Kubernetes** | Giám sát chi phí từng Namespace/Pod bằng Kubecost, dọn dẹp tài nguyên rác và kết hợp Spot/Preemptible Instances an toàn. | Bài 24, 29 | Cài đặt Kubecost, phân tích lượng CPU/RAM lãng phí và đưa ra phương án cắt giảm 30% chi phí hạ tầng. | 2.5 giờ | Senior Platform |

---

## GIAI ĐOẠN 9: NÂNG CAO, MỞ RỘNG & PLATFORM ENGINEERING (Bài 50 – 55)
*Mục tiêu:* Mở rộng Kubernetes API, xây dựng công cụ nền tảng nội bộ bằng code (Operator, Webhooks) và điều phối microservices bằng Service Mesh.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 50** | **Đào sâu Kube-APIServer & Concurrency Control** | Hiểu cơ chế Optimistic Concurrency Control, `resourceVersion`, etcd Watch mechanism và các hàng đợi ưu tiên (API Priority & Fairness). | Bài 5, 46 | Mô phỏng tình huống 2 tiến trình cùng cập nhật một tài nguyên để chứng kiến xung đột `Conflict 409` và cách retry. | 3 giờ | Senior Internals |
| **Bài 51** | **Policy-as-Code & Dynamic Admission Webhooks** | Chặn đứng các cấu hình vi phạm quy chuẩn ngay từ cửa vào bằng Kyverno hoặc OPA Gatekeeper (Validating & Mutating). | Bài 5, 32 | Viết chính sách Kyverno: Tự động từ chối bất kỳ Pod nào không có label `owner` hoặc dùng image gắn tag `latest`. | 3.5 giờ | CKS, Senior |
| **Bài 52** | **Custom Resource Definitions (CRD): Mở rộng từ điển K8s** | Tự định nghĩa các loại tài nguyên mới mang tính nghiệp vụ riêng cho doanh nghiệp bằng OpenAPI v3 schema. | Bài 5, 50 | Tạo CRD có tên `DatabaseInstance` với schema kiểm tra hợp lệ nghiêm ngặt cho tên DB, version và dung lượng. | 2.5 giờ | Senior |
| **Bài 53** | **Viết Kubernetes Operator: Tự động hóa vận hành bằng Code** | Lập trình vòng lặp điều hòa (Reconcile loop) bằng Golang với Kubebuilder để tự động sinh Pod, Service, Backup khi có CRD. | Bài 52, Golang cơ bản | Lập trình Operator đơn giản: Khi dev tạo một tài nguyên `Website`, Operator tự động sinh ra Deployment + Service + Ingress tương ứng. | 5 giờ | Senior Platform |
| **Bài 54** | **Service Mesh: mTLS & Quản trị lưu lượng chuyên sâu** | Giải quyết vấn đề mã hóa toàn bộ lưu lượng giữa các dịch vụ (mTLS) và kỹ thuật Canary release không cần sửa code app (Istio). | Bài 18, 20 | Cài đặt Istio, bật tự động mã hóa mTLS toàn cluster và cấu hình VirtualService phân chia 10% traffic vào bản thử nghiệm. | 4 giờ | Mid/Senior |
| **Bài 55** | **Custom Scheduler & Descheduler: Cân bằng lại cụm máy chủ** | Tự viết luật lập lịch riêng hoặc dùng Descheduler để đuổi bớt Pod khỏi các node quá tải sang các node đang rảnh rỗi. | Bài 25, 26 | Cài đặt Descheduler chạy định kỳ dạng CronJob để di tản Pod khi phát hiện vi phạm phân bố node. | 3 giờ | Senior SRE |

---

## GIAI ĐOẠN 10: DỰ ÁN CAPSTONE TỐT NGHIỆP (Bài 56)
*Mục tiêu:* Tự tay triển khai toàn bộ nền tảng microservices production giả lập chuẩn Enterprise tích hợp đầy đủ mọi tiêu chuẩn cao nhất.

| Bài | Tên bài | Mục tiêu học (1–2 câu) | Kiến thức cần trước | Lab thực hành | Thời lượng | Kỳ thi liên quan |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Bài 56** | **Capstone Project: Xây dựng Enterprise Platform hoàn chỉnh** | Tự tay thiết kế và vận hành một nền tảng Microservices đa môi trường đáp ứng đầy đủ tiêu chí: HA, GitOps, Bảo mật Zero-Trust, Observability và Auto-healing. | Toàn bộ Bài 1 – 55 | **Xây dựng hệ thống hoàn chỉnh:**<br>1. Cụm K8s Multi-node với KinD/Kubeadm.<br>2. Cài CNI Cilium + NetworkPolicy Zero-Trust.<br>3. Triển khai Ingress/Gateway API có SSL.<br>4. Quản lý cấu hình toàn bộ qua ArgoCD (GitOps).<br>5. Lưu trữ Secret an toàn qua Vault + ESO.<br>6. Giám sát bằng Prometheus, Loki, Alertmanager.<br>7. Đóng gói app bằng Helm, tích hợp HPA.<br>8. Chạy kịch bản giả lập sự cố (Chaos test) và chứng minh hệ thống tự phục hồi. | 15–20 giờ | Tổng hợp CKA, CKAD, CKS & Phỏng vấn Senior |

