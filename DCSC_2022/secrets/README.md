## Secrets
For this reversing challenge we are given a binary and told it has some secrets. 

Running this application by itself resulted in a segfault. So I tried with some arguments.

![segfault](/DCSC_2022/secrets/seg_fault.png)

I decided to open up the application in ghidra and have a poke around. I saw that there was a red herring so I decided to chase it.

![main](/DCSC_2022/secrets/main.png)

![red_herring](/DCSC_2022/secrets/red_herring.png)

We can see there is a string comparison with local_14 which is a string that resolves to "hello_there" so lets try that as arguments.

![red_herring](/DCSC_2022/secrets/args.png)

We can see that chasing the herring will get no where so lets have a look at some other functions. I did notice one called the_real_secret.

![real_secret](/DCSC_2022/secrets/the_real_secret.png)

This certainly looks alot more interesting but there are no function calls to this method. Lets fire up GDB and call it ourselves. I also have and extension called gef installed, I recommend it. 

![real_secret](/DCSC_2022/secrets/gdb.png)

The last step was to decode the ascii dump.

![real_secret](/DCSC_2022/secrets/cyberchef.png)

This was a call one for me as I learnt that you can call functions with GDB.
