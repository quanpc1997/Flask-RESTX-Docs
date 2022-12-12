## [2. Quick start](/content/2-quick-start.md)

### a. Migrate from Flask-RESTPlus
Tại thời điểm này, Flask-RESTX vẫn tương thích 100% với API của Flask-RESTPlus. Tất cả những gì bạn cần làm là cập nhật các yêu cầu của mình để sử dụng Flask-RESTX thay vì Flask-RESTPlus. Sau đó, bạn cần cập nhật tất cả các lần nhập của mình. Điều này có thể được thực hiện bằng cách sử dụng một cái gì đó như:
```sh
find . -type f -name "*.py" | xargs sed -i "s/flask_restplus/flask_restx/g"
```
Cuối cùng, bạn cần cập nhật các configuration options của bạn _([Xem thêm Configuration Options](https://flask-restx.readthedocs.io/en/latest/configuration.html))_

### b. Initialization
```python
from flask import Flask
from flask_restx import Api

app = Flask(__name__)
api = Api(app)
```

Hoặc sử dụng Factory Pattern:
```python
from flask import Flask
from flask_restx import Api

api = Api()
app = Flask(__name__)
api.init_app(app)
```

### c. A Minimal API
```python
from flask import Flask
from flask_restx import Resource, Api

app = Flask(__name__)
api = Api(app)

@api.route('/hello')
class HelloWorld(Resource):
    def get(self):
        return {'Hello', 'World'}

if __name__ == '__main__':
    app.run(debug=True)
```
> **:boom: Chú ý**:
> Debug mode **bắt buộc phải tắt** trên môi trường Production.

Để chạy code trên ta dùng lệnh sau:
```sh
$ python api.py
* Running on http://127.0.0.1:5000/
* Restarting with reloader
```

Chúng ta có thể truy cập vào đường link: [http://127.0.0.1:5000/hello](http://127.0.0.1:5000/hello) để kiểm tra. Hoặc sử dụng cách sau:
```sh
curl http://127.0.0.1:5000/hello
{"hello": "world"}
```

### d. Resourceful Routing
[Xem thêm Resourceful Routing](https://flask-restx.readthedocs.io/en/latest/quickstart.html#migrate-from-flask-restplus)

### e. Endpoints
Một chương trình của bạn sẽ có nhiều URL. Bạn có thể thêm nhiều URL cùng lúc vào hàm __add_resource()__ hoặc sử dụng __route()__ decorator. Cả 2 đều nằm trong đối tượng **Api**. 

Cú pháp như sau:
```python
api.add_resource(class_name, url1, url2)
```
Ví dụ:
```python
api.add_resource(HelloWorld, '/hello', '/world')
# or

@api.route('/hello', '/world')
class HelloWorld(Resource):
    pass
```

Bạn cũng có thể thêm đường dẫn của mình thông qua các biến:
Ví dụ:
```python
api.add_resource(Todo, '/todo/<int:todo_id>', endpoint='todo_ep')

# or

@api.route('/todo/<int:todo_id>', endpoint='todo_ep')
class HelloWorld(Resource):
    pass
```

> :boom: **Chú ý:**
> Nếu request của bạn không match với bất kì enpoints nào. Flask-RESTX sẽ trả về lỗi 404 cùng với một vài gợi ý đến các endpoints khác mà có tên gần gần giống với endpoint đó. Điều này có thể được tắt bởi setting **RESTX_ERROR_404_HELP**. Cờ này sẽ set False trong config của bạn.

### g. Argument Parsing.
[Xem thêm Argument Parsing](https://flask-restx.readthedocs.io/en/latest/quickstart.html#migrate-from-flask-restplus)

### h. Data Formatting.
Ta có thể tạo một model từ Class Api. Sử dụng decorator **marshal_with** để đẩy các trường trong model vào trong 1 class. Lúc này class sẽ không cần tạo ra từng trường một.

```python
from flask import Flask
from flask_restx import fields, Api, Resource

app = Flask(__name__)
api = Api(app)

model = api.model('Model', {
    'task': fields.String,
    'uri': fields.Url('todo_ep')
})

class TodoDao(object):
    def __init__(self, todo_id, task):
        self.todo_id = todo_id
        self.task = task

        # This field will not be sent in the response
        self.status = 'active'

@api.route('/todo')
class Todo(Resource):
    @api.marshal_with(model)
    def get(self, **kwargs):
        return TodoDao(todo_id='my_todo', task='Remember the milk')
```
[Xem thêm Data Formatting](https://flask-restx.readthedocs.io/en/latest/quickstart.html#migrate-from-flask-restplus)