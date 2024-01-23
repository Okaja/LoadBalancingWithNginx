# Implementing Load Balancers With Nginx

## Introduction
Load Balancing helps to distribute traffic efficiently across multiple servers, optimize performance, ensure high availabiltiy for web application. This helps to keep everything running smoothly and ensures that websites and apps work quickly and don't get too slow.

The load balancer stands in front of the webservers, all traffic gets to it first, it then distibutes the traffic across the set of webservers.

For this project we would use Nginx, which is a versatile software. it can act like a webserver, reverse proxy, a load balancer etc.Nginx would be configured to work as a load balancer.



## STEPS

1. Provisioning  EC2 instances

- The first two EC2 instances(MaxWebServer1, MaxWebServer2 ) would run on ubuntu 22.04 and apache webserver would be installed on them. 
-While the third EC2 instance (LoadBalancerNgnix) would also run on ubuntu 22.04 and nginx software would be installed on it  and this would act as the load balancer
- Port 8000  would be opened on the  apache webserver  by adding a rule to the security group, to allow traffic from anywhere

- Port 80  would be opened on the nginx  webserver, by adding a rule to the security group to allow traffic from anywhere

- The default pages of the apache webservers would updated to display their public IP addresses


![EC2](./img/Webservers.png)


2. Install Apache Webserver:
apache software would be installed on MaxWebServer1 and MaxWebServer2 instances.

- Syntax: sudo apt update -y &&  sudo apt install apache2 -y

![InstallApacheOnWebserver](./img/1.installapache.png)

- Verify that apache is running: sudo systemctl status apache2

![VerifyThatApacheisRunning](./img/2.checkapacherunning.png)

3. Configure Apache to server a page showing its public IP

First, The Apache webserver would be configured to serve content on port 8000 instead of its default port 80

- We would edit *sudo vi /etc/apache2/ports.conf* using a vi editor, by adding a new Listen directive for port 8000
![ConfigureApache](./img/3.listento8000.png)

-Also edit *sudo vi /etc/apache2/sites-available/000-default.conf* and change port 80 on the virtualhost to 8000



![EditVirtualHost](./img/5.editapachevirtualhost.png)

- Restart apache to reload the new configuration- *sudo systemctl restart apache2*

4. Create a new html for each of the webservers

A new html file is created to display the public IP of each of the webservers

![IndexToDisplayIP](./img/6.indexfile.png)

- The file ownership of the index.html file is changed with the syntax: *sudo chown www-data:www-data ./index.html*

- Override the default html file of Apache Webserver: The default html file is replaced with our new html file with syntax: *sudo cp -f ./index.html /var/www/html/index.html*

- To load the use configuration we use the command  *sudo systemctl restart apache2*

- Below is the webpage(index.html) from one of the apache webserver

![Webserver1](./img/7.viewwebpage.png)

5. Configuring Nginx as a Load Balancer

At the beginning of this project we provisioned a new EC2 instance for the load balancer name *LoadBalancerNgnix* and also opened port 80 to accept traffic from anywhere.

- Next we install Nginx into the instance using the syntax: *sudo apt update -y && sudo apt install nginx -y* 

![InstallNginx](./img/8.installngix.png)

- Verify Nginx is installed using syntax: *sudo systemctl status nginx*

![CheckNginx](./img/9.checknginxstatus.png)

- Open Nginx configuration file and change the server IP address to the public IP of the webservers that was created using syntax *sudo vi /etc/nginx/conf.d/loadbalancer.conf*

![ConfNginx](./img/10.loadbalanceconf.png)

From the image, the upstream backend_servers defines a group of backend servers. The server lines the upstream block list the addresses and port of the backend servers. The proxy_pass inside the location block sets up the load balancing, passing the request to the backend servers. The proxy_set_header lines pass necessary headers to the backend servers to correctly handle the requests.

- Test the configuration and restart nginx with syntax- *sudo nginx -t* and to restart  nginx-  *sudo systemctl restart nginx*

![TestNginx](./img/11.testnginxconf.png).

- To ensure our new configuration works we need to disable default host and then reload Nginx to apply changes using the command *Syntax: sudo unlink /etc/nginx/sites-enabled/default* and reload using -Syntax:$ sudo systemctl reload nginx

![UnlinkNginx](./img/12.unlinkdefaulthost.png).


- The  public IP address of the Nginx is pasted on the browser and same webpages displayed by each of the webservers (MaxWebserver1) and MaxWebServer2 is served as shown in the image below:

![DisplayWebPages](./img/13.webserver1.png)

![DisplayWebPages](./img/14.webserver2.png)





 

















  
