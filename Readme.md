# Introduction to buffer overflows
As a first project for my University's Computer Security course I was tasked to find and exploit buffer overflow vulnerabilities on three different simple programs.

### The scenario
There are three other users on the same Linux machine so the /home directory looks like this:
```
jason@linux /home $ ls

hyperuser jason masteruser superuser
```
Each of the users (superuser, hyperuser, masteruser) have an executable on their home directory which can be executed by other users and runs with the **suid bit enabled**. That means that when executed the program will have the owners privileges and not the privileges of the user executing it. 

Each user also have a *secret.txt* file which can be read only by him.


### Aim of the project
The goal is to introduce students to buffer overflow vulnerabilities. In order to reveal the final secret, each of the three secrets from superuser, hyperuser and masteruser must be combined.

Each of the executables have a different flaw that must be exploited with the ultimate goal in each case to [create a Shell](https://github.com/JasonPap/Buffer-Overflows/tree/master/shellcode) running with the victim's privileges.

* [superuser](https://github.com/JasonPap/Buffer-Overflows/tree/master/superuser): he has the *convert.c* program without any protection against buffer overflows and is the easiest target.
* [hyperuser](https://github.com/JasonPap/Buffer-Overflows/tree/master/hyperuser): has the *arpsender.c* program which uses a canary to protect against buffer overflows.
* [masteruser](https://github.com/JasonPap/Buffer-Overflows/tree/master/masteruser): has the *zoo.cpp* program that must be exploited using the VPTR.


### Links
* Computer Security course [website](http://crypto.di.uoa.gr/csec/Asphaleia_Ypologistikon_Systematon/YS13.html)
* "Smashing the stack for fun and profit" great introduction to buffer overflows [here](http://insecure.org/stf/smashstack.html).


### License
MIT License - fork, modify and use however you want.
