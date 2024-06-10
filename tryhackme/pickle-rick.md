# Pickle Rick Writeup 

![image](https://github.com/dharneegan/ctf/assets/122503989/cdd64ba5-2db7-42c8-8bdb-e97b0935911d)

# Rustscan

Let's start off by scanning for open ports with this command:
```
rustscan -a $IP --ulimit 5000
```
This scan shows us that ports 22 and 80 are open for ssh and http respectively. Lets visit the website at `http://$IP` . We are met with this screen.

![image](https://github.com/dharneegan/ctf/assets/122503989/41a9095d-d281-4d18-8d3f-ef7b3fc5cf87)

Looking at the source we can see there is a comment saying the username is R1ckRul3s. We can use this to potentially log in to something. 

## Gobuster

Let's scan for hidden directories with this command:
```
gobuster dir -u http://$IP -w /usr/share/wordlists/dirb/common.txt
```
![image](https://github.com/dharneegan/ctf/assets/122503989/5324ea12-3479-4f4f-b3cc-6c2f46da32d1)

From this we see that there is a robots.txt file. Visiting `http://$IP/robots.txt` we see there is `Wubbalubbadubdub` printed out. This could be a potential password. Lets visit the assets directory next.

![image](https://github.com/dharneegan/ctf/assets/122503989/2e8b6ab7-3919-4dc2-9667-45bb440c6485)

We see that there are many images. Some of them suggest other potential directories. For example, portal.jpg could mean that there is a directory or file named portal. We can test this by visiting `http://$IP/portal.php`. Once visiting this, we are met with a login page. We can use the credentials we found to log in.
```
Username: R1ckRul3s
Password: Wubbalubbadubdub
```
It worked! We are now met with a command panel where we can execute commands on the system. Visiting the other pages we can see that we don't have access because only the **REAL** rick can view those pages. Let's start off by running some commands.
Let's start by running `ls` on this panel. We are met with a bunch of files. 

![image](https://github.com/dharneegan/ctf/assets/122503989/85c6cec6-00ab-460a-94ef-f6f82ea572f7)

Sup3rS3cretPickl3Ingred.txt and clue.txt looks of interest to us. The thing is, we can't run `cat` on this command panel because the command is disabled. What we can do to view these files instead is put the file names in the URL. For example visit `http://$IP/Sup3rS3cretPickl3Ingred.txt` to view that file and same for clue.txt.

![image](https://github.com/dharneegan/ctf/assets/122503989/5e9a49a5-b017-4ea1-8890-e215dd08e611)

We have got the first ingredient that Rick needs.

**What is the first ingredient that Rick needs?**: mr. meeseek hair

Reading clue.txt through `http://$IP/clue.txt` it says we have to look around the file system for the other ingredient. Let's do exactly that. If we look around in `/home/rick` we see that there is a file named `second ingredients`.

![image](https://github.com/dharneegan/ctf/assets/122503989/0242e434-4cfe-44fc-b1de-093eec68b72d)

Since we can't use `cat` to read this file, let's use `less` instead. 
```
less /home/rick/second\ ingredients
```
The backslash in the filename is used as an escape character because spaces in file names are handled differently. The file says the second ingredient is 1 jerry tear.

**What is the second ingredient in Rick’s potion?**: 1 jerry tear

Let's run `sudo -l` to see what we can run with sudo privileges. 

![image](https://github.com/dharneegan/ctf/assets/122503989/b59bd77a-3761-4fe6-bfd4-305aaa9d9c95)

We can run all command with no password too! Because of this let's run 
```
sudo ls /root
```
We see in the root directory there is a file named 3rd.txt. This has to be final ingredient. Run: `sudo less /root/3rd.txt` to reveal the final ingredient being fleeb juice.

**What is the last and final ingredient?**: fleeb juice


