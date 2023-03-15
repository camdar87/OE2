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
- change ram to `512m`
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
