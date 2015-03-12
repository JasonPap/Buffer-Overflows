## What's a shellcode
>In computer security, a shellcode is a small piece of code used as the payload in the exploitation of a software vulnerability. It is called "shellcode" because it typically starts a command shell from which the attacker can control the compromised machine, but any piece of code that performs a similar task can be called shellcode.

-- Wikipedia --

Usually a *shellcode* is written in machine/byte code and is passed to the vulnerable program in order to be executed through a buffer overflow vulnerability.

### Shellcode used
The shellcode I used on all three attacks is found below.

```sh
\xeb\x1f\x5e\x89\x76\x08\x31\xc0\x88\x46\x07\x89\x46\x0c\xb0\x0b\x89\xf3\x8d\x4e\x08\x8d\x56\x0c\xcd\x80\x31\xdb\x89\xd8\x40\xcd\x80\xe8\xdc\xff\xff\xff/bin/sh
```

As the shellcode is byte code it's also architecture independent so in order to be sure you'll have to search online for a shellcode that works on the platform you are exploiting.

### NOP slide
When trying to execute a shellcode through a buffer overflow vulnerability it can be quite hard to figure out the exact padding needed (extra bytes) before your shellcode in order to overwrite the `saved eip` exactly with the start of the shellcode.

In order to solve this problem it's common to use what's called a `NOP slide`. A NOP slide is basically a series of NOP instructions (their byte code representation), in my case `\x90`.


### Links
For more information see [here](https://lthieu.wordpress.com/2012/11/10/exploit-stack-based-buffer-overflow-using-nop-sled-technique/) or [here](http://stackoverflow.com/questions/14760587/how-does-a-nop-sled-work) for more information. 
