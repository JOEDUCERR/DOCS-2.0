Docker 101

Docker uses a client server architecture where the client communicates to the server using REST api. Containers are kind of like a process. Dockerfile defines a docker image which is packaged. Good as we can push it to DockerHub and share it with other devs. NO NEED FOR LENGTHY DOCUMENTATION.

Main docker config files are stored in /home/user/.docker as .dock

Tips:
* To avoid sudo add Docker to a group with admin privilages. (Check path if having errors).
* docker --config ~/testconfigs/ ps overrides the main docker config file location to this one

Docker Daemon : You can connect to docker daemon running on a different machine by changing the unix in cli using -H or using ssh or using TCP socket. E.g. docker -H ssh://ubuntu@3.90.248.137 ps. (This lets u command docker using cli from your machine which daemon is running on 3.90.348.137).

Command to run a specific command in docker:
sudo docker exec <command>

