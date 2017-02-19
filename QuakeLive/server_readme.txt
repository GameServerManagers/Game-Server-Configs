===========================
QUAKE LIVE DEDICATED SERVER
===========================

1. Requirements
2. Updating and Launching
3. Configuring your server
4. Managing server access list
5. Setting up a map pool
6. Creating custom gametypes (factories)
7. Using Steam Workshop
8. Using external remote console
9. Using external stats

1. Requirements
---------------

The dedicated server is available in both 32-bit and 64-bit versions, and should run on any modern machine that can
run the Steam client. We recommend Ubuntu Server 14.04.

To run the 32-bit version of the server on 64-bit installs, you will need to install 32-bit versions of all libraries.
See your distribution's instructions on how to perform this.


2. Updating and Launching
-------------------------

Install and update the server through SteamCMD, using a command similar to below.

./steamcmd.sh +login anonymous +force_install_dir ./steamapps/common/qlds/ +app_update 349090 +quit

Please see the Valve Developer Community for full SteamCMD options: https://developer.valvesoftware.com/wiki/SteamCMD

Run the server using the provided run_server_x86.sh and run_server_x64.sh scripts. This will set the proper working
directory and LD_LIBRARY_PATH to the correct libsteam_api.so. You can add command line options at will.

A standard server or VPS can host many instances of Quake Live, so you may wish to run multiple servers. We recommend
managing them through a third party process control system. Below is a sample script that will take one parameter: a
number starting at 0. This will launch a server with a unique hostname, port, rcon port, and stats port. This is a
great way to quickly run a small cluster of servers on a single box.

You should change the path to reflect where you have installed the game. Never run the server under root.

  #!/bin/bash
  gameport=`expr $1 + 27960`
  rconport=`expr $1 + 28960`
  servernum=`expr $1 + 1`

  exec /home/user/steamcmd/steamapps/common/qlds/run_server_x86.sh \
      +set net_strict 1 \
      +set net_port $gameport \
      +set sv_hostname "My Quake Live Server #$servernum" \
      +set fs_homepath /home/user/.quakelive/$gameport \
      +set zmq_rcon_enable 1 \
      +set zmq_rcon_password "mypass" \
      +set zmq_rcon_port $rconport \
      +set zmq_stats_enable 1 \
      +set zmq_stats_password "mypass" \
      +set zmq_stats_port $gameport

This can be combined with a process control system like supervisord in order to manage and launch any number of
instances. Note that supervisord is third party software, and the below may change at any point. We will not support
issues involving third party software.

You will need to replace "user" with the user name of the dedicated server.

  [program:quakelive]
  command=/home/user/steamcmd/steamapps/common/qlds/launch.sh %(process_num)s
  user=user
  process_name=qzeroded_%(process_num)s
  numprocs=10
  autorestart=true

In conjunction with the above launch script, this will give you 10 server instances, numbered #1 to #10, starting
at port 27960, with rcon starting at port 28960. They will automatically restart if crashed. For information on using
rcon, please see later in this document.


3. Configuring your server
--------------------------

Configure your server through server.cfg. You may wish to make a copy of this file and place it into your fs_homepath,
as updates to the default server.cfg may revert your changes to default. The default server.cfg is heavily commented,
so please see that for further information.

There is no need to specify a map on the command line or server.cfg. The server will automatically execute the variable
"serverstartup" when the server has completed initialization. By default, this will launch a random map selected
from the server's map pool. Please see the below section on configuring map pools.

Quake Live servers are designed to handle multiple gametypes, and multiple gametype variations on one server. This
means that your server can switch from Free For All to Capture the Flag to Clan Arena, to even variants of gametypes,
such as InstaGib FFA, and custom variations that provide more traditional competitive settings.

Because of this, you should generally not place gameplay settings inside server.cfg. Although the server will attempt
to restore all old settings after switching gametypes, these factory settings will override settings in server.cfg.

See the below section on creating custom factories to setup your own match rules.


4. Managing server access list
------------------------------

Quake Live reads and stores to a persistent text file on disk information about who has access to server moderation,
server administration, and ban list.  By default, this is in the file access.txt, and can be changed through the
console variable "g_accessFile"

Only edit this file on disk while all servers are stopped, as the game will write out the current access list before
each map load. In order to have access to your server, you will need to add yourself as an admin. Obtain your
64-bit Steam ID by connecting to any match, and typing /players in the console. Put it in access.txt in the
given format. Example:

76561198072786081|admin

Save the file, and start the server. You can then use /mod, /admin, /demote, /ban and /unban commands to manage the
access list while the server is running.


5. Setting up a map pool
------------------------

The server's map pool accepts a map, and a factory, delimited with a |. See the default mappool.txt for an example.
A different file can be specified through "sv_mapPoolFile"

If you wish to use your own custom factories, you can specify them in here so that your custom factory will appear
in the three map voting system at the end of the game.

If you wish to lock your server to one gametype, then make sure your map pool only contains maps in that factory,
then add 8 to g_voteFlags to stop "callvote map" from accepting the optional factory argument. This is not
usually needed though; we recommend allowing your players to play and callvote whatever gametypes they wish to
play on your servers, but it is often not bad to restrict Duel servers due to the low player counts.


6. Creating custom gametypes (factories)
----------------------------------------

Setting up a custom factory is a bit more involved than the past solutions of editing config files, but it allows for
more flexibility in match rules, while ensuring that settings don't leak over into future matches. By default,
Quake Live ships with base rules for every gametype, and a few variations. Other factories may be available on Steam
Workshop.

To define your own gametype factory, create a "scripts" folder inside your baseq3 directory, and name the file with
anything that ends in ".factories"

Example: baseq3/scripts/mynewgametype.factories

This file is a JSON file that contains either a JSON array with multiple factories, or a JSON object containing a
single factory. For example, here is the built in InstaGib FFA factory:

  {
    "id": "iffa",
    "title": "Instagib FFA",
    "author": "id Software",
    "description": "Railgun and Gauntlet only. One shot, one kill.",
    "basegt": "ffa",
    "cvars": {
      "g_dropCmds": "0",
      "g_spawnArmor": "0",
      "dmflags": "28",
      "g_instagib": "1",
      "g_startingWeapons": "65",
      "timelimit": "15",
      "g_allowKill": "0",
      "fraglimit": "50",
      "g_overtime": "0",
      "g_loadout": "0"
    }
  }

All fields should be present and of the correct type.

"id" is how you will refer to the factory inside a map pool or a callvote, and must be a string.
"title", "author" and "description" are all strings containing info about the factory, and can be seen in Start Match
"basegt" is a string of the base gametype it should apply the settings on. Valid values for "basegt" are:
  ffa, duel, race, tdm, ca, ctf, oneflag, har, ft, dom, ad, rr
"cvars" is an object containing key value pairs of the cvar names and values to set.

If a factory is invalid, the reason why will be printed in the console during startup, and the factory will not be
available for play.


7. Using Steam Workshop
-----------------------

Quake Live will only autodownload custom content that has been uploaded to Steam Workshop. The legacy HTTP and UDP
download functions have been removed. If you want your players to be able to play custom content on your servers,
you must use Steam Workshop.

To add a workshop item to your server, add its item ID to workshop.txt. The item ID can be obtained from the URL
when browsing the Steam Workshop in your browser.

Before the server finishes initializing, it will attempt to download all items in workshop.txt, such that custom
content can be included in your map pool. The progress of this process will be printed to the server console, and if
any download fails, it will be skipped and the server will continue to start.

To upload content, you will need to use SteamCMD's workshop_build_item command. Please refer to the Steam documentation
on how to upload Workshop content.

Occasionally, the Workshop startup process will fail, usually due to temporarily unavailable content server. Some users
have reported that switching from the 64-bit dedicated to the 32-bit dedicated binary will fix downloads. In these
cases, you can use SteamCMD to download the workshop item, and then move it to the appropriate place.

Download the item with:

./steamcmd.sh +login anonymous +workshop_download_item (appid) (workshop_id) +quit

and then copy the steamapps/workshop folder into the steamapps folder where the server is installed. This will
cause the game to see the item as cached, and not attempt to download it.


8. Using external remote console
--------------------------------

The remote console works differently than previous titles. Rcon is disabled by default, and can be configured using
"zmq_rcon_enable", "zmq_rcon_ip". "zmq_rcon_port" and "zmq_rcon_password". Unlike previous titles, rcon will bind to
the TCP port you specify, not UDP. The socket itself is a ZeroMQ socket, using CZMQ for authentication. Included with
the server is a simple rcon client, zmq_rcon.py, written for Python 2.x. You will need to install Python CZMQ, which
can most easily be done through "pip install czmq" if pip is available on your system. To run the script, use something
similar to the following:

python zmq_rcon.py --host=tcp://127.0.0.1:27961 --password=mypass


9. Using external stats
-----------------------

Quake Live also exposes a ZeroMQ publish/subscribe socket that will emit a great amount of detail about the ongoing
match, live as events occur. A basic script is included, zmq_stats_verbose.py, but it will simply print the stats events
to screen as an example. All events emitted by the engine should be valid JSON. Any additional functionality and
