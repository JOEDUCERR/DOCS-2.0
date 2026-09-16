1. Pre-requisites (set these up beforehand):
	1. Domain (obtain one for your website)
	2. Backend
	3. SSL Certs
2. Working on the config file
	1. Adjust worker processes
	2. Create a server block
	3. Enable config (check if config is fine and restart nginx)
3. Security and SSL:
	1. SSL
		1. Obtain Cert
		2. Verify ssl config
		3. If working on multiple domains, set it up accordingly.
	2. Security
		1. Add headers
		2. enable HSTS
		3. Add rate limiting
		4. add OCSP sampling
4. Performance:
	1. Enable HTTP/2
	2. Add caching (for static files)
	3. Add Gzip compression
	4. Do TCP Tunining
5. Testing:
	1. Test nginx conf using "sudo nginx -t"
	2. SSL with SSL Labs
	3. Check reverse proxy ports
	4. test redirects
6. Monitoring:
	1. /var/log/nginx/error.log
	2. Automate certificate renewals using certbot
	3. monitor using prometheus
	4. Tip: Keep upgrading Nginx regularly.
7. Optional:
	1. HTTP/3 Support
	2. WebSockets
	3. Load Balancing

[[Nginx Workflow project]]