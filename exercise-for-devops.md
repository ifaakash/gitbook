# Exercise for DevOps

<pre class="language-python"><code class="lang-python">// Some code
from flask import Flask, Response from prometheus_client import Counter, Histogram, generate_latest, REGISTRY import time
<strong>
</strong><strong>app = Flask(name)
</strong>REQUEST_COUNT = Counter('app_requests_total', 'Total app HTTP requests') 
REQUEST_LATENCY = Histogram('app_request_latency_seconds', 'Request latency in seconds')

@app.route('/') 
@REQUEST_LATENCY.time() 
<strong>    def hello(): REQUEST_COUNT.inc() 
</strong><strong>    return "Hello, OpenShift!"
</strong>@app.route('/health') 
    def health(): 
    return "OK", 200
@app.route('/metrics') 
    def metrics(): 
    return Response(generate_latest(REGISTRY), mimetype='text/plain')

if name == 'main': 
app.run(host='0.0.0.0', port=8080)
</code></pre>



Dockerfile

```docker
// Some code
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 8080

CMD ["python", "app.py"]
```



Requirements.txt

> Flask==2.0.1&#x20;
>
> prometheus-client==0.11.0

