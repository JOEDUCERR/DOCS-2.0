## Functions:
* Reverse Proxy, Proxy server for email, Load Balancer for different protocols.
* In reverse proxy mode acts as a request forwarder to the main servers accordingly. Advantages:
	* Security
	* Caching
	* Logging
	* SSL/TLS support
	* Load Balancing
* Has event driven architecture + asynchronous request dealing.
* Can handle extreme loads like almost 10,000 simultaneous connections with low resources.
## DOCS:
* Basically, Nginx has one master process and several sub process which are called workers. Master: Reads and evaluates config. Worker: actual processing.
* Main conf file location :
	* `/usr/local/nginx/conf`, `/etc/nginx`, or /usr/local/etc/nginx.
## Nginx Conf
[[Nginx Conf Deep Dive]]
[[Sever block in Nginx conf]]


# Building a Load Balancer
### The Setup
.net core web is running on kestrel server serving on 5001 and it needs to talk to the internet through a reverse proxy.