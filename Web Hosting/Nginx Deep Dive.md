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
Helps to serve specific pages or endpoints. If you want to serve a specific page from a specific folder in your project.

```
location /fruits {
	root /var/www/html/;
}

### Upstream Backend
You can use this to assign multiple backend servers.
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