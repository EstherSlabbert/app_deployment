# App deployment with Vagrant and Nginx

## Contents of repository

This repository contains 3 markdown files detailing the deployment of an app to a webserver using Vagrant and Nginx.

[app_deployment.md](https://github.com/EstherSlabbert/tech230_app_deployment/blob/main/app_deployment.md) outlines the basics of deploying an app to a webserver manually.

[automation_of_deployment.md](https://github.com/EstherSlabbert/tech230_app_deployment/blob/main/automation_of_deployment.md) outlines how to automate the steps in the app_deployment.md.

[multimachine_app_with_database_deployment.md](https://github.com/EstherSlabbert/tech230_app_deployment/blob/main/multimachine_app_with_database_deployment.md) outlines how to deploy 2 VMs with a single Vagrantfile and how to connect the DB machine to the APP machine using an environment variable.

It also contains a Vagrantfile and a provision.sh file to showcase what these files should look like for the app deployment.

## Nginx reverse proxy - information

[Beginner's guide to Nginx](https://nginx.org/en/docs/beginners_guide.html)

### What are ports?

In computer networking, a port is a communication endpoint used in Internet Protocol (IP) networking to identify a specific process to which a message or packet is to be forwarded for further processing.

Ports are identified by a number ranging from 0 to 65535, with some of the well-known ports being reserved for specific purposes, such as port 80 for HTTP traffic, port 443 for HTTPS traffic, and port 22 for SSH traffic.

Ports are essential for enabling communication between different applications and services running on a networked system, allowing multiple applications to send and receive data concurrently without interfering with each other.

### What is a reverse proxy? How is it different to a proxy?

A reverse proxy is a server that sits between client devices and one or more backend servers, forwarding client requests to the appropriate server and returning the server's responses to the clients. The primary function of a reverse proxy is to distribute incoming traffic among multiple servers, helping to improve performance, reliability, and scalability of applications.

The key difference between a reverse proxy and a regular proxy is that while a regular proxy acts on behalf of clients by forwarding requests to servers, a reverse proxy acts on behalf of servers by forwarding requests from clients to the appropriate server. In other words, a regular proxy hides the identity of the client while a reverse proxy hides the identity of the server.

Another key difference is that reverse proxies are typically deployed in front of web servers to manage and optimize web traffic, while regular proxies are more commonly used to access content on the internet anonymously or to bypass content restrictions.

Overall, reverse proxies are an essential component of modern web applications, helping to improve performance, reliability, and security by managing traffic and optimizing server resources.

[Proxy vs Reverse Proxy Diagram](https://miro.medium.com/v2/resize:fit:1200/1*WUQ1wM4V1GCAPvyigOASTg.png)

### What is Nginx's default configuration?

The default nginx configuration file inside `/etc/nginx/sites-available/default`.

The default configuration of Nginx is a server block listening on port 80, which is the default HTTP port. This server block usually serves files located in the /var/www/html directory. The server block has a location block that handles requests for static files, such as images, CSS, and JavaScript files.

Additionally, the default configuration includes comments that explain how to configure Nginx to handle PHP and other dynamic content, as well as how to configure SSL and other advanced settings. The configuration file for the default server block is usually located at /etc/nginx/sites-enabled/default.

### Setting up an Nginx reverse proxy

1. Install Nginx. Using the following commands:`sudo apt-get update` and then `sudo apt-get install nginx`.
2. Configure Nginx. The default Nginx configuration file is located at `/etc/nginx/nginx.conf` using `sudo nano /etc/nginx/sites-available/default`. You will need to modify it to define your reverse proxy configuration.

```
server {
    listen 80; # The server listens on port 80, which is the default HTTP port.
    server_name 129.168.10.100; # The server name is set to 129.168.10.100.

    location /posts { # This block defines the location of the server block.
        proxy_pass http://192.168.10.100:3000; # The requests are forwarded to the specified IP and port.
        proxy_set_header Host $host; # Sets the value of the "Host" header field to the value of the $host variable.
        proxy_set_header X-Real-IP $remote_addr; # Sets the value of the "X-Real-IP" header field to the IP address of the client.
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  # Sets the value of the "X-Forwarded-For" header field to the client IP and any forwarded IP addresses.
        proxy_set_header X-Forwarded-Proto $scheme; # This header is used to inform the backend server of the protocol (HTTP or HTTPS) used by the client to access the Nginx server.
    }

    error_page 404 /404.html; # The server returns a custom 404 error page for requests that result in a 404 status code.
    location = /404.html { # This block defines the location of the 404 error page.
        internal; # This directive tells Nginx to handle the request internally and not return it to the client.
        root /usr/share/nginx/html; # Sets the document root directory where the 404.html file is located.
    }
}
```
OR
```
location / {
        proxy_pass http://localhost:3000; # proxies the request to http://localhost:3000, which is the address of the backend server
        proxy_http_version 1.1; # sets the HTTP version used for proxy requests to 1.1.
        proxy_set_header Upgrade $http_upgrade; # negotiates a protocol upgrade to WebSocket or another protocol.
        proxy_set_header Connection 'upgrade'; # indicates that the connection should be upgraded to a different protocol.
        proxy_set_header Host $host; # specifies the hostname of the proxied server.
        proxy_cache_bypass $http_upgrade; # ensures that WebSocket connections are not cached.
    }
```
This block specifies that Nginx should listen on port 80 for requests to example.com, and pass those requests to the server running on localhost:3000.
3. Test the configuration: Once you have updated the configuration file, test it to make sure there are no syntax errors using: `sudo nginx -t`. If there are no errors, reload the Nginx service with: `sudo systemctl reload nginx` or `sudo systemctl restart nginx` to restart it.
4. Verify the setup: Access the URL you specified in the `server_name` directive in your web browser, and verify that the expected content is displayed.

[Set up your own Reverse Proxy](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-16-04)

Additional notes on Environment Variables [microsoft overview](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7.3), [article on how they are used in the industry](https://medium.com/chingu/an-introduction-to-environment-variables-and-how-to-use-them-f602f66d15fa).