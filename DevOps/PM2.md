Install: npm install pm2@latest -g
Start: pm2 start [app.js](http://app.js) --name "my-app"
List all processes: pm2 list
Logs: pm2 logs
Stop / restart: pm2 stop <app id> / pm2 restart <id>

  
# To configure auto restart on server boot
1. pm2 startup (this creates boot script configuratiaon)
2. pm2 save (saves the processes which were running to remember what to load on boot)

-------------------

## Installing dependencies

* npm ci --omit=dev:
- `npm ci` installs dependencies using `package-lock.json`.
- `--omit=dev` skips development-only dependencies.
- `node_modules` is created on the server.

## Steps
1. Install node js
2. install pm2
3. transfer application code to server
4. install dependencies

## What to do if code updates

pm2 restart pm2-practice-api