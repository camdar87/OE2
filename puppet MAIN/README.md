# OE2

# install puppet/Enable the Puppet platform repository
- `using apt`
- update apt package  
```md
sudo apt-get update
```

- get puppet platform 
```md
wget https://apt.puppetlabs.com/puppet6-release-bionic.deb
sudo dpkg -i puppet6-release-bionic.deb
```

# Install pupper server

- `puppet server uses 2gb of ram by defualt`

- install puppet server
```md
sudo apt install puppetserver
```

- Start and enable puppetserver service
```md
sudo systemctl start puppetserver.service
sudo systemctl enable puppetserver.service
```

- check status 
```md
systemctl status puppetserver.service 
```
- it should look like 
- ![Image of ](https://github.com/camdar87/OE2/blob/main/puppet%20MAIN/img/serverstatus.png)

# Configure Puppet Master
- change ram to `512m` this has now been changed to 2gb
```md
sudo vim /etc/default/puppetserver
```
- ![Image of ](https://github.com/camdar87/OE2/blob/main/puppet%20MAIN/img/changeRam.png)

- server will need restarted 
```md
sudo systemctl restart  puppetserver
```

# puppet agent 

- install puppet agent ubuntu 18.04
```md
sudo apt install wget
curl -O https://apt.puppet.com/puppet6-release-bionic.deb
sudo apt install ./puppet6-release-bionic.deb
sudo apt update
sudo apt install  puppet-agent
```
- check  hosts
```md
sudo vim /etc/hosts
```
should look like-
- ![Image of ](https://github.com/camdar87/OE2/blob/main/puppet%20MAIN/img/hostnameAGENT.png)

- check conf file
```md
sudo vim /etc/puppetlabs/puppet/puppet.conf
```

should look like 
- ![Image of ](https://github.com/camdar87/OE2/blob/main/puppet%20MAIN/img/agent%20confFile.png)

- main conf 
```md
sudo vim /etc/puppetlabs/puppet/puppet.conf
```
- ![Image of ](https://github.com/camdar87/OE2/blob/main/puppet%20MAIN/img/main%20conf.png)

- now start service 
```md
sudo systemctl start puppet
sudo systemctl enable puppet
```


- on puppet master list  certificates
```md
sudo /opt/puppetlabs/bin/puppetserver ca list --all
```

- sign all 
```md
sudo /opt/puppetlabs/bin/puppetserver ca sign --all
```

- it should all be working. you can test with 

```md
sudo /opt/puppetlabs/bin/puppet agent --test
```

# Install Nagios

- On the main server


`/etc/puppetlabs/code/modules/nagios/manifests/target.pp`

```md
class nagios::target {

nagios_host { "backup-e.foo.org.nz":
target => "/etc/nagios3/conf.d/ppt_hosts.cfg",
alias => "backup",
check_period => "24x7",
max_check_attempts => 3,
check_command => "check-host-alive",
notification_interval => 30,
notification_period => "24x7",
notification_options => "d,u,r",
mode => "0644",
contacts => "slack",
}
nagios_host { "db-e.foo.org.nz":
target => "/etc/nagios3/conf.d/ppt_hosts.cfg",
alias => "db",
check_period => "24x7",
max_check_attempts => 3,
check_command => "check-host-alive",
notification_interval => 30,
notification_period => "24x7",
notification_options => "d,u,r",
mode => "0644",
contacts => "slack",
}

nagios_host { "app-e.foo.org.nz":
target => "/etc/nagios3/conf.d/ppt_hosts.cfg",
alias => "app",
check_period => "24x7",
max_check_attempts => 3,
check_command => "check-host-alive",
notification_interval => 30,
notification_period => "24x7",
notification_options => "d,u,r",
mode => "0644",
contacts => "slack",
}

nagios_hostgroup { "my-ssh-servers":
        target => "/etc/nagios3/conf.d/ppt_hostgroups.cfg",
        mode => "0444",
        alias => "My SSH servers",
        members => "db-e.foo.org.nz, backup-e.foo.org.nz, app-e.foo.org.nz",
}

nagios_hostgroup { "my-mariadb-servers":
        target => "/etc/nagios3/conf.d/ppt_hostgroups.cfg",
        mode => "0444",
        alias => "My MariaDB Servers",
        members => 'db-e.foo.org.nz, backup-e.foo.org.nz, app-e.foo.org.nz',
}

nagios_service {"ssh":
        service_description => "SSH Service",
        hostgroup_name => "my-ssh-servers",
        target => "/etc/nagios3/conf.d/ppt_services.cfg",
        mode => "0444",
        check_command => "check_ssh",
        max_check_attempts => 3,
        retry_check_interval => 1,
        normal_check_interval => 5,
        check_period => "24x7",
        notification_interval => 30,
        notification_period => "24x7",
        notification_options => "w,u,c",
        contact_groups => "admins",
}

nagios_service {"mariadb":
        service_description => "MariaDB Service",
        hostgroup_name => "my-mariadb-servers",
        target => "/etc/nagios3/conf.d/ppt_services.cfg",
        check_command => "check_mysql_cmdlinecred!puppetmaster!password",
        max_check_attempts => 3,
        retry_check_interval => 1,
        normal_check_interval => 5,
        check_period => "24x7",
        notification_interval => 30,
        notification_period => "24x7",
        notification_options => "w,u,c",
        contact_groups => "admins",
}

nagios_contact { "slack":
target => "/etc/nagios3/conf.d/ppt_contacts.cfg",
mode => "0444",
alias => "Slack",
service_notification_period => "24x7",
host_notification_period => "24x7",
service_notification_options => "w,u,c,r",
host_notification_options => "d,r",
service_notification_commands => "notify-service-by-slack",
host_notification_commands => "notify-host-by-slack",
email => "root@localhost",
}

nagios_contactgroup { "slackgroup":
target => "/etc/nagios3/conf.d/ppt_contactgroups.cfg",
mode => "0444",
alias => "Slack channel",
members => "slack",
contactgroup_name => "admin",
}

}

```

`/etc/puppetlabs/code/modules/nagios/manifests/install.pp`

```md
class nagios::install {
package { "nagios3" :
ensure => present,
}

package { "apache2-utils":
ensure => present,
}
}
```

`/etc/puppetlabs/code/modules/nagios/manifests/init.pp`

```md
class nagios {
include nagios::install, nagios::target}
```

`/etc/puppetlabs/code/environments/production/manifests/site.pp`

```md
node 'mgmt-e.foo.org.nz' {
  include sudo
  include ntp_service
  #include mariadb
  include nagios
}

node 'backup-e.foo.org.nz' {
  package { 'vim':ensure => present }
  include sudo
  include ntp_service
  include mariadb
  #include nagios::target
}

node 'db-e.foo.org.nz' {
  package { 'vim':ensure => present }
  include sudo
  include ntp_service
  include mariadb
  #include nagios::target
}

node 'app-e.foo.org.nz' {
  package { 'vim':ensure => present }
  include sudo
  include ntp_service
  include mariadb
  #include nagios::target
}
```


# set time on promt 

`PS1="\t [\u@\h \W]\\$ \[$(tput sgr0)\]"`

change in `bashrc` file on line 69
```md
PS1="\t [\u@\h \W]\\$ \[$(tput sgr0)\]"
```

# prod vs manifets
- we write modules in manifest and run them in environment
