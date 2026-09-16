### Important use case
* Basic web server
* Load Balancer
* HTTPs encryption while acting as reverse proxy.
* It also does caching when working as a proxy.
* Can compress large files (mostly video) or distribute data in chunks to improve speed and efficiency.
* Acts as **ingress controller** when working with kubernetes (Part load balancer) put in front of a AWS load balancer. Basically helps to hide the cluster architecture from the outside.

------------------------------------------------------

* The main configuration is stored in "/etc/nginx/nginx.conf".
* You can add configurations for sites in "/etc/nginx/conf.d/", "/etc/nginx/sites-available" or in the nginx.conf itself.
* Adding "mime.types" fixes any file type issues and user may not have to specify the files.
* You can also add rewrites and redirects.

------------------------------------------------------

### Location Block
Helps to serve specific pages or endpoints. If you want to serve a specific page from a specific folder in your project. (Also used for reverse proxy to back-end servers)

```
location /fruits {
	root /var/www/html/;
}

### Upstream Backend
You can use this to assign multiple backend servers (load balancing).
```

### Reverse Proxy

```
upstream backendserver {
	# You can define algo used to distribute here
	least_conn;
	server 127.0.0.1:1111;
	server 127.0.0.1:2222;
	server 127.0.0.1:3333;
	server 127.0.0.1:4444;
}

location / {
	proxy_pass http://backendserver
}
```

There are 2 types of ways Nginx does reverse proxy:
1. Least Connections: Sends requests to server with the least connections.
2. Round Robin: Sends requests in a distributed cyclic manner.

------------------------------------------------------
## Practical
[[Nginx Deep Dive Practical]]

### Commands:
* sudo systemctl start nginx
* sudo systemctl stop nginx
* sudo systemctl status nginx
* sudo nginx -t
* sudo systemctl reload nginx
### Error log locations
* /var/log/nginx/error.log
* /var/log/nginx/access.log
### Error types
* 502: Backend server not responding
* 404 Incorrect root or location
* 403: Check file permissions for ssl and website directory
### Let's Encrypt commands
* sudo apt install certbot python3-certbot-nginx
* sudo certbot --nginx -d example.com -d www.example.com
* Ready Nginx for ssl
```
server { listen 80; listen 443 ssl; server_name example.com; # Redirect HTTP to HTTPS return 301 https://$host$request_uri; } server { listen 443 ssl; server_name example.com; ssl_certificate /etc/ssl/self-signed.crt; # Path to your cert ssl_certificate_key /etc/ssl/self-signed.key; # Path to your private key ssl_protocols TLSv1.2 TLSv1.3; # Enable modern protocols ssl_ciphers HIGH:!aNULL:!MD5; # Enable strong ciphers location / { root /var/www/html; index index.html; } }
```

* Command used to redirect HTTP to HTTPS: "return 301 https://$host$request_uri;" (used in the server block listening to port 80 and 443 both)
### Let's Encrypt command for Reverse Proxy role

* Needs different certificate for each domain.
* sudo certbot --nginx -d app1.example.com -d www.app1.example.com
* sudo certbot --nginx -d app2.example.com -d www.app2.example.com
```
# app1.example.com server { listen 80; listen 443 ssl; server_name app1.example.com; # Redirect HTTP to HTTPS return 301 https://$host$request_uri; } server { listen 443 ssl; server_name app1.example.com; ssl_certificate /etc/letsencrypt/live/app1.example.com/fullchain.pem; ssl_certificate_key /etc/letsencrypt/live/app1.example.com/privkey.pem; ssl_protocols TLSv1.2 TLSv1.3; ssl_ciphers HIGH:!aNULL:!MD5; location / { proxy_pass http://localhost:3000; # Backend app running on port 3000 proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; } } # app2.example.com server { listen 80; listen 443 ssl; server_name app2.example.com; return 301 https://$host$request_uri; } server { listen 443 ssl; server_name app2.example.com; ssl_certificate /etc/letsencrypt/live/app2.example.com/fullchain.pem; ssl_certificate_key /etc/letsencrypt/live/app2.example.com/privkey.pem; ssl_protocols TLSv1.2 TLSv1.3; ssl_ciphers HIGH:!aNULL:!MD5; location / { proxy_pass http://localhost:5000; # Backend app running on port 5000 proxy_set_header Host $host; proxy_set_header X-Real-IP $remote_addr; proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; } }
```

### Adding OCSP stapling in SSL block
* ssl_stapling on;
* ssl_stapling_verify on;
* ssl_trusted_certificate /path/to/trusted-chain.crt;

------------------------------------------------------

## Core architecture
* Event driven model with **Master Process** (manages workers) and **Worker Processes** (handled multiple connections).
------------------------------------------------------

## SSL/TLS
* Both are used to encrypt data between client and server.
* Components include: Certificate (to validate server identitiy), Private Key (used to decrypt data), Certificate Authority (CA) (Trusted third-party issuing the certificates).
* We can use openssl self signed certificate but they are not trusted by browsers so we use Let's encrypt for prod.
* Regularly renew certificates (Let's encrypt does it automatically).
* You may also enable HSTS (HTTP Strict Transport Security)
* OCSP (Online Certificate Status Protocol) used to check revocation status of a certificate in real time. (Basically check everytime a client connects and if the cert is invalid or revoked, the connection is terminated).
* **OCSP stapling** is a technique where the server includes the OCSP response directly in the TLS handshake, eliminating the need for the client to query an external OCSP server. (This is used in high traffic sites or production environments).
------------------------------------------------------

# The working setup
(Proper workflow)

[[Nginx Workflow (Proper setup)]]

------------------------------------------------------

