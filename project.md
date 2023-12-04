## Implementing Loadbalancers with Nginx

### Setting up a basic load balancer

### Step 1

I provisioned 2 EC2 instances on AWS console to serve as a web server 1 and 2

![apt update status](./images/1.PNG)

### Step 2

I opened port 8000 of the webserves where they will be running and to allow traffic from anywhere

![apt update status](./images/2.PNG)

### Step 3

I installed apache on the two webservers by run `sudo apt update -y &&  sudo apt install apache2 -y`
on each server.

webserver1

![apt update status](./images/3a.PNG)

![apt update status](./images/3b.PNG)

webserver2

![apt update status](./images/4a.PNG)

![apt update status](./images/4b.PNG)

After the installation, I run ` sudo systemctl status apache2` to verify that apache is running on both servers.

webserver1

![apt update status](./images/3c.PNG)

webserver2

![apt update status](./images/4c.PNG)

### Step 4

In the step I configured apache webservers to to serve content on port 8000 instead of its default which is port 80. Also, created a new index.html file and then override apache webserver's default html file with the new file.

A. Configuring Apache on the both webservers to serve content on port 8000

i. I opened this file `sudo vi /etc/apache2/ports.conf` to add a new listen directive for port 8000 and then saved the file.

webserver1

![apt update status](./images/5a.PNG)

![apt update status](./images/5b.PNG)

webserver2

![apt update status](./images/6a.PNG)

![apt update status](./images/6b.PNG)

ii. I opened the second config file too to edit virtual host from 8o to 8000 by running `sudo vi /etc/apache2/sites-available/000-default.conf`

webserver1

![apt update status](./images/7a.PNG)

![apt update status](./images/7b.PNG)

webserver2

![apt update status](./images/8a.PNG)

![apt update status](./images/8b.PNG)

iii. I restarted the apache to load the new configuration using `sudo systemctl restart apache2`

webserver1

![apt update status](./images/9.PNG)

webserver2

![apt update status](./images/10.PNG)

B. Creating the new html file

i. I created a new file using `sudo vi index.html` on both servers and pasted the code into the file and input my ip address in the space of public ip and saved the file.

        <!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: YOUR_PUBLIC_IP</p>
        </body>
        </html>


 

 webserver1

![apt update status](./images/11a.PNG)

![apt update status](./images/11b.PNG)

webserver2

![apt update status](./images/12a.PNG)  

![apt update status](./images/12b.PNG)

ii. I changed the ownership of the file on both servers by running `sudo chown www-data:www-data ./index.html` 

webserver1

![apt update status](./images/13.PNG)

webserver2

![apt update status](./images/14.PNG)

C. To override the default html file of Apahe webserver, i run ` sudo cp -f ./index.html /var/www/html/index.html` on both servers and then restarted Apache to load the new configuration using `sudo systemctl restart apache2`

webserver1

![apt update status](./images/15.PNG)

![apt update status](./images/17.PNG)

webserver2

![apt update status](./images/16.PNG)

![apt update status](./images/18.PNG)

Then, I texted the ip address on the broswer

webserver1

![apt update status](./images/19.PNG)

webserver2

![apt update status](./images/20.PNG)

### Step 5

i. I provisioned a new EC2 instance to serve as a loadbalancer. 

![apt update status](./images/21.PNG)

ii. Logged into the server on the terminal using ssh

![apt update status](./images/22.PNG)

iii. Updated the softwares and installed Nginx using ` sudo apt update -y && sudo apt install nginx -y` 

![apt update status](./images/23a.PNG)

![apt update status](./images/23b.PNG)

iv. Verified that Nginx is installed using `sudo systemctl status nginx`

![apt update status](./images/24.PNG)

v. Opened Nginx configuration file to configure Nginx to act as a loadbalancer `sudo vi /etc/nginx/conf.d/loadbalancer.conf`

Paste the code inside the file 
        
        upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server 127.0.0.1:8000; # public IP and port for webserser 1
            server 127.0.0.1:8000; # public IP and port for webserver 2

        }

        server {
            listen 80;
            server_name <your load balancer's public IP addres>; # provide your load balancers public IP address

            location / {
                proxy_pass http://backend_servers;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            }
        }
    
![apt update status](./images/25a.PNG)

![apt update status](./images/25b.PNG)

vi. Test that the configuration is ok `sudo nginx -t`

![apt update status](./images/26.PNG)

vii. Restart the Nginx to load the new configuration ` sudo systemctl restart nginx`

![apt update status](./images/27.PNG)

viii. I pasted the public Ip address of the load balancer on the browser

![apt update status](./images/29.PNG)

THANK YOU FOR REVIEWING!



