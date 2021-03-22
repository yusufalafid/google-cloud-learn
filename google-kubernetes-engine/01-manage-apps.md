01. Create Apps 
```
mkdir lac-app && cd lac-app 
vim app.py
---
from flash import Flask
import os
import socket

app = Flask(__name__)

@app.route("/")
def hello():
    html = "<h1 style=text-align:center;margin:20px;>Greeting from linux academy</h1>"
    return html

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)
---

vim Dockerfile
---
FROM python:2.7-slim
WORKDIR /app
ADD . /app

RUN pip install -r requirements.txt

EXPOSE 80

CMD ["python", "app.py"]
---

vim requirements.txt
---
Flask
---
```

02. Build App

```
docker build -t lac-app-image 
```

03. Configure docker auth 

```
gcloud auth configure-docker
```

04. Tag image 

```
docker tag lac-app-image gcr.io/playground-s-11-94c871ea/lac-app-image:v1
```

05. Push image 

```
docker push gcr.io/playground-s-11-94c871ea/lac-app-image:v1
```

    