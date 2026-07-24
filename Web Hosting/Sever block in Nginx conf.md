Perfect. Since your goal today is **reverse proxies with Nginx**, we'll build on `nginx.conf` and move into the configurations you'll actually write every day.

Think of the hierarchy like this:

```
nginx.conf
    │
    ├── events {}
    │
    └── http {}
          │
          ├── include sites-enabled/*
          │
          └── server {}
                  │
                  └── location {}
```

`nginx.conf` is mostly global settings. The real work happens inside **server** and **location** blocks.

---

# 1. The Server Block

A **server block** represents one website (or one virtual host).

Example:

```nginx
server {
    listen 80;
    server_name example.com;

    root /var/www/html;
    index index.html;
}
```

Imagine someone types:

```
http://example.com
```

The request reaches Nginx.

Nginx asks:

> "Which server block should handle this request?"

It checks the `server_name`.

```
Internet
      │
      ▼
Nginx
      │
      ├── server_name google.com
      │
      ├── server_name example.com  ← Match
      │
      └── server_name api.example.com
```

The matching server block is chosen.

---

# 2. listen

```
listen 80;
```

This tells Nginx which port to listen on.

Examples:

```
listen 80;
```

HTTP

```
http://example.com
```

---

```
listen 443 ssl;
```

HTTPS

```
https://example.com
```

---

You can also specify an IP.

```
listen 192.168.1.10:80;
```

Now Nginx only accepts requests arriving on that IP.

---

# 3. server_name

Example

```nginx
server_name example.com;
```

Suppose your browser requests

```
Host: example.com
```

Nginx compares

```
Incoming Host

↓

example.com

↓

Matches?

↓

Yes

↓

Serve this website
```

---

Multiple names are allowed.

```nginx
server_name example.com www.example.com;
```

---

Wildcards

```nginx
server_name *.example.com;
```

Matches

```
blog.example.com

shop.example.com

api.example.com
```

---

# 4. root

```
root /var/www/html;
```

This is the folder containing the website.

Suppose

```
/var/www/html

      │

      ├── index.html

      ├── about.html

      └── images/
```

Browser requests

```
GET /
```

Nginx serves

```
index.html
```

---

Browser requests

```
GET /about.html
```

Nginx serves

```
/var/www/html/about.html
```

---

# 5. index

```
index index.html;
```

If someone requests

```
/
```

Nginx searches

```
index.html

index.htm

etc.
```

depending on what you've configured.

---

# 6. Location Block

Inside every server block you usually have locations.

Example

```nginx
server {

    listen 80;

    server_name example.com;

    location / {

    }

}
```

A location matches part of the URL.

Suppose the browser requests

```
/images/logo.png
```

Possible locations

```
location /

location /images/

location /api/

location /downloads/
```

Nginx finds the best match.

---

Example

```nginx
location /images/ {

    root /var/www/html;

}
```

Request

```
/images/logo.png
```

Served from

```
/var/www/html/images/logo.png
```

---

# 7. Multiple Locations

```nginx
server {

    location / {

    }

    location /api/ {

    }

    location /images/ {

    }

}
```

Request

```
/
```

↓

```
location /
```

---

Request

```
/api/users
```

↓

```
location /api/
```

---

Request

```
/images/logo.png
```

↓

```
location /images/
```

---

# 8. Reverse Proxy (The Most Important Part)

Suppose your FastAPI app is running here

```
localhost:8000
```

Users should never access it directly.

Instead:

```
Internet

↓

Nginx

↓

FastAPI
```

Configuration:

```nginx
server {

    listen 80;

    server_name example.com;

    location / {

        proxy_pass http://127.0.0.1:8000;

    }

}
```

Now

Browser

```
GET /
```

goes to

```
Nginx
```

which forwards it to

```
FastAPI
```

FastAPI sends back JSON or HTML.

Nginx returns it to the browser.

The client never knows FastAPI exists.

---

# 9. proxy_pass Flow

```
Browser

↓

GET /

↓

Nginx

↓

proxy_pass

↓

localhost:8000

↓

FastAPI

↓

Response

↓

Nginx

↓

Browser
```

Nginx becomes the middleman.

---

# 10. Why Use a Reverse Proxy?

Without Nginx

```
Internet

↓

FastAPI
```

Problems

- FastAPI must handle static files
    
- SSL certificates
    
- Compression
    
- Logging
    
- Multiple applications
    

---

With Nginx

```
Internet

↓

Nginx

├── SSL

├── Logging

├── Compression

├── Static files

├── Rate limiting

└── Reverse proxy

↓

FastAPI
```

FastAPI only focuses on application logic.

---

# 11. Multiple Applications on One Server

Suppose

```
React

Port 3000

FastAPI

Port 8000

Grafana

Port 3001
```

Instead of exposing three ports:

```
example.com:3000

example.com:8000

example.com:3001
```

Nginx gives users one entry point:

```nginx
server {

    listen 80;

    server_name example.com;

    location / {

        proxy_pass http://localhost:3000;

    }

    location /api/ {

        proxy_pass http://localhost:8000;

    }

    location /grafana/ {

        proxy_pass http://localhost:3001;

    }

}
```

Now users access:

```
example.com/
```

↓

React

---

```
example.com/api
```

↓

FastAPI

---

```
example.com/grafana
```

↓

Grafana

Everything is served from a single domain.

---

# 12. The Headers You Should Almost Always Add

A production reverse proxy usually includes:

```nginx
location / {
    proxy_pass http://127.0.0.1:8000;

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

These headers preserve important information for the backend:

- **Host**: the original hostname requested (e.g., `example.com`).
    
- **X-Real-IP**: the client's IP address.
    
- **X-Forwarded-For**: a chain of client/proxy IPs, useful when multiple proxies are involved.
    
- **X-Forwarded-Proto**: whether the original request used `http` or `https`.
    

Without these, your backend often only sees the proxy (`127.0.0.1`) rather than the real client.

---

## Practice Lab

Since you've already worked with Docker, React, and FastAPI, here's a realistic setup to reinforce reverse proxies:

```
Internet
      │
      ▼
Port 80
      │
   Nginx
      │
      ├── /       → React (localhost:3000)
      │
      └── /api/   → FastAPI (localhost:8000)
```

You'll learn:

- How `proxy_pass` changes with and without a trailing slash.
    
- How `location` matching works.
    
- Why `proxy_set_header` is important.
    
- How a React frontend communicates with a FastAPI backend through a single Nginx entry point.
    

This architecture is very close to what you'll deploy in production on an EC2 instance or a VPS.