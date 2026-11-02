Install: npm install pm2@latest -g

Start: pm2 start [app.js](http://app.js) --name "my-app"

List all processes: pm2 list

Logs: pm2 logs

Stop / restart: pm2 stop <app id> / pm2 restart <id>

  

# To configure auto restart on server boot

1. pm2 startup (this creates boot script configuration)

2. pm2 save (saves the processes which were running to remember what to load on boot)