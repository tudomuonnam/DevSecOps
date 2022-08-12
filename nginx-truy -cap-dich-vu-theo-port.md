# Bài toán: 
Trên VPS có hai app chạy trên docker, sử dụng hai port khác nhau, Ví dụ

- App 1, domain_name web1.local : Port 8001
- App 2, domain_name web2.local : Port 8002

Setup nginx sao cho khi trỏ địa chỉ web1.local sẽ tới app 1, web2.local sẽ tới app2

## Thực hiện

Thêm file config to /etc/nginx/sites-enable/multi_port.conf
(Tốt nhất là thêm vào /nginx/sites-available/ sau đó link đến /nginx/sites-enable)

file multi_port.conf
```v


server {
    server_name A.domain.com;
    listen 80;
    root someplace/;
    try_files /maintenance.html $uri $uri.html @puma;
    location / {
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header Host $http_host; 
        proxy_set_header Client-IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;               
        proxy_pass http//localhost:8001;
    }
}

server {
    server_name B.domain.com;
    listen 80;
    root someplace/;
    try_files /maintenance.html $uri $uri.html @puma;
    location / {
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header Host $http_host; 
        proxy_set_header Client-IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;               
        proxy_pass http//localhost:8002;
    }
}
```