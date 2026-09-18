![[Pasted image 20260918115424.png]]

## Architecture
1. Main app Building (Main Coding Part)
	1. React: Frontend
	2. FastAPI: Backend
	3. PostgreSQL: DB
2. Containerize
	1. Nginx: Reverse Proxy
	2. Frontend
	3. Backend
	4. PostgreSQL
3. Host on Linux Server / AWS
	1. Nginx -> Docker
4. Add HTTPS (without buying a domain for now)
5. Add CI/CD
	1. GitHub Actions (Pipeline)
6. AWS
	1. Switch from Local Linux server to AWS
7. Video File Storage
	1. AWS S3 Buckets
8. Kubernetes
	1. Move from Docker to Kubernetes
9. Monitoring
	1. Grafana -> Prometheus -> Info from Nodes, Pods and Application