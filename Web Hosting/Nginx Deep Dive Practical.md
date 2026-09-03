* First we create an index.html webpage
* We then serve the webpage using node and express on the port 3000
* create package.json used to manage project dependencies
* Download node js which also installs npm
* Now we do npm install (installs packages from package.json) and then node server.js (starts the server)
* Now, we will dockerize our app and put nginx as a load balancer.
	* Browser request -> Nginx reverse proxy -> Node.js server
* Create a Dockerfile to containerize the app and build.
* Build the dockerfile and test.
* Now we create a docker compose to create multiple instances of the dockerfile on 3 different ports.
* Now we need to create nginx configuration file to help it act as a reverse proxy.
	* worker_process: defines how many parallel workers are spawned by nginx to handle client requests. (Usually is set to the number of cpu cores for better optimization).
	* 