<style>
div.sourceCode::before {
    content: attr(data-filename);
    display: block;
    background-color: #cfeadd;
    font-family: monospace;
}
</style>

# Cách push docker image to docker hub


## Tạo image
Để đơn giản ta tạo một image flask http trên docker

Dockerfile tạo flask image
Tạo folder flaskapp gồm
```
.
├── Dockerfile
├── requirements.txt
├── templates
│   └── index.html
└── view.py

1 directory, 4 files
```
```vi
Dockerfile

FROM python:3.8-alpine

# copy the requirements file into the image
COPY ./requirements.txt /app/requirements.txt

# switch working directory
WORKDIR /app

# install the dependencies and packages in the requirements file
RUN pip install -r requirements.txt

# copy every content from the local file to the image
COPY . /app

# configure the container to run in an executed manner
ENTRYPOINT [ "python" ]

CMD ["view.py" ]
```

```python
"view.py"

from flask import Flask, render_template
import os

app = Flask(__name__)


@app.route('/')
def home():
    return render_template('index.html')


if __name__ == "__main__":
    port = int(os.environ.get('PORT', 5000))
    app.run(debug=True, host='0.0.0.0', port=port)

```
```html
templates/index.html 

<!DOCTYPE html>

<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Flask Docker</title>
</head>
<body>
    <h1>This is a Flask App containerised with Docker</h1>
</body>
</html>

```

Tạo file requerements.txt bằng cách
```terminal
pip install pipreqs

pipreqs .
```

Sau khi tạo file xong, từ trong thư mục, tạo image docker bằng lệnh
```terminal
docker image build -t flask_docker .
```
Kiểm tra lại image đã có sẵn chưa
```ternimal
docker images
REPOSITORY     TAG       IMAGE ID       CREATED        SIZE
flask_docker   latest    b1f22b9e379f   34 hours ago   58.3MB
```
Chỉnh sửa lại image trước khi push. Lưu ý, để push image lên [Docker hub](https://hub.docker.com) bạn phải đăng kí tài khoản trước.

```terminal
# Đăng nhập docker hub
docker login
# Thêm tag và đổi image theo định dạng <user>/tên-image
docker tag flask_docker:latest <user>/flask-docker
docker push <user>/flask_docker
```
Bạn có thể lên trang [Docker hub](https://hub.docker.com)vào mục Repostory để kiểm tra lại

## Đọc thêm

[Link flask app - github](https://github.com/tudomuonnam/flaskApp)
