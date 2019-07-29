# AWS-System-Administration
# basic puppet 1 master 1 client setup with Ubuntu 16.04
1. Upload json file through cloudformation
# validate template
$ aws cloudformation validate-template --template-body file://puppet-instance-manual.json
# create stack
$ aws cloudformation create-stack --region us-east-2 --stack-name puppetStack --template-body file://puppet-instance-manual.json \
--parameters ParameterKey=AMI,ParameterValue=ami-ubuntu-t2-micro-free \
ParameterKey=KeyName,ParameterValue=KeyName
# getting stack output
$ aws cloudformation describe-stacks --stack-name puppetStack | jq '.Stacks[0].Outputs[]'

# ssh to puppetmaster server
$ sudo apt update
$ sudo apt install --yes puppetmaster
$ sudo systemctl status puppetmaster

# create file at puppet master: /etc/puppet/manifests/site.pp as below
node default {
  package { 'nginx':
  ensure => installed
  }
  service { 'nginx':
  ensure => running,
  require=> Package['nginx']
  }
  file { '/tmp/hello_world':
  ensure => present,
  content=> 'Hello, World!'
  }
}

# ssh to puppet client
$ sudo apt update
$ sudo apt install --yes puppet

# configure puppet client at /etc/puppet/puppet.conf 
# append line to [main] section with value below
server = internal DNS name of master //ip-x-x-x-x.us-east-2.compute.internal

# initiating client on puppet-client waiting puppet master to sisn cert
$ sudo puppet agent --waitforcert 120 --test

# ssh to puppet-master
$ list client cert
$ sudo puppet cet list

# at puppet-maseter, sign client cert
$ sudo puppet cert sign ip-x-x-x

# ssh to puppet-client - enabling puppet client
$ sudo puppet agent --enable
$ sudo puppet agent --test

# puppet-client testing
$ sudo systemctl nginx status
$ cat /tmp/hello_world
