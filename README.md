# Hosting a Website on AWS EC2 with NGINX and a Custom Domain

A walkthrough of deploying NGINX on an AWS EC2 instance (free tier) and
serving a page over my own domain with HTTPS.

## Architecture

Browser → Cloudflare (DNS) → Elastic IP → EC2 (Ubuntu + NGINX) → index.html

## What I used

- AWS EC2 (t3.micro, Ubuntu 24.04)
- Elastic IP
- Cloudflare (domain registration + DNS)
- NGINX
- Certbot / Let's Encrypt (HTTPS)

## Steps

1. **Launch EC2:** Ubuntu 24.04, t3.micro, security group allowing ports
   22 (my IP only), 80, and 443.

2. **Attach an Elastic IP** so the address doesn't change on restart.

3. **Install NGINX:**

	```
        	sudo apt update && sudo apt install nginx -y
	```
4. **Buy a domain** in Route 53 and verify the contact email.

5. **Create DNS records:** A records for the root domain and `www`,
   pointing at the Elastic IP.

6. **Configure NGINX** using [`nginx/example.com.conf`](nginx/example.com.conf)
   and copy the page from [`site/index.html`](site/index.html) to
   `/var/www/example.com/`.

7. **Enable HTTPS:**

	```
	sudo certbot --nginx -d example.com -d www.example.com

	```


## Troubleshooting

- Site times out → check security group ports 80/443
- Wrong page shows → check `server_name` and remove the default site
- Certbot fails → DNS must already point at the server

## What I learned

- How to launch and configure an EC2 instance on AWS
- How security groups work, including inbound rules (SSH, HTTP, HTTPS) and outbound traffic
- Why an Elastic IP matters: a normal public IP changes whenever an instance is stopped and started, so a fixed address keeps DNS working
- How to buy a domain through Cloudflare and create A records pointing it at my instance's IP
- How to set up TLS with Certbot so the site is served over HTTPS
