# Case Study 01: Hosting Multiple Homelab Services with Traefik, DNS, and HTTPS

## Overview

This case study explains how I use my Raspberry Pi homelab to host multiple self-hosted services using Docker containers, Traefik, DNS records, and HTTPS.

Instead of accessing every service by typing an IP address and port number, I use subdomains such as:

vaultwarden.anthonylearchive.com
homepage.anthonylearchive.com
dozzle.anthonylearchive.com
uptime.anthonylearchive.com
grafana.anthonylearchive.com
portainer.anthonylearchive.com

This makes the homelab easier to use, easier to document, and closer to how real business environments host internal or external web applications.

## Goal

The goal of this setup was to make multiple containerized services available through clean domain names while using HTTPS for encrypted access.

Before using Traefik, a service might be accessed like this:

192.168.50.199:3001

After using Traefik and DNS, the same type of service can be accessed like this:

uptime.anthonylearchive.com

This is easier to remember and looks more professional.

Technologies Used
Raspberry Pi
Ubuntu Server
Docker
Docker Compose
Traefik reverse proxy
DNS records
HTTPS / TLS certificates
Self-hosted web services
Problem

Running multiple services on one server can become confusing if each service uses a different port.

For example:

Service A: 192.168.50.199:3000
Service B: 192.168.50.199:3001
Service C: 192.168.50.199:9000

This works, but it is not clean or easy to manage.

It also creates a few problems:

Users have to remember IP addresses and port numbers.
Services do not look professional.
HTTPS is harder to manage manually for every service.
Exposing multiple ports can become messy.
Documentation becomes harder to understand.
Solution

To solve this, I used Traefik as a reverse proxy.

Traefik receives web traffic first, then sends that traffic to the correct Docker container based on the subdomain being used.

For example:

vaultwarden.anthonylearchive.com

Traefik sees that request and forwards it to the Vaultwarden container.

Another example:

uptime.anthonylearchive.com

Traefik sees that request and forwards it to the Uptime Kuma container.

This allows multiple services to run on the same Raspberry Pi while still having their own clean web addresses.

Simple Traffic Flow
User visits subdomain
        |
        v
DNS points the subdomain to my public IP
        |
        v
Router forwards web traffic to the Raspberry Pi
        |
        v
Traefik receives the request
        |
        v
Traefik checks the hostname
        |
        v
Traefik sends traffic to the correct Docker container
Example

When I visit:

vaultwarden.anthonylearchive.com

The process works like this:

1. DNS sends the request to my home network.
2. My router forwards web traffic to the Raspberry Pi.
3. Traefik receives the request.
4. Traefik checks the hostname.
5. Traefik forwaSrds the request to the Vaultwarden container.
6. Vaultwarden loads in the browser using HTTPS.
Why Traefik Is Useful

Traefik is useful because it automatically works well with Docker labels.

Instead of manually creating a separate configuration file for every service, I can add labels inside each service’s docker-compose.yml file.

Example labels:

labels:
  - "traefik.enable=true"
  - "traefik.http.routers.vaultwarden.rule=Host(`vaultwarden.anthonylearchive.com`)"
  - "traefik.http.routers.vaultwarden.entrypoints=websecure"
  - "traefik.http.routers.vaultwarden.tls=true"
  - "traefik.http.routers.vaultwarden.tls.certresolver=myresolver"
  - "traefik.http.services.vaultwarden.loadbalancer.server.port=80"

These labels tell Traefik:

Enable Traefik for this container.
Use this subdomain.
Use HTTPS.
Use the certificate resolver.
Forward traffic to the correct internal container port.
DNS Role

DNS is what connects the domain name to the correct public IP address.

Without DNS, the browser would not know where to send traffic for:

vaultwarden.anthonylearchive.com

In this setup, each subdomain points toward my home network. Once the request reaches my network, Traefik decides which container should receive the traffic.

HTTPS Role

HTTPS is important because it encrypts traffic between the browser and the service.

Instead of using:

http://vaultwarden.anthonylearchive.com

The service uses:

https://vaultwarden.anthonylearchive.com

This is especially important for services that handle sensitive data, such as Vaultwarden.

Traefik helps manage HTTPS certificates so each service can use secure web access.

Services Using This Setup

Examples of services that can use this setup include:

Service	Purpose
Homepage	Dashboard for homelab services
Vaultwarden	Self-hosted password manager
Uptime Kuma	Uptime and service monitoring
Grafana	Monitoring dashboards
Dozzle	Docker container log viewer
Portainer	Docker container management
Nextcloud	Self-hosted file storage
What I Learned

While working on this setup, I learned how DNS, reverse proxies, Docker containers, and HTTPS work together.

The most important thing I learned is that Traefik acts like a traffic director. It does not replace Docker or DNS. Instead, it works with them.

DNS gets the user to my network.

Docker runs the services.

Traefik routes the user to the correct service.

HTTPS secures the connection.

Troubleshooting Notes

Some issues I ran into included:

Subdomains showing 404 errors when Traefik labels were incorrect.
Services not loading correctly when the wrong internal container port was used.
Certificate resolver names needing to match the Traefik configuration.
Containers needing to be on the same Docker network as Traefik.
Some services requiring extra environment variables to work correctly behind a reverse proxy.

These issues helped me better understand how Traefik reads Docker labels and routes traffic.

Real-World IT Connection

This setup is similar to how businesses host multiple internal or external web applications.

In a business environment, a reverse proxy or load balancer may be used to route traffic to different applications, apply HTTPS, and keep the environment organized.

This homelab project helped me practice skills related to:

Web hosting
DNS
Linux servers
Docker containers
Reverse proxies
HTTPS certificates
Troubleshooting web applications
Infrastructure documentation
Summary

This case study shows how I used Traefik, DNS, Docker, and HTTPS to host multiple services from my Raspberry Pi homelab.

The setup makes my environment cleaner because each service has its own subdomain instead of requiring IP addresses and port numbers.

This project helped me better understand how real infrastructure routes web traffic and how different infrastructure components work together.