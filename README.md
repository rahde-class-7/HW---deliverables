# HW---deliverables

Must be in github repo link Screenshot of running web server Copy of the start up script saved as a .txt file Readme instructions on how to configure the EC2 with teardown instructions
Must be in github repo link  
Screenshot of running web server  
Copy of the start up script saved as a .txt file  
Readme instructions on how to configure the EC2 with teardown instructions

----

### EC2 Instance Deployment

### Instructions  
1\. Log into aws console  
2\. Search ec2 in searchbar  
3\. Click on ec2  
4\. Click on launch instances  
5\. Name instance web server  
6\. Create key pair called web server keypair  
7\. Create an security group  
8\. Select allow http  
9\. Select advance details  
10\. Get the script the following git repo and paste it in https://github.com/MookieWAF/bmc4/blob/main/ec2scrpit  

~~~~
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd

# Get the IMDSv2 token
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

# Background the curl requests
curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/local-ipv4 &> /tmp/local_ipv4 &
curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/placement/availability-zone &> /tmp/az &
curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/ &> /tmp/macid &
wait

macid=$(cat /tmp/macid)
local_ipv4=$(cat /tmp/local_ipv4)
az=$(cat /tmp/az)
vpc=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/${macid}/vpc-id)

echo "
<!doctype html>
<html lang=\"en\" class=\"h-100\">
<head>
<title>Details for EC2 instance</title>
</head>
<body>
<div>
<h1>AWS Instance Details</h1>
<h1>Samurai Katana</h1>

<br>
# insert an image or GIF
<img src="https://www.w3schools.com/images/w3schools_green.jpg" alt="W3Schools.com">
<br>

<p><b>Instance Name:</b> $(hostname -f) </p>
<p><b>Instance Private Ip Address: </b> ${local_ipv4}</p>
<p><b>Availability Zone: </b> ${az}</p>
<p><b>Virtual Private Cloud (VPC):</b> ${vpc}</p>
</div>
</body>
</html>
" > /var/www/html/index.html

# Clean up the temp files
rm -f /tmp/local_ipv4 /tmp/az /tmp/macid
~~~~

11.Wait for the server to stop initializing  
12.Then paste dns into browser the url

Now we have an running web server
![alt text](https://github.com/rahde-class-7/HW---deliverables/blob/main/homework%201%20-%20Google%20Docs_page-0001.jpg?raw=true)

