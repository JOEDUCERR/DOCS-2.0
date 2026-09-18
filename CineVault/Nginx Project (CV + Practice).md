![[Pasted image 20260918115424.png]]

## Architecture
1. Main app Building (Main Coding Part) [[Website Code Main]]
	1. React: Frontend 
	2. FastAPI: Backend
	3. PostgreSQL: DB
2. Containerize
![[Linux Architecture for CineVault.png]]
	1. Nginx: Reverse Proxy
	2. Frontend
	3. Backend
	4. PostgreSQL (Containerizing the DB as it should not be publicly accessible.)
3. Host on Linux Server / AWS
	1. Nginx -> Docker
4. Add HTTPS (without buying a domain for now)
5. Add CI/CD (Automating deployment)
	1. GitHub Actions (Pipeline)
	2. [[CICD for CineVault]]
6. AWS
	1. Switch from Local Linux server to AWS
7. Video File Storage
	1. AWS S3 Buckets
	2. Not to be mistaken with movie metadata which will be stored in PostreSQL. The actual movies will be stored in AWS S3
8. Kubernetes
![[Kubernetes CineVault.png]]
	1. Move from Docker to Kubernetes (Kubernetes orchestration).
	2. We would add an Ingres Controller
9. Monitoring
	1. Grafana -> Prometheus -> Info from Nodes, Pods and Application
	2. Prometheus: Collects metrics
	3. Grafana: Displays the metrics (dashboard)
	4. You can also add alerts
## Final Architecture
![[CineVault/Final Architecture.png]]