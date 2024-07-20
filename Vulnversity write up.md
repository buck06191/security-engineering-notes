## Step 1 - scan the box using nmap


| Nmap flag     | Description                                                                         |
| ------------- | ----------------------------------------------------------------------------------- |
| -sV           | Attempts to determine the version of the services running                           |
| -p <x> or -p- | Port scan for port <x> or scan all ports                                            |
| -Pn           | Disable host discovery and scan for open ports                                      |
| -A            | Enables OS and version detection, executes in-build scripts for further enumeration |
| -sC           | Scan with the default Nmap scripts                                                  |
| -v            | Verbose mode                                                                        |
| -sU           | UDP port scan                                                                       |
| -sS           | TCP SYN port scan                                                                   |
`nmap -sV 10.10.77.61`

```
Starting Nmap 7.60 ( https://nmap.org ) at 2024-07-20 20:31 BST
Nmap scan report for ip-10-10-77-61.eu-west-1.compute.internal (10.10.77.61)
Host is up (0.00055s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3128/tcp open  http-proxy  Squid http proxy 3.5.12
3333/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))
MAC Address: 02:E9:CD:9C:AC:EF (Unknown)
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.23 seconds
```
Six open ports.
Squid proxy 3.5.12 is running
Likely running Ubuntu 
Apache web server running on Port 3333

## Step 2 - Locating directories using gobuster 

Gobuster is a tool for brute-forcing URIs (directories and files), DNS subdomains, and virtual host names. For this machine, we will focus on using it to brute-force directories.  

Download Gobuster [here](https://github.com/OJ/gobuster), or if you're on Kali Linux run `sudo apt-get install gobuster`.

To get started, you will need a wordlist for Gobuster (which will be used to quickly go through the wordlist to identify if a public directory is available. If you use [Kali Linux](https://tryhackme.com/room/kali), you can find many wordlists under `/usr/share/wordlists`. You can also use the wordlist for directories located at `/usr/share/wordlists/dirbuster/directory-list-1.0.txt` in the AttackBox.  

| **Gobuster flag** | **Description**                           |
| ----------------- | ----------------------------------------- |
| -e                | Print the full URLs in your console       |
| -u                | The target URL                            |
| -w                | Path to your wordlist                     |
| -U and -P         | Username and Password for Basic Auth      |
| -p **<x>**        | Proxy to use for requests                 |
| -c <http cookies> | Specify a cookie for simulating your auth |
Now let's run Gobuster with a wordlist using `gobuster dir -u http://10.10.77.61:3333 -w` .

```
root@ip-10-10-147-195:~# gobuster dir -u http://10.10.77.61:3333 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt -e
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.77.61:3333
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-1.0.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2024/07/20 20:38:42 Starting gobuster
===============================================================
http://10.10.77.61:3333/images (Status: 301)
http://10.10.77.61:3333/css (Status: 301)
http://10.10.77.61:3333/js (Status: 301)
http://10.10.77.61:3333/internal (Status: 301)
===============================================================
2024/07/20 20:40:18 Finished
===============================================================
```
the internal web page has an upload form 

## Step 3 - Compromise the webserver

Now that you have found a form to upload files, we can leverage this to upload and execute our payload, which will lead to compromising the web server. We will fuzz the upload form to identify which extensions are not blocked.

To do this, we'll use BurpSuite. If you need clarification on what BurpSuite is or how to set it up, please complete our [BurpSuite module](https://tryhackme.com/module/learn-burp-suite) first.

Using BurpSuite

We're going to use Intruder (used for automating customised attacks). To begin, make a wordlist with the following extensions:

- .php
- .php3
- .php4
- .php5
- .phtml

![terminal screenshot](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1716554636721)  

Now, make sure BurpSuite is configured to intercept all your browser traffic. Upload a file; once this request is captured, send it to the Intruder. Click on "`Payloads`" and select the "`Sniper`" attack type.

Click the "`Position`s" tab now, find the filename and "`Add §`" to the extension. It should look like this:

![payload position burpsuite](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1716554707341)

Now that we know what extension we can use for our payload, we can progress.

Getting a Reverse Shell

We are going to use a PHP reverse shell as our payload. A reverse shell works by being called on the remote host and forcing this host to make a connection to you. So you'll listen for incoming connections, upload and execute your shell, which will beacon out to you to control! You can download the following reverse PHP shell [here](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php).

To gain remote access to this machine, follow these steps:  

1. Edit the php-reverse-shell.php file and edit the ip to be your tun0 ip (you can get this by going to [http://10.10.10.10](http://10.10.10.10/) in the browser of your TryHackMe connected device).  
    
2. Rename this file to `php-reverse-shell.phtml`.  
    
3. We're now going to listen to incoming connections using netcat. Run the following command: `nc -lvnp 1234`.  
    
4. Upload your shell and navigate to `http://10.10.77.61:3333/internal/uploads/php-reverse-shell.phtml` - This will execute your payload.

You should see a connection on your Netcat session.

![shell access](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1716554998048)


## Privilege Escalation 
Now that you have compromised this machine, we will escalate our privileges and become the superuser (root).

In Linux, SUID (**set owner userId upon execution**) is a particular type of file permission given to a file. SUID gives temporary permissions to a user to run the program/file with the permission of the file owner (rather than the user who runs it).

For example, the binary file to change your password has the SUID bit set on it (`/usr/bin/passwd`). This is because to change your password, you will need to write to the shadowers file that you do not have access to; `root` does, so it has root privileges to make the right changes.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1716555383491)

It's challenge time! We have guided you through this far. Unleash your skills and exploit this system further to escalate your privileges and answer the following questions.

Done via
https://gist.github.com/A1vinSmith/78786df7899a840ec43c5ddecb6a4740

