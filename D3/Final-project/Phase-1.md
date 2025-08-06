<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# based on the above code, create an complete end to end lab setup with all dependencies details

Below is a **complete end-to-end lab setup** for a Python + Redis application with all dependencies and instructions for CI/CD using GitHub Actions and Docker. This guide provides everything you need:

## 1. Project Structure

```
.
├── app.py
├── requirements.txt
├── Dockerfile
├── .flake8            # (Optional, for code style)
├── tests/
│   └── test_basic.py
└── .github/
    └── workflows/
        └── ci-cd.yml
```


## 2. Application Code (`app.py`)

```python
from flask import Flask, jsonify
import redis
import os

app = Flask(__name__)

redis_host = os.getenv('REDIS_HOST', 'localhost')
redis_port = int(os.getenv('REDIS_PORT', 6379))
redis_client = redis.Redis(host=redis_host, port=redis_port, decode_responses=True)

@app.route('/')
def home():
    return "Welcome to the Python Redis app!"

@app.route('/set/<key>/<value>')
def set_key(key, value):
    redis_client.set(key, value)
    return jsonify({"message": f"Key '{key}' set to '{value}'."})

@app.route('/get/<key>')
def get_key(key):
    value = redis_client.get(key)
    if value:
        return jsonify({key: value})
    else:
        return jsonify({"error": f"Key '{key}' not found."}), 404

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```


## 3. Dependencies (`requirements.txt`)

```
flask
redis
pytest
flake8
```


## 4. Dockerfile

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV REDIS_HOST=redis
ENV REDIS_PORT=6379
EXPOSE 5000
CMD ["python", "app.py"]
```


## 5. Optional: Code Style (`.flake8`)

```
[flake8]
max-line-length = 88
extend-ignore = E203, W503
```


## 6. Testing Code (`tests/test_basic.py`)

```python
import redis

def test_redis_connection():
    r = redis.Redis(host="localhost", port=6379, decode_responses=True)
    r.set("foo", "bar")
    value = r.get("foo")
    assert value == "bar"
```


## 7. CI/CD Workflow (`.github/workflows/ci-cd.yml`)

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    services:
      redis:
        image: redis:latest
        ports:
          - 6379:6379

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python 3.11
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest flake8

      - name: Run Lint (flake8)
        run: flake8 .

      - name: Run Tests
        env:
          REDIS_HOST: localhost
          REDIS_PORT: 6379
        run: pytest tests/

      - name: Build Docker image
        run: docker build -t ghcr.io/${{ github.repository }}:latest .

      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Push Docker image
        run: |
          docker tag ghcr.io/${{ github.repository }}:latest ${{ secrets.DOCKERHUB_USERNAME }}/sample-python-redis:latest
          docker push ${{ secrets.DOCKERHUB_USERNAME }}/sample-python-redis:latest

      # - name: Deploy to server (optional, via SSH)
      #   uses: appleboy/ssh-action@v0.1.10
      #   with:
      #     host: ${{ secrets.SERVER_IP }}
      #     username: ${{ secrets.SERVER_USER }}
      #     key: ${{ secrets.SERVER_SSH_KEY }}
      #     script: |
      #       docker pull ${{ secrets.DOCKERHUB_USERNAME }}/sample-python-redis:latest
      #       docker stop app || true && docker rm app || true
      #       docker run -d --name app -p 80:5000 ${{ secrets.DOCKERHUB_USERNAME }}/sample-python-redis:latest
```


## 8. Setup Notes and Guidance

- **Secrets**: In GitHub repo, set `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`, and (optionally) deploy SSH keys as repository secrets.
- **pytest**: All test code should be in `tests/` directory with files prefixed by `test_`.
- **Docker Deployment**: Out-of-the-box, you can run the final image locally:

```bash
docker run -it --rm -e REDIS_HOST=<host_ip> -e REDIS_PORT=6379 -p 5000:5000 <your_image>
```

- **Redis**: For local testing, you can run Redis using Docker or install natively:

```bash
docker run -p 6379:6379 redis
```


## 9. Lab Execution Steps

1. **Clone the repository**
2. **Set up GitHub repository secrets**
3. **Push code to main branch**
4. **Observe GitHub Actions trigger pipeline:** auto-test, lint, build image, push image, (optional: deploy)
5. **Application accessible at**: `http://<docker-host>:5000/`

This setup ensures you have a working sample app, automated tests, Docker containerization, and a secure full CI/CD pipeline with all dependencies and configurations clearly declared. If you need step-by-step lab instructions or troubleshooting, let me know!

