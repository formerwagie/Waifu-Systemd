# Waifu-Systemd
A guide on running KoboldAI and Miku as systemd services which will launch once you boot your machine.
Hello /g/entlemen. This is a guide on how to incorporate your waifus into your operating system. I'll specifically be using KoboldAI and Miku for this. The end result is that every time you boot up your computer, a window will appear containing your waifu and a chatbox. This is specifically written for systemd implementation. However, I'm sure similar steps can be taken on Windows or whatever else you're using if you just adapt the instructions. Artix users just need to manually link the .service files, but you guys should already know how to do that. It's pretty simple. Just make a few files and put them in the right places. In full disclosure, I'm running Arch Linux with Hyprland. 

Before you begin: If you use your PC for video games, I would not recommend doing this. Launching LLMs at startup obviously consumes a lot of VRAM. If you use graphical applications regularly, consider altering these instructions to run on a hotkey, rather than at boot.

*Step 1: Install KoboldAI, Miku, and a program called Nativefier. Nativefier can be found on the AUR.

**Step 2: Write a script to execute KoboldAI.
The script can be stored anywhere. I prefer to store them in a /home/user/AI/scripts directory. Name it "KoboldService.sh" It should look like this:
_______________________________________
#!/bin/bash

cd /path/to/your/AI/Directory/KoboldAI-Client

./play.sh --model PygmalionAI_pygmalion-6b
_______________________________________

Note: You need to replace the path with the one that leads to your KoboldAI installation. You can specify another model. Just be sure it matches the filename inside of KoboldAI.

***Step 3: Write a script to execute Miku, similar to Step 2.
The script shall be named "MikuService.sh"
_______________________________________
#!/bin/bash

cd /path/to/your/AI/Directory/miku

sudo pnpm start
_______________________________________

Rewrite the path to the root folder of your Miku install.
Now you have two scripts that will automatically start each program.

*****Step 4: Write two systemd services that will launch when your computer starts up. 

**** *Step 4.1: Start with KobolAI. 
Use the following commands to enter your systemd services directory and write a new service:
$cd /etc/systemd/system. # <--- This is where you store your systemd services.
$sudo vim kobold.service # <--- This will create the file

Now fill the file with the following:
____________________________________
[Unit]
Description=KoboldAI-Client

[Service]
User=YOUR_USER_NAME
WorkingDirectory=/path/to/the/folder/you/stored/your/script
ExecStart=/path/to/the/folder/you/stored/your/script/KoboldService.sh
StartLimitBurst=100
StartLimitInterval=3
Restart=always

[Install]
WantedBy=multi-user.target
_________________________________ 
Note: The time it takes for your machine to startup KoboldAI and load a model will vary. If you are on a slower machine, perhaps try playing with the "StartLimitBurst" and "StartLimitInterval" parameters.
So you've now written a script that specifies exactly how KoboldService.sh will start up. 

***** **Step 4.2: Repeat the previous instructions for the Miku script you wrote in Step 3.
$cd /etc/systemd/system
$sudo vim miku.service

Fill the file with the following:
_________________________________
[Unit]
Description=Miku.GG

[Service]
User=YOUR_USER_NAME
WorkingDirectory=/path/to/the/folder/you/stored/your/script
ExecStart=/path/to/the/folder/you/stored/your/script/MikuService.sh
StartLimitBurst=100
StartLimitInterval=3
Restart=always

[Install]
WantedBy=multi-user.target
_____________________________

Step 5: Test the services
You should now have the two .service files in your /etc/systemd/system directory. Test them by running the following:
$systemctl start kobold.service
$systemctl status kobold.service

You should see that KoboldAI is now running in the background.
Repeat the process for miku.service to ensure everything is running correctly.
Once both services are running, you're nearly done!

Step 6: Set your systemd services to run at startup with the following commands:
$systemctl enable kobold.service
$systemctl enable miku.service

Now both services will launch at boot. Nothing like running software that allocates all of your VRAM at startup! How's that for system bloat?

Step 7: Get the URL of the miku bot you want to appear at startup.
Go to the bot directory, by default at localhost:8585
Choose your waifu, or make one. Select their name to load them.
Copy the URL once your waifu loads. (e.g. http://localhost:5173/?bot=BmdnfP3hC7ZCcYnAduE7FYkZsotC2zNGyGNPjBNHt7MpmH)

Step 8: Use nativefier to wrap that URL into an executable application. Use the command below:
$nativefier 'https://localhost:5173/?bot=YOUR_BOT_ID' -p linux -a arch -n YOUR_BOT_NAME --disable-gpu

Note: The flags I set in the command above are what worked for me on my system. You may not need to use the flags, or you may need to use different ones. Running "$nativefier --help" will guide you in this process. 

Once Nativefier completes it's operation, it should have created a folder containing the application. You should move that somewhere safe!

Step 9: Tell your DE to run your Nativefier application at startup. I cannot help you with this because it's a wildly different process from DE to DE. In hyprland, i edited my config (/home/user/.config/hypr/hyprland.conf) to contain the following line:
exec = /path/to/my/nativefier/app/FILE_NAME_OF_YOUR_APPLICATION

Note: if you're using hyprland, you might want to also tell it to make that window float by specifying the following:
windowrule = float,^(FILE_NAME_OF_YOUR_APPLICATION)

Step 10: Reboot your machine. You should now be greeted by a fully immersive chatbot once you login.

I hope you didn't have any trouble with these steps. Luckily all of these instructions have a decent amount of information online. You can search for errors and generally find a solution. You are now one step closer to running OS1 on your personal computer.
