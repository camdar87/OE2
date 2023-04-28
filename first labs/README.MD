# #118: Fw: Change the hostnames and also the prompts according to the purposes they serve.
 **this was done on `app-e` VM**
- pstree -p

```txt
systemd(1)─┬─accounts-daemon(1039)─┬─{accounts-daemon}(1047)
           │                       └─{accounts-daemon}(1051)
           ├─agetty(1078)
           ├─agetty(1084)
           ├─atd(1061)
           ├─cron(1027)
           ├─dbus-daemon(1041)
           ├─hv_kvp_daemon(7869)
           ├─lvmetad(460)
           ├─lxcfs(1050)─┬─{lxcfs}(27797)
           │             ├─{lxcfs}(14466)
           │             ├─{lxcfs}(14467)
           │             ├─{lxcfs}(14469)
           │             ├─{lxcfs}(14470)
           │             ├─{lxcfs}(14471)
           │             ├─{lxcfs}(5422)
           │             ├─{lxcfs}(5423)
           │             ├─{lxcfs}(5424)
           │             └─{lxcfs}(5425)
           ├─mysqld(25861)─┬─{mysqld}(25863)
           │               ├─{mysqld}(25864)
           │               ├─{mysqld}(25865)
           │               ├─{mysqld}(25866)
           │               ├─{mysqld}(25867)
           │               ├─{mysqld}(25868)
           │               ├─{mysqld}(25869)
           │               ├─{mysqld}(25870)
           │               ├─{mysqld}(25871)
           │               ├─{mysqld}(25872)
           │               ├─{mysqld}(25873)
           │               ├─{mysqld}(25874)
           │               ├─{mysqld}(25875)
           │               ├─{mysqld}(25878)
           │               ├─{mysqld}(25879)
           │               ├─{mysqld}(25880)
           │               ├─{mysqld}(25881)
           │               ├─{mysqld}(25882)
           │               ├─{mysqld}(25883)
           │               ├─{mysqld}(25884)
           │               ├─{mysqld}(25885)
           │               ├─{mysqld}(25886)
           │               ├─{mysqld}(25887)
           │               ├─{mysqld}(25889)
           │               ├─{mysqld}(25890)
           │               ├─{mysqld}(25907)
           │               └─{mysqld}(25009)
           ├─networkd-dispat(1053)───{networkd-dispat}(1111)
           ├─ntpd(23752)───{ntpd}(23767)
           ├─polkitd(1068)─┬─{polkitd}(1074)
           │               └─{polkitd}(1077)
           ├─puppet(1054)───{puppet}(23959)
           ├─python3(1028)───python3(1094)─┬─{python3}(1163)
           │                               ├─{python3}(1164)
           │                               ├─{python3}(1166)
           │                               └─{python3}(1167)
           ├─rsyslogd(1031)─┬─{rsyslogd}(1043)
           │                ├─{rsyslogd}(1044)
           │                └─{rsyslogd}(1045)
           ├─sshd(1089)───sshd(23267)───sshd(23395)───bash(23396)───pstree(24081)
           ├─systemd(23276)───(sd-pam)(23277)
           ├─systemd-journal(451)
           ├─systemd-logind(1052)
           ├─systemd-network(886)
           ├─systemd-resolve(897)
           ├─systemd-udevd(469)
           └─unattended-upgr(1085)───{unattended-upgr}(1115)
```
- 2. type 
`systemctl list-unit-files --type=target`
- 3. make a file called `helloworld.sh` 
```md
#!/bin/bash
while #(sleep 30);
do
        echo "hello, systemd world"
done
```
- 4. make a file called `hello.service` hello.sh will need to be able to run `chmod`
```md
[Unit] 
Description=Hello world service
After-systemd-user-sessions.service
[Service]
SyslogIdentifier=HelloWorldService
Type=simple
ExecStart=/home/<your home dir>/hello.sh
```

5. copy hello.service into `/etc/systemd/system`
```md
cp hello.service /etc/systemd/system
```
`systemctl list-unit-files | grep hello.service`

`systemctl start hello.service`

`systemctl status hello.service`

`journalctl -u hello -e`

# useful commands



`systemctl start cron.service`

Starts the service'.
`systemctl status cron.service`


Opens the services unit file in the default editor. Note that this and the cat command above finds the effective unit file in the directories we listed above. We don't need to look around to find the file ourselves.
Note also that cron's unit file has an [Install] section, which means that this service can be enabled to start at boot.

`systemctl enable cron.service`

Identifies the service to be started automatically on boot.
`systemctl disable cron.service`

Removes the service from the list of those started at boot.
`journalctl -u nginx.service`

Show journal entries for the service. Note that we often want to add the -e option to see the newest entries.

