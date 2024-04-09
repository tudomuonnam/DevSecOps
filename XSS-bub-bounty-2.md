
XSS Ngoài vùng xác định

# Mô tả

Một website dạng `comunity.redacted.com` có lỗi XSS nhưng nằm ngoài vùng nhận được tiền thưởng (là vùng website redacted.com) nên mọi người bỏ qua không xem xét sâu hơn. 

Tuy nhiên dựa vào lỗi thiết lập CORS (Cross-Origin Resource Sharing - chia sẻ tài nguyên trong một tên miền). Khi thiết lập `Access-Control-Allow-Origin` trên header của website `*.redacted.com`, bằng cách khai thác thiết lập trên `comunity.redacted.com`, hacker có thể truy cập vào `redacted.com`

Vấn đề còn tốt hơn nữa khi tiêu đề `Access-Control-Allow-Credentials` được đặt thành `true`. Tiêu đề này cho biết yêu cầu từ `comunity.redacted.com` đến `*.redacted.com` có thể bao gồm thông tin xác thực như cookie, tiêu đề ủy quyền hoặc chứng chỉ ứng dụng khách TLS hay không.  Điều đó có nghĩa là máy chủ sẵn sàng chấp nhận thông tin xác thực từ nguồn yêu cầu, giả sử nó được cho phép bởi tiêu đề `Access-Control-Allow-Origin`.

Tóm lại, bằng cách cho phép `Access-Control-Allow-Origin` các website có dạng `*.redacted.com` có thể chia sẻ thông tin và quan trọng là `Access-Control-Allow-Credentials` bật có thể cho phép chia sẻ thông tin về cookies, xác thực.

# Khai thác lỗi

Sử dụng lỗi xss trên `community.redacted.com` truy cập vào `redacted.com` để lấy thông tin nhạy cảm 

```
url= 'https://www.redacted.com/profile'; 
fetch(url ,{credentials:`include`}) // To send the cookies in the request
  .then(response=>{return(response.text());}) 
  .then(data=>alert(JSON.stringify(JSON.parse(data)[`user`])));
  
``` 

### Source: 

1. [XSS on out of scope domain? CORS is your secret weapon](https://c4rrilat0r.medium.com/xss-on-out-of-scope-domain-cors-is-your-secret-weapon-93e433278080)
