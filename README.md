# Flask_learning

## 1. Application

Một app Flask đơn giản như sau:

```Python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
   return 'Hello World’

if __name__ == '__main__':
   app.run()
```

Trong đó `Flask` constructors nhận vào tên của `current module` qua tham số `__name__`.

`route()` function để define route cho app. Cú pháp như sau:

```Python
@app.route(rule, options)
```

- `rule` là tên route
- `options` là list các parameters truyền vào `Rule` object

Cuối cùng là chạy server lên với syntax:

```Python
app.run(host, port,debug,options)
```

Chú thích tham số:

Tham số | Description
--------|------------
host | Nếu không truyền vào thì default host là `127.0.0.1`(localhost). Truyền vào `0.0.0.0` để chạy máy chủ riêng.
port | port chạy server, nếu không truyền vào thì default là 5000
debug | Default là false ở chế độ này nếu có thay đổi trong code thì phải kill terminal đi khởi động lại server. Khi set sang `True` thì server sẽ tự động lắng nghe các thay đổi trong code và tự động khởi động lại.
options | To be forwarded to underlying Werkzeug server.

## 2. Routing

Như với ví dụ đơn giản thứ 1 thì route được define như sau:

```Python
@app.route('/route_name')
def route_func():
   ...smt
```

Để cho clean hơn thì ta có cách viết khác như sau:

```Python
app.add_url_rule('/route_name',route_func )
```

## 3. Route Params

Để sử dụng dynamic route với params thay đổi, ta có:

```Python
@app.route('/profile/<id>')
def profile(id)
   return 'Hello %s' % id
```

Với ví dụ trên thì phần thay đổi trong route là `<id>`, route này sẽ nhận các các giá trị id khác nhau và được truyền làm tham số trong `route function`. Chú ý rằng ở đây bắt buộc phải có tham số truyền vào `route function` và tham số này bắt buộc phải cùng tên với `slug` để ở `route` đã define. Trong trường hợp này là `id`.

Ta có thể định nghĩa kiểu dữ liệu cho `params`. Kiểu `int`, `float` hoặc `path`.

- Default là kiểu `path` tức param có thể mang giá trị gồm các kí tự

- Kiểu `int`, param chỉ nhận tham số kiểu `int`, ví dụ nếu cho vào param là `/profile/2.0` hoặc `/profile/a` => Sẽ lỗi

- Kiểu `float`, param chỉ nhận tham số kiểu `float` => nếu cho vào param là `/profile/2` hoặc `/profile/a` => Sẽ lỗi

```Python
@app.route('/profile/<int:id>')   #int
@app.route('/profile/<float:id>') # float
@app.route('/profile/<id>')       #path
```

Chú ý nếu như define route như sau:

```Python
@app.route('/home')
@app.route('/home/')
```

Đối với kiểu đầu tiên. Khi ta chỉ có thể truy cập server thông quá route chính xác là `/home`, nếu như truy cập bằng `/home/` => sẽ lỗi

Đối với kiểu thứ 2. Ta có thể truy cập server bằng cả 2 route là `/home` hoặc `/home/` thì đều như nhau.

## 4. URL building

```Python
from flask import Flask, redirect, url_for
app = Flask(__name__)

@app.route('/admin')
def hello_admin():
   return 'Hello Admin'

@app.route('/guest/<guest>')
def hello_guest(guest):
   return 'Hello %s as Guest' % guest

@app.route('/user/<name>')
def hello_user(name):
   if name =='admin':
      return redirect(url_for('hello_admin'))
   else:
      return redirect(url_for('hello_guest',guest = name))

if __name__ == '__main__':
   app.run(debug = True)
```

Như vậy khi vào `route` là `/user/admin` => redirect sang trang `/admin`.

Khi vào `route` là `/user/smt` => redirect sang trang `/guest/smt`.