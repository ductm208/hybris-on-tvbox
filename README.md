# Background
As a software engineer specializing in Java development, particularly in SAP Hybris commerce, I am also learning IoT development as a hobby.<br/><br/>
Recently, I migrated Home Assistant to new 4GB RAM-equipped TV box. This upgrade has significantly improved the friendliness of Java development due to the ARM architecture of the CPU (where JVM can be run) and importantly it is running on Linux Debian!!!<br/><br/>
So I decided to reload Debian without installing Home Assistant and tried to install Hybris. Technically, I managed to run a SAP commerce product (a fancy platform) on the cheap entertainment system(US$25) - it is kind of you are owning a Rolls Royce but park it in a motel garage. That means this experiment should be considered as a hobby, to see how much my work can survive in different habitats. We have seen that all enterprise server-side software is hosted on x86(32 and 64) CPU architecture. On the other hand, coding on ARM computers (such as Apple Silicon, Microsoft SQx...) has been progressing relatively slowly.

To start Hybris on this tv box, application server would take 30 mins to be up from minimal extensions setup. Of course we cannot expect it to run as fast as a real computer because it is originally designed to run Android TV system, surviving from 5V power without any heat fan. But please SAP, it would be great if you can collborate with an ARM chip manufacturer to make your own SOC design. I can imagine that one day a ray of 10 tv boxes sitting on the desk can be run as well as SAP commerce cloud CCV2 development enviroments. They would not be as expensive as Azure cloud, wouldn't occupy much space, would work silently, and could be easily scaled up by adding real and cheap devices.

# Preparation
  ## Hardware
### Devices using Amlogic
If you are owning a Tanix W2 (or any X905W2) with 4GB of RAM you are having same hardware specs I have done setting up. In this experiment I use this **Amlogic** SOC chip + Debian distribution from **devmfc** https://github.com/devmfc/debian-on-amlogic - please visit their git repo to check out yourself if more details needed. <br/>
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/941b879a-24bd-4850-b798-be6b880a7010) <br/>
For your convenience, I'll recap essential information: <br/>
System needs to have: <br/>
  - **RAM is at least 4GB**: Hybris itself needs at least 2GB to run. Linux requires 1GB to maintain its processes. Moreover, GPU would take few hundred MB even though headless mode is being used.
  - **Has ethernet port**: Most of tv boxes are not running with wifi on Linux because of driver compatibility.
From above conditions we can choose the below SOCs:

||High-end|Medium|Cheap|
|-----|-----|-----|------|
|Gen 2|S922X|X905X2|X905W2|
|Gen 3||X905X3||
|Gen 4||X905X4||

Afaik, if you tv box is one of the brands below, you probably can go ahead:
- H96
- X96
- Tanix
- Beelink
- X88
- T96
- Magicsee
- A95X

### Devices using Rockchip/Allwinner/Amlogic
If your tvbox hardware does not meet hardware specs from devfmc to install Debian you can search on Armbian (ARM with deBIAN) forum, they have plenty of posts sharing about Amlogic + Rockchip + Allwinner installed Debian and other Linux distribution. As long as you can install Linux, have 2GB RAM spare you can install SAP Hybris on the device.<br/>
- Rockchip https://www.armbian.com/soc/rockchip/
- Allwinnner https://www.armbian.com/soc/allwinner/
- Amlogic https://www.armbian.com/soc/amlogic/

You might wonder why Armbian has such a huge community, but I chose the devmfc git repo for installation. Actually all repos are good but devfmc is offering lightweight package which is more than enough for my setup.
  
  ## Software
  ### 1. Get OS installation done
  Get your installation finish from devfmc or Armbian
  > - Installing on SDcard or USB drive should be better than internal storage (eMMC) because you do not need to erase Android on the tvbox - your setup can be plug and play whenever you want. However, running on external storages means sacrificing the performance.
  > - Choosing right external storage is also very important because SoC have some kernel limitations. I also shared 1 topic to help others choose right accessories https://github.com/devmfc/debian-on-amlogic/discussions/48
  > - To boot externally, you have to enter recovery mode. Some devices hide the reset button in the output jack. My Tanix W2 has that button inside the 3.5 video/audio plug.
  
  ### 2. Install file transfer software
  WinSCP or any SFTP software if you are using Windows. MacOS or Linux we can use scp command to transfer the file.
  
  ### 3. Get SAP Hybris installtion package
  I used CXCOMCL221100U_19-70007431.zip which is release 2211 patch 19. However if you have any 2105 or above it should be fine because we are going to install JDK 17. Due to licensing policy I can not share with you the zip file.

  ### 4. Get localextensions.xml and local.properties from my repo
  It is not neccessarily to get my files. You could have your own definitions.
  - https://github.com/ductm208/hybris-on-tvbox/blob/main/localextensions.xml
  - https://github.com/ductm208/hybris-on-tvbox/blob/main/local.properties
  ### 5. Install terminal to access via SSH
  - Install Putty if you are on Windows.
  ### 6. Make sure you can access through SSH
  You have to know the ip to access SSH tunnel.
  - In some devices, they will show the ip-temperature-time intervally. Since my client is 192.168.1.21 I know 220 abbreviates for tv box 192.168.1.220<br/>
    ![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/060b4b68-9828-49d9-9306-a6690dd74a64)
  - You can plug HDMI to any screen to get the ip - make sure this port plugged before tv box is powered.
  - Go to router to check allocated ips. Some router is showing as tvbox in hostname. (Image below from Google)<br/>
    ![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/fd843e8c-ff23-46c7-99a2-703e4d763ca1)

    

  Access using root, terminal will show you SSH password prompt screen.
  ![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/70195e88-0e83-4af7-9bd8-0e3575cdcafb)
  Default password is tvbox, terminal will bring you to root access as below.
  ![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/c8978b67-eb0e-4625-bd23-85660e9ce2c2)
  Right up the dashboard we can see that 3GB could be used to manage our application.<br/>
  
  Before install any software you have to update + upgrade repo (5mins eta):
  ```
  # apt-get update
  # apt-get upgrade
  ```


    
# Hybris Installation
> If you are the *nix family users (Unix, Linux, MacOS) you might not required to follow exactly my instructions below. However there is a wrapper issue would be arisen when you try to start hybris server. If this happens, please search for the wrapper hacks section.

## Install Java JDK 17
At SSH using tvbox account, enter:<br/>
`# apt install openjdk-17-jdk`
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/d5a4bea8-3764-49ab-9af5-74cf069b19b4)

If thing goes well <br/>
`# java -version`
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/3ac4ec4f-4501-486e-b265-9f74f77df614)

## Create hybris user

> You can use tvbox (which is root) to bypass creating hybris user. I just want to share the best practice of <i>not to use root account to run application to avoid security vulnebilities</i>

Run command from tvbox account to create hybris user<br/>
`# useradd -m hybris`<br/> <br/>
Run set password<br/>
`# passwd hybris`<br/> <br/>
Let assume that password is Hybris123<br/>
If you run command below you will see /home/hybris created <br/>
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/d28ae977-c03d-40d4-83ac-6f1d83d07bf4)
Open another terminal from client, try to access with hybris/Hybris123 to make sure you can access SSH:<br/>
`$ ssh hybris@192.168.1.220`

![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/5f49c5fc-771b-482d-98f8-809966d20ec3)


## Copy installation package to tv box
### *uix users
If you can use scp command from your client, I placed Hybris package zip in Downloads<br/>
`ductran@DucTran:~$ scp ~/Downloads/CXCOMCL221100U_19-70007431.ZIP hybris@192.168.1.220:/home/hybris`
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/e53027de-82f3-4f26-bee8-a126b858d42f) <br/>
scp will ask password and then copy the file from your client and place into tv box as in /home/hybris/ <br/>

### Windows users
You can use any FTP file uploader, url would be sftp://hybris@192.168.1.220 <br/>

--------


Switch back to terminal window accessed with hybris account and type: <br/>
`hybris@tvbox:~$ ls /home/hybris` <br/> <br/>
Ideally file should be up here<br/>
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/b4d3aaf7-761b-4930-85cf-2711421e9ef9) <br/>
Now we need to create /home/hybris/hybrisapp folder<br/>
`hybris@tvbox:~$ mkdir /home/hybris/hybrisapp` <br/> <br/>
Unzip the file by command (2 mins eta):<br/>
`hybris@tvbox:~$ unzip CXCOMCL221100U_19-70007431.ZIP -d hybrisapp` <br/><br/>

Run this command, "README, SIGNATURE.SMF..." should be located in hybris app<br/>
`hybris@tvbox:~$ ls /home/hybris/hybrisapp`<br/>
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/d6f64a0d-06d4-4d84-b41c-9033c3e8f19d)

## Compile + intialize + run the system
Now we can do all stuffs as normal SAP Hybris commerce consultant do except small hack from wrapper. HYBRIS_HOME is /home/hybris/hybrisapp/hybris
### Compile the package
Set ant environment values<br/>
`hybris@tvbox:~/hybrisapp/hybris/bin/platform$ . ./setantenv.sh`<br/><br/>
Run ant clean all as usual<br/>
`hybris@tvbox:~/hybrisapp/hybris/bin/platform$ ant clean all`<br/><br/>
It will ask to create config folder, just enter to create develop profile
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/c3ef2513-dee9-4ddb-af7f-5b4c10a276cf)<br/>
It would take few minutes to create all stuffs but we need only config folder, ant build would be performed again later. As soon as config folder create you can terminate the command by Ctrl + C.<br/>
Now you need to replace newly created local.properties and localextensions.xml. Those files are nothing special, they contains minimal setup.

On *nix you just run from client<br/>
`ductran@DucTran:~$ scp ~/Downloads/local.properties hybris@192.168.1.220:/home/hybris/hybrisapp/hybris/config`<br/>
`ductran@DucTran:~$ scp ~/Downloads/localextensions.xml hybris@192.168.1.220:/home/hybris/hybrisapp/hybris/config`<br/><br/>
On Windows, just drop 2 files to /home/hybris/hybrisapp/hybris/config<br/>

Run ant clean all again, my tv box took 24 minutes to finish building<br/>
`hybris@tvbox:~/hybrisapp/hybris/bin/platform$ ant clean all`<br/>
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/f9a1e568-3329-43e0-b6b7-b138139953a4)<br/><br/>

Now you can run ant initialize and make some :coffee: 😄, it would take 1-2hrs to complete - actually not bad because my Ubuntu on Thinkpad P1 takes 10 minutes for the same task<br/>
`hybris@tvbox:~/hybrisapp/hybris/bin/platform$ ant initialize`<br/>

## Start the server
Now this is time to bring the wrapper hack, if you try to run 
`hybris@tvbox:~/hybrisapp/hybris/bin/platform$ ./hybrisserver.sh debug` you would get something like this <br/>
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/54186f1d-0678-4a93-88ee-f3ae1570b2aa)<br/>
It seems hybrisserver.sh is trying to find the wrapper for aarch64 but this architecture profile is not there.<br/>
Types the command below we will see: The wrapper for Linux is around for different CPU architectures but none of them is aarch64<br>
`hybris@tvbox:~/hybrisapp/hybris/bin/platform$ ls /home/hybris/hybrisapp/hybris/bin/platform/tomcat/bin | grep wrapper`
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/dfe34d58-e8cf-429a-84f0-60d61920bd9c)<br/><br/>

Technical aarch64 is arm-64. We can help the script to be routed to arm-64 by making the symbolic link. At /home/hybris/hybrisapp/hybris/bin/platform/tomcat/bin type: <br/>
`hybris@tvbox:~/hybrisapp/hybris/bin/platform/tomcat/bin$ ln -s wrapper-linux-arm-64 wrapper-linux-aarch64-` <br/>
**Be careful, wrapper-linux-aarch64- is ending with dash (-).**. Check again with different command.
`hybris@tvbox:~/hybrisapp/hybris/bin/platform/tomcat/bin$ ls -l | grep wrapper`<br/>
You will see the there are symbolic link created and it actually is wrapper-linux-arm-64

![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/686ceb28-3cf9-4809-9337-e5f2ce496a5d)<br/><br/>

Now you definitely can run hybris server normally.<br/>

![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/ed54e920-8f96-4b1f-a9c5-1a02e91eb1f2) <br/>

It will show aarch64 is taken, and takes around 30 minutes to start.<br/>

![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/5e578d94-3962-4d38-84e9-14bd7176b8b7)

## Check the result:
![image](https://github.com/ductm208/hybris-on-tvbox/assets/4532530/2c95271c-6ba8-46b3-a23c-7ed3d8a81399)<br/><br/>

You can check the storefront from the url: https://192.168.1.220:9002/yacceleratorstorefront/?site=electronics<br>
And backoffice too: https://192.168.1.220:9002<br>
Surprisingly, once server is ready the platform will run well, not slow as I thought.
