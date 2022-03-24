
# How To Set Up Nginx as a Load Balancer on aws EC2-instace

 



## Description

Nginx is a web server like apache. Not only as a web server, but it can also act as a load balancer, reverse proxy, etc. Nginx, a popular web server software, can be configured as a simple yet powerful load balancer to improve your servers resource availability and efficiency.

This practical guide shows how to use Nginx as an load balancer to distribute incoming client requests between two servers each having an instance of the same application.


![](https://raw.githubusercontent.com/radin-lawrence/myimagefile/main/setup-nginx-load-balancer.png?token=GHSAT0AAAAAABSJRHEX5HJBID2JXI4735R6YR4FHCQ)


## Pre-requisites

**Instance Setup:** Launch an instance using the management console. While launching the instance , configure the security group to allow traffic from HTTP 80 port & HTTPS 443.
## Nginx Setup on RHEL/Centos/Ubuntu EC2-Instance
**Install Nginx on RHEL & Centos**

 In RHEL/Centos you cannot download and install Nginx directly. You have to setup the epel ( extra packages for enterprise Linux) repo to install Nginx. 


Install EPEL package repository.

```bash
  sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

Execute the following.

```bash
  sudo yum update -y
```

 Install Nginx

```bash
  sudo yum install nginx -y
```


**Install Nginx on Ubuntu**


Execute the following steps to install Nginx on Ubuntu servers.

```bash
   sudo apt update
   sudo apt install nginx -y

```

## Start and Enable Nginx

Following steps are common for RHEL and Ubuntu systems.

Check the version to make sure Nginx is installed.
```bash
sudo nginx -v
```
 Start and enable Nginx.

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```
Check the status of Nginx to make sure it is running as expected.
```bash
sudo systemctl status nginx
```
Visit the nginx page using the Server IP. You should be seeing


```bash
http://[server-ip]
```


## Load balancing using Nginx

You can use Nginx as a load balancer to balance the load between server fleets. Nginx proxies the incoming requests and sends it to the backend servers. To configure Nginx as a load balancer, you have to add two blocks of code to the nginx configuration file.

Create a server block file called /etc/nginx/conf.d/loadbalancer.conf (give a name of your choice).

```bash
sudo vi /etc/nginx/conf.d/loadbalancer.conf
```
Add the upstream group under the HTTP section. The upstream group is the group of servers which comes under the load balancer. You can give any user defined name for the group. Here am going to give the name as “myapp”.

(This configuration defaults to round-robin as no load balancing method is defined, [Click here](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/) to choose a different Load-Balancing Method)
```bash
upstream myapp {
    server $backend_server_ip:80;
    server $backend_server_ip:80;
}
server {
    listen 80;
    server_name example-one.com www.example-one.com;
    location / {
        proxy_pass http://myapp;
    }
}
```

Validate the server block configuration.

```bash
sudo nginx -t
```
If should get the following success message.
```bash
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Restart the Nginx server.

```
sudo systemctl restart nginx
```

**Note**: Replace the domain.com with your domain name.
Now, if you access your nginx server using the DNS(http://example-one.com), the request will be routed to the backend server fleet present in the upstream block.

## Enabling Let's encrypt SSL on Nginx-loadbalancer using Certbot

Certbot can automatically configure NGINX for SSL/TLS. It looks for and modifies the server block in your NGINX configuration that contains a server_name directive with the domain name you’re requesting a certificate for. In our example, the domain is www.example-one.com.

 Install Certbot the utilities.
 
 ```
sudo yum install -y certbot 
sudo yum install -y python-certbot-nginx
```

Run the following command to generate certificates with the NGINX plug‑in. (replace domain.com with your domain name)

```
 sudo certbot --nginx -d example-one.com -d www.example-one.com

```


Respond to prompts from certbot to configure your HTTPS settings, which involves entering your email address and agreeing to the Let’s Encrypt terms of service.

When certificate generation completes, NGINX reloads with the new settings. certbot generates a message indicating that certificate generation was successful and specifying the location of the certificate on your server.

## Automatically Renew Let’s Encrypt Certificates
Let’s Encrypt certificates expire after 90 days. We encourage you to renew your certificates automatically. Here we add a cron job to an existing crontab file to do this.

Open the crontab file.
```
crontab -e
```

Add the certbot command to run daily. In this example, we run the command every day at noon. The command checks to see if the certificate on the server will expire within the next 30 days, and renews it if so. The --quiet directive tells certbot not to generate output.

```
0 12 * * * /usr/bin/certbot renew --quiet
```

Save and close the file. All installed certificates will be automatically renewed and reloaded.





## Conclusion

In this article I have explained how to set up Nginx as a Load Balancer on aws EC2-instace. Please contact me when you encounter any difficulty error while installing nginx loadbalancer on amzon-linux ec2-instance. Thank you!!!

### ⚙️ Connect with Me

<p align="center">
<a href="https://www.linkedin.com/in/radin-lawrence-8b3270102/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>
<a href="mailto:radin.lawrence@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>

