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



