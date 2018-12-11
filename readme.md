#### SELKS Ver5 Build from Source for AWS and AZURE Cloud Network Security Monitoring 

SELKS is a free and open source Debian (with LXDE X-window manager) based IDS/IPS platform 
released under GPLv3 from Stamus Networks (https://www.stamus-networks.com).

Their GITHUB is: https://github.com/StamusNetworks

Their SELKS WIKI: https://github.com/StamusNetworks/SELKS






#### This is a how-to about installing SELKS from source and not from ISO, if you wish to use ISO:                          
You can download ready to use images from the SELKS download page https://www.stamus-networks.com/open-source/selks  


 Prerequisites 
=

- Debian 9.5+ Stretch
- 2CPU / 8+ GB RAM (More RAM the better 16+)
- Two NIC cards: One for management access and the other one for monitoring
- Coffee.... lots of coffee 


 Add current user is in sudo group 
=

-  usermod -aG sudo username


Install time!!!
=


Update and upgrade debian
=
```sh
apt-get update
apt-get upgrade
```
Install GIT 
=
```sh
sudo apt-get install -y git net-tools
```

Change directory to OPT
=
```sh
cd /opt/
```

Pull GIT from Nimdy
=
```sh
git clone https://github.com/Nimdy/SELKS-Install-from-source.git
```


 Clone GIT from SELKS for staging files required system config  
 =
 ##### The install script will copy these over to the correct directories and then ask you to reboot

```sh 
git clone -b SELKS5 https://github.com/StamusNetworks/SELKS.git SELKS_CONFIGS
```

Change directory
=
```sh
cd /SELKS-Install-from-source
```

Change permissions to execute script
=
```sh
sudo chmod -x install_SELKS.sh
```
During install you might be asked a question about Scirius database related information:
=
```
        Select "Yes" for Scirius database configuration
```

After Succesfull Installation 
=

- Reboot and login to the system

- Change root password, if required. Default password is StamusNetworks.
```sh
cd /opt/selks/Scripts/Setup
```

Setup interface and mon Full Packet Capture settings
=
```sh
./selks-first-time-setup.sh
```        

Select interface you wish to receive network traffic
=

> Select interface - ie: eth1
> Select PCAP setting -  ie: option 1     



Verfiy services are running
=

```sh
systemctl status kibana logstash elasticsearch suricata
```

### If there are any errors with kibana or any other service restart the service and review the logs, if issues continue
=
```sh
        systemctl restart kibana 
        systemctl status kibana 
```        

Configure HOMENET for Suricata 
=
```sh
        vi /etc/suricata/suricata.yaml
```
> Edit to reflect network range monitored:   HOME_NET: '[192.168.0.0/16,10.0.0.0/8,172.16.0.0./12]"
 
 Restart suricata service to reflect changes
=
```sh
        systemctl restart suricata
```
      
#### Visit SELKS Scirius Dashboards to verify dashboards are setup and populating data


- https://ipofSELKSinstall

- Click Dashboards

- Click logstash-* 

- Set as default index by clicking the star icon
       
- Click Dashboards 

- Click SN-ALL
        

Test Suricata is working
=
```sh
        curl testmyids.com
```
- Visit Scirius dashboard and review alert.
 

Validate interface is in promisc mode
=
```sh
        ifconfig
```
> if interface does not say promisc, then set it manually.

Configure /etc/network/interfaces
=
```sh
        vi /etc/network/interface
```

> Replace or add the following 

        auto eth1
            iface eth1 inet manual
            up ifconfig eth1 0.0.0.0 up
            up link set eth1 promisc on
            down ip link set eth1 promisc off
            down ifconfig eth1 down
> Save File          
```sh
    :wq!
```


Restart networking
=
```sh
        systemctl restart networking
```


## Security in mind - Check STIGs                
### Security Technical Implementation Guide for Debian 
> Download from Hardendlinux's GITHUB 
>More info visit: https://github.com/hardenedlinux/STIG-4-Debian

```sh
cd /opt/
git clone https://github.com/hardenedlinux/STIG-4-Debian.git
cd /opt/STIG-4-Debian
chmod +x stig-4-debian.sh
./stig-4-debian.sh -H
```

# More security steps and how-to:
```sh
https://static.open-scap.org/ssg-guides/ssg-debian8-guide-index.html
```
> Yes, it is for Debian 8 however these recommendations work for Debian 9 as well



TWEAKS and breaking stuff maybe
=

>Visit: https://github.com/StamusNetworks/SELKS/wiki/SELKS-5.0-Beta1 for more tips.

>If you have a lot of ram, then feed the machine of logstash and elasticsearch.
>For example, if I had 16GB is ram. I would give 6 to logstash and 6 to elasticsearch

# Edit  jvm.options for elasticsearch and logstash
```sh
vi /etc/elasticsearch/jvm.options
```
> Change:

- -Xms1g
- -Xmx1g
>to
- -Xms6g
- -Xmx6g
```sh
vi /etc/logstash/jvm.options
```
> change 
- -Xms1g
- -Xmx1g
>to
- -Xms6g
- -Xmx6g



#### Not complete but this how-to will get you up and running.  The hardest thing is ensuring that you are receiving traffic on the interface you setup. If you need help just ask... I will post more how-tos about piping traffic because AWS and AZURE want to hold tight to the network monitoring $$$$$ applications but it is do-able...just a few hacks.  If you have some issues google before asking... learning how to deploy a IDS on a system with ELK is awesome but sometimes you run into errors.  Most of these errors can be fixed by googling and fixing some type of config file. After you have it up and running, delete it and do it again.  Learning is run?!?


# SPECIAL Thanks to the SELKS team for helping me during my Azure nightmare and enabling me to build this how-to for others on the interwebz.




