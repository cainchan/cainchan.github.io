---
title: Flask+Gevent实现异步非阻塞访问
date: 2017-05-01
tags:
 - Python
 - Flask
---

```python
from flask import Flask
from gevent import monkey
from gevent.pywsgi import WSGIServer
monkey.patch_all()
app = Flask(__name__)

@app.route("/")
def hello():
    return "Welcome to Flask!"

if __name__ == "__main__":
    http_server = WSGIServer(('0.0.0.0', 5000), app)
    http_server.serve_forever()
```

