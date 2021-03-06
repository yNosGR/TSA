# Infrastructure Technical Skills Assessment 
This is a quick hack bumped out in 4ish hours - would be better with loops and better array management.   
>   "If I had more time, I'd write a shorter letter"   
>     --Someone cool

## What it does:
Currently this code will:
- Deploy a couple subnets to house a database, a couple subnets to house the load balancer, and a couple subnets to house a variable number of nodes managed by an autoscaler. 
- The nodes will display some plain text showing their hostname and the uptime of the database they are connecting to.
- output the URL you can use to hit the ELB, the DB name in case you need to update this script, and the bucket used for logs and file storage. 


## What to do:
  Install pulumi - https://www.pulumi.com/docs/get-started/aws/begin/   
  setup your account at pulumi, it's free.
  ```
  mkdir CraigsTSA
  cd CraigsTSA
  pulumi new https://github.com/yNosGR/TSA.git
    #give it a name if you want, otherwise hit enter a few times
  pulumi up -y
  ```
  due to a S3 policy bug I couldnt work out in a reasonable amount of time, you need to run this twice. It fails with the ELB not being able to write logs to the bucket. Running it a second time fixes it.
  ```
  pulumi up -y
  ```
  Once the ASG and LB are happy check the elb address listed as an output
  ```
  watch curl -sm 1 $(pulumi stack output TSAelb)
  ```
  Now destroy it all
  ```
  pulumi destroy
  pulimi stack rm
  ```
## What it looks like when you run BuildMe.sh
  [![asciicast](https://asciinema.org/a/458306.svg)](https://asciinema.org/a/458306)
  
## What I'd do differently on something bigger than a demo.
- Create a dashboard to see the cloudwatch stats.
- Create SNS alarms that trigger when things get out of range.
- Have a domain in route53 to make a records and certs. This would fix hardcoding the DB in user_data.  
- Put DB passwords in a secret keystore of some sort..  
- Setup user_data to deploy/register puppet/ansible/salt/whatever and deploy the app via SSM.    
- Create another S3 bucket and use that to back cloudfront for static content.    
- Use an ALB rather than a classic elb.   
- ~~Use autoscaling groups to manage the number of instances behind the lb.~~
- Implement a better bucket policy apply method. 
  

