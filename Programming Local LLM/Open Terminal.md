sudo docker run -d \
  --name open-terminal \
  --restart unless-stopped \
  --network bridge \
  --memory 2g \
  --cpus 2 \
  -v open-terminal:/home/user \
  -e OPEN_TERMINAL_API_KEY="YOUR_KEY_HERE" \
  ghcr.io/open-webui/open-terminal:latest

* We are not exposing 8002 over the network
* http://172.17.0.3:8000 is the link of Open Terminal