# Phase 3:Perimeter Defense & Hadening

## **Tasks:**

- Install **OPNsense**
- Configure **WAN**/**LAN**
- deploy **Zenarmor**/**Suricata** in **IDS** mode and tune rules

## 1-Install OPNsense

the OPNsense installation comes in form of `OPNsense-25.7-dvd-amd64.iso.bz2`  by using 7zip we exctract it to  `OPNsense-25.7-dvd-amd64.iso`  now in VirtualBox we give it 4gb ram with 2 core

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image.png)

and 20gb of storage 

In Network settings we 

- **first Adapter** :  same as others (DC and Workstation) to config LAN(Host-Only)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%201.png)

- **Second Adapter :** to config WAN (Bridget)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%202.png)

<aside>
<img src="https://www.notion.so/icons/info-alternate_green.svg" alt="https://www.notion.so/icons/info-alternate_green.svg" width="40px" />

Make sure the OS version is 64-bit or you will face `CPU doesnt support long mode`  error in OPNsense installation

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%203.png)

</aside>

now in the installation n for LAGGs(because we have one **WAN NIC** and one **LAN NIC** LAGG is only useful if we have multiple NIV to bond together)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%204.png)

and no for the others until we reach log in page **`*root:opnsense`***  

and the next step for configure WAN and LAN

## 2-Configure **WAN**/**LAN**

in the  options of the opnsense

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%205.png)

choose 2)  we can set IP address

for WAN we enable DHCP(so we connect to the internet)

and LAN disable DHCP and give it static ip `192.168.77.55`  with subnet `25` 

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%206.png)

then we can access the url `https://192.68.77.55` 

in DC

pinging the OPNsense gave us:

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%207.png)

So we can access the website and log in as `root:opnsense`

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%208.png)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%209.png)

the live mdeia mode will  prevent us from saving the dashboard state so we need to make sure our opnsense run from the disk directry 

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2010.png)

in system we put the Optical(iso) as first boot option then ruuning the VM and log in as `install:opnsense` this menu will appear

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2011.png)

then enter in **Continue with defaule keymap** and choose **Install(UFS)**

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2012.png)

then after choosing the disk we wait for the installation to finish 

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2013.png)

after everything done reboot the vm and remove the iso from Devices

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2014.png)

and then running the vm and reconfigure the WAN/LAN and here the last resulte

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2015.png)

after this we head to Wizard System configuration

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2016.png)

We fill the informations

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2017.png)

For the **WAN** we select the DHCP type

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2018.png)

and for **LAN** we put the IP address the opnsense one

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2019.png)

we kept the same password for now then apply in Finish

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2020.png)

Now the dashborad all set

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2021.png)

and here the dashboard form

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2022.png)

## 3-deploy **Zenarmor**/**Suricata** in **IDS** mode and tune rules

### A-Deploy Zenarmor

first of all we update Status from Frimware

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2023.png)

we may run throught some errors here so its better to use OPNsense shell

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2024.png)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2025.png)

and run `opnsense-update -fp`  to update directry from the shell instead of Fimware/Status

after all updats are up

go ahead to System/Firmware/Plugins search for `os-sunnyvally`  (the vendor for Zenarmor)and install

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2026.png)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2027.png)

<aside>
<img src="https://www.notion.so/icons/info-alternate_green.svg" alt="https://www.notion.so/icons/info-alternate_green.svg" width="40px" />

make sure Version of OPNsense is 25.7.11_9(lastest update) or the installation wont work

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2028.png)

</aside>

ow in plugins look for `os-sensi`  and intall it

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2029.png)

now Zenarmor will be apeared in the menu

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2030.png)

then accept and install local SQLite

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2031.png)

choosing deployment mode Routed Mode and em0(lan)

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2032.png)

the finish

ad here the zenarmor dashboard:

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2033.png)

for The IDS mode put the **Passive Mode(Reporting Only)** in Deployment mode

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2034.png)

### B-tune rules

in Zenarmor Policies we gonna edit the Default(i left backup)

we disable some rules :D

![image.png](Phase%203%20Perimeter%20Defense%20&%20Hadening/image%2035.png)