# Background
As a software engineer specializing in Java development, particularly in SAP Hybris commerce, I am also learning IoT development as a hobby.<br/><br/>
Recently, I migrated Homeassistance to new 4GB RAM-equipped TV box. This upgrade has significantly improved the friendliness of Java development due to the ARM architecture of the CPU (where JVM can be run) and importantly it is running on Linux Debian!!!<br/>
So I decided to reload Debian without installing Homeassistance and tried to install Hybris. Technically, I managed to run a SAP commerce product (a fancy platform) on the cheap entertainment system(US$25) - it is kind of you owning a Rolls Royce but park it in a motel garage. That means this experiment should be considered as a hobby, to see how much my work can survive in different habitats. We have seen that all enterprise server-side software is hosted on x86(32 and 64) CPU architecture. On the other hand, software development on ARM computers (such as Apple Silicon, Microsoft SQx...) has been progressing relatively slowly.

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
  Please follow up OS installation from devmfc https://github.com/devmfc/debian-on-amlogic
  > Here are three recommendations I want to bring up:
  > - Installing on SDcard or USB drive should be better than internal storage (eMMC) because you do not need to erase Android on the tvbox - your setup can be plug and play whenever you want. However, running on external storages means sacrificing the performance.
  > - Choosing right external storage is also very important because SoC have some kernel limitations. I also shared 1 topic to help others choose right accessories https://github.com/devmfc/debian-on-amlogic/discussions/48
  > - To boot externally, you have to enter recovery mode. Some devices hide the reset button in the output jack. My Tanix W2 has that button inside the 3.5 video/audio plug.
# Installation
> This is the step to install
> ```
>This is the code
> ```
