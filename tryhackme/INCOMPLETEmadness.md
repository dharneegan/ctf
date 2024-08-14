# Madness

![image](https://github.com/user-attachments/assets/2b3048a2-199e-4b81-b3c1-b71aa23f557e)

## Rustcan 

As always, lets start off with scanning for open ports with rustscan. Run this command:
```
rustscan -a $IP
```

There are not many ports open, just port 80 for http and 22 for ssh. Let's visit the site at `http://$IP`.

![image](https://github.com/user-attachments/assets/06996376-e54c-4945-93ee-6ccb96fc7b61)

It's just the apache default page. Let's view the source code to see if theres anything interesting. 

![image](https://github.com/user-attachments/assets/457738aa-fb21-4fc9-9af7-4c6b44ff8ed7)

Hmm.. They will never find me with an image linked right above it. When we click on this image,`thm.jpg`, it says that the image could not be viewed. Let's try downloading this image using wget.


![image](https://github.com/user-attachments/assets/fb52d58a-20dc-41a2-8090-45e0ee509fb5)

Right click this and click copy image link. Then, run `wget <PASTELINKHERE>`. After downloading the image, we will now inspect it with a hex editor. I use [this website](https://hexed.it/) as my hex editor. 

![image](https://github.com/user-attachments/assets/89399097-2799-4a98-b568-fbc6a78c1799)

This is weird, a jpg file with a png file header?? We need to change the file header of this image to one of an actual jpg file.

![image](https://github.com/user-attachments/assets/628c916f-3d74-49a6-8d95-70e2b567f00e)

This is the file header of an actual jpg file, lets just replace the png file header with this header and redownload the image to see what happens. 

![image](https://github.com/user-attachments/assets/764e821b-630e-4075-8491-ca14b48f03f9)

This is what your file header should look like after you replace it. Downloading the image and opening it greets us with this.

![image](https://github.com/user-attachments/assets/02137540-fabe-42f4-a65f-2ab28fc1c141)

Let's visit this hidden directory next.

![image](https://github.com/user-attachments/assets/7f39ed96-fa96-49ee-9292-666860b761b9)

This is weird, let's check the source code.

![image](https://github.com/user-attachments/assets/40fa6ea2-7bae-4b08-9d67-9468f20b99e1)

This makes more sense. We have to try every possible number between 0-99 as a URL parameter. Let's test this out.

![image](https://github.com/user-attachments/assets/196224f6-ca2a-4eb8-8877-7f3d8a9d548f)

When I set the parameter "secret" to 1, it checked if it was the right number. Obviously, 1 is not the right number. We will have to make a bash script to curl every possible number from 0-99.

![image](https://github.com/user-attachments/assets/6d9e17bc-424f-4f95-bde6-2422fe5fb371)

This is the bash script I made. Give execute permissions to the file with `chmod +x script.sh` and then run it. When we scroll through the output, we can see that 73 is the right number

![image](https://github.com/user-attachments/assets/d2d02304-ef5d-4e9a-8f91-4b417e62c455)

Let's visit the webpage for a cleaner look.

![image](https://github.com/user-attachments/assets/761b9f59-43fc-4c2b-8f54-f2431f83ed76)

The string provided sort of looks like a password. When we try running steghide on thm.jpg(the file that provided us with the hidden directory), it asks us for a passphrase. We can input the string given to us in the webpage, `y2RPJ4QaPF!B`, to get a secret file called hidden.txt.

![image](https://github.com/user-attachments/assets/370361c9-0931-4691-822d-c6da8c282c77)

Viewing the file presents us with a username.

![image](https://github.com/user-attachments/assets/2862c4e7-340f-4ba3-8f83-009ae78248fa)

If we view the hint for the user.txt question on tryhackme, it tells us that there is something ROTten about the persons name. This obviously implies a rotation cipher of some sorts. Taking the string `wbxre` and bruteforcing rot13 on it with [CyberChef](https://gchq.github.io/CyberChef/), we find something really interesting.

![image](https://github.com/user-attachments/assets/b6f1a136-bdf4-4058-9c9d-c569d8ffa2e3)

We found the username! Let's try and find the password next. Let's use steghide on the image that was presented to us at the start of the challenge. This image:

![image](https://github.com/user-attachments/assets/799ba5e5-3313-4ee3-8ac5-096feb46a1f6)

Let's run steghide now.

![image](https://github.com/user-attachments/assets/82fc708c-debd-471f-a333-7e365178885e)

Hmm.. What's the password?? I didn't know what to put in so I just left it blank and clicked enter.

![image](https://github.com/user-attachments/assets/eb96f53a-52ef-4e88-a9b2-11325b23c350)

I guess there isn't a password. That's good for us. Now let's view the file contents of `password.txt `. 

![image](https://github.com/user-attachments/assets/a4e142a7-66e0-4708-9803-4a49e7f7e43f)

We got the password! We can now login to the target machine with ssh. I thought it was really sneaky to put the password file in the image presented at the start of the challenge. Many people just skip over images like that thinking it has no significance. I did too, until after a long time I figured it out.

## SSH

Let's login with the credentials that we found. 

![image](https://github.com/user-attachments/assets/060244f5-bc64-4be6-a029-2b22896a0005)

We're in! We can now get the first flag by viewing user.txt with `cat user.txt`

**user.txt: THM{d5781e53b130efe2f94f9b0354a5e4ea}**

### Privilege Escalation

Let's start by looking for vulnerabilities on the machine. Running `sudo -l` doesn't work since we don't have sudo permissions as the user joker. Let's run a find command to find files that have the SUID bit set.

![image](https://github.com/user-attachments/assets/e6b830b5-4145-42b6-93a5-aac7aa7bfaf1)

/bin/screen-4.5.0 is very interesting. I searched up "screen-4.5.0 privilege escalation" and I found a [script](https://www.exploit-db.com/exploits/41154) that can potentially make us root. I ran `nano script.sh` and pasted the script from the website to the machine. After this, I set executable file permissions for the script and ran it.

![image](https://github.com/user-attachments/assets/570ff054-f6b9-43a9-bb51-86924ebe7eee)

It worked! We are now root. We can finally run `cat /root/root.txt` to get the root flag.

**root.txt: THM{5ecd98aa66a6abb670184d7547c8124a}**

I hope this writeup helped!




