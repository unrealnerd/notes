
- Under EC2 ➝ instances ➝ Launch Instance
- Select amazon Linux AMI
- Instance type : t2.small
- Select existing key pair `demoKey`

- edit network settings and update security group name and description `basicdemo-sg`
- Launch the instance
- go to the newly created instance and click on connect
- `ssh -i "demoKey.pem" ec2-user@ec2-43-205-116-142.ap-south-1.compute.amazonaws.com`
- Install git using  `sudo yum install git`
- generate a ssh key using `ssh-keygen -t rsa -C support-demo@bitsmonkey.com`
- Name of the key is support-demo
- Password for the ssh cert `****`
- move the public and private key to ~/.ssh folder
- output the public key from the ssh cat support-demo.pub
- now paste the above generated public key to github using support login.
- name as per the ec2 instance name ec2-basicdemo
- run the below commands
```
touch ~/.ssh/config
vi ~/.ssh/config
```
<paste the below content in the file>

```
Host github.com
  IdentityFile ~/.ssh/support-demo
```
- Use this command to test the ssh connection with  `github ssh -T git@github.com -i ~/.ssh/support-demo`
- now clone the repo on the ec2 ssh session git `clone git@github.com:org/demo.git`
- cd demo change to code folder and pull the latest
- follow this documentation to install node  [Install Node](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html)
- execute following commands to set up the demo app
```
    npm config set "@fortawesome:registry" https://npm.fontawesome.com/
    npm config set "//npm.fontawesome.com/:_authToken" XXXXXXXXXXXXXX
    npm i
    npm run build
    pm2 start npm --name "demo-app" -- run start
```
- go to EC2 ➝ Security groups ➝ basicdemo-sg
- edit the inbound rules and custom TCP for port 3000 and port 80  and allow all ipv4 addresses.
- go to EC2 ➝ Load balancers ➝ create a new network load balancer ➝ select default VPC vpc-176b9f7c  and basicdemo-sg security group
- Create a target group basicdemo-tg  for the instance basicdemo and port 3000
- login to godaddy and add a CNAME record with the loadbalancers DNS name `basicdemo-lb-XXXXXXXXXX.elb.ap-south-1.amazonaws.com.` Note: the dot at the end to be added manually
- for https certificate, go to certificate manager in AWS and request certificate for basicdemo.bitsmonkey.com
- now use the cname name  XXXXXXXXXXX.basicdemo and value as `XXXXXXXXXX.xxxxxxxxx.acm-validations.aws.`   NOTE only the subdoaim part is retained in the cname name.
- Once the validation is completed, goto the load balancer and add a new listener for port 443 and select the certificate you just created
