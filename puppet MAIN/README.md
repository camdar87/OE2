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

# install pupper server

- `puppet server uses 2gb of ram by defualt`

- 