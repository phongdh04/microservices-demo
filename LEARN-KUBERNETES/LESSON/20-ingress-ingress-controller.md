# Bài 20: Ingress & Ingress Controller: Mở cửa đón lưu lượng HTTP/HTTPS

## 1. Thông tin bài học
* **Tên bài:** Bài 20: Ingress & Ingress Controller: Mở cửa đón lưu lượng HTTP/HTTPS
* **Mục tiêu học:** Nắm vững sự khác biệt sống còn giữa cân bằng tải tầng giao vận (Layer 4 Service: TCP/UDP) và cổng đón tiếp tầng ứng dụng (Layer 7 Ingress: HTTP/HTTPS); phân biệt rạch ròi giữa bản hợp đồng định tuyến (Đối tượng `Ingress`) và động cơ thực thi thực tế (`Ingress Controller`); làm chủ hai cơ chế điều hướng cốt lõi: Định tuyến theo tên miền ảo (Host-based Routing) và Định tuyến theo đường dẫn URL (Path-based Routing với `Prefix` và `Exact`); cấu hình mã hóa bảo mật SSL/TLS tập trung (TLS Termination); thực hành triển khai Ingress Controller và cấu hình Ingress điều phối lưu lượng truy cập vào hệ thống Online Boutique trên cụm kind.
* **Thời lượng ước tính:** 180 phút (90 phút lý thuyết, 90 phút thực hành)
* **Kiến thức cần có trước:** Bài 10 (Service: Cầu nối mạng bền vững - ClusterIP, NodePort, LoadBalancer), Bài 13 (Secret: Quản lý thông tin nhạy cảm - TLS Secret), Bài 18 (Mô hình mạng phẳng & CNI), Bài 19 (CoreDNS & Service Discovery nội bộ).
* **Liên quan kỳ thi:** CKA, CKAD (Chủ đề bắt buộc chiếm 12–15% bài thi CKA/CKAD: viết manifest Ingress với các luật định tuyến host/path phức tạp, cấu hình TLS termination từ Secret, giải quyết xung đột `pathType`, gỡ lỗi mã trạng thái 404 Not Found và 503 Service Unavailable).

---

## 2. Bảng thuật ngữ

| Thuật ngữ | Giải thích đơn giản | Ví dụ / Ẩn dụ đời thường |
| :--- | :--- | :--- |
| **Ingress** | Đối tượng tài nguyên API định nghĩa bộ quy tắc (Rules) điều hướng lưu lượng HTTP/HTTPS từ ngoài Internet vào các Service nội bộ bên trong cluster. | Tấm bảng chỉ dẫn sơ đồ phòng ban treo ngay tại tiền sảnh của một tòa nhà văn phòng cao ốc. |
| **Ingress Controller** | Ứng dụng Reverse Proxy thực tế (như Nginx, Traefik, HAProxy) chạy bên trong cluster, liên tục đọc các quy tắc Ingress để thực thi điều phối lưu lượng thật. | Cô lễ tân chuyên nghiệp ngồi tại quầy tiền sảnh: đọc bảng chỉ dẫn và trực tiếp dẫn khách lên đúng từng phòng ban tương ứng. |
| **IngressClass** | Đối tượng định danh liên kết một Ingress cụ thể với một Ingress Controller nhất định khi trong cụm có nhiều bộ điều khiển cùng hoạt động. | Thẻ phân loại lối vào: cửa dành riêng cho khách VIP đi thang máy riêng, cửa dành cho nhân viên đi lối thang bộ. |
| **Host-based Routing** | Kỹ thuật định tuyến lưu lượng dựa trên tên miền ảo (Virtual Host) nằm trong trường `Host` của tiêu đề HTTP request. | Phân loại bưu phẩm theo tên công ty: thư gửi đến "Công ty A" chuyển vào hòm A, thư gửi đến "Công ty B" chuyển vào hòm B dù chung một địa chỉ tòa nhà. |
| **Path-based Routing** | Kỹ thuật định tuyến lưu lượng dựa trên đường dẫn URL (URI Path, ví dụ `/cart`, `/products`, `/checkout`). | Phân loại hồ sơ theo phòng chức năng: hồ sơ kế toán chuyển lên tầng 3, hồ sơ nhân sự chuyển lên tầng 4. |
| **`pathType`** | Quy định mức độ khớp của đường dẫn URL trong Ingress (`Prefix`: khớp tiền tố, `Exact`: khớp chính xác từng ký tự). | Khớp tiền tố như biển báo "Đường vào Quận 1" (mọi ngõ ngách trong Quận 1 đều tính); khớp chính xác như số nhà cụ thể. |
| **TLS Termination** | Kỹ thuật giải mã lưu lượng HTTPS ngay tại cửa ngõ Ingress Controller, sau đó chuyển tiếp HTTP không mã hóa vào các Pod nội bộ. | Trạm an ninh sân bay kiểm tra và bóc phong bì niêm phong trước khi chuyển văn thư cho các bộ phận nội bộ để tiết kiệm thời gian. |

---

## 3. Bức tranh lớn: Vấn đề thực tế và ẩn dụ đời thường

### Nhắc lại bài trước
Ở Bài 19, chúng ta đã nắm vững CoreDNS để giúp các microservices tự động tìm thấy nhau qua tên miền nội bộ `.svc.cluster.local`. Tuy nhiên, CoreDNS và Service loại `ClusterIP` chỉ phục vụ các cuộc gọi khép kín bên trong mạng nội bộ của cluster. Khi hàng triệu khách hàng từ khắp nơi trên thế giới muốn truy cập vào website bán hàng Online Boutique thông qua trình duyệt web bằng địa chỉ `https://shop.mycompany.com`, làm thế nào để mở cửa đón nhận lưu lượng đó? 

### Tại sao cần cái này ở production? (Vấn đề thực tế)

Ở Bài 10, chúng ta đã học về `NodePort` và `LoadBalancer`. Tại sao không dùng chúng mà lại phải sinh ra Ingress?

1. **Hạn chế tai hại của NodePort:**
   NodePort bắt buộc người dùng phải truy cập qua một dải cổng kỳ quặc từ 30000 đến 32767 (ví dụ `http://shop.com:31254`). Không có một khách hàng thương mại điện tử nào chấp nhận gõ thêm số cổng loằng ngoằng sau tên miền! Hơn nữa, mở cổng NodePort trực tiếp trên máy chủ gây ra rủi ro an ninh mạng rất lớn.
2. **Cơn ác mộng chi phí của Service LoadBalancer:**
   Nếu bạn sử dụng Service loại `LoadBalancer` trên các nền tảng đám mây (AWS, Google Cloud, Azure), **mỗi một Service sẽ tạo ra một bộ cân bằng tải vật lý riêng biệt kèm theo một địa chỉ IP công cộng (Public IP) riêng**! Mỗi bộ cân bằng tải đám mây có giá khoảng $18 - $25/tháng. Nếu hệ thống Online Boutique có 15 microservices cần kết nối ra ngoài, bạn sẽ phải trả thêm hàng trăm USD mỗi tháng chỉ để thuê 15 chiếc Load Balancer độc lập!
3. **Sự "mù quáng" ở Layer 4 của Service:**
   Service trong Kubernetes hoạt động ở Tầng giao vận (Layer 4 - TCP/UDP). Nó chỉ nhìn thấy địa chỉ IP nguồn/đích và số cổng. Nó hoàn toàn **không thể đọc được** nội dung của giao thức HTTP: nó không biết người dùng đang gõ tên miền gì (`Host: shop.com` hay `Host: api.com`), không biết đường dẫn URL là gì (`/cart` hay `/products`), và không biết xử lý Cookie hay chứng chỉ bảo mật HTTPS!

**Giải pháp tối ưu của Kubernetes Ingress:**
> **"Toàn bộ hàng chục microservices trong cluster chỉ cần dùng chung DUY NHẤT MỘT bộ cân bằng tải và một địa chỉ Public IP tại cửa ngõ! Ingress Controller đứng tại cửa ngõ đó, phân tích tiêu đề HTTP (Layer 7) và thông minh điều phối từng URL đến đúng Service nội bộ tương ứng!"**

### Ẩn dụ đời thường: Cổng ra vào tòa nhà chọc trời và Quầy lễ tân trung tâm

```mermaid
flowchart TD
    subgraph MultiLoadBalancer ["Cách cũ: Mỗi phòng khoét một cửa riêng ra đường"]
        LB1["Public IP 1: $20/tháng"] --> S1["frontend Service"]
        LB2["Public IP 2: $20/tháng"] --> S2["cartservice"]
        LB3["Public IP 3: $20/tháng"] --> S3["productcatalogservice"]
        Note1["Tốn tiền thuê 3 IP và 3 bộ cân bằng tải riêng biệt!"]
    end

    subgraph IngressGateway ["Cách chuẩn Ingress: Đại sảnh trung tâm của Tòa nhà"]
        PUB["DUY NHẤT 1 Public IP & 1 Load Balancer\n(Cổng chính tòa nhà: Port 80/443)"]
        PUB --> IC["Cô Lễ Tân Thông Minh\n(Ingress Controller - Nginx)"]
        
        IC -->|URL: shop.com/| S1
        IC -->|URL: shop.com/cart| S2
        IC -->|URL: shop.com/api/products| S3
        Note2["Chỉ tốn 1 IP duy nhất! Lễ tân tự chia khách về đúng phòng!"]
    end
```

1. **Mô hình Service LoadBalancer giống như mỗi căn hộ khoét một cửa riêng ra mặt phố:**
   * Một tòa nhà chung cư có 100 căn hộ. Mỗi chủ căn hộ tự đập tường làm một chiếc cửa cuốn mở thẳng ra đường phố chính.
   * Chi phí thi công cực kỳ tốn kém, mặt tiền tòa nhà bị xé nát, và việc bảo trì an ninh trở thành cơn ác mộng.
2. **Mô hình Ingress giống như Tiền sảnh Lễ tân của Tòa nhà cao ốc hiện đại:**
   * Cả tòa nhà chỉ có **duy nhất một cánh cổng xoay lớn** ở tầng trệt (Cổng 80 cho HTTP và Cổng 443 cho HTTPS).
   * Tại sảnh có một **Cô lễ tân thông minh (Ingress Controller)** đứng đón khách.
   * Trên tường phía sau cô lễ tân dán một **Tấm biển chỉ dẫn quy chế (Ingress Resource)**:
     * Khách nào bảo: *"Tôi muốn xem hàng"* (`/`) $\rightarrow$ Mời vào thang máy số 1 tới phòng `frontend`.
     * Khách nào bảo: *"Tôi muốn thanh toán giỏ hàng"* (`/cart`) $\rightarrow$ Mời vào thang máy số 2 tới phòng `cartservice`.
     * Khách ngoại quốc đưa thư niêm phong có mã hóa (`HTTPS`) $\rightarrow$ Cô lễ tân kiểm tra chứng thực, tháo phong bì bảo vệ (TLS Termination) rồi nhẹ nhàng chuyển thư vào trong.

---

## 4. Giải thích khái niệm theo từng bước

### Bước 1: Sự phân tách giữa Ingress Resource và Ingress Controller

Đây là điểm mấu chốt khiến nhiều kỹ sư mới hiểu sai:

* **Tài nguyên Ingress (`Ingress Resource`):** Chỉ là một file manifest YAML định nghĩa các quy tắc định tuyến (Rules). Bản thân đối tượng Ingress **hoàn toàn vô dụng và không thể tự chạy** nếu nó nằm trơ trọi trong cơ sở dữ liệu `etcd`!
* **Bộ điều khiển Ingress (`Ingress Controller`):** Là một ứng dụng chạy thực tế trong cụm (thường là một Deployment chạy Nginx, Envoy, Traefik hoặc HAProxy). Nó liên tục mở luồng theo dõi (Watch) tới Kube-APIServer. Mỗi khi bạn tạo hoặc sửa một file Ingress YAML, Ingress Controller sẽ tự động sinh lại file cấu hình reverse proxy (ví dụ file `nginx.conf`) và nạp lại (reload) cấu hình trong tích tắc mà không làm rớt kết nối mạng!

```mermaid
flowchart LR
    DEV["Lập trình viên\n(Tạo Ingress YAML)"] -->|kubectl apply| API["Kube-APIServer\n(Lưu vào etcd)"]
    API -.->|Watch event thông báo| IC["Ingress Controller\n(Deployment Nginx)"]
    IC -->|Tự động cập nhật file| CONF["/etc/nginx/nginx.conf"]
    CLIENT["Khách hàng ngoài Internet\n(Gửi HTTP Request)"] ==>|Port 80/443| IC
    IC ==>|Chuyển tiếp trực tiếp| POD["Pod Backend"]
```

---

### Bước 2: Cấu trúc của một Ingress Manifest chuẩn (`networking.k8s.io/v1`)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: boutique-ingress
  namespace: default
  annotations:
    # Các chỉ thị tùy biến dành riêng cho Ingress Controller (Nginx)
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  # 1. Chỉ định rõ Ingress Controller nào sẽ phụ trách xử lý
  ingressClassName: nginx

  # 2. Cấu hình bảo mật HTTPS (TLS Termination)
  tls:
    - hosts:
        - shop.boutique.com
      secretName: boutique-tls-secret  # Tên Secret chứa chứng chỉ (Bài 13)

  # 3. Danh sách các quy tắc định tuyến (Routing Rules)
  rules:
    - host: shop.boutique.com  # Khớp theo tên miền
      http:
        paths:
          # Quy tắc 1: Khớp đường dẫn trang chủ
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 80

          # Quy tắc 2: Khớp đường dẫn giỏ hàng
          - path: /cart
            pathType: Prefix
            backend:
              service:
                name: cart-service
                port:
                  number: 80
```

---

### Bước 3: So sánh 2 loại định tuyến: Host-based vs Path-based

#### 1. Định tuyến theo tên miền ảo (Host-based Routing):
Cho phép bạn chạy hàng chục website với các tên miền khác nhau trên cùng một địa chỉ IP duy nhất:
* Khách truy cập `https://shop.mycompany.com` $\rightarrow$ Chuyển vào `frontend-service`.
* Khách truy cập `https://admin.mycompany.com` $\rightarrow$ Chuyển vào `admin-dashboard-service`.
* Khách truy cập `https://api.mycompany.com` $\rightarrow$ Chuyển vào `backend-api-service`.

#### 2. Định tuyến theo đường dẫn (Path-based Routing):
Cho phép gom các vi dịch vụ con về chung một tên miền duy nhất:
* `https://mycompany.com/` $\rightarrow$ Chuyển vào `frontend`.
* `https://mycompany.com/products` $\rightarrow$ Chuyển vào `productcatalogservice`.
* `https://mycompany.com/cart` $\rightarrow$ Chuyển vào `cartservice`.

#### Giải mã thông số `pathType`:
Kubernetes chuẩn hóa 3 chế độ kiểm tra đường dẫn:
* **`Prefix` (Khuyên dùng):** Khớp theo tiền tố được phân tách bởi dấu gạch chéo `/`.  
  *Ví dụ:* Đường dẫn khai báo là `/cart`. Các URL hợp lệ gồm: `/cart`, `/cart/`, `/cart/checkout`, `/cart/item/123`. Các URL KHÔNG hợp lệ: `/cartservice`, `/cart_items`.
* **`Exact`:** Khớp chính xác 100% từng ký tự và phân biệt chữ hoa/thường.  
  *Ví dụ:* Khai báo `/cart` thì chỉ duy nhất URL `/cart` được chấp nhận; `/cart/` hoặc `/cart/items` sẽ bị trả về mã lỗi 404 Not Found!
* **`ImplementationSpecific`:** Tùy thuộc hoàn toàn vào cách triển khai riêng của từng loại Ingress Controller.

---

### Bước 4: Cơ chế TLS Termination (Giải mã SSL tại cửa ngõ)

Lưu lượng từ trình duyệt của người dùng gửi tới Ingress Controller được mã hóa bằng chứng chỉ số SSL/TLS (HTTPS cổng 443).
Tại Ingress Controller:
1. Ingress Controller đọc khóa riêng tư (`tls.key`) và chứng chỉ (`tls.crt`) từ Secret (Bài 13).
2. Thực hiện quá trình bắt tay TLS Handshake với trình duyệt khách hàng và giải mã gói tin.
3. Chuyển tiếp gói tin HTTP bản rõ vào mạng nội bộ của cluster để gửi tới các Pod backend.

```mermaid
flowchart LR
    BROWSER["Trình duyệt Khách hàng"] ==="1. HTTPS (Mã hóa SSL cổng 443)\nBảo vệ dữ liệu trên đường truyền Internet"===> IC["Ingress Controller\n(Giải mã TLS Termination)"]
    IC ---|"2. HTTP (Bản rõ nhanh nhẹn cổng 80)\nTruyền an toàn trong mạng nội bộ CNI"---| POD["Pod Backend\n(Không tốn CPU giải mã SSL)"]
```

> 🎯 **Lợi ích kiến trúc cực lớn:** Các Pod backend (viết bằng Python, Go, Node.js) không cần phải nạp chứng chỉ SSL và không tốn tài nguyên CPU quý giá để thực hiện giải mã toán học TLS; toàn bộ gánh nặng này được Ingress Controller gánh vác tập trung!

---

## 5. Thực hành (Lab)

* **Môi trường:** Cụm kind (`k8s\kind-config.yaml`) gồm 1 control-plane và 1 worker.
* **Mức RAM ước tính:** ~130 MB (chạy 2 microservices Nginx mô phỏng Frontend và Catalog Service).

### Kịch bản thực hành:
1. Khám phá cách Ingress hoạt động bằng cách triển khai 2 dịch vụ độc lập của Online Boutique: `boutique-frontend` (phục vụ trang chủ) và `boutique-catalog` (phục vụ dữ liệu sản phẩm).
2. Tự tạo một cặp chứng chỉ SSL/TLS tự ký (Self-signed Certificate) và đóng gói vào một Secret Kubernetes.
3. Xây dựng một file manifest Ingress hoàn chỉnh thực hiện cả hai chức năng:
   * **Host-based routing:** Định tuyến tên miền `shop.boutique.local`.
   * **Path-based routing:** Điều hướng `/` về `boutique-frontend` và `/products` về `boutique-catalog`.
   * **TLS Termination:** Bảo vệ toàn bộ lưu lượng bằng giao thức HTTPS.
4. Kiểm chứng tính năng điều phối lưu lượng và chứng chỉ SSL bằng PowerShell.
5. Dọn dẹp tài nguyên (Cleanup).

---

### Bước 1: Triển khai 2 dịch vụ mẫu Frontend và Catalog

Tạo file `boutique-services.yaml`:

```powershell
@'
# 1. Dịch vụ Frontend
apiVersion: apps/v1
kind: Deployment
metadata:
  name: boutique-frontend
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: boutique-frontend
  template:
    metadata:
      labels:
        app: boutique-frontend
    spec:
      containers:
        - name: web
          image: nginx:alpine
          command: ["sh", "-c"]
          args:
            - |
              echo "<h1>[Online Boutique] Chao mung den voi Trang chu Cua hang!</h1>" > /usr/share/nginx/html/index.html
              nginx -g "daemon off;"
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
  namespace: default
spec:
  type: ClusterIP
  selector:
    app: boutique-frontend
  ports:
    - port: 80
      targetPort: 80
---
# 2. Dịch vụ Product Catalog
apiVersion: apps/v1
kind: Deployment
metadata:
  name: boutique-catalog
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: boutique-catalog
  template:
    metadata:
      labels:
        app: boutique-catalog
    spec:
      containers:
        - name: web
          image: nginx:alpine
          command: ["sh", "-c"]
          args:
            - |
              mkdir -p /usr/share/nginx/html/products
              echo "{\"products\": [\"Vintage Camera\", \"Hipster Watch\", \"Coffee Mug\"]}" > /usr/share/nginx/html/products/index.html
              nginx -g "daemon off;"
---
apiVersion: v1
kind: Service
metadata:
  name: catalog-svc
  namespace: default
spec:
  type: ClusterIP
  selector:
    app: boutique-catalog
  ports:
    - port: 80
      targetPort: 80
'@ | Set-Content -Path .\boutique-services.yaml -Encoding UTF8

kubectl apply -f .\boutique-services.yaml
kubectl wait --for=condition=Ready pod -l app=boutique-frontend --timeout=60s
kubectl wait --for=condition=Ready pod -l app=boutique-catalog --timeout=60s
```

---

### Bước 2: Tạo chứng chỉ SSL/TLS tự ký và đóng gói vào Secret

Chúng ta sẽ sử dụng PowerShell để sinh nhanh một cặp chứng chỉ SSL tự ký cho tên miền `shop.boutique.local` mà không cần cài thêm OpenSSL:

```powershell
# Tạo chứng chỉ tự ký trên Windows PowerShell
$cert = New-SelfSignedCertificate -DnsName "shop.boutique.local" -CertStoreLocation "cert:\LocalMachine\My"

# Xuất Certificate ra file CRT (Base64)
$certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Cert)
$certB64 = [System.Convert]::ToBase64String($certBytes)
"-----BEGIN CERTIFICATE-----`n$certB64`n-----END CERTIFICATE-----" | Set-Content -Path .\tls.crt -Encoding UTF8

# Tạo một key giả lập cho môi trường lab (hoặc xuất private key thực)
"-----BEGIN RSA PRIVATE KEY-----`nMIIEowIBAAKCAQEA0fakekeyforlabpurposesonly==`n-----END RSA PRIVATE KEY-----" | Set-Content -Path .\tls.key -Encoding UTF8

# Tạo TLS Secret trong Kubernetes (ôn lại Bài 13)
kubectl create secret tls boutique-tls-cert --cert=.\tls.crt --key=.\tls.key --dry-run=client -o yaml | kubectl apply -f -
```

---

### Bước 3: Định nghĩa cấu hình Ingress (`boutique-ingress.yaml`)

Tạo file manifest Ingress kết nối tên miền, đường dẫn và chứng chỉ TLS:

```powershell
@'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: boutique-main-ingress
  namespace: default
  annotations:
    # Bật tính năng điều hướng SSL nếu client gửi HTTP
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - shop.boutique.local
      secretName: boutique-tls-cert
  rules:
    - host: shop.boutique.local
      http:
        paths:
          # Đường dẫn 1: Trang chủ cửa hàng
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-svc
                port:
                  number: 80

          # Đường dẫn 2: Danh mục sản phẩm
          - path: /products
            pathType: Prefix
            backend:
              service:
                name: catalog-svc
                port:
                  number: 80
'@ | Set-Content -Path .\boutique-ingress.yaml -Encoding UTF8

kubectl apply -f .\boutique-ingress.yaml
```

Kiểm tra đối tượng Ingress vừa tạo:
```powershell
kubectl get ingress boutique-main-ingress
```

#### Kết quả mong đợi (Expected Output):
```text
NAME                    CLASS   HOSTS                 ADDRESS   PORTS     AGE
boutique-main-ingress   nginx   shop.boutique.local             80, 443   10s
```
*Nhận xét:* Ingress đã mở cả 2 cổng `80` (HTTP) và `443` (HTTPS) đón nhận cho tên miền `shop.boutique.local`!

---

### Bước 4: Kiểm chứng tính năng định tuyến Ingress

Để kiểm tra trực tiếp mà không cần cài đặt một Ingress Controller cồng kềnh ngốn RAM vào máy 8GB, chúng ta sẽ sử dụng một Pod công cụ chạy Nginx reverse proxy kiểm chứng ngay quy tắc điều phối của Ingress, hoặc dùng lệnh `kubectl describe ingress`:

```powershell
kubectl describe ingress boutique-main-ingress
```

#### Kết quả phân tích quy tắc định tuyến:
```text
Name:             boutique-main-ingress
Namespace:        default
Address:          
Ingress Class:    nginx
Rules:
  Host                 Path  Backends
  ----                 ----  --------
  shop.boutique.local  
                       /           frontend-svc:80 (10.244.1.18:80)
                       /products   catalog-svc:80 (10.244.1.19:80)
TLS:
  boutique-tls-cert terminates shop.boutique.local
```

> 🎯 **Quan sát quan trọng:**  
> Bạn hãy nhìn vào phần `Backends`: Kubernetes đã tự động ánh xạ:
> * Đường dẫn `/` trỏ thẳng tới địa chỉ IP Endpoint của Pod frontend (`10.244.1.18:80`).
> * Đường dẫn `/products` trỏ thẳng tới IP Endpoint của Pod catalog (`10.244.1.19:80`).
> * Khối TLS xác nhận chứng chỉ `boutique-tls-cert` sẽ đảm nhiệm `terminates shop.boutique.local`!

Kiểm chứng gọi thực tế từ một Pod công cụ mạng mô phỏng client:
```powershell
# Chạy một pod curl tạm thời để gửi request kèm Host Header
kubectl run curl-client --image=curlimages/curl:latest -i --tty --rm -- \
  curl -s -H "Host: shop.boutique.local" http://frontend-svc.default.svc.cluster.local/
```
Output:
```html
<h1>[Online Boutique] Chao mung den voi Trang chu Cua hang!</h1>
```

Thử nghiệm gọi đường dẫn `/products`:
```powershell
kubectl run curl-client --image=curlimages/curl:latest -i --tty --rm -- \
  curl -s -H "Host: shop.boutique.local" http://catalog-svc.default.svc.cluster.local/products/
```
Output:
```json
{"products": ["Vintage Camera", "Hipster Watch", "Coffee Mug"]}
```

---

### Bước 5: Dọn dẹp tài nguyên (Cleanup)
```powershell
kubectl delete -f .\boutique-ingress.yaml
kubectl delete -f .\boutique-services.yaml
kubectl delete secret boutique-tls-cert
Remove-Item .\boutique-ingress.yaml, .\boutique-services.yaml, .\tls.crt, .\tls.key
```

---

## 6. Lỗi thường gặp và cách debug

### Lỗi 1: Truy cập Ingress bị trả về mã lỗi 404 Not Found từ Nginx
* **Dấu hiệu:** Bạn truy cập `http://shop.com/api/v1` nhưng trình duyệt hiển thị trang trắng với dòng chữ `404 Not Found (nginx)`.
* **Nguyên nhân cốt lõi:**
  1. Thiếu hoặc cấu hình sai annotation `nginx.ingress.kubernetes.io/rewrite-target`. Khi bạn cấu hình path là `/api/v1`, Ingress Controller mặc định sẽ gửi nguyên văn đường dẫn `/api/v1` vào Pod backend. Nếu ứng dụng backend chỉ lắng nghe tại đường dẫn gốc `/`, backend sẽ trả về 404!
  2. Sử dụng `pathType: Exact` nhưng URL của người dùng lại có thêm dấu gạch chéo `/` ở cuối.
* **Cách debug và sửa:**
  Sử dụng biểu thức chính quy (Regex) và rewrite target trong annotation:
  ```yaml
  metadata:
    annotations:
      nginx.ingress.kubernetes.io/use-regex: "true"
      nginx.ingress.kubernetes.io/rewrite-target: /$2
  spec:
    rules:
      - http:
          paths:
            - path: /api/v1(/|$)(.*)
              pathType: ImplementationSpecific
  ```

### Lỗi 2: Lỗi 502 Bad Gateway hoặc 503 Service Temporarily Unavailable
* **Dấu hiệu:** Trình duyệt trả về lỗi `502 Bad Gateway` hoặc `503 Service Unavailable`.
* **Nguyên nhân:**
  * **502 Bad Gateway:** Ingress Controller kết nối được tới Pod nhưng Pod đóng kết nối đột ngột hoặc trả về dữ liệu hỏng.
  * **503 Service Unavailable:** Service backend hoàn toàn **không có bất kỳ Endpoint nào khả dụng** (tất cả các Pod con đều đang bị sập hoặc chưa vượt qua Readiness Probe ở Bài 09).
* **Cách debug:**
  Chạy lệnh `kubectl get endpoints <tên-service>`. Nếu cột `ENDPOINTS` hiển thị `<none>`, hãy kiểm tra lại Selector của Service và trạng thái các Pod con!

### Lỗi 3: Ingress tạo thành công nhưng cột `ADDRESS` bị bỏ trống vĩnh viễn
* **Dấu hiệu:** Chạy `kubectl get ingress` thấy cột ADDRESS luôn trống rỗng sau nhiều giờ.
* **Nguyên nhân:** Trong cluster chưa cài đặt Ingress Controller, hoặc trường `ingressClassName` trong file Ingress YAML không trùng khớp với tên IngressClass mà Controller đang theo dõi.
* **Cách sửa:** Kiểm tra IngressClass trong cụm bằng lệnh `kubectl get ingressclass` và chỉnh sửa trường `ingressClassName` cho khớp.

---

## 7. Góc nhìn Senior

### 1. Sự đánh đổi (Trade-offs): Ingress Controller vs API Gateway vs Service Mesh

| Tiêu chí | Kubernetes Ingress truyền thống | API Gateway chuyên dụng (Kong, Apache APISIX) | Service Mesh (Istio Ingress Gateway) |
| :--- | :--- | :--- | :--- |
| **Phạm vi chức năng** | Định tuyến cơ bản theo Host và Path, TLS termination, rewrite URL cơ bản. | Quản lý API nâng cao: Authentication (JWT/OAuth2), Rate Limiting, Biến đổi request/response, Thanh toán hóa đơn API. | Quản lý lưu lượng nội bộ siêu sâu: mTLS tự động giữa mọi Pod, Canary Deployment theo % traffic, Distributed Tracing. |
| **Tiêu tốn tài nguyên** | Rất nhẹ nhàng (~100MB - 200MB RAM). | Trung bình (~500MB RAM, cần DB hoặc CRD lưu cấu hình plugin). | Nặng nhất (chiếm 10–20% tài nguyên cluster cho sidecar proxies). |
| **Khuyến nghị kiến trúc** | Đủ tốt cho **80% nhu cầu** của các ứng dụng web thông thường và thương mại điện tử. | Doanh nghiệp cung cấp Open API cho đối tác bên thứ ba cần kiểm soát bảo mật và tính phí API. | Hệ thống khổng lồ hàng trăm microservices phức tạp có yêu cầu Zero-Trust nội bộ khắt khe. |

### 2. Best practices tại production

1. **Bắt buộc cấu hình Rate Limiting để chống tấn công từ chối dịch vụ (DDoS):**
   Cửa ngõ Ingress là tấm bia đỡ đạn đầu tiên của toàn hệ thống. Hãy luôn cấu hình giới hạn số lượng request từ một địa chỉ IP bằng annotation:
   ```yaml
   nginx.ingress.kubernetes.io/limit-rps: "20"
   nginx.ingress.kubernetes.io/limit-connections: "10"
   ```
2. **Tự động hóa hoàn toàn việc cấp phát và gia hạn chứng chỉ SSL với `cert-manager`:**
   Ở production, đừng bao giờ tạo chứng chỉ SSL thủ công! Hãy cài đặt công cụ **`cert-manager`**. Công cụ này sẽ tự động kết nối với tổ chức cấp chứng chỉ miễn phí **Let's Encrypt** qua giao thức ACME, tự động sinh chứng chỉ SSL thật và tự động gia hạn trước khi hết hạn 30 ngày mà không cần con người can thiệp!
3. **Cấu hình Horizontal Pod Autoscaler (HPA) cho Ingress Controller:**
   Ingress Controller là nút thắt cổ chai duy nhất của mọi lưu lượng vào cụm. Nếu một chiến dịch Flash Sale bùng nổ, Ingress Controller có thể bị nghẽn CPU. Luôn thiết lập HPA để Ingress Controller tự động nhân bản từ 2 Pod lên 10 Pod khi lượng truy cập tăng vọt.

### 3. Câu hỏi phỏng vấn Senior

* **Câu hỏi 1:** *"Tại sao Ingress-Nginx Controller lại cố tình bỏ qua (Bypass) cơ chế cân bằng tải của kube-proxy và iptables trong Service để định tuyến trực tiếp vào địa chỉ IP của Pod Endpoint? Quyết định kiến trúc này mang lại những lợi ích vượt trội nào?"*
* **Gợi ý trả lời chuẩn:**
  1. **Lý do Bypass:** Thông thường, một client gọi tới Service sẽ đi qua iptables hoặc IPVS do kube-proxy thiết lập để chọn ngẫu nhiên một Pod IP. Nhưng Ingress-Nginx Controller chọn cách **tự tra cứu danh sách Endpoints trực tiếp từ API Server** và duy trì danh sách IP của từng Pod trong bộ nhớ riêng của nó.
  2. **Các lợi ích vượt trội:**
     * **Thuật toán cân bằng tải thông minh hơn:** kube-proxy chỉ hỗ trợ cân bằng tải ngẫu nhiên (Random) hoặc vòng tròn (Round-Robin). Bằng cách kết nối trực tiếp, Ingress Controller có thể áp dụng các thuật toán cân bằng tải cấp cao như `Least Connections` (gửi request cho Pod đang ít việc nhất) hoặc `Session Affinity / Sticky Sessions` (giữ chân khách hàng gắn chặt với một Pod cụ thể qua Cookie).
     * **Giảm thiểu độ trễ mạng (Latency):** Bỏ qua một bước nhảy mạng trung gian (Network Hop) và không phải duyệt qua bảng quy tắc iptables dài dặc của nhân Linux.
     * **Hỗ trợ kết nối liên tục (WebSockets / gRPC):** Duy trì kết nối hai chiều ổn định mà không bị iptables ngắt đột ngột.

* **Câu hỏi 2:** *"Trình bày sự khác biệt giữa hai mô hình kiến trúc: Edge TLS Termination (giải mã tại Ingress) và End-to-End TLS Encryption (mã hóa xuyên suốt tới tận Pod). Khi nào thì một Platform Engineer bắt buộc phải áp dụng End-to-End TLS?"*
* **Gợi ý trả lời chuẩn:**
  * **Edge TLS Termination:** Lưu lượng được giải mã thành HTTP bản rõ ngay tại Ingress Controller. Từ Ingress Controller tới Pod backend chạy bằng HTTP thuần. Ưu điểm: Đơn giản, tiết kiệm CPU cho backend, quản lý chứng chỉ tại 1 nơi duy nhất.
  * **End-to-End TLS Encryption:** Ingress Controller sau khi nhận HTTPS sẽ thực hiện mã hóa lại một lần nữa (Re-encrypt) trước khi gửi tới Pod, hoặc dùng cơ chế `SSL Passthrough` để chuyển tiếp nguyên vẹn gói tin mã hóa cho Pod tự giải mã.
  * **Trường hợp bắt buộc dùng End-to-End TLS:** Trong các hệ thống tài chính, ngân hàng, cổng thanh toán thẻ tín dụng bắt buộc phải tuân thủ nghiêm ngặt các chứng chỉ bảo mật quốc tế như **PCI-DSS** hoặc **HIPAA**. Các tiêu chuẩn này quy định: Dữ liệu nhạy cảm của khách hàng (số thẻ tín dụng, hồ sơ bệnh án) tuyệt đối không được phép tồn tại ở dạng bản rõ (Plaintext) trên bất kỳ đoạn dây mạng nào, kể cả mạng nội bộ giữa các container trong cùng một cụm máy chủ!

---

## 8. Tóm tắt bài học

* 📌 **1. Sự phân tách cốt lõi:** Ingress Resource là bản hợp đồng quy tắc định tuyến (YAML); Ingress Controller là ứng dụng Proxy thực tế (Nginx/Traefik) thực thi việc điều phối lưu lượng.
* 📌 **2. Tối ưu chi phí:** Cho phép hàng chục dịch vụ nội bộ dùng chung DUY NHẤT một địa chỉ Public IP và một bộ cân bằng tải đám mây.
* 📌 **3. Hai chế độ định tuyến Layer 7:** Host-based Routing (theo tên miền ảo `shop.com`) và Path-based Routing (theo đường dẫn URL `/cart`).
* 📌 **4. Ba kiểu `pathType`:** `Prefix` (khớp tiền tố linh hoạt), `Exact` (khớp chính xác 100%) và `ImplementationSpecific`.
* 📌 **5. TLS Termination tiện lợi:** Giải mã HTTPS tập trung tại cửa ngõ Ingress giúp giảm tải CPU và giải phóng các Pod backend khỏi gánh nặng quản lý chứng chỉ số.

---

## 9. Bài tập tự làm

* 🟢 **Mức Dễ:** Viết một manifest Ingress đơn giản cho phép truy cập dịch vụ `web-svc` qua tên miền `my-test.local` ở cổng 80 với `path: /` và `pathType: Prefix`. Sử dụng lệnh `kubectl describe ingress` để kiểm tra các rule được thiết lập.
* 🟡 **Mức Vừa (Path Routing với Rewrite):** Tạo 2 Deployment chạy Nginx phục vụ 2 trang khác nhau. Viết Ingress định tuyến đường dẫn `/service-a` vào Deployment A và `/service-b` vào Deployment B. Sử dụng annotation `nginx.ingress.kubernetes.io/rewrite-target: /` để đảm bảo khi người dùng gõ `/service-a`, backend A chỉ nhận request tại gốc `/`.
* 🔴 **Mức Khó (Cấu hình White-list IP và Rate Limit):** Nghiên cứu các annotation của Nginx Ingress Controller. Bổ sung cấu hình để chỉ cho phép các IP thuộc dải `192.168.1.0/24` được phép truy cập vào đường dẫn `/admin` (`whitelist-source-range`), đồng thời giới hạn tối đa 5 request/giây (`limit-rps: "5"`) cho toàn bộ các đường dẫn khác.

---

## 10. Câu hỏi tự kiểm tra

1. Tại sao nói Service trong Kubernetes chỉ hoạt động ở Layer 4, còn Ingress hoạt động ở Layer 7 của mô hình mạng OSI?
2. Nếu bạn chỉ áp dụng một file manifest Ingress vào cluster mà không cài đặt bất kỳ Ingress Controller nào, điều gì sẽ xảy ra với các request gửi tới cluster?
3. Sự khác nhau giữa việc cấu hình `pathType: Prefix` và `pathType: Exact` trong Ingress là gì?
4. Khái niệm TLS Termination tại Ingress Controller có ý nghĩa kỹ thuật gì và mang lại lợi ích gì cho các Pod ứng dụng backend?
5. Để Ingress Controller chuyển tiếp request cho backend tại thư mục gốc `/` khi người dùng truy cập đường dẫn `/api/v1/orders`, bạn bắt buộc phải cấu hình annotation nào?
6. Khi người dùng gặp lỗi `503 Service Temporarily Unavailable` khi truy cập qua Ingress, nguyên nhân gốc rễ thường nằm ở tầng Ingress Controller hay tầng Pod backend?

<details>
<summary>👉 Bấm vào đây để xem đáp án câu hỏi tự kiểm tra</summary>

* **Đáp án 1:** Vì Service chỉ có thể định tuyến dựa trên thông tin địa chỉ IP và số cổng (Port) TCP/UDP; trong khi Ingress có thể đọc hiểu và phân tích nội dung của giao thức ứng dụng HTTP/HTTPS (như tên miền Host Header, đường dẫn URL Path, Cookie, SSL certificate).
* **Đáp án 2:** Đối tượng Ingress sẽ chỉ nằm yên dưới dạng dữ liệu cấu hình trong cơ sở dữ liệu `etcd`; **hoàn toàn không có bất kỳ lưu lượng mạng nào được chuyển tiếp** và cột `ADDRESS` của Ingress sẽ bị bỏ trống vĩnh viễn vì không có bộ điều khiển nào thực thi nó.
* **Đáp án 3:** `Prefix` khớp theo tiền tố đường dẫn phân tách bởi dấu gạch chéo `/` (ví dụ `/app` khớp cả `/app/users` và `/app/info`); trong khi `Exact` bắt buộc URL phải trùng khớp chính xác 100% từng ký tự (chỉ khớp đúng `/app`).
* **Đáp án 4:** Có nghĩa là Ingress Controller sẽ đảm nhận việc giải mã gói tin HTTPS thành HTTP bản rõ ngay tại cửa ngõ; giúp các Pod backend không cần phải cài đặt chứng chỉ SSL và tiết kiệm tài nguyên CPU giải mã mật mã học.
* **Đáp án 5:** Bắt buộc cấu hình annotation: **`nginx.ingress.kubernetes.io/rewrite-target: /`**.
* **Đáp án 6:** Thường nằm ở **tầng Pod backend**; lỗi 503 xảy ra khi Service backend không có bất kỳ Endpoint Pod nào ở trạng thái sẵn sàng (Ready) để tiếp nhận lưu lượng (Pod bị sập, crash, hoặc fail Readiness probe).
</details>

---

## 11. Tài liệu đọc thêm và bài tiếp theo

### Tài liệu tham khảo
* [Tài liệu chính thức Kubernetes: Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* [Tài liệu Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)
* [Tài liệu chính thức NGINX Ingress Controller: Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)
* [Hướng dẫn cài đặt Ingress trên kind: Ingress on kind](https://kind.sigs.k8s.io/docs/user/ingress/)

### Bài tiếp theo
👉 **Bài 21: Gateway API: Chuẩn mực định tuyến thế hệ mới**

---

## PHỤ LỤC: ĐÁP ÁN GỢI Ý BÀI TẬP TỰ LÀM (MỤC 9)

### Đáp án Mức Dễ
```powershell
@'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: easy-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: my-test.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web-svc
                port:
                  number: 80
'@ | kubectl apply -f -

kubectl describe ingress easy-ingress
kubectl delete ingress easy-ingress
```

### Đáp án Mức Vừa
```powershell
@'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rewrite-ingress
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
    - host: boutique-app.local
      http:
        paths:
          - path: /service-a(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: service-a
                port:
                  number: 80
          - path: /service-b(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: service-b
                port:
                  number: 80
'@ | kubectl apply -f -

kubectl delete ingress rewrite-ingress
```

### Đáp án Mức Khó
```powershell
@'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-admin-ingress
  annotations:
    # 1. Giới hạn IP truy cập cho toàn Ingress
    nginx.ingress.kubernetes.io/whitelist-source-range: "192.168.1.0/24, 10.0.0.0/8"
    # 2. Giới hạn số lượng request tối đa 5 req/s
    nginx.ingress.kubernetes.io/limit-rps: "5"
spec:
  ingressClassName: nginx
  rules:
    - host: secure.company.local
      http:
        paths:
          - path: /admin
            pathType: Prefix
            backend:
              service:
                name: admin-internal-svc
                port:
                  number: 8080
'@ | kubectl apply -f -

kubectl delete ingress secure-admin-ingress
```

