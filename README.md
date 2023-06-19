# Venom-walkthrough-Vulnhub-Writeup
a walk through for venoum machine

hello friends

This machine was created for the OSCP Preparation. Enumeration is the Key.

after knowing the machine ip lets try to get open ports and services running on it

```
nmap -p- -A 10.10.0.36 -oN nmapscan.txt
```
-p- to scan all ports #the defult scan only scan the first 1000 port

-A for enables OS detection, version detection, script scanning, and traceroute

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/d125ec01-f2a7-4986-a01e-81f00a6d3d31)

we have got 6 port and 3 is open 

as long as ftp does not allow anonymous login i will not focus on in 

i'll go first for the http port 80

opening it in the browser 
![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/da25e071-4212-48e0-9236-fcb1956e211d)

we got a defulat apache page but there is interesting setnetce down there opening the soutce code to see if i get anything else 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/38c90275-863b-4e09-8f21-0edf8644ad53)

to identify the has type i used hash identifier

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/959e23e9-4169-4494-953c-db487ce18ce2)

```
hashcat -m 0 -a 0 md5.txt /usr/share/wordlists/rockyou.txt
```
simplifing the command

-m 0: Specifies the hash mode, where 0 represents MD5.

-a 0: Specifies the attack mode, where 0 represents straight (or dictionary) attack.

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/15bc2723-378c-480d-ab3a-03d01efab383)

we've got something here

i tried to paste this in the url but nothing was there

second trying to use it as cred for ftp

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/591b6a46-91dc-4d80-a079-66561da6158f)

we got a succefull login and found a hint there 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/1ad68d3c-fd3f-4bcb-b226-a85a6f1c6dc3)

okay now we have 2 base64 encryption

and name of user "dora"

and a domain name "venom.box"

lets add the domain name to hosts

```
sudo gvim /etc/hosts
```
![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/781c8976-477e-46d1-ac49-50ed3c26d027)

now lets see what we got 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/4d9567b1-41d3-42a8-a1cd-0e3d07e133d4)

a web page and we have a login panel

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/3f7fdbdb-5db8-4e14-8253-39573026d18c)

i tried hostinger as user and pass but failed

okay moving now to decrypt the base64 encryption

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/c97b8fc8-d59d-4931-a583-751d19491e3a)

we got a goot note here to use standard vigenere cipher this migh help when decoding the password

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/27329470-a9dc-4471-9c31-3b3b378db6aa)

the other base64 get us with a link of vigenere cypher decoder

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/cdb75e6e-1f79-4355-9e4f-086094e0c256)


i tried hostinger as the key of the cypher 

and with those creds tried to login

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/c2ba1e8e-3789-4ce7-81b8-f003daf403fe)

now i'm in

i searched for posible vulnerability for "Subrion CMS"

and got one here 

https://www.exploit-db.com/exploits/49876

setting the needed parameters

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/6eb23865-d809-4067-b433-9870d4e7929b)

we are in

but i couldn go any where from there so i tried to upload a rev shell manually 

the thing that i've noticed after some fails that the file extension is "phar"

i uploaded pentestmonkey revshell

and uploaded it 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/39d0bf89-3bbd-49cd-94ff-6c79e385ea20)

before open the file i setuped a netcat listner on my machine

```
nc -nlvp 9001
```

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/e3b69f8d-e6e6-4796-8047-26e773b2211a)

you can run your payload from the link

http://venom.box/uploads/rev.phar

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/9e88fa50-2b12-4b03-b127-8cd258f8ab51)

now we are in i like to try manual enumeration for easy wins but before setup a full tty

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

now lets start enumeration

when i cat into /etc/passwd 

i have noticed there is a user named hostinger 

tring to switch users

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/a4abdaeb-d6d8-4bb9-9012-1d782fd85724)

checking the history we see that user nathan opend file .htaccess 

cat into /var/www/html/subrion/backup/.htaccess
we get 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/5f4f17ae-2337-4098-966c-4871f3c29529)

trying to use it as the password of user nathan we are now nathan 

trying to enumerate again with user 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/9ea42e75-895c-4ef2-ba40-0a00bfe80dc3)

now we can get root 

![image](https://github.com/0xA4O/Venom-alkthrough-Vulnhub-Writeup/assets/57577405/9670a73d-1741-413e-b510-788dc7983f2c)

congratulations you are now root!!!
