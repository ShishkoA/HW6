## Task 1:
### As a result of each point, you should provide a corresponding command.
localhost - your CentOS VM running in VirtualBox
remotehost - 18.221.144.175 (public IP)
webserver - 172.31.45.237 (private IP)


1.1. SSH to remotehost using username and password provided to you in Slack. Log out from remotehost.
1.2. Generate new SSH key-pair on your localhost with name "hw-5" (keys should be created in ~/.ssh folder).
```bash
ssh-keygen
```
1.3. Set up key-based authentication, so that you can SSH to remotehost without password.
```bash
ssh-copy-id -i hw-5.pub Aleksandr_Shishko@18.221.144.175
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
```
1.4. SSH to remotehost without password. Log out from remotehost.
```bash
ssh Aleksandr_Shishko@18.221.144.175
```
1.5. Create SSH config file, so that you can SSH to remotehost simply running `ssh remotehost` command. As a result, provide output of command `cat ~/.ssh/config`.
```bash
[root@localhost linux]# cat ~/.ssh/config
Host remotehost
    HostName 18.221.144.175
    Port 22
    User Aleksandr_Shishko
    IdentityFile /root/.ssh/hw-5
```
1.6. Using command line utility (curl or telnet) verify that there are some webserver running on port 80 of webserver.  Notice that webserver has a private network IP, so you can access it only from the same network (when you are on remotehost that runs in the same private network). Log out from remotehost.
```bash
[Aleksandr_Shishko@ip-172-31-33-155 ~]$ curl 172.31.45.237
```
1.7. Using SSH setup port forwarding, so that you can reach webserver from your localhost (choose any free local port you like).
```bash
ssh -L 9999:172.31.45.237:80 remotehost –N
```
On another terminal 
```bash
[root@localhost linux]# ss -tln
State      Recv-Q Send-Q Local Address:Port               Peer Address:Port     
LISTEN     0      128    127.0.0.1:9999                     *:*      
curl 127.0.0.1:9999
```bash
1.8 Like in 1.6, but on localhost using command line utility verify that localhost and port you have specified act like webserver, returning same result as in 1.6.
```bash
ssh remotehost curl http://172.31.45.237
```
1.9 (*) Open webserver webpage in browser of your Host machine of VirtualBox (Windows, or Mac, or whatever else you use). You may need to setup port forwarding in settings of VirtualBox.

# Task 2:
## Following tasks should be executed on your localhost as you will need root privileges
2.1. Imagine your localhost has been relocated to Havana. Change the time zone on the localhost to Havana and verify the time zone has been changed properly (may be multiple commands).
```bash
[linux@localhost ~]$ timedatectl list-timezones | grep Havana
America/Havana
[root@localhost linux]# timedatectl set-timezone America/Havana
[root@localhost linux]# timedatectl
      Local time: Mon 2021-12-20 06:09:00 CST
  Universal time: Mon 2021-12-20 11:09:00 UTC
        RTC time: Mon 2021-12-20 11:09:00
       Time zone: America/Havana (CST, -0500)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: no
 Last DST change: DST ended at
                  Sun 2021-11-07 00:59:59 CDT
                  Sun 2021-11-07 00:00:00 CST
 Next DST change: DST begins (the clock jumps one hour forward) at
                  Sat 2022-03-12 23:59:59 CST
                  Sun 2022-03-13 01:00:00 CDT
```
2.2. Find all systemd journal messages on localhost, that were recorded in the last 50 minutes and originate from a system service started with user id 81 (single command).
```bash
journalctl _UID=81 --since "50 minutes ago"
```
2.3. Configure rsyslogd by adding a rule to the newly created configuration file /etc/rsyslog.d/auth-errors.conf to log all security and authentication messages with the priority alert and higher to the /var/log/auth-errors file. Test the newly added log directive with the logger command (multiple commands).
```bash
[root@localhost linux]# cat /var/log/auth-errors
Dec 20 14:42:59 localhost linux: Hello
Dec 20 14:48:23 localhost linux: Emergency
Dec 20 14:48:28 localhost linux: Emergency
Dec 20 14:48:40 localhost linux: Alert
[root@localhost linux]# cat /etc/rsyslog.d/auth-errors.conf
auth.alert,emerg        /var/log/auth-errors
logger -p 'auth.alert' 'Alert'
logger -p 'auth.emerg' 'Emergency'
logger -p 'auth.info' 'Info'
```
