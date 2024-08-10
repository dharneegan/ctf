# Easy Peasy

![image](https://github.com/user-attachments/assets/1491e1ff-41b5-4852-a05f-f73624d5efa5)

## Rustscan
First lets start off by scanning the machine for open ports. Use this command:
```
rustscan -a $IP
```
![image](https://github.com/user-attachments/assets/9528c9f9-b8b5-48c9-b513-93bebb0e01ca)

We can see that theres 3 open ports, 80,6498, and 65524. 

**How many ports are open?**: 3


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

We can see that 
