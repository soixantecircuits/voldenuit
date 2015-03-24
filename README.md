Ansible playbooks for voldenuit

# Usefull commands
`ansible-playbook playbook-master.yml`
`ansible 4voldenuit -a "echo ok" -u pi`
`ansible 4voldenuit -a "sudo reboot" -u pi --sudo`
`ansible voldenuit01.local -m service -a "name=watchy state=restarted" -u pi --sudo`

# Copy instead of pull&build

Using git on 64 computers at the same time is too slow.
Therefore I recommend building watchy only once on a test raspberry, then copying the watchy folder to this folder, and then use the synchronise module as showin in playbook-master.yml


# Configure master server for autoboot

## Install pm2
`$ sudo npm i -g pm2`

## Panini

```
$ sudo npm install -g demeteorizer
$ cd /home/mina/sources/soixante/nodejs/panini
$ demeteorizer
$ cd .demeteorized
$ npm install
```

configure mongo
```
$ sudo apt-get install mongodb
```

 we’ll setup two database accounts. One for the administrator and second for the application. Login to the database
```
$ mongo
```
while in the mongo shell type
```
use admin
db.addUser( { user: "admin", pwd: "adminpwd", roles: [ "userAdminAnyDatabase" ] } )
use meteor
db.addUser("meteor", "meteorpwd")
exit
```
Then enable security for the mongodb.
```
sudo vi /etc/mongodb.conf
```
and uncomment line
```
auth = true
```
Restart mongodb
```
sudo /etc/init.d/mongodb restart
```
Test connections:
```
mongo --verbose -u admin -padminpwd localhost/admin
mongo --verbose -u meteor -pmeteorpwd localhost/meteor
```
Edit meteor settings in the pm2 app declaration
`vim pm2-settings/pm2-panini.json`

Run panini in pm2
`pm2 start pm2-settings/panini-pm2.json`

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## Nuwa

```
$ cd /home/mina/sources/soixante/nodejs/nuwa
$ demeteorizer
$ cd .demeteorized
$ npm install
```

configure mongo

 we’ll setup one database account for nuwa. One for the administrator and second for the application. Login to the database
```
$ mongo admin -u admin -p adminpwd
```
while in the mongo shell type
```
use meteornuwa
db.addUser("meteornuwa", "meteornuwapwd")
exit
```
Restart mongodb
```
sudo /etc/init.d/mongodb restart
```
Test connections:
```
mongo --verbose -u meteornuwa -pmeteornuwapwd localhost/meteornuwa
```
Edit meteor settings in the pm2 app declaration
`vim pm2-settings/pm2-nuwa.json`

Run nuwa in pm2
`pm2 start pm2-settings/nuwa-pm2.json`

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## Watchy 
Configure watchy config file
```
vim /home/mina/sources/nodejs/soixante/watchy/config/config.json
```
Run in pm2
```
pm2 start /home/mina/sources/nodejs/soixante/watchy/app.js --name "watchy" --log-date-format "YYYY-MM-DD HH:mm Z"
```

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## Web-recorder 
Configure watchy config file
```
vim /home/mina/sources/nodejs/soixante/web-recorder/config/config.json
```
Run in pm2
```
cd /home/mina/sources/nodejs/soixante/web-recorder/
pm2 start /home/mina/sources/nodejs/soixante/web-recorder/app.js --name "web-recorder" --log-date-format "YYYY-MM-DD HH:mm Z"
```

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## The socialite
Configure socialite config file
```
vim /home/mina/sources/nodejs/soixante/the-socialite/config/config.json
```
Run in pm2
```
cd /home/mina/sources/nodejs/soixante/the-socialite/
pm2 start /home/mina/sources/nodejs/soixante/the-socialite/serve.js --name "socialite" --log-date-format "YYYY-MM-DD HH:mm Z"
```

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## lucca
Configure lucca config file
```
vim /home/mina/sources/nodejs/soixante/lucca/config/config.json
```
Run in pm2
```
cd /home/mina/sources/nodejs/soixante/lucca/
pm2 start /home/mina/sources/nodejs/soixante/lucca/serve.js --name "lucca" --log-date-format "YYYY-MM-DD HH:mm Z"
```

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

Note:
Lucca is an app run with grunt.
To make it run with pm2, the steps are

### configure the serve.js file
`$ vim serve.js `

### make symbolic links for each component in the bower_components
```
cd app
ln -s ../bootstrap bootstrap
...
```

## fechty-server
Configure fechty server config file
```
vim /home/mina/sources/nodejs/soixante/fechty/server/config/config.json
```
Run in pm2
```
cd /home/mina/sources/nodejs/soixante/fechty/server
pm2 start /home/mina/sources/nodejs/soixante/fechty/server/app.js --name "fechty-server" --log-date-format "YYYY-MM-DD HH:mm Z"
```

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## fechty-client
Run in pm2
```
cd /home/mina/sources/nodejs/soixante/fechty/www
pm2 start /home/mina/sources/nodejs/soixante/fechty/serve.js --name "fechty-client" --log-date-format "YYYY-MM-DD HH:mm Z"
```

Check it is a running with no issues`
`pm2 list`
`pm2 logs`

## Set pm2 to restart at startup
```
pm2 save
sudo env PATH=$PATH:/usr/bin pm2 startup linux -u mina
```
