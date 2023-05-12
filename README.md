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

[Reverse Proxy Diagram]()

### What is Nginx's default configuration?

The default nginx configuration file inside `/etc/nginx/sites-available/default`.

The default configuration of Nginx is a server block listening on port 80, which is the default HTTP port. This server block usually serves files located in the /var/www/html directory. The server block has a location block that handles requests for static files, such as images, CSS, and JavaScript files.

Additionally, the default configuration includes comments that explain how to configure Nginx to handle PHP and other dynamic content, as well as how to configure SSL and other advanced settings. The configuration file for the default server block is usually located at /etc/nginx/sites-enabled/default.

### Setting up an Nginx reverse proxy

1. Install Nginx.
2. Configure Nginx. The default Nginx configuration file is located at /etc/nginx/nginx.conf. You will need to modify it to define your reverse proxy configuration.

```
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```

This block specifies that Nginx should listen on port 80 for requests to example.com, and pass those requests to the server running on localhost:3000. The proxy_set_header directives are used to pass the original Host, X-Real-IP, and X-Forwarded-For headers from the client to the server.

3. Test the configuration: Once you have updated the configuration file, test it to make sure there are no syntax errors. `sudo nginx -t` If there are no errors, reload the Nginx service: `sudo systemctl reload nginx`
4. Verify the setup: Access the URL you specified in the `server_name` directive in your web browser, and verify that the expected content is displayed.