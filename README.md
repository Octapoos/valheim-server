# valheim-server
Repository for Valheim world backups

## Tutorial for Setting Up a Raspberry Pi for the Valheim Server
> **Please note that to run a Valheim server on your Raspberry Pi, you need to use a 64-bit operating system. Therefore, for this guide, we will be focusing on using the 64-bit release of Raspberry Pi OS.**

To maximize the performance of your Valheim server, we highly recommend setting your Raspberry Pi up as headless.

As the Valheim development team hasn’t provided an ARM build of their server, we must do a few things to get it to run on our Raspberry Pi.

# Installing the x86 and x64 Emulators to the Raspberry Pi
We will need to use both an x86 and x64 emulator within this guide as the software we are using isn’t natively built for the Pi’s ARM architecture.

The “SteamCMD” software will require us to install “box86“, and the Valheim dedicated server will need us to use “box64“.

1. First off, we need to install Box86 to our Raspberry Pi. This piece of software will allow us to run the “SteamCMD” software on our device.

SteamCMD is what we will use to download the latest version of the Valheim dedicated server to our Raspberry Pi.

To compile and install this software, please follow [our guide on using Box86 on the Raspberry Pi](https://pimylifeup.com/raspberry-pi-x86/).

While following this guide, ensure that you get the latest “tagged” release, and do not clone the master version of the code. At the time of writing, there were issues with running “SteamCMD” using the master version of Box86.

2. Once you have installed Box86, you will also need to install Box64. This software is similar to Box86 but is used to run x64 software on our ARM64 device.

This software will let us run the Valheim dedicated server on our Raspberry Pi as there are no native builds for ARM.

Make sure to follow [our guide on installing Box64 to the Raspberry Pi](https://pimylifeup.com/raspberry-pi-x64/) before continuing any further with this guide.

# Setting up SteamCMD on the Pi
With Box86 and Box64 installed on your Raspberry Pi. we can now move on to installing the final piece of the puzzle.

To download the Valheim dedicated server on our Raspberry Pi, we need to use the Steam command-line tool called “steamcmd“.

The following steps will walk you through the process of installing and running SteamCMD on your Raspberry Pi.

1. Our first step is to make a directory where we will store the SteamCMD software. In our case, we will keep this within a directory called “steamcmd” in our “pi” users home directory.

You can make this directory and change to it on your Raspberry Pi by using the following commands on your device.

```
mkdir /home/pi/steamcmd
cd /home/pi/steamcmd
```

2. Now that we are in the directory, we can download and extract SteamCMD using the command below.

This command will use curl to download the file and pass it straight into the tar tool using a pipe (|). A pipe allows us to extract the data provided from curl in a single command.

```
curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -
```

3. Before installing the Valheim dedicated server to our Raspberry Pi, let us ensure that SteamCMD is now working.

We can verify that the software is working correctly by running the script with the command below.

```
./steamcmd.sh
```

During the first run of the “steamcmd” software, it will self-update and prepare itself for use.

4. Once SteamCMD finishes updating, you can quit out of the tool by using the following command on your device.

```
quit
```

### Installing the Valheim Server on the Raspberry Pi
Now that we have prepared our Raspberry Pi, we can install the Valheim dedicated server.

To achieve this, we will be using the SteamCMD tool that we set up in the previous section to download the latest release.

1. Make sure that we are in the same directory as where we downloaded SteamCMD by using the following command.

```
cd /home/pi/steamcmd
```

2. Now that we are in the correct location, we can use SteamCMD to download the Valheim dedicated server.

You can use SteamCMD to begin downloading the server by using the following command.

```
./steamcmd.sh +@sSteamCmdForcePlatformType linux +login anonymous +force_install_dir /home/pi/valheim_server +app_update 896660 validate +quit
```

With this, we are issuing several different commands to the Steam command line. We will quickly go through this, so you have an idea of what everything does.

```+@sSteamCmdForcePlatformType linux``` – Using this part of the command, we are forcing the platform type to Linux. This stops the command line tool from picking an incorrect platform.

```+login anonymous``` – This option will log us into the Steam network as an “anonymous” user. The anonymous doesn’t require a password, so it is helpful for downloading openly available software.

```+force_install_dir /home/pi/valheim_server``` – Using this option, SteamCMD allows us to choose where we want the server installed. In our case, we will install the Valheim dedicated server to “/home/pi/valheim_server“. On my Pi use directory ```/mnt/ssd/Valheim/valheim_server```.

```+app_update 896660``` – This part is what allows us to select what we want to download. In this case, “896660” is the app ID for the Valheim dedicated server.

3. Our next step is to modify the start server script for Valheim so that you can configure it to best suit your needs.

Begin editing the “start_server.sh” script by using the nano text editor with the following command.

```
nano /home/pi/valheim_server/start_server.sh
```
On this pi: 
```
nano /mnt/ssd/Valheim/valheim_server/start_server.sh
```

4. Within this file, you will want to replace one line in particular. This is the line that will start the Valheim dedicated server on our Raspberry Pi.

You can use CTRL + W within nano to search for this line.

```
./valheim_server.x86_64 -name "My server" -port 2456 -world "Dedicated" -password "secret"
```

Once you have found the above line, replace it with the following.

```
./valheim_server.x86_64 -nographics -batchmode -port 2456 -public 0 -name "My Server Name" -world "MyWorldName" -password "MySecretPassword" -savedir "/home/pi/valheim_data"
```

On this Pi:
```
./valheim_server.x86_64 -nographics -batchmode -port 6000 -public 0 -name "Octapoos Server" -world "Raft2Point0" -password "Samual" -savedir "/mnt/ssd/Valheim/valheim_data"

```

When typing in this new line, you may want to change a couple of options. We will quickly go through them now.

```-port 2456``` – The port option specifies what port the Valheim dedicated server will listen on. Typically it is safe to keep this set to the default unless another program is using it. If you are behind a firewall, you will need to make sure that you port forward this port.

```-public 0``` – Using this option, you can decide whether you want your server to appear in the in-game server browser. The default value for this option is 0, meaning it will not be listed. Set this to 1 if you want it listed in the browser.

```-name "My Server Name"``` – This option stores the name you want your server to use when listed within the server browser.

```-word "MyWorldName"``` – Use this option to specify the name of your Valheim world. Make sure that this name does not contain any file extensions such as “.db” or “.fwl“.

```-password "MySecretPassword"``` – You can use this option to specify a password a user must enter to log in to the game world. This option is optional and can be removed. When entering your password, make sure that it contains at least 5 characters.

```-savedir "/home/pi/valheim_data"``` – With this option, we specify where we want our Valheim world data saved on our Raspberry Pi. By default, we will save this world within a directory in our “pi” user’s home called “valheim_data“.

5. Once you have made these changes to the file, you can save and quit by pressing CTRL + X, then Y, followed by the ENTER key.

# Running your Raspberry Pi Valheim Dedicated Server
Now that we have installed and configured our Valheim server on the Raspberry Pi, we can run it using a single command.

As we modified the “start_server.sh” script earlier, the server will be created with your

1. To start the Valheim server on our Raspberry Pi, we need to make sure that we are in the directory where we installed it earlier.

Change into the correct directory by using the following command on your device.

```
cd /home/pi/valheim_server/
```

This Pi:
```
cd /mnt/ssd/Valheim/valheim_server/
```

2. Once we are in the correct director,y we can start up the Valheim dedicated server by using the following command.

By using this command, we will start the server using the options we set earlier.

```
./start_server.sh
```

Please note, the Valheim servers start-up process can take considerable time to complete. This is because it needs to generate the game world, location list, and various other things.

3. To connect to your server, you can use the “Join by IP” option within Valheim.

If you don’t know the local IP address of your Raspberry Pi, you can retrieve it using the hostname command.

```
hostname -I
```

4.If you have finished with the Valheim server, you can quit out of it by pressing CTRL + C.

By pressing these keys, the server will close all connections, save the world and then shut down.

# Getting the Valheim Server to Start at Boot
To get the Valheim server to start at boot on our Raspberry Pi, we must create a service file.

For this file, you will need to remember the options you set earlier when you modified the “start_server.sh” script earlier.

1. We can begin creating this new service by using the following command on your device.

This new service that we are creating will be named “valheim“.

```
sudo nano /etc/systemd/system/valheim.service
```

2. Within this file, you need to enter the following lines. These lines define the service and how the Valheim server should be started.

```
[Unit]
Description=Valheim Dedicated Server
Wants=network-online.target
After=network-online.target

[Service]
Environment=SteamAppId=892970
# Environment=LD_LIBRARY_PATH=/home/pi/valheim_server/linux64:$LD_LIBRARY_PATH
Environment=LD_LIBRARY_PATH=/mnt/ssd/Valheim/valheim_server/linux64:$LD_LIBRARY_PATH
Type=simple
Restart=on-failure
RestartSec=10
KillSignal=SIGINT
User=pi
Group=pi
# WorkingDirectory=/home/pi/valheim_server
WorkingDirectory=/mnt/ssd/Valheim/valheim_server
# ExecStart=/home/pi/valheim_server/valheim_server.x86_64 -nographics -batchmode -port 6000 -public 0 -name "Octapoo>
ExecStart=/mnt/ssd/Valheim/valheim_server/valheim_server.x86_64 -nographics -batchmode -port 6000 -public 0 -name "O>

[Install]
WantedBy=multi-user.target
```

While typing in these new lines, you will need to make sure you modify the “ExecStart” line to reflect the same options you used with the “start_server.sh” file you modified earlier.

3. Once you have finished creating the service, save and quit by pressing CTRL + X, then Y, and finally the ENTER key.

4. You can enable the Valheim server to start at boot on your Raspberry Pi by enabling the service that we just created.

Enable our new “valheim” service by using the following command.

```
sudo systemctl enable valheim
```

5. Instead of waiting until our Raspberry Pi restarts for the service to start, we can manually start it ourselves.

To manually start the “valheim” service, you need to use the command below.

```
sudo systemctl start valheim
```

6. It is possible to check the status of the Valheim service to ensure that it is running.

Run the command below on your device to retrieve the status of the service.

```
sudo systemctl status valheim
```

If your Valheim dedicated server is running correctly, you should see the status marked as “Active: active (running)“.

# Updating your Valheim Server on the Raspberry Pi
Updating your Valheim dedicated server is a reasonably straightforward process and mainly involves us using the “steamcmd” tool that we set up earlier in this guide.

1. Before you continue, you must ensure that your server is stopped. You can’t update the Valheim server when it’s currently in use.

Using our service, you can stop the Valheim dedicated server by using the command below.

```
sudo systemctl stop valheim
```

2.Once you have stopped the server, it is possible to update the dedicated server by typing in the following command.

This command will call the “steamcmd” tool and force it to update the software to the latest available version.

```
/home/pi/steamcmd/steamcmd.sh +@sSteamCmdForcePlatformType linux +login anonymous +force_install_dir /home/pi/valheim_server +app_update 896660 +quit
```

This Pi: 
```
/home/pi/steamcmd/steamcmd.sh +@sSteamCmdForcePlatformType linux +login anonymous +force_install_dir /mnt/ssd/Valheim/valheim_server +app_update 896660 +quit
```

3. Your server now should be updated to the latest version. There is a chance that the update process overrides the “start_server.sh” script, so you may need to change this file again if you aren’t using the service.

If you use the service we created in the previous section, you can now start it back up by running the following command.

```
sudo systemctl start valheim
```

Conclusion
At this point in the guide, you should now have the Valheim dedicated server up and running on your Raspberry Pi.

The Pi operates as a relatively capable and energy-efficient way of running a Valheim server. As a result, you should have a decent experience gaming on the server, with the only hitching we experienced being during the start-up and saving process.

If you have run into any issues with getting the Valheim dedicated server running on your Pi, you can leave a comment below.

You can also check out our other gaming projects or learn what other servers you can run on your Raspberry Pi.
# Useful Links
https://www.reddit.com/r/valheim/comments/lll0ry/how_to_figure_out_latest_valheim_server_version/
https://www.reddit.com/r/valheim/comments/suesde/updating_your_dedicated_valheim_linux_server_to/
