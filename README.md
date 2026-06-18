<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

<h1 align="center">osTicket — Prerequisites and Installation</h1>

This project documents the end-to-end installation and configuration of <b>osTicket</b>, an open-source help desk ticketing system, hosted on a Microsoft Azure Virtual Machine running <b>Windows 11 Pro</b>. It covers all prerequisite software installation (IIS, PHP, MySQL), the osTicket setup wizard, and post-install configuration — simulating a real-world IT help desk deployment.

<h2>🖥️ Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines / Compute)
- Remote Desktop Protocol (RDP)
- Internet Information Services (IIS)
- PHP 7.3.8
- MySQL 5.5.62
- HeidiSQL
- osTicket v1.15.8

<h2>🪟 Operating Systems Used</h2>

- Windows 11 Pro (local — RDP client)
- Windows 11 Pro, version 25H2 (Azure VM — osTicket host)

<h2>✅ Prerequisites</h2>

Before beginning the installation, the following must be in place:

- An active <b>Azure subscription</b>
- An Azure <b>Resource Group</b> and <b>Virtual Network</b>
- An Azure VM running <b>Windows 11 Pro, version 25H2</b> (Standard FX4mds v2 — 4 vCPUs, 84 GiB RAM)
- RDP access to the VM
- <a href="https://drive.google.com/uc?export=download&id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD">osTicket Installation Files (Google Drive)</a>

<h2>🚀 Installation Steps</h2>

<h3>Step 1 — Create the Azure Virtual Machine</h3>
<p>
<img src="https://i.imgur.com/GtPehTT.png" height="80%" width="80%" alt="Azure VM Creation"/>
</p>
<p>
In the Azure Portal, navigate to <b>Virtual Machines → Create → Azure Virtual Machine</b>. Create a new Resource Group named <b>osTicket</b> and configure the VM with <b>Windows 11 Pro, version 25H2 - Gen2</b>, Standard FX4mds v2 (4 vCPUs, 84 GiB RAM). Under <b>Inbound Port Rules</b>, allow <b>RDP (3389)</b>. Click <b>Review + Create</b> → <b>Create</b> and wait for deployment to complete. Once deployed, connect via RDP using the VM's Public IP address.
</p>
<br />

<h3>Step 2 — Enable IIS with CGI</h3>
<p>
<img src="https://i.imgur.com/uxbGqxY.png" height="80%" width="80%" alt="IIS with CGI enabled"/>
</p>
<p>
copy Google Drive 
osTicket-Installation-Files Inside the VM go to edge and paste link into edge address bar. download and extract to the desktop, open <b>Control Panel → Programs → Turn Windows Features On or Off</b>. Enable <b>Internet Information Services → World Wide Web Services → Application Development Features → CGI</b>. Also ensure all <b>Common HTTP Features</b> are checked. Click OK and allow installation to complete. Verify IIS is running by opening a browser on the VM and navigating to <b>127.0.0.1</b> — the default IIS welcome page should appear.
</p>
<br />

<h3>Step 3 — Install PHP Manager, URL Rewrite, and PHP 7.3.8</h3>
<p>
<img src="https://i.imgur.com/hYgE4Hi.png" height="80%" width="80%" alt="PHP Manager in IIS"/>
</p>
<p>
From the installation files, install <b>PHP Manager for IIS</b> and the <b>install Rewrite </b>. Next, create the directory <b>C:\PHP</b>, by clicking on windows(C:) then right clicking and creating a folder called PHP then extract the PHP 7.3.8 zip into that folder. Also install the <b>VC++ Redistributable</b> (required by PHP). Open <b>IIS Manager → PHP Manager → Register New PHP Version</b> and point it to <b>C:\PHP\php-cgi.exe</b>. Restart IIS (Stop → Start) to apply the changes.
</p>
<br />

<h3>Step 4 — Install MySQL 5.5.62</h3>
<p>
<img src="YOUR_SCREENSHOT_URL_HERE" height="80%" width="80%" alt="MySQL installation"/>
</p>
<p>
Run the MySQL 5.5.62 installer and select <b>Typical Setup</b>. After installation, launch the <b>MySQL Configuration Wizard</b> and choose <b>Standard Configuration</b>. Set a root password (save this — it will be needed during the osTicket setup wizard). MySQL will run as a Windows service automatically after this step.
</p>
<br />

<h3>Step 5 — Install osTicket v1.15.8</h3>
<p>
<img src="https://i.imgur.com/SgSNSbx.png" height="80%" width="80%" alt="osTicket files copied to wwwroot"/>
</p>
<p>
Extract the osTicket zip from the installation files. Copy the <b>upload</b> folder into <b>C:\inetpub\wwwroot</b> and rename it to <b>osTicket</b>. Reload IIS, then in IIS Manager navigate to <b>Sites → Default Web Site → osTicket</b> and click <b>Browse *:80</b> on the right panel. The osTicket installer should open in the browser — note any extensions shown as disabled (red X).
</p>
<br />

<h3>Step 6 — Enable Required PHP Extensions</h3>
<p>
<img src="<img width="1919" height="1078" alt="image" src="https://i.imgur.com/J0NnK9E.png" />
" height="80%" width="80%" alt="PHP extensions enabled in IIS"/>
</p>
<p>
In IIS Manager, go to <b>Sites → Default Web Site → osTicket → PHP Manager → Enable or Disable an Extension</b>. Enable the following three extensions: <b>php_imap.dll</b>, <b>php_intl.dll</b>, and <b>php_opcache.dll</b>. Refresh the osTicket installer page in the browser — the previously red checkmarks should now appear green. APCu extension and Zend OPcache Extension are not required.
</p>
<br />

<h3>Step 7 — Rename Config File and Set Permissions</h3>
<p>
<img src="YOUR_SCREENSHOT_URL_HERE" height="80%" width="80%" alt="ost-config.php permissions"/>
</p>
<p>
Navigate to <b>C:\inetpub\wwwroot\osTicket\include\</b> and rename <b>ost-sampleconfig.php</b> to <b>ost-config.php</b>. Right-click the file → <b>Properties → Security → Advanced</b>. Click <b>Disable Inheritance → Remove all inherited permissions</b>, then add <b>Everyone</b> with <b>Full Control</b> (this is temporary for the setup wizard only — it will be locked down afterward).
</p>
<br />

<h3>Step 8 — Complete the Setup Wizard and Create the Database</h3>
<p>
<img src="YOUR_SCREENSHOT_URL_HERE" height="80%" width="80%" alt="osTicket setup wizard"/>
</p>
<p>
Open <b>HeidiSQL</b>, create a new session using the MySQL root credentials, and connect. Create a new database named <b>osTicket</b>. Back in the browser, continue the osTicket setup wizard — fill in your Help Desk Name, default email, and admin credentials. Under <b>Database Settings</b>, enter <b>osTicket</b> as the database name along with the MySQL root username and password. Click <b>Install Now!</b>
</p>
<br />

<h3>Step 9 — Post-Installation Cleanup</h3>
<p>
<img src="YOUR_SCREENSHOT_URL_HERE" height="80%" width="80%" alt="osTicket admin panel"/>
</p>
<p>
Once the "Congratulations!" page appears, perform two cleanup tasks: (1) <b>Delete</b> the setup folder at <b>C:\inetpub\wwwroot\osTicket\setup</b>. (2) Set <b>ost-config.php</b> back to <b>Read Only</b> — right-click → Properties → Security → Advanced → change Everyone permission to <b>Read</b> only. The help desk is now live. Access the end-user portal at <b>http://&lt;VM-IP&gt;/osTicket</b> and the staff/admin panel at <b>http://&lt;VM-IP&gt;/osTicket/scp/login.php</b>.
</p>
<br />
