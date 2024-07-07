# Intercepted Transmissions

Category: MISC (Beginner)

Points: 100

Solves: 275

>Those monsters! They've kidnapped the Quokkas! Who in their right mind would capture those friendly little guys.. We've managed to intercept a CCIR476 transmission from the kidnappers, we think it contains the location of our friends! Can you help us decode it? We managed to decode the first two characters as '##'

### Solution

Looking into the encoding we see a super long binary string encoded with CCIR476. After a bit of research we found https://en.wikipedia.org/wiki/CCIR_476 and http://www.frisnit.com/navtex/?id=navtex_data_format which explains how the encoding works. Essentially every 7 bits (4 1's and 3 0's for error correction) are converted to hex. The reciever of the hex then maps the hex onto 1 of 2 lookup tables where the top one is for the alphabet and the bottom for symbols:

![Lookup table](/images/InterceptedTransmissionsLookup.png)

To know which one to use, there are 2 control characters that switch between the table. `5A` is mapped to `LTRS` which switches us to the first table, and `36` is mapped to `FIGS` which goes to the second table.

First we write a script to convert every 7 bits to a hex:

```py
>>> for i in range(0,len(s),7): print(hex(int(s[i:i+7],2)))
0x5a
0x36
0x69
0x69
...
```
Testing for the first 4 values we get:
```
5A --> LTRS
36 --> FIGS
69 --> #
69 --> #
```

We are on the right track. After manually converting every single hex we got:

`##TH3[SP]QU0KK4[BEL]S[SP]AR3[SP]H3LD[SP]1N[SP]F4C1LITY[SP]#11911!`

From here, we replace [SP] with ` ` (space) and [Bel] with `'` (On the Wikipedia table) which yields:

`##TH3 QU0KK4'S AR3 H3LD 1N F4C1LITY #11911!`

### Flag

```DUCTF{##TH3 QU0KK4'S AR3 H3LD 1N F4C1LITY #11911!}```