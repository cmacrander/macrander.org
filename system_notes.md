# System Administration Notes For macrander.org

## DNSEXIT

### Dynamic dns

Output from initial installation of dynamic dns client:

>ipUpdate.pl installed to /usr/local/bin
no crontab for root
"@reboot /usr/local/bin/ipUpdate.pl" inserted to crontab

>Generating config file:  /etc/dnsexit.conf

>Done creating config file. You can run the script now.
To do it you can run ipUpdate.pl or use init script.

>File '/tmp/dnsexit-ip.txt' will cache the ip address of
the last successful IP update to our system. For next
update, if the IP stays the same, the update request
won't be sent to our server. You can simply change the
IP at dnsexit-ip.txt file to force the update to DNSEXIT.

>Here are paths to some intresting files:
Config file:  /etc/dnsexit.conf
Pid file:   /var/run/ipUpdate.pid
Log file: /var/log/dnsexit.log
Cache file: /tmp/dnsexit-ip.txt

>Don't forget to read README.txt file in doc directory!

The major problem is that RCN blocks port 80. My way around that is to serve everything from port 443 via https URLs. This set up forwards everything people would normally type in (which are http by default) to an https URL. The only downside is that these URLs have to have different names that what they're being forwarded from.

The URL Forwarding settings give a what-you-type to what-you-get mapping.

### URL Forwarding

| With Cloaking |       From URL      |               To URL              |
|---------------|---------------------|-----------------------------------|
| No            | sadie.macrander.org | https://sadiesart.macrander.org   |
| No            | macrander.org       | https://christopher.macrander.org |
| No            | chris.macrander.org | https://christopher.macrander.org |
| No            | www.macrander.org   | https://christopher.macrander.org |

### DNS Settings

*Host Records (A & AAAA)*

|            Host           |       IP      |  TTL  |
|---------------------------|---------------|-------|
| macrander.org             | 64.85.164.214 | 00:02 |
| christopher.macrander.org | 209.6.113.249 | 00:02 |
| sadiesart.macrander.org   | 209.6.113.249 | 00:02 |
| sadiesblog.macrander.org  | 209.6.113.249 | 00:02 |

Where 209.6.113.249 is the IP address issued our home router by RCN. This may change, but doesn't seem to change often, and the dynamic DNS client running on the server should take care of it. The naked domain, macrander.org, does NOT point at this IP, but instead points at a DNSEXIT IP so it can be forwarded to a https URL.

One consequence of this is that I cannot ssh into macrander.org b/c it's not my machine. That IP address is THEIR machine. I *can* ssh into my machine by using a DNS entry that actually points at my IP, which at the time of this note is christopher.macrander.org.


*Alias Records (CNAME)*

|        Alias        |      Points to Host     |  TTL  |
|---------------------|-------------------------|-------|
| chris.macrander.org | web-forward.dnsexit.com | 00:02 |
| sadie.macrander.org | web-forward.dnsexit.com | 00:02 |
| www.macrander.org   | web-forward.dnsexit.com | 00:02 |


## Locking down ssh: iptables

Followed this tutorial to the letter:
http://blog.bigdinosaur.org/securing-ssh-with-iptables/

In particular, the log of denied packets is in /var/log/iptables.log.


## Launching minecraft

### Screen

Screen is a super cool utility I only just found out about. It lets you
switch between tasks in a terminal as if you were switching windows. So
you can launch a process and detach it and get back to the command line
while the process continutes to run.

    screen -dmS [name your new screen] [command]

will run your command in a detatched, named screen.

    screen -list

will list all the screens, and give you an id that is a combination of
the process id number and the name you assigned it, e.g. 17531.minecraft

    screen -X -S [screen id] kill

will kill a screen and its process

    screen -r [screen id]

will switch to that screen

    Ctrl-a d

will detach the screen again and return you to the command line.
