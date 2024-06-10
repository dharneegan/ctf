# Brooklyn Nine Nine

![image](https://github.com/dharneegan/ctf/assets/122503989/bc195561-17c8-4b6c-868a-a3aabd3052bd)

In this writeup I will only be showing one of the two ways.

## Rustscan 

Let's start by scanning for open ports using this command:
```
rustscan -a $IP --ulimit 5000
```
We can see that ports 21,22, and 80 are open which means ftp, ssh, and http are running. The method I will be showing doesnt involve using the web server.

## FTP

Let's login to the ftp server using `ftp $IP`. Input the name as Anonymous as this will be an anonymous login and click enter when it asks for a password

![image](https://github.com/dharneegan/ctf/assets/122503989/2b71352f-8c53-40d0-97c0-e308b0e5db5a)

Our login is successful! Now we run `ls` on the ftp server to see what files it contains. We can see that theres a file called `note_to_jake.txt`. Let's move that to our host system using `get note_to_jake.txt`. Read this file using `cat`

![image](https://github.com/dharneegan/ctf/assets/122503989/b6e1533f-7abe-4e54-b69c-eab97be57e03)

We can see that Jake's password is weak. This is a sign to brute force it with hydra. Run:
```
hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://$IP
```

![image](https://github.com/dharneegan/ctf/assets/122503989/ca47ee21-4323-4a09-afa9-a8c93f760e94)

We found the password!

## SSH

Login to the server as jake through ssh using the credentials.
```
ssh jake@$IP
```
Now lets find user.txt. Changinger directories into holt's home directory shows user.txt. 

![image](https://github.com/dharneegan/ctf/assets/122503989/c873c448-e443-481c-baab-27fe1309f8fa)

**user.txt**: ee11cbb19052e40b07aac0ca060c23ee

## Privilege escalation

Run `sudo -l` to see jake's sudo permissions.

![image](https://github.com/dharneegan/ctf/assets/122503989/30210a2d-9f8d-4078-bb17-9e9137365195)

Interesting... Jake can run the command `less` without a password. Visit [GTFOBins](https://gtfobins.github.io/gtfobins/less/#sudo) to see how we can exploit this. GTFOBins is telling us to run `sudo less /etc/profile` then type `!/bin/sh` to get root. Let's try this.


![image](https://github.com/dharneegan/ctf/assets/122503989/95a6c091-468e-4342-ad14-997374aee1ed)

Hit enter

![image](https://github.com/dharneegan/ctf/assets/122503989/0341e12c-c1ce-4687-bda1-f5577f3e04cf)

And we're root! Run `cat /root/root.txt` to get the root flag

**root.txt**: 63a9f0ea7bb98050796b649e85481845
