<h3>Linux Task: Build a Web-Based Network Scanner Dashboard using Nmap + Apache</h3>

---
<h4>Objective: Create a basic network scanner accessible through a web browser.</h4> 

<b>The scanner will:
1. Use cron to schedule Nmap scans every 10 minutes
2. Output results to a file in the Apache web root
3. Serve the file using a simple PHP script
4. Use secure Linux permissions and groups</b>
---
<h4>Requirements:</h4>
1. Ubuntu/Debian-based Linux
<br>2. Basic understanding of shell scripting
<br>3. Non-root user with sudo privileges

---
Step-by-Step Guide:

<br>1. Install Required Packages
```
sudo apt update
sudo apt install apache2 php nmap
```

<br>2. Set Up Web Directory with Proper Permissions
Instead of using chmod 777, follow this secure setup:
```
sudo groupadd webedit
sudo usermod -aG webedit $USER
sudo chown -R root:webedit /var/www/html
sudo chmod -R 775 /var/www/html
```
Log out and log back in to apply the group change.

<br>3. Create the Nmap Cron Script
File: ```/usr/local/bin/network_scan.sh```
```
#!/bin/bash
nmap -sn 192.168.1.0/24 -oN /var/www/html/scan_output.txt
```

Make it executable:
```
sudo chmod +x /usr/local/bin/network_scan.sh
```

<br>4. Set Up Cron Job
```
crontab -e
```
Add:
```
*/10 * * * * /usr/local/bin/network_scan.sh
```

<br>5. Create a Simple PHP Web Page
File: ```/var/www/html/index.php```
```
<?php
echo "<h2>Network Scan Report</h2>";
echo "<p><strong>Last Updated:</strong> " . date("Y-m-d H:i:s") . "</p>";
echo "<pre>";
include("scan_output.txt");
echo "</pre>";
?>
```

<br>6. Test the Setup
Restart Apache:
```
sudo systemctl restart apache2
```

In your browser, navigate to:
```http://<your-server-ip>/```

You should see a formatted network scan that refreshes every 10 minutes.

---
<h4>Security & Best Practices</h4>
<br>1. Avoid chmod 777; use groups and proper permissions (775, 664, etc.)
<br>2. Place scripts in /usr/local/bin rather than mixing them in web directories
<br>3. Limit Apache to internal access if necessary (via firewall or Allow/Deny rules)

---
<h4>Optional Challenges</h4>
<br>1. Change scan target to a different subnet range
<br>2. Add logging to a separate file: /var/log/nmap_scan.log
<br>3. Filter only active hosts and their MAC addresses into the output
<br>4. Use HTML/CSS to improve UI presentation (optional)

