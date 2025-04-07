
# 🚀 Docker + NGINX Load Balancer Setup

This project demonstrates how to containerize a Node.js application using Docker, scale it using Docker Compose, and load balance traffic using NGINX. Static files are served directly from NGINX for performance.

---

## 📦 1. Docker Setup and Version Check

```bash
docker version
```
- Verify Docker is installed and running.

---

## 🛠️ 2. Docker Image Creation

### Dockerfile:
```Dockerfile
FROM node:14
WORKDIR /app
COPY server.js .
COPY index.html .
COPY images ./images
COPY package.json .
RUN npm install
EXPOSE 3000
CMD ["node", "server.js"]
```

### Build Image:
```bash
docker build -t myapp:1.0 .
```

---

## ▶️ 3. Run Docker Container

```bash
docker run -p 3000:3000 myapp:1.0
```
- Runs the container and exposes it on port 3000.

---

## ⚙️ 4. Docker Compose for Multiple Instances

### docker-compose.yml:
```yaml
version: '3'
services:
  app1:
    build: .
    environment:
      - APP_NAME=App1
    ports:
      - "3001:3000"
  app2: 
    build: .
    environment:
      - APP_NAME=App2
    ports:
      - "3002:3000"
  app3:
    build: .
    environment:
      - APP_NAME=App3
    ports:
      - "3003:3000"
```

### Run:
```bash
docker-compose up --build -d
```

---

## 🌐 5. NGINX Installation

```bash
brew install nginx
```
- Installs NGINX on macOS.

---

## 🔀 6. NGINX as a Load Balancer

### nginx.conf:
```nginx
http {
    upstream nodejs_cluster {
        least_conn;
        server 127.0.0.1:3001;
        server 127.0.0.1:3002;
        server 127.0.0.1:3003;
    }
    server {
        listen 8080;
        location / {
            proxy_pass http://nodejs_cluster;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

### Restart NGINX:
```bash
brew services restart nginx
```

---

## 🖼️ 7. Serve Static Files via NGINX

### Move Files:
```bash
mkdir -p /opt/homebrew/var/www/images
cp ./images/*.jpg /opt/homebrew/var/www/images/
```

### Add to nginx.conf:
```nginx
location /images/ {
    root /opt/homebrew/var/www;
}
```

---

## 🧪 8. Testing

- **Browser:** http://localhost:8080/images/guitar.jpg
- **Curl:** 
```bash
curl -I http://localhost:8080/images/guitar.jpg
```

---

## ✅ Final Outcome

- Node.js app runs in Docker containers.
- Three replicas via Docker Compose.
- Load balanced with NGINX.
- Static files served directly from NGINX.
- Fully verified setup using browser and CLI.

---