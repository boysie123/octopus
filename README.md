# octopus setup

To set this up you need a vm or linux server running the follwoing
Node Red
SQLite3
Octopus Node red node: (https://github.com/borpin/node-red-contrib-octopus)

You also need:
Victron GX environment running MQTT
some sort of battery setup with Victron GX and ESS
Octopus Agile account

On the linux env:
Install SQLite
Install Node Red
Install Octopus node red node
Install SQLite node red
Install MQTT node red

How this works
At 4:20pm a trigger feteches the prices for the next day, the octopus node grabs the prices and works out the cheapest block of time you configure
This is passed to SQLite for storage
each hour the time is checked and passed to mqtt which the Victron GX device checks and sets the charging time for the schedule to be to cheapest time slot for the time you have requested 

things you need to setup for this to work:

On the linux VM with nide red and SQLite installed:
Create the database file and enter SQLite command prompt using:

sqlite3 agile_rates.sqlite

create the table using:
create table starttime(start_time unsigned integer primary key, cost unsigned smallint);

seed the first entry which will be updated later:
update starttime set cost= 1 where start_time = 0;

press ctrl + d (to exit SQLite CLI)

install Octopus node red node using: 
npm install https://github.com/borpin/node-red-contrib-octopus

make sure node red and sqlite are running and start automatically

goto node your red website (https://node red IP:1880)
Using the hamburger top right select import

paste the contents of the flow.txt and click Import

double click the Octopus node and select your region and adjust the periord length accordingly (its in 1/2 hour blocks so set what you need to charge your batteries) and click done

double click the Agile time node and adjust to your DB location to yours and click done

double click the Agile rates node and adjust to your DB location to yours and click done

Double click the mqtt start node and edit the server and replace with your GX device IP and click update (rename if needed)
replace your-VRMPortalID-here with your VRM portal ID in the node mqtt start (you can get this from VRM https://vrm.victronenergy.com/ device list/gateway VRM portal ID) and click done

Doble click the node "Request to APP to keep MQTT up" and update to your IP of the GX device and click done

Click deploy

On the GX remote console set a sechdule 
the fist slot needs to be the following settings:
Schedule 1:
Enabled Everyday
start time (any it will be updated by node red)
Duration (the same as your requitred time to charge your batteries)
Stop on SOC (up to you)

Schedule 2: (this is the time you dont want to discharge)
Enabled Everyday
start time (for me this is 8pm)
Duration (21h to take it to 4pm the next day)
Stop on SOC enabled
SOC 5% (or below your min SOC)











