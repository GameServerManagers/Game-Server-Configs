## Admins.cfg
Admins are now created by groups and groups are assigned to admins. You can create as many groups as you want with any types of permissions. All the permissions will be in the config file commented out. These files will not be over writeable so when we add new permissions you will need to check here or the wiki for updates. The below are just examples. Please note that it needs to be SteamId64. You can convert your ID at steamid.io/lookup/

## Bans.cfg
This is where your bans will go. They need to be in the format of `steamid:unix timestamp of unban //comment` So an example file will look like this:
```76561198039509812:0 //Permanent ban for cheating
7862895148978485:1454455855 //team killing
```

## MapRotation.cfg
If you add the general map names (such as below without a specific game mode). Our parser will do all variations of it including AAS, INS, etc. Map names can be seen from the DefaultGame.ini file. Each map entry goes on a new line.
```Logar
Kohat
OP First Light
Chora
Fool's Road
Sumari
Gorodok
Yehorivka
Mestia
Jensen's Range
Kokan
Narva
Al Basrah
```
If you want a specifc map version you can add the below into the rotation file
```Al Basrah
Al Basrah AAS v1
Al Basrah Invasion v1
Al Basrah PAAS v1
Chora
Chora AAS v1
Chora AAS v2
Chora INS v1
Chora Invasion v1 Night
Chora PAAS v1
Fool's Road
Fool's Road AAS v1
Fool's Road AAS v1 INF
Fool's Road AAS v2
Fool's Road AAS v3
Fool's Road Skirmish
Gorodok
Gorodok AAS v1
Gorodok AAS v1 INF
Gorodok AAS v2
Gorodok Conquest v1
Gorodok Invasion v1
Jensen's Range
Kohat
Kohat AAS v1
Kohat AAS v1 INF
Kohat INS v1
Kohat Invasion v1
Kokan
Kokan AAS v1
Kokan AAS v2
Kokan INS v1
Kokan PAAS v1
Logar
Logar PAAS v1
Logar Valley AAS v1
Logar Valley AAS v1 INF
Logar Valley INS v1
Logar Valley INS v1 Night
Mestia PAAS v1
Mestia Invasion v1
Mestia Invasion v2
Mestia AAS v1
Mestia AAS v2
Narva
Narva PAAS v1
Narva Invasion v1
Narva AAS v1
Op First Light
Op First Light AAS v1
Op First Light Conquest v1
Sumari
Sumari AAS v1
Sumari AAS v1 INF
Sumari AAS v2
Sumari AAS v3
Sumari INS v1
Sumari ITC v1
Yehorivka
Yehorivka AAS v1
Yehorivka AAS v1 INF
Yehorivka AAS v2
Yehorivka Conquest v1
Yehorivka Invasion v1
```

## RemoteAdminListHosts.cfg
Each line should contain a URL to the admin file. The admin file will follow the exact same format as above. This should be used if you run multiple servers and would like to have a single admin file for all of them. If a player is listed in multiple files, their permissions will be combined.

## RemoteBanListHosts.cfg
Each line should contain a URL to the ban file. The ban file will follow the exact same format as above. This should be used if you run multiple servers and would like to have a single admin file for all of them.

## Server.cfg
This is the general server config file. Each variable should be on its own line. New additions will be added to this wiki. Since we do not overwrite config files you will need to manually add them to your existing server config if you wish to alter the default value.
```ServerName="Squad Dedicated Server"
MaxPlayers=40
NumReservedSlots=0
IsLANMatch=false
ShouldAdvertise=true
NumPlayersDiffForTeamChanges=3
AllowTeamChanges=true
PreventTeamChangeIfUnbalanced=true
EnforceTeamBalance=true
RejoinSquadDelayAfterKick=180
AllowCommunityAdminAccess=true
RecordDemos=false
ServerMessageInterval=300
ServerPassword=Password
AllowQA=false
```

## ServerMessages.cfg
This will rotate server messages every x seconds (based on Server.cfg setting). Each message should have its own line. This should be used to display server rules and contact information.

## Rcon.cfg
If you do not want to use RCON, leave the password= empty. NOTE: Leaving the RCONIP=0.0.0.0 will bind it to the public default IP. If your server has multiple IP's please specify the address here.
