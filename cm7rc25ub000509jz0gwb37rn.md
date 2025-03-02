---
title: "Step-by-Step Guide to Building a Static Website Using AWS, Nginx, and Cloudflare"
seoTitle: "AWS, Nginx, Cloudflare for static website"
seoDescription: "Learn to build a secure static website using AWS, Nginx, and Cloudflare. Optimize for speed, security, and scalability"
datePublished: Sun Mar 02 2025 07:52:09 GMT+0000 (Coordinated Universal Time)
cuid: cm7rc25ub000509jz0gwb37rn
slug: step-by-step-guide-to-building-a-static-website-using-aws-nginx-and-cloudflare
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740901815286/80e7aae5-9119-48c2-9f6b-95c01c4b2e66.png
tags: cloudflare, ec2, aws, development, design, nginx, security, developer, devops, software-engineering, aws-lambda, devops-articles, devops-journey, devopscommunity, ec2-instance

---

---

## **Why This Stack?**

* **AWS EC2**: Flexible virtual servers with full control over configuration.
    
* **Nginx**: High-performance web server optimized for static content.
    
* **Cloudflare**: Free CDN, DDoS protection, and SSL/TLS encryption.
    

---

## **Prerequisites**

1. An AWS account with EC2 access.
    
2. A domain name (e.g., [`example.com`](http://example.com)).
    
3. A Cloudflare account.
    
4. Basic terminal/SSH skills.
    

---

## **Step 1: Launch an EC2 Instance**

Start by launching an Ubuntu 22.04 LTS instance on AWS EC2.

### **1.1 Create a Security Group**

Allow HTTP, HTTPS, and SSH traffic:

```bash
# Create security group  
aws ec2 create-security-group \
  --group-name StaticWebsiteSG \
  --description "Security group for static website"

# Allow SSH (port 22), HTTP (80), and HTTPS (443)  
aws ec2 authorize-security-group-ingress \
  --group-name StaticWebsiteSG \
  --protocol tcp --port 22 --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
  --group-name StaticWebsiteSG \
  --protocol tcp --port 80 --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
  --group-name StaticWebsiteSG \
  --protocol tcp --port 443 --cidr 0.0.0.0/0
```

### **1.2 Launch the Instance**

```bash
aws ec2 run-instances \
  --image-id ami-0c55b159cbfafe1f0 \  # Ubuntu 22.04 LTS in us-east-1  
  --instance-type t2.micro \
  --key-name YourKeyPair \
  --security-groups StaticWebsiteSG \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=StaticWebServer}]'
```

**Note**: Replace `YourKeyPair` with your EC2 key pair name.

---

## **Step 2: Install and Configure Nginx**

SSH into your EC2 instance and set up Nginx.

### **2.1 Connect to the Instance**

```bash
ssh -i "your-key.pem" ubuntu@<EC2_PUBLIC_IP>
```

### **2.2 Install Nginx**

```bash
sudo apt update && sudo apt upgrade -y  
sudo apt install nginx -y  
sudo systemctl start nginx  
sudo systemctl enable nginx
```

### **2.3 Deploy Static Files**

Create a directory for your site and upload your HTML/CSS/JS files:

```bash
sudo mkdir -p /var/www/example.com/html  
sudo chown -R ubuntu:ubuntu /var/www/example.com  
nano /var/www/example.com/html/index.html  # Add your static content
```

### **2.4 Configure Nginx Server Block**

Create a config file for your domain:

```bash
sudo nano /etc/nginx/sites-available/example.com
```

**Paste this configuration**:

```nginx
server {
    listen 80;
    listen [::]:80;

    root /var/www/example.com/html;
    index index.html;

    server_name example.com www.example.com;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Enable the site and test the config:

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/  
sudo nginx -t              # Validate syntax  
sudo systemctl reload nginx
```

---

## **Step 3: Secure the Server**

### **3.1 Configure Firewall (UFW)**

```bash
sudo ufw allow 'Nginx Full'  
sudo ufw enable
```

### **3.2 Enable HTTPS with Let’s Encrypt**

Install Certbot and request a certificate:

```bash
sudo apt install certbot python3-certbot-nginx -y  
sudo certbot --nginx -d example.com -d www.example.com
```

Certbot automatically updates your Nginx config to use HTTPS.

---

## **Step 4: Set Up Cloudflare**

### **4.1 Add Your Domain to Cloudflare**

1. Log in to Cloudflare and add your domain.
    
2. Note the Cloudflare nameservers (e.g., [`lara.ns.cloudflare.com`](http://lara.ns.cloudflare.com)).
    
3. Update your domain’s DNS nameservers via your registrar.
    

### **4.2 Create DNS Records**

Add an **A record** pointing to your EC2 instance’s public IP:

```bash
# Use Cloudflare API (optional)  
curl -X POST "https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/dns_records" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  --data '{
    "type": "A",
    "name": "example.com",
    "content": "<EC2_PUBLIC_IP>",
    "proxied": true
  }'
```

### **4.3 Configure SSL/TLS**

1. In Cloudflare Dashboard:
    
    * Go to **SSL/TLS &gt; Overview** and set mode to **Full (strict)**.
        
    * Enable **Always Use HTTPS**.
        

### **4.4 Enable Caching**

Create a **Page Rule** to cache static assets:

```yaml
URL Pattern: example.com/*  
Settings: Cache Level = Cache Everything
```

---

## **Step 5: Optimize Performance**

### **5.1 Enable Gzip Compression in Nginx**

Edit `/etc/nginx/nginx.conf` and add:

```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

### **5.2 Configure Cloudflare Caching**

Set browser cache TTL to 1 month:

```bash
# Use Cloudflare API  
curl -X PATCH "https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/settings/browser_cache_ttl" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  --data '{"value": 2592000}'
```

---

## **Step 6: Monitor and Maintain**

* **Nginx Logs**: Check access/error logs at `/var/log/nginx`.
    
* **Cloudflare Analytics**: Monitor traffic and threats in the dashboard.
    
* **Renew SSL Certificates**: Certbot auto-renews certificates, but test manually:
    
    ```bash
    sudo certbot renew --dry-run
    ```
    

---

## **Architecture**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740901268159/e7011d36-04ab-4716-adbf-25cad8545a54.jpeg align="center")

  
*Your site is now served via Nginx on EC2, protected by Cloudflare’s CDN and security features.*

---

## **Cost Breakdown**

* **EC2**: ~$3.50/month (t2.micro, non-free tier).
    
* **Cloudflare**: Free plan includes CDN, SSL, and DDoS protection.
    
* **Domain**: ~$10/year (varies by registrar).
    

---

## **Conclusion**

You’ve built a production-ready static website with AWS EC2, Nginx, and Cloudflare. This setup guarantees speed, security, and scalability. To automate further, explore **Terraform** for infrastructure-as-code or **GitHub Actions** for CI/CD.

*Got questions? Drop them in the comments below! For more guides, subscribe to our newsletter. 🚀*

---

**Further Reading**:

* [Nginx Documentation](https://nginx.org/en/docs/)
    
* [Cloudflare SSL Best Practices](https://developers.cloudflare.com/ssl/)
    
* [AWS EC2 Pricing](https://aws.amazon.com/ec2/pricing/)