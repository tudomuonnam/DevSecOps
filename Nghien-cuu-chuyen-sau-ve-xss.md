
# Mô hình bảo mật của trình duyệt web
- Ngăn webside tự động truy cập vào tài nguyên trên máy: đọc file, tải file ...
- Ngăn website tự truy cập vào website khác từ trình duyệt (same-site-origin (SOP))
# Cách XSS hoạt động

- Nơi nhập thông tin (input) vào website
- Nơi lưu trữ payload 
## Self XSS

- Lỗi từ website nơi chỉ có bạn tự truy cập và lây nhiễm (như trang setting từ profile của bạn)

## Blind XSS

# Hiểu thêm về XSS

Có 3 cách để thực thi javascript
- HTML injection: <script> tag
```
<?php
 echo "Hello, <h1>" . $_GET['name'] . '</h1>';
?>
```
- Attribute injection: Xử lý sự kiện trong thuộc tính (luôn bắt đầu vớn on: onload, onerror, )
```
<div class=""><img src=not_exist onerror=alert(1)>">
  Demo
</div>

```
- Javascript injection: javascript: giao thức giả
```
<script>
  const name = "<?php echo $_GET['name'] ?>";
  alert(name);
</script>
Hoặc 
<script> 
document.body.innerHTML =
  '<iframe srcdoc="&lt;script>alert(1)&lt;/script>"></iframe>';
</script>
```
Các giao thức thường dùng liên quan đến mạng như http, https, ftp, pseudo protocol không liên quan đến mạng như mailto:, tel: . Đặc biệt nguy hiểm khi chúng dùng đến javascript

Ví dụ: 
`
<a href="javascript:alert(1)">Link</a>
`
Trong iframe
`<iframe src="javascript:alert(1)"></iframe>`
Không giống như trong thẻ a, cần người dùng tương tác, iframe có thể tự chạy 

Trong action form
```html
<form action="javascript:alert(1)">
  <button>submit</button>
</form>

<form id="f2"></form>
<button form="f2" formaction="javascript:alert(2)">submit</button>
```
Nếu không lọc user input cẩn thận, hacker có thể lợi dụng để tác động đến các phần mã này. 

Ví dụ, khi một trang web cho phép người dùng nhập một địa chỉ youtube và hiển thị video lên website, họ thường viết như sau: 

```html
<iframe src="<?= $youtube_url ?>" width="500" height="300"></iframe>
```
Nếu người dùng nhập `javascript:alert(1)` hoặc nếu yêu cầu xác thực địa chỉ có thể dùng `javascript:alert(1);console.log('youtube.com')`

Các frontend cũng có thể mắc lỗi này nếu developer không lưu ý. Xem thêm tại #Nguồn

## Page redirect có thể ẩn chứa XSS

Logic thông thường của webiste, khi login sẽ redirect về vị trí trước khi đăng nhập 

```html
const searchParams = new URLSearchParams(location.search);
window.location = searchParams.get("redirect");
```
Tuy nhiên nếu chúng ta truy cập trang đăng nhập bằng link: 

`https://example.com/login?target=javascript:alert(1)`

Chúng ta có thể kích hoạt mã XSS 

# Chống lại XSS

## Xác thực đầu vào


# Nguồn
1. [](https://aszx87410.github.io/beyond-xss/en/ch1/browser-security-model/)