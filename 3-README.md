# load the 3-json file to create standalone puppet instance
aws cloudformation create-stack --stack-name puppetmasterless --template-body file://3-puppet-master-less.json \
--region us-east-2 --capabilities CAPABILITY_IAM \
--parameters ParameterKey=AMI,ParameterValue=ami-xxx \
ParameterKey=KeyName,ParameterValue=key-name

# ssh into new created instance
- check/etc/puppet/ folder
$ nginx -v
$ curl http://127.0.0.1
