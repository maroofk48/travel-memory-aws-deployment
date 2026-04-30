#  Travel Memory – MERN Stack Deployment on AWS

##  Project Overview

Travel Memory is a full-stack MERN application that allows users to add and view travel experiences.
This project demonstrates deployment of a production-ready application on AWS with scalability, load balancing, and custom domain integration.

---

##  Tech Stack

* **Frontend:** React.js
* **Backend:** Node.js, Express.js
* **Database:** MongoDB
* **Web Server:** Nginx
* **Process Manager:** PM2
* **Cloud Platform:** AWS (EC2, ALB, Route 53, ACM)

---

##  Architecture

* React frontend served via Nginx
* Node.js backend running on EC2
* Nginx used as reverse proxy (`/api → backend`)
* Multiple EC2 instances behind Application Load Balancer
* Domain mapped using Route 53
* HTTPS enabled using AWS Certificate Manager

---

##  Deployment Steps

### 1️⃣ Launch EC2 Instances

* Create Ubuntu EC2 instances
* Allow ports: 22, 80, 443, 3000

---

### 2️⃣ Backend Setup

```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory/backend
npm install
```

Create `.env` file:

```env
PORT=3000
MONGO_URI=your_mongodb_connection_string
```

Run backend:

```bash
pm2 start index.js
pm2 save
```

Test:

```bash
curl http://localhost:3000/hello
```

---

### 3️⃣ Nginx Reverse Proxy Setup

Edit config:

```bash
sudo nano /etc/nginx/sites-available/default
```

Add:

```nginx
server {
    listen 80;

    location / {
        root /var/www/html;
        index index.html;
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:3000/;
    }
}
```

Restart:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

### 4️⃣ Frontend Setup

```bash
cd ../frontend
npm install
npm run build
```

Copy build:

```bash
sudo rm -rf /var/www/html/*
sudo cp -r build/* /var/www/html/
```

---

### 5️⃣ Fix API URL

Update file:

```js
src/url.js
```

```js
export const baseUrl = "http://<YOUR-DOMAIN>/api";
```

Rebuild frontend again after change.

---

### 6️⃣ Application Load Balancer

* Create Target Group (HTTP : 80)
* Register EC2 instances
* Create ALB
* Add Listener (HTTP : 80)
* Attach target group

Test:

```bash
http://<ALB-DNS>
```

---

### 7️⃣ Domain Setup (Route 53)

* Create Hosted Zone
* Add record:

```
Type: A
Name: @
Alias: YES
Target: ALB DNS
```

---

### 8️⃣ HTTPS Setup (ACM)

* Request certificate in AWS Certificate Manager
* Validate domain (DNS validation)
* Attach certificate to ALB

Add HTTPS listener:

```
Port: 443
Protocol: HTTPS
```

---

##  Application URL
 https://mfaraunak.xyz

---

##  Load Balancing Test

* Created multiple EC2 instances
* Verified traffic distribution using test file:

```bash
echo "Server 1" > /var/www/html/test.txt
```

Result: Load balancer distributes requests across instances.

---

##  Architecture Diagram

(Attach draw.io diagram here)

```
User → Domain → ALB → EC2 (Nginx) → Backend (Node.js) → MongoDB
```

---

##

The Travel Memory application is successfully deployed on AWS using EC2, Nginx, and Application Load Balancer. The application is accessible via a custom domain and secured using HTTPS with AWS Certificate Manager. Load balancing ensures high availability and scalability.

---

## 

* EC2 deployment
* Nginx reverse proxy
* Load balancing (ALB)
* Domain mapping (Route 53)
* SSL setup (ACM)
* Full-stack production deployment

---

## Repository

GitHub Repo:
https://github.com/UnpredictablePrashant/TravelMemory
