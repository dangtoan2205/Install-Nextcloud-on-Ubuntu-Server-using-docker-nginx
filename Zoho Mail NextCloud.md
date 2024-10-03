Configuring Zoho Mail as your email server in Nextcloud involves a few key steps. Here’s how you can do it:
---

# Step 1: Set Up Zoho Mail
1/ Enable IMAP Zoho App:</br>
Go to Settings > Mail accounts > IMAP
Select: IMAP Access, Auto-Expunge Emails, Include Archived emails </br>
![image](https://github.com/user-attachments/assets/8a12394e-9306-47f3-aa49-b048c8ddf426)  </br>
![image](https://github.com/user-attachments/assets/d376da6d-a707-4713-90e5-71ac74ce27ef)

2/ Setting App Password  
Log in to your Zoho Mail account:   </br>
https://accounts.zoho.com/signin?servicename=AaaServer&serviceurl=https%3A%2F%2Faccounts.zoho.com%2Fhome  </br>

Go to Security > App Passwords > + Generate New Password  </br>
![image](https://github.com/user-attachments/assets/29638005-9284-4cc8-8829-537808c61ad7)  </br>
Copy Password   </br>
![image](https://github.com/user-attachments/assets/4f6e6097-ba16-4f9b-97e7-2c49d691f607)

# Step 2: Configure Nextcloud Mail App
Install the Mail App:  </br>

Log in to your Nextcloud instance. </br>
Go to the Apps section and find the Mail app. Install it if you haven’t done so. </br>
Access Mail Settings: </br>

After installing, navigate to the Mail app. </br>
Click on the Settings icon (usually a gear or wrench icon). </br>
Add Your Zoho Account: </br>

Select Add Account or similar option. </br>
Fill in the required information: </br>
Email Address: Your full Zoho email address (e.g., you@yourdomain.com). </br>
Username: Your full Zoho email address again. </br>
Password: Your Zoho email password. </br>
Configure Incoming Server: </br>
```
Server Type: IMAP
Server Address: imap.zoho.com
Port: 993
Security: SSL/TLS
Authentication: Normal password
Configure Outgoing Server:

Server Address: smtp.zoho.com
Port: 465 (SSL) or 587 (TLS)
Security: SSL/TLS
Authentication: Normal password
Save Settings: After entering all the details, save your settings.
```

```
Connect your mail account > Manual
Name: Dang Toan
Mail address: toan.dang@seta-international.vn

IMAP Settings
IMAP Host: imappro.zoho.com
IMAP Security *
SSL/TLS
IMAP Port: 993
IMAP User: toan.dang@seta-international.vn
IMAP Password: Pawword copy in Website Zoho account (App Pawword > Nextcloud)

SMTP Settings
SMTP Host: smtppro.zoho.com
IMAP Security *
SMTP Port: 465
SMTP User: toan.dang@seta-international.vn
SMTP Password: Pawword copy in Website Zoho account (App Pawword > Nextcloud)

Connect
```

