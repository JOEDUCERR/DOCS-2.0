sudo docker run -d \
  --name open-terminal \
  --restart unless-stopped \
  --network bridge \
  --memory 2g \
  --cpus 2 \
  -v open-terminal:/home/user \
  -e OPEN_TERMINAL_API_KEY="YOUR_KEY_HERE" \
  ghcr.io/open-webui/open-terminal:latest

(Trying again)
sudo docker run -d   --name open-terminal   --restart unless-stopped   -p 9000:9000   -v open-terminal:/home/user   -e OPEN_TERMINAL_API_KEY=welcome@123   ghcr.io/open-webui/open-terminal

* We are not exposing 8002 over the network
* Docker ip address is 172.19.0.2/16
* Open Webui is on 172.19.0.3/16
* http://172.17.0.3:8000 is the link of Open Terminal
* Now trying through documentation

## Use Cases
* Code Execution
* Analyze Documents and Data
* Build & Preview WEbsites
* Automate Tasks
* Advanced Workflows

# Skill
* Mention in chat "$".
* User needs to mention the skill.

![[Pasted image 20260826181333.png|700]]