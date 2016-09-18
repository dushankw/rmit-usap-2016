# Puppet

### Installation Guide

0. sudo -s
1. yum -y update
2. yum -y install wget
3. cd /tmp
4. wget http://titan.csit.rmit.edu.au/~e09962/usap2015/puppet/puppet.tar.gz
5. tar -zxvf puppet.tar.gz
6. puppet-enterprise-2015.2.0-el-7-x86_64/puppet-enterprise-installer (follow the prompts)
7. Once the installer is finished, go to your EC2 Instances **external** hostname on port 3000
8. Follow the installer (select `Monolithic` and enter the external hostname as the `FQDN`)
9. Ignore any validation errors (our server is fine for testing)
10. Hit deploy now and wait until it is done
11. Log into the console once it's finished

### Our first module

```
class usaptest {
    user { 'becca':
        ensure => present,
        groups => ['rmit'],
    }

    group { 'rmit':
        ensure => present,
    }

    package { 'vim':
        ensure => present,
    }
}
```

Put this on the master in CHANGEME

Let's apply this via the PE console

Let's prove it worked

### Things to discuss

* How does puppet know what OS we are running the agent on
* How does puppet work out dependencies (how does it know to create the group before the user for example)
* How does puppet know how to create the user (this differs on platforms)
* How does puppet know how to install the package vim (explain what `facter` is)
* What is a no-op
* PE vs Puppet Open Source

### Next steps

Docs https://docs.puppet.com/pe/2015.3/
(make sure you refer to the 2015.3 docs at all times, there are breaking changes in newer versions)
