# Wacky Recipe

Category: MISC (Easy)

Points: 143

Solves: 148

>Our flightless birds can run upto 50km/h but we want them to go faster. I've been messing with a mutigen but it seems to have corrupted. Can you help me recover this research?

### Solution

Analyzing the challenge file, it seems like we are given some obfuscated string encoded as a broken DNA strand in ruby:

```rb
require "doublehelix"

 AT
A--T
T- -A
G----C
 G---- 
     --C
   T---A
    G--C
     AT
     GC
    T-- 
   G- - 
...
```

After a bit of searching we can find the doublehelix gem package on [github](https://github.com/mame/doublehelix). Looking through it we can see the encoding table, `"AT"=>"00", "CG"=>"01", "GC"=>"10", "TA"=>"11"`.

Using this and our elementary biology knowledge, we can write a python script to recover the lost base pairs and convert it to a binary string:

```py
#!/usr/bin/env python3

# Encoding table
enc = {
    "AT" : "00",
    "CG" : "01",
    "GC" : "10",
    "TA" : "11"
}

# First letter on pair
encFirst = {
    "A" : "00",
    "C" : "01",
    "G" : "10",
    "T" : "11"
}

# Last letter on pair
encLast = {
    "T" : "00",
    "G" : "01",
    "C" : "10",
    "A" : "11"
}


# Offsets for first char in strand
offset = [1,0,0,0,1,2,3,4,5] + [1,0,0,0,1,2,3,4,5][::-1]

idx = 0
out = ""
missing = ord("A")

# Open dna file
with open("dna.rb") as f:
    # Skip past first 2 lines
    f.readline()
    f.readline()

    # Convert to binary
    for line in f:
        # If both bases are present in a single line
        if len(pair:=line.strip().replace(" ","").replace("-",""))==2:
            out += enc[pair]

        # If no bases are present add a letter to signify missing
        elif len(pair) == 0:
            out += chr(missing)
            missing += 1
        
        # If one pair is present
        else:
            # If first pair is present
            if line[offset[idx]] in "ATCG":
                out += encFirst[pair]
            # If second pair is present
            else:
                out += encLast[pair]

        
        idx+=1
        idx%=len(offset)

# Looking at `doublehelix` src shows its reversed, decoded, then reversed again
out = out[::-1]
print(out)
```

Which gives:

`0010001001111101H00110001001100001100110100001101011111010000010100010001G111101100110011011110101111101000101011100F0101010101101111010010000101111101110010010001010111011100110000010100000101111101100101011010000011011101E11110011010100110001DC1111001101000110100101010010011001B0110111001001111010010000110A1100110000011101000110100101001101010111110011001101001000001101110111101101000110010101000100001101010101010001000010001001110011011101000111010101110000`

Putting this into [CyberChef](https://gchq.github.io/CyberChef/#recipe=Find_/_Replace(%7B'option':'Regex','string':'A'%7D,'00',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'B'%7D,'00',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'C'%7D,'01',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'D'%7D,'01',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'E'%7D,'01',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'F'%7D,'11',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'G'%7D,'01',true,false,true,false)Find_/_Replace(%7B'option':'Regex','string':'H'%7D,'01',true,false,true,false)From_Binary('Space',8)Reverse('Character')&input=MDAxMDAwMTAwMTExMTEwMUgwMDExMDAwMTAwMTEwMDAwMTEwMDExMDEwMDAwMTEwMTAxMTExMTAxMDAwMDAxMDEwMDAxMDAwMUcxMTExMDExMDAxMTAwMTEwMTExMTAxMDExMTExMDEwMDAxMDEwMTExMDBGMDEwMTAxMDEwMTEwMTExMTAxMDAxMDAwMDEwMTExMTEwMTExMDAxMDAxMDAwMTAxMDExMTAxMTEwMDExMDAwMDAxMDEwMDAwMDEwMTExMTEwMTEwMDEwMTAxMTAxMDAwMDAxMTAxMTEwMUUxMTExMDAxMTAxMDEwMDExMDAwMURDMTExMTAwMTEwMTAwMDExMDEwMDEwMTAxMDAxMDAxMTAwMUIwMTEwMTExMDAxMDAxMTExMDEwMDEwMDAwMTEwQTExMDAxMTAwMDAwMTExMDEwMDAxMTAxMDAxMDEwMDExMDEwMTAxMTExMTAwMTEwMDExMDEwMDEwMDAwMDExMDExMTAxMTExMDExMDEwMDAxMTAwMTAxMDEwMDAxMDAwMDExMDEwMTAxMDEwMTAwMDEwMDAwMTAwMDEwMDExMTAwMTEwMTExMDEwMDAxMTEwMTAxMDExMTAwMDA&oenc=65001&oeol=CR) and manually tweaking the missing bits gives us:
`puts"DUCTF{7H3_Mit0cHOndRi4_15_7he_P0wEr_HoUsE_of_DA_C3LL}"`

### Flag

```DUCTF{7H3_Mit0cHOndRi4_15_7he_P0wEr_HoUsE_of_DA_C3LL}```