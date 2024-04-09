# Kiến trúc kubernetes

Người mới học Kubernetes (như mình) thường rất khó hiểu sâu về kiến trúc của toàn bộ hệ thống. Nên thường phải mày mò rất khổ.

Ví dụ, riêng về hệ thống IP đã có khá nhiều: Cluster-IP, Nodes- IP, Service IP, pod-ip ... dẫn đến khi khởi tạo và kết nối dịch vụ ra ngoài thường cảm thấy khó khăn và phức tạp

Bài này cố gắng tìm kiếm logic đằng sau sự phức tạp này
## Theo hiểu biết của mình
Trình tự ảo hóa của Kubernetes đi từ Cluster -> Nodes -> Pods
## Các thành phần trong Kubernetes
### Về mặt phần cứng
- Node là đơn vị nhỏ nhất. Chúng là các máy tính, laptop hay một cái đồng hồ thông minh có tài nguyên riêng (CPU, RAM, network...)thể tự chạy chương trình vào giao tiếp với nhau qua mạng
- Cluster (Cụm máy)
Mặc dù làm việc với từng node là hữu ích, đó không phải là mục tiêu mà Kubernetes hướng đến. Thay vào đó, chúng ta nhắm đến các cụm máy gồm nhiều node khác nhau. Khi triển khai chương trình đến cluster, chúng sẽ tự phân phối đến các node
- Persistent Volume
Bởi vì các node là linh động, chúng có thể được thêm hay xóa khi chạy chương trình. Việc lưu dữ liệu như database trong node là không hợp lý. Dữ liệu thường được lưu trữ riêng vào trong Kubernetes trong persistent volume. Volume này có thể nằm trên ổ vật lý hoặc kết nối với thiết bị lưu trữ trên mạng.
### Về mặt phần mềm
- Container. 

Các chương trình trong Kubernetes được đóng gói sẵn trong các container. Có vô số các container được viết sẵn trên mạng để thực thi các chương trình. 
- Pods,

Không giống như Docker, Kubernetes không chạy trực tiếp các container mà đóng gói chúng trong các Pod. Các container trong cùng một pod sẽ có chung tài nguyên và tách biệt với các pod khác.

Pod có thể nhân bản dễ dàng. Nếu chương trình có tải cao, bạn có thể nhân bản pod để giảm tải cho pod cũ.

Có thể cài đặt nhiều container cho một pod. Nhưng đó không phải là cách làm tốt. Bởi vì Pod có thể nhân bản, bạn nên giữ pod nhỏ nhất có thể
- Deployments (triển khai)

Mặc dù pod là đơn vị nhỏ nhất, chúng không được triển khai trực tiếp trong cluster. Trên thực tế, chúng được quản lý thông qua Deployments

Khi khai báo nội dung và số lượng pod trong deployment. Khi thực thi, Deployment sẽ quản lý pod (ví dụ nhân bản, thêm, bớt, hiển thị)

Bằng cách sử dụng deployment, bạn không phải xử lý các pod trực tiếp

- Services

Tạo các pod, Node xong rồi, nhưng giao tiếp với chúng ra sao. Đó là lúc phải sử dụng đến dịch vụ. Dịc vụ đơn giản là cầu nối giao tiếp giữa Node, Pod ra ngoài Cluster.
Các loại dịch vụ gồm
 - NodePort
 - LoadBalance
## Cách tạo thành phần (code)
### Tạo Pod
- Thông qua Deployment
- apply yaml
### Tạo Service
- Nodepord
- Loadbalance
- Egress
### Tạo Node
```sh
kubectl cordon $NODENAME

```
### Tạo Cluster
## Đọc thêm
- https://stackoverflow.com/questions/41509439/whats-the-difference-between-clusterip-nodeport-and-loadbalancer-service-types
- https://medium.com/google-cloud/kubernetes-101-pods-nodes-containers-and-clusters-c1509e409e16