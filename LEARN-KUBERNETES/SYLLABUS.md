# LỘ TRÌNH ĐÀO TẠO KUBERNETES TỪ ZERO ĐẾN SENIOR

Quy ước: [ ] chưa viết, [x] đã viết

---

## Giai đoạn 1: Nền tảng: Linux, container, Docker, tại sao cần Kubernetes
Áp dụng vào Online Boutique: Khảo sát kiến trúc microservices-demo, chọn dịch vụ đơn giản (emailservice/productcatalogservice) để phân tích Dockerfile và chạy thử bằng Docker đơn lẻ.

- [x] Bài 01 | Linux cơ bản cho Container: Namespace & Cgroups | Hiểu cơ chế cô lập tiến trình và giới hạn tài nguyên của Linux làm nền móng cho container | Dòng lệnh Linux cơ bản | Tự tạo container thô bằng unshare và cgroups | 2.5 giờ | CKA, CKS
- [x] Bài 02 | Container & Docker từ bản chất: Image, Layer & CRI | Nắm vững cấu trúc layer của Container Image và chuẩn giao tiếp Container Runtime Interface (CRI) | Bài 01 | Phân tích image layer và thao tác với containerd/crictl | 3.0 giờ | CKAD
- [x] Bài 03 | Tại sao cần Kubernetes? Bài toán Orchestration | Thấy rõ giới hạn của Docker đơn lẻ khi quản lý hàng loạt container và lý do Kubernetes ra đời | Bài 02 | Giả lập sự cố container chết để thấy sự cần thiết của orchestrator | 1.5 giờ | Nền tảng
- [x] Bài 04 | Thiết lập môi trường Lab thực chiến: KinD & kubectl | Tự dựng cụm Kubernetes nhiều node bằng kind và cấu hình công cụ kubectl trên PowerShell | Bài 02, Bài 03 | Khởi tạo cluster 1 control-plane + 1 worker bằng kind-config.yaml | 2.0 giờ | CKA, CKAD

---

## Giai đoạn 2: Kiến trúc và đối tượng cốt lõi: control plane, node, Pod, ReplicaSet, Deployment, Service, Namespace, label/selector
Áp dụng vào Online Boutique: Triển khai 2 dịch vụ độc lập (frontend và productcatalogservice) lên cluster kind bằng Pod, ReplicaSet, Deployment, Service và Namespace.

- [x] Bài 05 | Kiến trúc Kubernetes: Control Plane, Worker Node & Vòng lặp hòa giải | Nắm vững vai trò các thành phần cốt lõi và nguyên lý chuyển dịch từ Actual State về Desired State | Bài 04 | Quan sát luồng tương tác giữa API Server, Scheduler, Kubelet khi tạo tài nguyên | 2.5 giờ | CKA
- [x] Bài 06 | Pod: Đơn vị tính toán nguyên tử & Multi-container | Hiểu vòng đời Pod và cách chạy nhiều container chia sẻ chung mạng/bộ nhớ (Sidecar pattern) | Bài 05 | Triển khai Pod chứa container chính và sidecar ghi log | 2.5 giờ | CKAD, CKA
- [x] Bài 07 | Labels, Selectors & Annotations: Xương sống định tuyến | Làm chủ cơ chế nhóm và ghép nối tài nguyên lỏng lẻo (loose coupling) trong Kubernetes | Bài 06 | Gắn nhãn và dùng selector lọc tài nguyên có điều kiện | 1.5 giờ | CKAD, CKA
- [x] Bài 08 | ReplicaSet: Đảm bảo số lượng bản sao | Hiểu cơ chế tự phục hồi số lượng Pod mong muốn và lý do không chạy Pod trần ở production | Bài 07 | Tạo ReplicaSet, cố tình xóa Pod và quan sát Pod mới tự sinh | 1.5 giờ | CKAD, CKA
- [x] Bài 09 | Deployment: Quản lý triển khai và cập nhật không gián đoạn | Thành thạo kỹ thuật Rolling Update, kiểm soát lịch sử phiên bản và rollback khi có lỗi | Bài 08 | Cập nhật phiên bản frontend Online Boutique và thực hiện rollback tức thì | 3.0 giờ | CKAD, CKA
- [x] Bài 10 | Service: Cầu nối mạng bền vững (ClusterIP, NodePort, LoadBalancer) | Nắm vững cách Service cung cấp IP cố định và cân bằng tải nội bộ giữa các Pod biến động | Bài 09 | Tạo ClusterIP kết nối frontend với productcatalogservice và mở NodePort kiểm tra | 3.0 giờ | CKAD, CKA
- [x] Bài 11 | Namespace & ResourceQuota cơ bản: Phân chia không gian làm việc | Phân tách môi trường làm việc trên cùng một cluster và kiểm soát tài nguyên tránh tranh chấp | Bài 10 | Tạo namespace dev-boutique và gán hạn ngạch giới hạn số lượng Pod | 2.0 giờ | CKAD, CKA

---

## Giai đoạn 3: Cấu hình và lưu trữ: ConfigMap, Secret, Volume, PV/PVC, StorageClass, StatefulSet
Áp dụng vào Online Boutique: Tách toàn bộ biến môi trường của cartservice và redis-cart sang ConfigMap/Secret; lưu trữ giỏ hàng bền vững với StatefulSet và PVC.

- [x] Bài 12 | ConfigMap: Tách rời cấu hình khỏi mã nguồn | Nạp cấu hình ứng dụng động qua biến môi trường và file mount mà không cần build lại image | Bài 09 | Truyền cấu hình cổng và endpoint cho frontend Online Boutique qua ConfigMap | 2.0 giờ | CKAD, CKA
- [ ] Bài 13 | Secret: Quản lý thông tin nhạy cảm | Lưu trữ mật khẩu, API key, chứng chỉ TLS và hiểu rõ giới hạn an toàn của Base64 | Bài 12 | Tạo và gắn Secret an toàn cho cartservice dưới dạng volume mount | 2.0 giờ | CKAD, CKA, CKS
- [ ] Bài 14 | Ephemeral Volumes: Lưu trữ tạm thời với emptyDir & hostPath | Sử dụng volume tạm để chia sẻ dữ liệu giữa các container trong Pod và lưu cache | Bài 06, Bài 12 | Dùng emptyDir làm thư mục đệm dùng chung giữa 2 container | 1.5 giờ | CKAD, CKA
- [ ] Bài 15 | PersistentVolume (PV) & PersistentVolumeClaim (PVC) | Hiểu hợp đồng phân tách lưu trữ giữa người cấp phát hạ tầng và người sử dụng | Bài 14 | Tạo PVC xin dung lượng lưu trữ cho redis-cart và kiểm tra dữ liệu sống sót khi xóa Pod | 3.0 giờ | CKA
- [ ] Bài 16 | StorageClass & Dynamic Volume Provisioning | Tự động hóa hoàn toàn việc cấp phát ổ đĩa ảo theo yêu cầu qua StorageClass | Bài 15 | Khai thác local-path provisioner trên kind để cấp phát PV tự động từ PVC | 2.5 giờ | CKA
- [ ] Bài 17 | StatefulSet & Headless Service: Triển khai ứng dụng có trạng thái | Phân biệt ứng dụng Stateful với Stateless, quản lý định danh mạng cố định và ổ đĩa độc lập | Bài 10, Bài 16 | Triển khai cụm redis-cart có trạng thái với StatefulSet và Headless Service | 3.5 giờ | CKAD, CKA

---

## Giai đoạn 4: Mạng: DNS, Ingress/Gateway API, NetworkPolicy, CNI
Áp dụng vào Online Boutique: Cấu hình Ingress truy cập frontend từ máy host; áp dụng NetworkPolicy thiết lập Zero Trust chỉ cho phép luồng gọi hợp lệ giữa các service.

- [ ] Bài 18 | Mô hình mạng phẳng & CNI (Container Network Interface) | Hiểu quy tắc định tuyến mọi Pod đều có IP riêng và cơ chế vận hành của plugin CNI | Bài 10 | Truy vết gói tin đi qua veth pair và kiểm tra bảng định tuyến giữa các node kind | 2.5 giờ | CKA
- [ ] Bài 19 | CoreDNS & Service Discovery nội bộ | Nắm vững cách K8s phân giải tên miền dịch vụ nội bộ và cơ chế cấu hình DNS client của Pod | Bài 18 | Debug lỗi mất kết nối dịch vụ do DNS timeout và cấu hình CoreDNS tùy biến | 2.0 giờ | CKA, CKAD
- [ ] Bài 20 | Ingress & Ingress Controller: Mở cửa đón lưu lượng HTTP/HTTPS | Định tuyến tên miền và đường dẫn URL vào các Service nội bộ chỉ qua một điểm tiếp nhận | Bài 10 | Cài ingress-nginx trên kind và cấu hình routing truy cập frontend Online Boutique | 3.0 giờ | CKA, CKAD
- [ ] Bài 21 | Gateway API: Chuẩn mực định tuyến thế hệ mới | Nắm bắt kiến trúc định tuyến phân quyền thay thế Ingress với GatewayClass, Gateway và HTTPRoute | Bài 20 | Cấu hình HTTPRoute thực hiện traffic splitting giữa hai phiên bản ứng dụng | 2.5 giờ | Nâng cao
- [ ] Bài 22 | NetworkPolicy: Tường lửa cô lập mạng nội bộ theo Zero Trust | Kiểm soát luồng traffic vào/ra giữa các Pod, ngăn ngừa nguy cơ di chuyển ngang khi bị tấn công | Bài 18 | Viết NetworkPolicy chặn toàn bộ traffic đến redis-cart ngoại trừ cartservice | 2.5 giờ | CKA, CKAD, CKS
- [ ] Bài 23 | Debug sự cố mạng: Phương pháp luận từ Pod đến Node | Xây dựng quy trình xử lý lỗi mạng có hệ thống từ DNS, iptables/kube-proxy đến CNI | Bài 18 - 22 | Dùng ephemeral debug container giải quyết tình huống đứt kết nối mạng giả lập | 3.0 giờ | CKA, CKS

---

## Giai đoạn 5: Lập lịch và tài nguyên: requests/limits, scheduler, taints/tolerations, affinity, HPA/VPA, autoscaling
Áp dụng vào Online Boutique: Đặt requests/limits khoa học cho frontend và cartservice; cấu hình Pod Anti-Affinity rải đều qua các node; thiết lập HPA tự co giãn frontend.

- [ ] Bài 24 | Resource Requests & Limits, QoS Classes | Phân biệt cơ chế lập lịch theo Request và siết tài nguyên theo Limit, tránh OOMKilled và CPU Throttling | Bài 09 | Cố tình làm container bị OOMKilled do vượt RAM và kiểm tra các cấp độ QoS | 2.5 giờ | CKAD, CKA
- [ ] Bài 25 | Kube-Scheduler: Thuật toán lọc (Filter) và chấm điểm (Score) | Hiểu sâu quy trình Scheduler chọn node tối ưu cho Pod và cách xử lý khi Pod dính Pending | Bài 05, Bài 24 | Giả lập tình huống cạn tài nguyên node và điều tra event từ Scheduler | 2.0 giờ | CKA
- [ ] Bài 26 | Node Affinity & Pod Anti-Affinity: Điều khiển phân bố Pod | Điều hướng Pod vào đúng nhóm phần cứng và phân tán các bản sao để đảm bảo tính sẵn sàng cao | Bài 07, Bài 25 | Dùng Pod Anti-Affinity rải các Pod frontend không nằm chung node | 2.5 giờ | CKAD, CKA
- [ ] Bài 27 | Taints & Tolerations: Xua đuổi và dung thứ trên Node | Dành riêng nhóm node cho tải chuyên biệt và ngăn chặn Pod thông thường chen chân | Bài 26 | Đặt Taint lên worker node và cấp Toleration cho Pod đặc quyền chạy độc quyền | 2.0 giờ | CKAD, CKA
- [ ] Bài 28 | Horizontal Pod Autoscaler (HPA): Tự động co giãn số lượng Pod | Thiết lập cơ chế tự động tăng/giảm bản sao theo mức tiêu thụ CPU thực tế qua Metrics Server | Bài 09, Bài 24 | Cài metrics-server, bơm tải HTTP vào frontend và quan sát HPA scale-out Pod | 3.0 giờ | CKAD, CKA
- [ ] Bài 29 | Vertical Pod Autoscaler (VPA) & Tối ưu hóa kích cỡ Pod | Tự động tính toán khuyến nghị và điều chỉnh requests/limits phù hợp với tải thực tế | Bài 28 | Cài VPA chế độ Recommendation để tối ưu cấu hình tài nguyên cho Online Boutique | 3.0 giờ | Best Practice

---

## Giai đoạn 6: Bảo mật: RBAC, ServiceAccount, Pod Security, secret management, supply chain
Áp dụng vào Online Boutique: Áp dụng Pod Security Standards cấp Restricted cho namespace; khóa chặt SecurityContext cho từng container; quét CVE các image bằng Trivy.

- [ ] Bài 30 | Authentication & Phân quyền RBAC | Quản trị quyền hạn chặt chẽ theo nguyên tắc quyền tối thiểu bằng Role, ClusterRole và Binding | Bài 11 | Tạo tài khoản người dùng giới hạn chỉ được xem log trong namespace dev-boutique | 3.0 giờ | CKA, CKS
- [ ] Bài 31 | ServiceAccount & Projected Tokens | Cấp phát danh tính an toàn cho ứng dụng gọi API Server với token tự động xoay vòng | Bài 30 | Cấu hình ServiceAccount cho ứng dụng tự truy vấn danh sách Pod trong namespace | 2.5 giờ | CKA, CKS
- [ ] Bài 32 | Pod Security Standards (PSS) & Admission (PSA) | Chuẩn hóa an toàn container ở cấp namespace với 3 mức Privileged, Baseline và Restricted | Bài 11 | Bật nhãn PSA restricted và quan sát K8s từ chối các manifest vi phạm quy chuẩn | 2.5 giờ | CKS
- [ ] Bài 33 | SecurityContext: Siết chặt an toàn tiến trình cấp Linux Kernel | Khóa chặt container: cấm chạy quyền root, bật readOnlyRootFilesystem và gỡ bỏ Capabilities | Bài 01, Bài 32 | Cấu hình SecurityContext toàn diện cho frontend Online Boutique chạy non-root | 3.0 giờ | CKAD, CKS
- [ ] Bài 34 | Quản lý Secret chuẩn Enterprise với External Secrets Operator | Chống lộ mật khẩu trong Git bằng cách đồng bộ an toàn từ kho bí mật tập trung | Bài 13 | Cài External Secrets Operator (bản rút gọn cho 8GB) đồng bộ secret giả lập | 3.5 giờ | CKS
- [ ] Bài 35 | Bảo mật chuỗi cung ứng: Quét lỗ hổng Image bằng Trivy | Tích hợp kiểm tra bảo mật hình ảnh container, ngăn chặn image chứa mã độc và CVE nguy hiểm | Bài 02 | Dùng Trivy quét image các microservice trong Online Boutique và phân tích báo cáo | 3.0 giờ | CKS
- [ ] Bài 36 | Kiểm toán & Giám sát bất thường Runtime: Audit Log & Falco | Ghi vết hành động gọi API và phát hiện hành vi xâm nhập trái phép trong container thời gian thực | Bài 05, Bài 30 | Bật Audit Policy cho API Server và cài Falco (bản rút gọn cho 8GB) bắt sự kiện exec | 3.5 giờ | CKS

---

## Giai đoạn 7: Quan sát hệ thống: logging, metrics, tracing, alerting, troubleshooting
Áp dụng vào Online Boutique: Thu thập log tập trung và metrics từ frontend/checkoutservice; tạo dashboard giám sát và diễn tập khắc phục lỗi CrashLoopBackOff, OOMKilled.

- [ ] Bài 37 | Quản lý và tập trung hóa Log: stdout/stderr & Logging Agent | Hiểu cơ chế gom log từ node và chuyển tiếp về kho tập trung bằng kiến trúc DaemonSet nhẹ | Bài 06, Bài 26 | Cài đặt FluentBit thu thập log container đẩy về kho lưu trữ (bản rút gọn cho 8GB) | 3.0 giờ | CKA, CKAD
- [ ] Bài 38 | Thu thập Metrics: Prometheus & ServiceMonitor | Giám sát sức khỏe hạ tầng và ứng dụng, tự động cào metrics qua ServiceMonitor | Bài 10, Bài 24 | Triển khai Prometheus Operator (bản rút gọn cho 8GB) cào metrics từ frontend | 3.5 giờ | CKA
- [ ] Bài 39 | Cảnh báo chuẩn SRE: Alertmanager & 4 Golden Signals | Thiết lập cảnh báo chủ động dựa trên độ trễ, lưu lượng, tỷ lệ lỗi và mức độ bão hòa | Bài 38 | Cấu hình Alertmanager (bản rút gọn cho 8GB) bắn cảnh báo khi service lỗi 5xx | 2.5 giờ | Best Practice
- [ ] Bài 40 | Distributed Tracing với OpenTelemetry & Jaeger | Truy vết đường đi của request qua chuỗi microservices để phát hiện điểm nghẽn độ trễ | Bài 20, Bài 38 | Cài Jaeger (bản rút gọn cho 8GB) và theo dõi hành trình checkout của người dùng | 3.0 giờ | Nâng cao
- [ ] Bài 41 | Phương pháp luận Troubleshooting: Khung chẩn đoán 5 tầng | Nắm vững quy trình khoanh vùng lỗi có hệ thống: Node -> Control Plane -> Mạng -> Kubelet -> App | Toàn bộ GĐ 2 - 5 | Tham gia bài tập tình huống: Cluster bị lỗi đa tầng và cô lập lỗi trong 15 phút | 3.0 giờ | CKA
- [ ] Bài 42 | Khắc phục các sự cố kinh điển: CrashLoopBackOff, OOMKilled, Pending | Rèn luyện phản xạ chẩn đoán và khắc phục nhanh các lỗi phổ biến nhất trong vận hành | Bài 41 | Thực hành sửa 4 ca sự cố giả lập trên các service của Online Boutique | 3.5 giờ | CKA, CKAD

---

## Giai đoạn 8: Vận hành production: Helm/Kustomize, GitOps, nâng cấp cluster, backup etcd, HA, multi-cluster, quản lý chi phí
Áp dụng vào Online Boutique: Đóng gói toàn bộ Online Boutique thành Helm Chart có cấu hình values theo môi trường; quản lý triển khai tự động qua ArgoCD GitOps.

- [ ] Bài 43 | Đóng gói ứng dụng với Helm 3: Chart, Values & Hooks | Chuẩn hóa đóng gói ứng dụng phức tạp thành gói biểu mẫu dễ triển khai và chia sẻ | Bài 09, Bài 10, Bài 12 | Tự viết Helm Chart từ số 0 đóng gói frontend và service phụ thuộc | 3.0 giờ | CKAD
- [ ] Bài 44 | Quản lý cấu hình đa môi trường bằng Kustomize | Tùy biến cấu hình dev/staging/prod theo dạng overlay mà không cần sửa đổi manifest gốc | Bài 09, Bài 12 | Xây dựng cấu trúc thư mục base/overlays Kustomize cho Online Boutique | 2.5 giờ | CKAD
- [ ] Bài 45 | Vận hành hạ tầng khai báo (GitOps) với ArgoCD | Đồng bộ trạng thái từ Git lên Cluster tự động, triệt tiêu trôi cấu hình và loại bỏ deploy tay | Bài 43, Bài 44 | Cài ArgoCD (bản rút gọn cho 8GB) tự động sync ứng dụng từ Git repo | 3.5 giờ | Chuẩn Prod
- [ ] Bài 46 | Sao lưu và phục hồi thảm họa etcd (Disaster Recovery) | Nắm vững kỹ thuật bảo vệ kho dữ liệu cốt lõi, thực hiện snapshot và restore etcd an toàn | Bài 05 | Tạo snapshot etcd, giả lập sự cố xóa sạch tài nguyên và khôi phục hoàn toàn | 3.0 giờ | CKA
- [ ] Bài 47 | Quy trình nâng cấp Cluster không gián đoạn (Zero-Downtime) | Thành thạo quy trình cordon, drain node và nâng cấp các thành phần bằng kubeadm | Bài 05, Bài 46 | Thực hành thao tác drain và bảo trì worker node trên cluster lab | 3.5 giờ | CKA
- [ ] Bài 48 | Thiết kế kiến trúc High Availability (HA) & Multi-Cluster | Nắm vững nguyên lý thiết kế cụm HA chịu lỗi đa vùng và chiến lược quản trị nhiều cluster | Bài 47 | Thiết kế sơ đồ kiến trúc HA 3 control-plane và phân tích kịch bản split-brain | 3.0 giờ | CKA, Senior
- [ ] Bài 49 | Quản lý và tối ưu hóa chi phí Kubernetes (FinOps) | Phân tích lãng phí tài nguyên, rightsizing kích cỡ Pod và chiến lược kết hợp Spot Instance | Bài 24, Bài 29 | Phân tích mức sử dụng CPU/RAM của cluster và lập phương án cắt giảm lãng phí | 2.5 giờ | Senior

---

## Giai đoạn 9: Nâng cao: CRD, Operator, admission webhook, service mesh, internals (etcd, kube-apiserver, controller loop)
Áp dụng vào Online Boutique: Viết chính sách Kyverno kiểm duyệt cấu hình container; triển khai Istio Service Mesh điều phối lưu lượng và mã hóa mTLS giữa các microservice.

- [ ] Bài 50 | Đào sâu Kube-APIServer & Optimistic Concurrency Control | Hiểu cơ chế resourceVersion, lưu trữ etcd KV và hàng đợi điều phối API Priority & Fairness | Bài 05, Bài 46 | Mô phỏng tình huống hai tiến trình cùng sửa tài nguyên và xử lý lỗi xung đột 409 | 3.0 giờ | Senior
- [ ] Bài 51 | Policy-as-Code với Admission Webhook (Kyverno) | Tự động hóa kiểm duyệt và ép buộc tuân thủ quy chuẩn trước khi manifest được ghi vào etcd | Bài 05, Bài 32 | Cài Kyverno (bản rút gọn cho 8GB) bắt buộc mọi Pod phải có nhãn owner và giới hạn RAM | 3.5 giờ | CKS, Senior
- [ ] Bài 52 | Tự định nghĩa tài nguyên với Custom Resource Definitions (CRD) | Mở rộng vốn từ vựng của Kubernetes API bằng các tài nguyên mang nghiệp vụ riêng | Bài 05, Bài 50 | Tạo CRD MicroserviceConfig kiểm tra tính hợp lệ bằng OpenAPI v3 schema | 2.5 giờ | Senior
- [ ] Bài 53 | Lập trình Kubernetes Operator với Golang & Kubebuilder | Tự động hóa tri thức vận hành thành code: điều khiển vòng lặp hòa giải Reconcile | Bài 52 | Xây dựng Operator đơn giản tự sinh Deployment và Service khi có Custom Resource | 5.0 giờ | Senior Platform
- [ ] Bài 54 | Quản trị lưu lượng chuyên sâu với Service Mesh (Istio) | Triển khai mã hóa mTLS tự động giữa các microservice và điều phối lưu lượng Canary thông minh | Bài 18, Bài 20 | Cài Istio (bản rút gọn cho 8GB) phân chia 10% traffic vào phiên bản frontend mới | 4.0 giờ | Senior
- [ ] Bài 55 | Tối ưu lập lịch nâng cao với Descheduler | Cân bằng lại mật độ Pod trên cluster khi có node mới hoặc khi cụm bị phân bố lệch | Bài 25, Bài 26 | Cài Descheduler chạy quét định kỳ để trục xuất Pod vi phạm phân bố tối ưu | 3.0 giờ | Senior SRE

---

## Giai đoạn 10: Dự án cuối khóa (capstone) mô phỏng production
Áp dụng vào Online Boutique: Tích hợp toàn diện: vận hành toàn bộ Online Boutique chuẩn Enterprise đáp ứng tiêu chí HA, GitOps, Zero-Trust, Observability và Auto-healing.

- [ ] Bài 56 | Capstone Project: Triển khai Online Boutique chuẩn Production Enterprise | Tổng hợp toàn bộ kỹ năng thiết kế, bảo mật, tự động hóa và xử lý sự cố vào nền tảng hoàn chỉnh | Toàn bộ Bài 01 - 55 | Vận hành Online Boutique hoàn chỉnh (bản rút gọn cho 8GB) kèm GitOps, SSL, HPA, giám sát và chaos test | 15.0 giờ | Tổng hợp CKA/CKAD/CKS

---

## 🎯 CÁC MỐC ĐÁNH GIÁ NĂNG LỰC (MILESTONES)

| Trình độ | Hoàn thành sau | Năng lực thực tế đạt được | Kỳ thi mục tiêu |
| :--- | :--- | :--- | :--- |
| **JUNIOR K8s Developer** | Sau **Giai đoạn 3** (Bài 17) | Hiểu bản chất container, tự tin viết manifest YAML chuẩn. Tự deploy, cấu hình biến môi trường, quản lý storage cơ bản cho ứng dụng web. Biết cách đọc log, debug các lỗi sai cấu hình thường gặp. | Sẵn sàng hoàn thành ~70% bài thực hành **CKAD** |
| **MID-LEVEL DevOps / Admin** | Sau **Giai đoạn 7** (Bài 42) | Làm chủ mạng nội bộ, Ingress/Gateway API, NetworkPolicy. Quản lý lập lịch, phân bổ tài nguyên, autoscaling (HPA/VPA). Áp dụng RBAC, Pod Security, xây dựng hệ thống giám sát Prometheus/Grafana/Loki và có phương pháp luận debug đa tầng. | Đủ năng lực thi đỗ chứng chỉ **CKA** và **CKAD**; nền tảng vững cho **CKS** |
| **SENIOR Platform / SRE** | Sau **Giai đoạn 10** (Bài 56) | Thiết kế kiến trúc HA, Multi-cluster, Disaster Recovery (etcd backup), nâng cấp zero-downtime. Chuẩn hóa GitOps (ArgoCD), Helm, FinOps. Tự viết Operator, Admission Webhooks, triển khai Service Mesh và hoàn thành Capstone quy mô Enterprise. | Vượt qua **CKS (Security Specialist)** và tự tin dẫn dắt Platform Team |
