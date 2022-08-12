# Nginx proxy pass ssl localhost
Thực tế thường sử dụng Nginx như một proxy để truy cập các docker service đằng sau (như wordpress hay Nodejs). Vì vậy, cùng với việc cấu hình nginx điều hướng khi có request qua cổng 80 hay 443 về website a về app A và request website B về  app B qua proxy_pass. Ngoài ra việc cấu hình còn sử dụng chứng chỉ ssl để cấu hình qua cổng 443

Việc cấu hình ssl hóa ra phức tạp hơn mình tưởng. Phải mất hơn 1 tuần để ra được cấu hình đơn giản nhất.

Dưới đây là một setup như vậy

## 1. Sử dụng Nodejs viết một https đơn giản chạy trên port 8000

### 1.1 Tạo file public cert.pem và file private key.pem
```bash
# generate a private key with the correct length
openssl genrsa -out key.pem 3072

# generate corresponding public key
openssl req -new -key key.pem -out csr.pem

# optional: create a self-signed certificate
openssl x509 -req -days 9999 -in csr.pem -signkey key.pem -out cert.pem

rm csr.pem

```

### 1.2 Viết file index.js đơn giản

```v
const https = require('https');
const fs = require('fs');
const options = {
  key: fs.readFileSync('key.pem'),
  cert: fs.readFileSync('cert.pem')
};

https.createServer(options, function (req, res) {
  res.writeHead(200);
  res.end("<h1>hello world</h1>\n<h2>From Nodejs port 8000</h2>");
// console.log("https, port: ",port);
}).listen(8000);

```

## 2. Cấu hình ssl trên nginx và proxy_pass đến Nodejs
### 2.1 Tạo file ssl trên nginx
Tạo một thư mục đường dẫn bất kì (ở đây ta chọn folder /etc/nginx/ssl/)
```bash
openssl req -x509 -nodes -new -sha256 -days 1024 -newkey rsa:2048 -keyout RootCA.key -out RootCA.pem -subj "/C=US/CN=Example-Root-CA"

openssl x509 -outform pem -in RootCA.pem -out RootCA.crt

openssl req -new -nodes -newkey rsa:2048 -keyout localhost.key -out localhost.csr -subj "/C=US/ST=YourState/L=YourCity/O=Example-Certificates/CN=localhost.local"

openssl x509 -req -sha256 -days 1024 -in localhost.csr -CA RootCA.pem -CAkey RootCA.key -CAcreateserial -extfile domains.ext -out localhost.crt

```

Lưu ý, đây là môi trường developer, nếu site online bạn phải sử dụng key tạo bởi [letencript](https://letsencrypt.org/vi/)


Cấu hình file node.conf trên /etc/nginx/sites-available/node.conf để khai báo dịch vụ

```vi
upstream backend{
	#server raspberrypi.local:8000;
	server localhost:8000;
}
server{
	listen 80;
	return 301 https://$host$request_uri;
}
server{
	listen 443 ssl http2;
	ssl_certificate /etc/nginx/ssl/localhost.crt;
	ssl_certificate_key /etc/nginx/ssl/localhost.key;
	access_log	/var/log/nginx/node_access.log;
	error_log	/var/log/nginx/node_error.log;

	location / {
	        proxy_set_header        Host       $host;
      		proxy_set_header        X-Forwarded-Proto $scheme;
		proxy_pass  https://backend;
    }
}

```
Ở đây ta chuyển request http (qua cổng 80) sang https (qua cổng 443) rổi proxy_pass qua Nodes đã cập nhật chứng chỉ self-ssl sử dụng openssl

Đây là chứng chỉ tự tạo nên khi lần đầu tiên truy cập, Browser như Firefox hay Chrome sẽ đánh dấu website sử dụng chứng chỉ không an toàn. Bạn phải click vào phần Advace và still process to website
