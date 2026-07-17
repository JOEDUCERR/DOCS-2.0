Docker

Install Docker:
sudo apt update
sudo apt install docker.io -y

Enable Docker:
sudo systemctl enable docker
sudo systemctl start docker

Check Status:
sudo systemctl status docker

Build image from project after creating dockerfile and .dockerignore:
docker build -t resume-analyzer .

Run the docker image:
docker run --name resume-api -p 8000:8000 -e OPENAI_API_KEY="YOUR_KEY" resume-analyzer

Check Logs:
docker logs <cont-id>

Instead of docker run command you can use:
docker compose up


## After everything if you want to upload on DockerHub:

Login into docker:
sudo docker login

Tag your image that you want to push:
sudo docker tag ai-resume-analyzer:latest joeducer/ai-resume-analyzer:latest

Push into DockerHub:
sudo docker push joeducer/ai-resume-analyzer:latest

Pull command:
sudo docker pull joeducer/ai-resume-analyzer:latest

Useful Commands
View logs:
sudo docker logs ai-resume-analyzer
Restart:
sudo docker restart ai-resume-analyzer
Stop:
sudo docker stop ai-resume-analyzer
Remove:
sudo docker rm -f ai-resume-analyzer

How to make docker container survive reboots:
sudo docker run -d \
--restart unless-stopped \
-p 8000:8000 \
-e OPENAI_API_KEY=YOUR_NEW_KEY \
--name ai-resume-analyzer \
YOUR_USERNAME/ai-resume-analyzer:latest

## Nginx between docker and internet:
sudo apt update
sudo apt install nginx -y

configure the nginx reverse proxy:
sudo nano /etc/nginx/sites-available/ai-resume-analyzer

server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8000;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

Enable it:
sudo ln -s /etc/nginx/sites-available/ai-resume-analyzer /etc/nginx/sites-enabled/

Remove default site:
sudo rm /etc/nginx/sites-enabled/default

sudo nginx -t

sudo systemctl reload nginx

Github Actions or CI/CD
1. add github secrets manually by going into repo settings
2. mkdir -p .github/workflows
