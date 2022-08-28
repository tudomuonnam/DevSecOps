# Tạo pods và expose dịch vụ

## Tạo Pods

Tạo file development định nghĩa pod
```vi
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
  labels:
    app: flask
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      labels:
        app: flask
    spec:
      containers:
      - name: flask
        image: <user>/flask-docker:latest
        ports:
        - containerPort: 5000
``` 
App có tên là flask-development, lấy image từ < user >/flask-docker, tạo 3 nhân bản, có nhãn là flask. Tạo nhãn là quan trọng, xem thêm ở bên dưới.

Các pod này đều chạy dịch vụ dưới cổng 5000

Khởi chạy pod bằng câu lệnh
```terminal
kubectl apply -f development.yaml
```
Kiểm tra lại xem pod đã hoạt động chưa bằng câu lệnh
```terminal
kubectl get pods -o wide

NAME                                  READY   STATUS    RESTARTS        AGE     IP            NODE       NOMINATED NODE   READINESS GATES
flask-deployment-7958c8f787-k9h6p     1/1     Running   0               7h30m   172.17.0.13   minikube   <none>           <none>
flask-deployment-7958c8f787-lhc8f     1/1     Running   0               7h30m   172.17.0.11   minikube   <none>           <none>
flask-deployment-7958c8f787-sxbp7     1/1     Running   0               7h30m   172.17.0.14   minikube   <none>           <none>

```
## Tạo service link đến các pods
Service này nằm trong file service.yaml

```vi
apiVersion: v1
kind: Service
metadata:
  name: flask-loadbalance
spec:
  type: LoadBalancer
  selector:
    app: flask
  ports:
    - protocol: TCP
      port: 8000
      targetPort: 5000
  externalTrafficPolicy: Local
```
Service dang loadbalance (cân bằng tải). Nhận request từ cổng 8000 và chuyển đến cổng 5000 từ các pod có tag flask

Sau khi chạy ta kiểm tra load balance này đã liên kết đến 3 pod tạo phía trên hay chưa
```terminal
kubectl describe svc flask-loadbalance |grep Endpoints
Endpoints:                172.17.0.11:5000,172.17.0.13:5000,172.17.0.14:5000
```
Như vậy service flask-loadbalance đã kết nối tới 3 pods

## Tại liên kết ra ngoài kubernetes

Đầu tiên tạo liên kết ra ngoài minikube
```terminal
minikube service  flask-loadbalance --url

http://192.168.49.2:64660

```
Ok vậy là từ local đã có thể kết nối được đến pod. Test thử
```terminal 
curl http://192.168.49.2:64660
<!DOCTYPE html>

<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Flask Docker</title>
</head>
<body>
    <h1>This is a Flask App containerised with Docker</h1>
</body>
```
Nhưng nếu truy cập từ bên ngoài thì sao? 

## Dùng nginx proxy_pass để truy cập dịch vụ từ bên ngoài

Thực sự là hơi loằng ngoằng, nhưng hiện tại mình chỉ biết có đến thế. 
Cài đặt nginx từ máy chủ
```sh
sudo apt-get install nginx
```
Tạo file app trong /etc/nginx/sites-available/
```vi
app
server {
    listen 8000;
    location / {
        proxy_pass http://192.168.49.2:64660;
    }
}
```

Link app đến sites-enable
```sh
sudo ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enable/app
```

Test và reset lại nginx
```sh
sudo nginx -t 
sudo nginx -s reload
```
Bây giờ bạn có thể truy cập và pod từ bên ngoài bằng địa chỉ http:// < external-ip>:8000
