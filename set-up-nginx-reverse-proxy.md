Bài toán: Trên VPS có hai dịch vụ khác nhau chạy trên các IP riêng biệt, Ví dụ

- App 1: Website giới thiệu (chạy trên IP1)
- App 2: Blog (chay trên IP2)

Setup nginx sao cho khi trỏ địa chỉ gốc.nginx sẽ chuyển tới IP1, trỏ tới /blog nginx sẽ chuyển tới IP2

Triển khai
1. Cài đặt nginx (sử dụng Raspi)

```bat
sudo apt-get install nginx -y
```
2. Disable virtual host
```bash
sudo unlink /etc/nginx/sites-enabled/default
```
3. Tạo nginx reverse Proxy
```bash
sudo vi sites-available/reverse-proxy.conf
```
Thêm nội dung vào file
```v
server {
    listen 80;
    location / {
        proxy_pass http://192.x.x.2;
    }
}
```
Ở dòng địa chỉ proxy_pass bạn có thể thay bằng địa chỉ trang web khác, mạng nội bộ, hoặc thêm post ví dụ:
```v
server {
    listen 80;
    location /blog {
        proxy_pass http://localhost:8000;
    }
}
```
4. Thêm cấu hình vào sites-enable/
```sh
sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/reverse-proxy.conf
```
a. Passing Request Headers

Mặc định Nginx định nghĩa lại 2 trường header trong request là "Host" và "Connection" và loại bỏ các trường header khác nếu chúng rỗng. Host đặt là Proxy_host và Connection đặt là close

Để thay đổi thiết lập này, bạn có thể cấu hình trong conf như sau:
```v
location /some/path/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_pass http://localhost:8000;
}
```
Để ngăn header pass đến proxy, bạn có thể cấu hình để các trường này rỗng:
```v 
location /some/path/ {
    proxy_set_header Accept-Encoding "";
    proxy_pass http://localhost:8000;
}
```

b. Cấu hình bộ nhớ đệm

Mặc định Nginx lưa phản hồi từ Proxy vào bộ nhớ đệm. Khi nhận được đầy đủ phản hồi, Nginx mới gửi trả client. Bằng cách cấu hình bộ nhớ đệm này, Bạn có thể cho phép Nginx lưa trữ số response và kích thước của response:
```v
location /some/path/ {
    proxy_buffers 16 4k;
    proxy_buffer_size 2k;
    proxy_pass http://localhost:8000;
}
```
Bạn cũng có thể tắt bộ nhớ đệm này. 

c. Lựa chọn IP_Address request

Nếu proxy server được cấu hình để chỉ nhận request từ 1 địa chỉ IP, bạn có thể cấu hình để request proxy từ IP Nginx sử dụng proxy_bind
```v
location /app1/ {
    proxy_bind 127.0.0.1;
    proxy_pass http://example.com/app1/;
}

location /app2/ {
    proxy_bind 127.0.0.2;
    proxy_pass http://example.com/app2/;
}
```

5. Kiểm tra cấu hình dịch vụ
```sh
sudo nginx -t
```
Nếu không báo lỗi, restart lại nginx
```sh
sudo service nginx restart
```
6. Truy cập vào trang đã cấu hình và tận hưởng
http://localhost/blog

## Link tham khảo
1. https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/