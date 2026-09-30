# NAGIOS Ubuntu Setup Guide

![](https://media.licdn.com/dms/image/v2/D562DAQHhsWfjg6Xj0A/profile-treasury-image-shrink_1280_1280/B56Z0AZtPcGgAQ-/0/1773828281479?e=1791392400&v=beta&t=7iv723EvUNSc-aw21c-_CfQ-MxcrXy4_I-ZiIFtW2bk)

## Prerequisites

### Disk Setup
- Use Disk Manager to reduce Windows disk size, leaving sufficient space for other programs

---

## Step 1: Install SSH Server

Enable remote access to Ubuntu server via SSH:

```bash
sudo apt install openssh-server
sudo systemctl start ssh
sudo systemctl enable ssh
```

---

## Step 2: Install Apache2



install apache2:

```>sudo apt install apache2```

Install Apache2 web server:

```bash
sudo useradd nagios
sudo groupadd nagios
sudo usermod -aG nagcmd nagios
sudo usermod -aG nagcmd www-data
```

---

## Step 4: Download & Extract Nagios

Download the latest Nagios release:o useradd nagios

sudo groupadd nagios

sudo usermod -aG nagcmd nagios

sudo usermod -aG nagcmd www-data
```bash
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-x.x.x.tar.gz
tar -xzf nagios-x.x.x.tar.gz
cd nagios-x.x.x
```

---

## Step 5: Configure & Compile Nagios

### Configure Nagios
```bash
sudo ./configure --with-nagios-group=nagios --with-command-group=nagios
```

### Compile & Install

Use the `make` command to compile source code into executables:

```bash
sudo make all
sudo make install
sudo make install-init
sudo make install-config
sudo make install-commandmode
sudo make install-webconf
```

---

## Step 6: Configure Apache for Nagios

Enable required Apache modules:

```bash
sudo a2enmod rewrite cgiWe use the make command to compile source code into executable:

```

sudo make all
---

## Step 7: Create Web Dashboard Credentials

Create credentials for the Nagios web dashboard using `htpasswd`:

```bash
sudo htpasswd /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

> **Important**: Username must be `nagiosadmin` to align with the default Nagios administrator account

---

## Next Steps

After completing this setup:
1. Start Apache: `sudo systemctl start apache2`
2. Start Nagios: `sudo systemctl start nagios`
3. Access dashboard: `http://your-server-ip/nagios`
4. Login with username: `nagiosadmin` and the password you set

---

## Quick Reference

| Service | Start Command | Enable on Boot |
|---------|---------------|----------------|
| Apache | `sudo systemctl start apache2` | `sudo systemctl enable apache2` |
| Nagios | `sudo systemctl start nagios` | `sudo systemctl enable nagios` |
| SSH | `sudo systemctl start ssh` | `sudo systemctl enable ssh` |


Configure Apache for Nagios

```

sudo a2enmod rewrite cgi

```

Make the credentials for the Nagios web dashboard using htpasswd:

```

sudo htpasswd /usr/local/nagios/etc/htpasswd.users nagiosadmin  //has to be nagiosadmin to be inline with the Nagios admin user which is nagiosadmin by default

```

htpasswd is an Apache tool that creates users for web authentication (HTTP Basic Auth).



Stores usernames/passwords in a file (e.g. htpasswd.users)



-for Apache2 to run php:

```sudo apt install php,  libapache2-mod-php php-cli php-common php-gd ```



Restart Apache

```

sudo systemctl restart apache2

```



and finally 

```

sudo systemctl start nagios

sudo systemctl enable nagios

```



Go to http://localhost/nagios , enter credentials 



==================================================

##### Start Monitoring

==================================================



\*\*Installing the necessary plugins:\*

We need plugins, if you downloaded the the https://assets.nagios.com/downloads/nagioscore/releases/nagios-x.x.x.tar.gz, then you should also have nagios-plugins-x-x-x.tar.gz, unzip it and```cd``` to it:

```

./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-snmp //if needed

sudo apt install snmp libsnm-dev libnet-snmp-perl

```



check plugins:

On Ubuntu: ```ls -l /usr/local/nagios/libexec```



You should see the check\_\* you may need, like ``check\_ping``` in our case, and to test it:



``` /usr/local/nagios/libexec/check\_ping -H 127.0.0.1 -w 100.0,20% -c 500.0,60% ```



=========================================

##### Adding devices to monitor:

=========================================

check for the objects like routers, linux-servers... in ```/usr/local/nagios/etc/objects/network-devices.cfg``` depending on your need.

create a file for the type of devices you are willing to monitor if it doesn't exist:

``` sudo nano /usr/local/nagios/etc/objects/network-devices.cfg ```, and add this file path to the Nagios object config file in: ```/usr/local/nagios/etc/nagios.cfg```

Now in the device file (created or built-in), Add the device:



```

define host{

\#	use 			generic-host //if templates work, else:

&nbsp;	host\_name		NAME

&nbsp;	alias			ALIAS

&nbsp;	address			<IP>

&nbsp;	check\_command		check-host-alive  //  command in /usr/local/nagios/etc/commands.cfg,you can make your own custom commands



\#	max\_check\_attempts	5	     //if templates don't work, add these

&nbsp;	check\_period 		24x7

&nbsp;	notification\_interval	30

&nbsp;	notification\_period	24x7

}

```



Add a service for Nagios to monitor (for example check it with ping command !):

```

define service{

&nbsp;	use 			generic-service

&nbsp;	host\_name		NAME

&nbsp;	service\_description	PING

&nbsp;	check\_command		check\_ping!100.1,20%!500.0,60%

}



```





\*Verify Config\*

```/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg```



Host check VS Service Check

Can define commands

APs Monitoring: >uplink status == Ethernet intrfc status, doesn't say DOWN on wan Loss







\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_



### Custom plugin to check if a Host (AP) can reach a different host (router)

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_



We are going to test if the AP can reach the router, so Nagios will connect to the AP via ssh, run the ping and show state based on the response.



We need to understand the pillars of Nagios first:

In Nagios;

-Hosts represent the physical or virtual devices being monitored, such as servers or network devices.

-Services are specific aspects of a host that you want to monitor using 'check\_command's, like disk usage or network connectivity . 

-A command in Nagios is a predefined or custom script used to check the status of a service (snmp/http/ping... test).





First we need the custom plugin script.

I tried this at first, simple, but you need sshpass ```sudo apt-get install sshpass``` first:

```

\#!/bin/bash

AP\_IP="AP\_IP\_ADDRESS"

ROUTER\_IP="ROUTER\_IP\_ADDRESS"

SSH\_USER="username"

SSH\_PASS="password"



\# SSH into the AP and send a ping from the AP to the router

sshpass -p "$SSH\_PASS" ssh -o StrictHostKeyChecking=no "$SSH\_USER"@"$AP\_IP" "ping -c 4 $ROUTER\_IP"



\# Check the result of the last command ($?) and output the status for Nagios, for ping: "Exit code 0" is returned as long as at least one packet gets a reply, even if there's packet loss => the destination is reachable. wheras Exit code 1 is returned when no packets get a response. It happens when the destination is completely unreachable.



if \[ $? -eq 0 ]; then

&nbsp;   echo "PING OK - Router is reachable from AP"

&nbsp;   exit 0

else

&nbsp;   echo "PING CRITICAL - Router is not reachable from AP"

&nbsp;   exit 2

fi



```



But, the AP's OS only allows interactive CLI:

So I used \*expect\* instead, relying on regex and outputs



```

\#!/usr/bin/expect -f

\# Usage: check\_reachability\_ssh <AP\_IP> <ROUTER\_IP> <USER> <PASS>



set timeout 15

set ap\_ip    \[lindex $argv 0]

set router\_ip \[lindex $argv 1]

set user     \[lindex $argv 2]

set pass     \[lindex $argv 3]



if { $ap\_ip == "" || $router\_ip == "" || $user == "" || $pass == "" } {

&nbsp; puts "UNKNOWN - Missing arguments"

&nbsp; exit 3

}



\# Start interactive SSH

spawn ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null $user@$ap\_ip



expect {

&nbsp; -re "assword:" { send "$pass\\r" }

&nbsp; timeout { puts "CRITICAL - SSH timeout"; exit 2 }

&nbsp; eof { puts "CRITICAL - SSH failed"; exit 2 }

}



\# Wait for a CLI prompt (adapt if needed)

expect {

&nbsp; -re {.+#\\s\*$} {}

&nbsp; timeout { puts "CRITICAL - No CLI prompt"; exit 2 }

}



\# Run ping (command may vary; adjust if your AP expects different syntax)

send "ping $router\_ip \\r "



\# Capture ping output, then decide OK/CRITICAL

set output ""

expect {

&nbsp; -re {(\[0-9]+)% packet loss} {

&nbsp;   append output $expect\_out(0,string)

&nbsp; }

&nbsp; -re {round-trip min/avg/max} {

&nbsp;   append output "Ping Successful"

&nbsp; }

&nbsp; timeout {

&nbsp;   # If ping output format is different, we may time out

&nbsp;   puts "UNKNOWN - Ping output not detected"

&nbsp;   exit 3

&nbsp; }

}



\# Exit the CLI cleanly

send "exit\\r"

expect eof



\# Check for packet loss and decide on the output

if {\[regexp {100% packet loss} $output]} {

&nbsp; puts "CRITICAL - Router not reachable from AP"

&nbsp; exit 2

} elseif {\[regexp {0% packet loss} $output]} {

&nbsp; puts "OK - Router reachable from AP"

&nbsp; exit 0

} else {

&nbsp; puts "UNKNOWN - Unable to determine ping result"

&nbsp; exit 3

}

```



Don't forget: ```chmod +x check\_ap\_ping.sh```







Nagios uses the exit code returned by the script to determine the status.

Exit codes:

&nbsp;	0 = OK

&nbsp;	1 = WARNING

&nbsp;	2 = CRITICAL

&nbsp;	3 = UNKNOWN



Define a command for the service check to use, the command in turn, uses our custom script:

```

define command{

&nbsp;   command\_name    check\_ap\_ping

&nbsp;   command\_line    /path/to/your/script.sh $ARG1$ $ARG2$

}

```

Now let's use this command, where? exactly. in a service:





define service{

&nbsp;   use                 generic-service

&nbsp;   host\_name           AP\_HOST\_NAME

&nbsp;   service\_description AP Ping to Router

&nbsp;   check\_command check\_ap\_ping!10.0.0.21!10.0.0.1

&nbsp;   normal\_check\_interval   5

&nbsp;   retry\_check\_interval    1

&nbsp;   max\_check\_attempts    3

&nbsp;   check\_interval        5

&nbsp;   notification\_interval 120

}







In the Nagios Services dashboard, the Status information is what your command writes to stdout, so hide what you don't need shown with 2>/dev/null and output status information you need, you may use ```log\_user 0``` to suppresses all interaction output from Expect or ```log\_user 1``` to renable them when u need to.







\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_



&nbsp;	    SNMP plugin to check Host (AP)'s Interface status			

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

First off we need to check the ability of our host to use snmp. The aruba AP does, and to check it:

```

\# show snmp-configuration



Engine ID:XXXXX

Community Strings

-----------------

Name

----

\*\*PUBLIC\*\*

SNMPv3 Users

------------

Name  Authentication Type  Encryption Type

----  -------------------  ---------------

SNMP Trap Hosts

---------------

IP Address  Version  Name  Port  Inform

----------  -------  ----  ----  ------

```



you may or may ot find an initial default snmp configuration, for simplicity I added the snmp v1/2c community string ```PUBLIC``` (it's like a passphrase)





Now we need to list the OIDs and select the correct ones:

we will use :``` snmpwalk -v2c -c PUBLIC 192.168.0.2 ```



and that will get us all the OIDs, because snmpwalk fires consecutive ```snmpgetnext``` requests thus receiving the whole MIB:

&nbsp;

```

iso.3.6.1.2.1.1.1.0 = STRING: "ArubaOS (MODEL: 515), Version 8.12.0.2-8.12.0.2 SSR"

iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.14823.1.2.107

iso.3.6.1.2.1.1.3.0 = Timeticks: (581853) 1:36:58.53

iso.3.6.1.2.1.2.2.1.2.1 = STRING: "eth0"

iso.3.6.1.2.1.2.2.1.2.2 = STRING: "eth1"

iso.3.6.1.2.1.2.2.1.2.10 = STRING: "eth99"

iso.3.6.1.2.1.2.2.1.2.50 = STRING: "radio0\_ssid\_id0"

iso.3.6.1.2.1.2.2.1.2.70 = STRING: "radio1\_ssid\_id0"

iso.3.6.1.2.1.2.2.1.2.90 = STRING: "gre0"

iso.3.6.1.2.1.2.2.1.2.91 = STRING: "gre1"

iso.3.6.1.2.1.2.2.1.2.92 = STRING: "gre2"

iso.3.6.1.2.1.2.2.1.2.93 = STRING: "gre3"

iso.3.6.1.2.1.2.2.1.2.94 = STRING: "gre4"

iso.3.6.1.2.1.2.2.1.2.500 = STRING: "BR0"

...

```



For example, I want to monitor the status of the ethernet and wifi interfaces, so I check their OIDs:



```

>> snmpwalk -v 2c -c PUBLIC 192.168.0.2 .1.3.6.1.2.1.2.2.1.2

iso.3.6.1.2.1.2.2.1.2.1 = STRING: "eth0"

iso.3.6.1.2.1.2.2.1.2.2 = STRING: "eth1"

iso.3.6.1.2.1.2.2.1.2.10 = STRING: "eth99"

iso.3.6.1.2.1.2.2.1.2.50 = STRING: "radio0\_ssid\_id0"

iso.3.6.1.2.1.2.2.1.2.70 = STRING: "radio1\_ssid\_id0"

iso.3.6.1.2.1.2.2.1.2.90 = STRING: "gre0"

iso.3.6.1.2.1.2.2.1.2.91 = STRING: "gre1"

iso.3.6.1.2.1.2.2.1.2.92 = STRING: "gre2"

iso.3.6.1.2.1.2.2.1.2.93 = STRING: "gre3"

iso.3.6.1.2.1.2.2.1.2.94 = STRING: "gre4"

iso.3.6.1.2.1.2.2.1.2.500 = STRING: "BR0"



>> snmpwalk -v 2c -c  192.168.0.2 .1.3.6.1.2.1.2.2.1.8  #this is the $global\_OID

iso.3.6.1.2.1.2.2.1.8.1 = INTEGER: 1   # Notice how the OID for each intrfc is the $global\_OID.$Id\_of\_each\_intrfc

iso.3.6.1.2.1.2.2.1.8.2 = INTEGER: 2

iso.3.6.1.2.1.2.2.1.8.10 = INTEGER: 2  

iso.3.6.1.2.1.2.2.1.8.50 = INTEGER: 1

iso.3.6.1.2.1.2.2.1.8.70 = INTEGER: 1  # 1 => UP 

iso.3.6.1.2.1.2.2.1.8.90 = INTEGER: 2  # 2 => DOWN

iso.3.6.1.2.1.2.2.1.8.91 = INTEGER: 2

iso.3.6.1.2.1.2.2.1.8.92 = INTEGER: 2

iso.3.6.1.2.1.2.2.1.8.93 = INTEGER: 2

iso.3.6.1.2.1.2.2.1.8.94 = INTEGER: 2

iso.3.6.1.2.1.2.2.1.8.500 = INTEGER: 1







So based on that, and combining it with Nagios' check\_snmp plugin, #for some reason iso. doesn't work but replacing it with .1. does ✓:

```

>./check\_snmp -H 192.168.0.2 -C PUBLIC -P 2c -o .1.3.6.1.2.1.2.2.1.8.1 # an interface that's UP

SNMP OK - 1 | iso.3.6.1.2.1.2.2.1.8.1=1



>./check\_snmp -H 192.168.0.2 -C PUBLIC -P 2c -o .1.3.6.1.2.1.2.2.1.8.2 # an interface that's DOWN

SNMP OK - 2 | iso.3.6.1.2.1.2.2.1.8.2=2



```



we need to tell the snmp\_check plugin when to say OK and when to say CRITICAL,but first we need to understand the range system:

-flag x:y: trigger flag when output value v is outside \[x;y] => y<v \& v<x



|Range Definition	|Generate an Alert if x...|
|-|-|
|10|< 0 or > 10, (outside the range of {0 .. 10})|
|10:|< 10, (outside {10 .. ∞})|
|~:10|> 10, (outside the range of {-∞ .. 10})|
|10:20|< 10 or > 20, (outside the range of {10 .. 20})|
|@10:20|≥ 10 and ≤ 20, (inside the range of {10 .. 20})|



 using these flags: 

```

-w 1: The warning threshold == displays warning when > 1 and <0 (meaning the interface is Up -> 1<=1  -> OK).

-c 1: The critical threshold == displays Critical when > 1 (meaning the interface is Down -> 2 -> Crtcl).

-r X: Ok when X (RegX Rule): -r ^up$

-l "Label":

```



play around and test till you get the desired result from the plugin.



then we can add it to a new Service:



```

define service {

&nbsp;   use                 generic-service

&nbsp;   host\_name           NAME

&nbsp;   service\_description AP eth0 Interface Status

&nbsp;   check\_command       check\_snmp!-H!192.168.0.2!2c!public!.1.3.6.1.2.1.2.2.1.8.1

&nbsp;   normal\_check\_interval   5

&nbsp;   retry\_check\_interval    1

&nbsp;   max\_check\_attempts    3

&nbsp;   check\_interval        5

&nbsp;   notification\_interval 120

}

```

entering the args and flags doesn't depend on the command as tested alone in the CLI, instead, it should align with how it is set up in the ```commands.cfg```

this is how it is defined in my case:

```

define command {



&nbsp;   command\_name    check\_snmp

&nbsp;   command\_line    $USER1$/check\_snmp -H $HOSTADDRESS$ $ARG1$ -w $ARG2$ -r $ARG3$ -c $ARG4$ -l

}

```

Updated it to:



```

define command {



&nbsp;   command\_name    check\_snmp

&nbsp;   command\_line    $USER1$/check\_snmp -H $ARG1$ -P $ARG2$ -C $ARG3$ -o $ARG4$ -w $ARG5$ -c $ARG6$  -r $ARG7$ #rule -l $ARG8$ #label   #don't have to use all args when calling the cmnd in services

}

```



\#same Order



======================

More Plugins:

======================

check\_nrpe: Standing for "Nagios Remote Plugin Executor," this plugin acts as a bridge to trigger scripts running on a remote machine.

check\_multi: execute multiple child checks within a single Nagios service execution simultaneously and aggregates the results.

