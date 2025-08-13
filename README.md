# Full-Manageble-Website-Using-Docker.Bind9.EC2

Full Walkthrough Video : 

https://drive.google.com/file/d/1vz09l0Uigcymf2pbmH2_-rJhuemOGaX8/view?usp=sharing

🌐 DevSphere Academy — DNS & Docker Deployment Guide
This guide documents how we deployed the DevSphere Academy web app using Docker on an EC2 instance, and configured a fully self-managed authoritative DNS server using BIND9. The domain holalho.online is registered with Namecheap, and DNS is now managed independently via our own nameservers.

📦 1. Install BIND9 on Ubuntu EC2
bash
sudo apt update
sudo apt install -y bind9 bind9utils bind9-doc dnsutils
⚙️ 2. Configure BIND9 for Authoritative DNS
named.conf.local
conf
zone "holalho.online" {
    type master;
    file "/etc/bind/db.holalho.online";
};
db.holalho.online
dns
$TTL    86400
@       IN      SOA     ns1.holalho.online. admin.holalho.online. (
                        2025081101 ; Serial
                        3600       ; Refresh
                        1800       ; Retry
                        604800     ; Expire
                        86400 )    ; Minimum TTL

        IN      NS      ns1.holalho.online.
        IN      NS      ns2.holalho.online.

ns1     IN      A       54.197.1.29
ns2     IN      A       54.197.1.29
@       IN      A       54.197.1.29
www     IN      CNAME   holalho.online.
🐳 3. Deploying the Web App with Docker on EC2
bash
docker run -d \
  --name apache-server \
  -p 80:80 \
  -v /path/to/app:/usr/local/apache2/htdocs/ \
  httpd:latest
EC2 Public IP: 54.197.1.29

App is accessible via:

http://54.197.1.29

http://ec2-54-197-1-29.compute-1.amazonaws.com

www.holalho.online

🔁 4. DNS Setup with Namecheap
✅ Steps:
Registered glue records:

ns1.holalho.online → 54.197.1.29
ns2.holalho.online → 54.197.1.29
Set custom nameservers in Namecheap:

ns1.holalho.online
ns2.holalho.online
🧪 5. Testing DNS
Local Test
bash
dig @54.197.1.29 holalho.online
Global Propagation
Use DNSChecker to monitor.

🛠️ 6. Common Issues & Fixes
Issue	Cause	Fix
Page not displaying	DNS not propagated	Wait 24–48 hours
Wrong IP	Old nameservers	Confirm switch in Namecheap
Apache not serving	Wrong doc root or port	Check config and firewall
🔍 7. BIND9 vs Namecheap DNS
Feature	Namecheap	BIND9
DNS Management	Web UI	Zone files
Hosting	Namecheap infra	Your EC2
Control	Limited	Full
Propagation	Fast	Manual, slower
✅ Final Notes
You’ve successfully replaced Namecheap’s DNS with your own BIND9 server.

Your EC2 instance is now authoritative for holalho.online.

DNS propagation may take up to 48 hours.

App is deployed via Docker and served by Apache inside the container.
