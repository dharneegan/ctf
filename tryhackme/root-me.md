# Root me Writeup

## Nmap

Lets start by scanning for open ports. Run this command:
```
nmap -vv -sV 10.10.103.55
```
**How many ports are open?**: 2

The nmap scan shows the service and the version due to the -sV flag. 

![image](https://github.com/dharneegan/ctf/assets/122503989/6ecaadb1-eecb-45c7-bbf3-f5fcfe120913)

**What version of Apache is running?**: 2.4.29

![image](https://github.com/dharneegan/ctf/assets/122503989/9d55c9dd-0aa9-406f-ab2b-82987ebef1d6)

**What service is running on port 22?**: SSH

## Gobuster

Let's find directories on the site with gobuster. Run this:
```
gobuster dir -u $IP -w /usr/share/wordlists/dirb/common.txt
```
After running that we get 

![image](https://github.com/dharneegan/ctf/assets/122503989/fe61645b-43c7-4253-9903-3c94863fcc2b)

**What is the hidden directory?**: /panel/

/panel and /uploads are of interest. Let's visit `http://$IP/panel` first. 

![image](https://github.com/dharneegan/ctf/assets/122503989/3ed3932b-a14a-45e3-8698-cd40af3b2379)

We are met with something we can upload files to. This means we can get a reverse shell if we upload a php script.

## Reverse shell

Let's download pentest monkey's php reverse shell [script](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) and try to upload it to the server. First change the necessary things in the script. 

![image](https://github.com/dharneegan/ctf/assets/122503989/2d89137d-3c42-418a-b5c9-293d03f8eb6c)

Change the IP to your host machine IP and change the port to your desired port. I will be changing it to port 53 because 53 is a port that's rarely ever closed. Anyway, after changing this let's try uploading it.

![image](https://github.com/dharneegan/ctf/assets/122503989/6052c359-c1af-406d-b0e3-ba8b1d558e4d)

Oh.. I think that means we can't upload files with the php file extension. It's okay because we can change the file extension to .php5 instead of .php. Rename your script to have the 5 after the file extension and upload it.

![image](https://github.com/dharneegan/ctf/assets/122503989/93fa9a57-d26d-4739-b2ed-67b77e20f097)

It worked! We can visit `http://$IP/uploads` and see that our script has successfully been uploaded.

![image](https://github.com/dharneegan/ctf/assets/122503989/a820f19d-b509-4fd3-9b1e-4b14cba09176)

Next step is to set up a netcat listener on the port that we changed the script to. In my case, I changed it to 53 so I will run:
```
rlwrap nc -lvnp 53
```
rlwrap is only used so that I can have a friendlier shell when I get it. Now that we have set up our listener, lets click on the link in /uploads/

![image](https://github.com/dharneegan/ctf/assets/122503989/d0785f8b-319b-4b88-9352-c4c880078c91)

![image](https://github.com/dharneegan/ctf/assets/122503989/9bca52a7-a652-4170-a1bc-ab702da17fd6)

We got our reverse shell! Now that we have our shell, lets run `find / -name user.txt 2>/dev/null` to find user.txt on the system. We can see that its located at `/var/www/user.txt`. Let's `cat` this.

![image](https://github.com/dharneegan/ctf/assets/122503989/0ae6374a-2729-4863-bba5-43bc47b317f4)

**user.txt**: THM{y0u_g0t_a_sh3ll}

## Privelege Escalation

Lets start by searching for files with SUID permissions. Run:
```
find / -perm /4000 2>/dev/null
```
And look for suspicious files. /usr/bin/python seems suspicious. Lets enter that as the answer to the question and its right!

**Search for files with SUID permission, which file is weird?**: /usr/bin/python

Let's now go on [GTFOBins](https://gtfobins.github.io/) to search for how to exploit python being in the SUID binary set. It's telling us to run this.
```
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```
Run this and it'll look like nothings working but you are actually root!

![image](https://github.com/dharneegan/ctf/assets/122503989/d13a6882-027f-43ee-a356-a477bec2a7e3)

![image](https://github.com/dharneegan/ctf/assets/122503989/931ecf5b-3ff4-4794-8593-80512484aace)

Now just change directories into `/root` and run `cat root.txt` 

**root.txt**: THM{pr1v1l3g3_3sc4l4t10n}

