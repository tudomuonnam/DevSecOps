# Cài đặt jenkins trên docker kết nối với kubernetes

## Tại sao?
Jenkins là phần mềm tự động hóa triển khai (CI) phần mềm. Khi viết một phần mềm (webapp, function ...) bạn sử dụng jenkins để chạy thử phần mềm, chạy các module test, nếu không phát sinh lỗi có thể tích hợp tự động vào sản phẩm
Kubernetes là công cụ quản lý các môi trường phát triển. Chúng gồm các nền tảng (pods) chạy trên các máy tính ảo (nodes) được quản lý bởi Kubernetes master (KM). KM có thể tạo nodes, pods từ các khai báo do người dùng viết. 

![Mô hình Kubernetes](https://upload.wikimedia.org/wikipedia/commons/b/be/Kubernetes.png "Mô hình kubertenes")

Để chạy thử phần mềm bằng jenkins, chúng ta phải tạo lập môi trường cho phần mềm chạy. Các môi trường này nằm trong Kubernetes, được thiết lập từ jenkins.

## Cài đặt Jenkin từ docker
Việc cài đặt là khá dễ dàng. Trên docker gõ
```sh
mkdir $HOME/jenkin_home

docker run --name jenkins -d -v jenkins_home:$HOME/jenkins_home -p 8080:8080 -p 5000:5000 jenkins/jenkins:lts
```
Jenkins chạy trên localhost, port 8080. Truy cập http://< IP>:8080 

Cài đặt addon để kết nối đến jenkins
Trên giao diện chính của Jenkins, vào  ``` Manage Jenkins > Plugin Manager > Available ```
Tìm kiếm ```kubernetes``` và tiến hành cài đặt addon này
## Cấu hình Docker Jenkins kết nối Kubertenes

Sau khi cài đặt xong addon, chọn ``` Manage Jenkins > Configure Clouds > Add a new Cloud > Kubernetes ``` Click Kubernetes Cloud Detail ...

Có mấy mục cần điền gồm:
 - Kubernetes URL, tìm giá trị này bằng lệnh 
```sh
kubectl cluster-info

Kubernetes control plane is running at https://192.168.49.2:8443  <-- Copy địa chỉ này
```


## Đọc thêm
- https://plugins.jenkins.io/kubernetes/
- [Hướng dẫn trên Youtube](https://www.youtube.com/watch?v=ZXaorni-icg&t=1076s)
