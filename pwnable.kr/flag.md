This is a writeup of reverse engineering challenge from pwnable.kr called "flag".
I used Radare2 to solve this challenge, so let's get started.


r2 ./flag          //Let's go through the file first
DON'T forget to change the permissions of the file using chmod.


Now, let's analyze the file and see what functions are there,

![Untitled](https://github.com/Ankit-Kumar6/writeups/assets/57087618/ebf82c30-dfe1-4bce-9b1f-8a4cdf664335)

Also let's see the strings that this binary file contains:


azz         //This is the command to view strings
or
azz ~..     //This basically using less to that command

![Untitled 1](https://github.com/Ankit-Kumar6/writeups/assets/57087618/5f3f7ce5-093b-486b-a2bf-e52095959044)

After running strings, I found this UPX! at the top. Upon further googling, I found that this a technique that can be used to run the program while being compressed and was often used in malwares.
So, lets quit the radare and quickly decompress it.


sudo apt install upx-ucl       //Installing the upx
upx -d ./flag                  //Decompressing the file

![Untitled 2](https://github.com/Ankit-Kumar6/writeups/assets/57087618/ba073534-b5e5-4f70-9436-17070287d9f4)

It automatically overwrites the file after decompressing it.
Now lets run the program again using radare2.

![Untitled 3](https://github.com/Ankit-Kumar6/writeups/assets/57087618/d8edff3b-bde0-4720-8b8f-b4c7f9abbe4a)

Now let's seek to main function and view it.

![Untitled 4](https://github.com/Ankit-Kumar6/writeups/assets/57087618/6268ed07-c932-4e09-b678-cd334759189f)


Hit p till you reach the decompiled view of the binary file.
You can also hit P if you wanna go backwards.

![Untitled 5](https://github.com/Ankit-Kumar6/writeups/assets/57087618/0e9769f3-acac-48c2-9bbc-64d3f2926a72)

Here we can see that there are three functions in total, one is puts, the other one is malloc and according to the author it says there's also strcpy function so the last one must be it.
To solve this and get the flag, we will go to the address after the strcpy function call, and print the flag from var_8h.
To do that let's enter debug mode in radare2.
Quit the program, use 'q' to do that.
And re run the program in debug mode.


r2 -d ./flag

![Untitled 6](https://github.com/Ankit-Kumar6/writeups/assets/57087618/d5ea9b8b-3fb9-4c1c-9727-bc8ed0fd340e)

Analyze the flag, wait a few minutes for that to analyze the binary file.
Then seek to main function. Hit 'V' to enter visual mode.
Now navigate to the debug view by pressing 'p' and going forward.

![Untitled 7](https://github.com/Ankit-Kumar6/writeups/assets/57087618/b5d234fb-75d1-44aa-9ba4-e07665efe172)

Now, set a breakpoint at the address '0x0040119a' just after the function call. To do that hit ':' semicolon. Then,


db 0x0040119a           //set a breakpoint at this address
dc                     //countinue execution

![Untitled 8](https://github.com/Ankit-Kumar6/writeups/assets/57087618/6fc6eb27-4f7b-4efc-b7a1-80f21b461b9c)

Now let's read the address where the flag is:


afvd var_8h            //afvd is used to show the value of args/local in the in the debugger
pf S @rbp-0x8         //printf string at rbp-0x8

![Untitled 9](https://github.com/Ankit-Kumar6/writeups/assets/57087618/5bee1450-566c-4cd5-a86d-296f68ed4e8a)

And we got the flag:
"UPX...? sounds like a delivery service :)"


pwned!
