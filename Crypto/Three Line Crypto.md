# Three Line Crypto

Category: Crypto (Medium)

Points: 247

Solves: 44

>This crypto challenge could fit in a tweet.

### Solution

We are given a very simple encryption algorithm and an encoded passage:

```py
import os, sys
q, y = os.urandom(16), 0
for x in sys.stdin.buffer.read(): sys.stdout.buffer.write(bytes([q[y % 16] ^ x])); y = x
```

Looks like we generate `q` as 16 random bytes, and `y` as our previous char. Then to encrypt the $i$th char, we take the xor of the $i$th char and the ($i-1$th char mod $16$)th random byte. Luckily, we are also told that the giant passage given to us is in English.

My first thought with this information would be to use frequency analysis, but what chars or words would encrypt to the same value everytime?

Take the string `" the "` for example, we can figure out what `"the "` encrypts to:

<div align="center">

| Previous Char | Ascii Value | Mod 16 | Current Char | Out |
| :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
|     |     |   | " " |  |
| " " | 32  | 0 | t   | random[0]^t |
| t   | 116 | 4 | h   | random[4]^h |
| h   | 104 | 8 | e   | random[8]^e |
| e   | 101 | 5 | " " | random[5]^" "|

</div>

As we can see from this table, if we know a string comes after a space, then the rest of the string will always encrypt to the same value no matter comes after it or before the initial space. This is useful because we can now do frequency analysis on words and prefixes of words. For example, if we see the string `"abcd"` pop up a lot we can likely assume `"the "` encodes to it, with this information we can then figure out the `0th`, `4th`, `8th`, and `5th` random number by reversing the xor. (`plain^random = enc` then `enc^plain = random`)

For frequency analysis reference:
- https://en.wikipedia.org/wiki/Most_common_words_in_English
- https://github.com/qpwoeirut/ClassicalCipherCrackers/tree/main/text_fitness/ngram_frequency

Cool, we can now write a simple script to help us with frequency analysis:

```py
def rank_n_letter_substring(s, n):
    # Dictionary to store the frequency of each substring
    substring_counts = {}
    
    # Iterate through the string to get all n-letter substrings
    # Not very efficient but good enough
    for i in range(len(s) - n + 1):
        substring = s[i:i+n]
        if substring in substring_counts:
            substring_counts[substring] += 1
        else:
            substring_counts[substring] = 1
    
    

    return substring_counts
    
def kth_common_n_letter_substring(s,n,k):
    substring_counts = rank_n_letter_substring(s, n)
    # Find the substring with the highest frequency
    sorted_substrings = sorted(substring_counts.items(), key=lambda item: item[1], reverse=True)

    substring = sorted_substrings[k-1]
    return substring

def kth_uncommon_n_letter_substring(s,n,k):
    substring_counts = rank_n_letter_substring(s, n)
    # Find the substring with the lowest frequency
    sorted_substrings = sorted(substring_counts.items(), key=lambda item: item[1])

    substring = sorted_substrings[k-1]
    return substring

# Open encoded passage
s = open("passage.enc.txt","rb").read()

n = 4
for i in range(1,5):
    print(kth_common_n_letter_substring(s,n,i))
```

We get that the most common 4 letter string is `b'\x13\xc0\x0bn'` which occurs `66` times. We can safely assume that these encode to `"the "`. (There will be false positive where another string randomly encodes to the same value but those aren't very common). With this information, we can now figure out that:

```py
# pseudo code
random[0] = "t"^"\x13" = 103
random[4] = "h"^"\xc0" = 168
random[8] = "e"^"\x0b" = 110
random[5] = " "^"n" = 78
```

Next, I moved on to the word `"and "` (shorter strings occur more variably so it might be hard to tell `th` apart from `he`, and longer strings don't occur frequently so more chance of false positive. Anway back to `"and "`):

<div align="center">

| Previous Char | Ascii Value | Mod 16 | Current Char | Out |
| :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
|     |     |   | " " |  |
| " " | 32  | 0  | a   | random[0]^a |
| a   | 97  | 1  | n   | random[1]^n |
| n   | 110 | 14 | d   | random[14]^d |
| d   | 100 | 4  | " " | random[4]^" "|

</div>

Running the script again we get the 2nd most common 4 letter string is `b'\x06\xff\x0f\x88'` that occurs `28` times. Going through the same process we get:

```py
# pseudo code
random[0] = "a"^"\x06" = 103
random[1] = "n"^"\xff" = 145
random[14] = "d"^"\x0f" = 107
random[4] = " "^"\x88" = 168
```

What's nice about this is that we already calculated `random[0]` and `random[4]`. Since the answers we got are consistent, we can safely assume that our words are correct.

Next I moved on to `be`, `to`, and `of`. Unfortunately I wasn't able to find `be` but for `to` and `of` the process went like this:

`to`:

<div align="center">

| Previous Char | Ascii Value | Mod 16 | Current Char | Out |
| :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
|     |     |    | " "  |  |
| " " | 32   | 0  | t    | random[0]^t |
| t   | 116  | 4  | o    | random[4]^o |
| o   | 111  | 15 | " "  | random[15]^" " |

</div>

Now we slightly modify the script. Since we know `random[0]` and `random[4]`, we can test if a common string in the encoded text matches `random[0]^t` and `random[4]^o`:

```py
n = 3
for i in range(1,100):
    val, cnt = kth_common_n_letter_substring(s,n,i)
    # random[0]^t = 19 and random[4]^o = 199
    if val[0] == 19 and val[1] == 199:
        print(val,cnt)
```

This gets us `b'\x13\xc7\x82'` which occurs `16` times:

```py
random[15] = " "^"\x82" = 162
```

`of`:

<div align="center">

| Previous Char | Ascii Value | Mod 16 | Current Char | Out |
| :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
|     |     |    | " "  |  |
| " " | 32   | 0  | o    | random[0]^o |
| o   | 111  | 15 | f    | random[15]^f |
| f   | 102  | 6  | " "  | random[6]^" " |

</div>

We now use the same trick:

```py
n = 3
for i in range(1,100):
    val, cnt = kth_common_n_letter_substring(s,n,i)
    # random[0]^o = 19 and random[15]^f = 196
    if val[0] == 8 and val[1] == 196:
        print(val,cnt)
```

This gets us `b'\x08\xc4\xad'` which occurs `20` times:

```py
random[6] = " "^"\xad" = 141
```

After this, I ran out of good words and prefixes to check after trying a bunch of things that didn't really help. However, I had one more idea. Instead of searching for frequent words, we can search for infrequent words. Specifically, I assume that the flag wrapper prefix `DUCTF{` would only occur once in the text:

<div align="center">

| Previous Char | Ascii Value | Mod 16 | Current Char | Out |
| :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
|     |    |   | " "  |  |
| " " | 32 | 0 | D  | random[0]^D |
| D   | 68 | 4 | U  | random[4]^U |
| U   | 85 | 5 | C  | random[5]^C |
| C   | 67 | 3 | T  | random[3]^T |
| T   | 84 | 4 | F  | random[4]^F |
| F   | 70 | 6 | {  | random[6]^{ |


</div>

Now we just loop through the most uncommon substrings:

```py
n = 6
for i in range(1,10000):
    val, cnt = kth_uncommon_n_letter_substring(s, n, i)
    # Calculate for every char except for random[3]^T
    if val[0] == 35 and val[1] == 253 and val[2] == 13 and val[-1] == 246 and val[-2] == 238: 
        print(val,cnt)
        break
```

This gets us `b'#\xfd\rn\xee\xf6'` which occurs `1` time:

```py
random[3] = "T"^"n" = 58
```


Great, we have recovered half of the random bytes but what now:
```py
random[0] = 103
random[1] = 145

random[3] = 58
random[4] = 168
random[5] = 78
random[6] = 141

random[8] = 110

random[14] = 107
random[15] = 162
```

Well, since we have these values, let's try to decode all the text that we have:

```py
# Decrypt
def decrypt(key):
    # Open file
    dec = open("passage.dec.txt", "w")

    # When can't decode jump to next part that matches these
    crib = ['the ', 'and ', 'DUCTF{']
    
    # Setup
    prev = 0
    i=0

    # Check if decoded is english or complete
    ok = True

    while i < len(s):
        # If the random value exists
        if key[prev] != None:
            # Decode
            dec.write(chr(s[i]^key[prev]))

            # Check if decoded is english
            if not chr(s[i]^key[prev]).isascii(): ok = False

            # Set new key
            prev = (s[i]^key[prev])%16
        # No random value
        else:

            # Dcoded is not complete
            ok = False

            # Check if encoded string matches any encoded known plain
            for word in crib:

                # Encode the word
                encword = []
                prev1 = 0
                for c in word:
                    encword.append(ord(c) ^ key[prev1])
                    prev1 = ord(c)%16
                encword = bytes(encword)
                
                # Check if encoded word matches the text
                if s[i:i+len(word)] == encword:
                    # Write space + word if matches
                    dec.seek(i-1)
                    dec.truncate()
                    dec.write(" "+word)    
                    i+= len(word)-1
                    prev = prev1
                    ok = True
                    break
            
            # If no match put X as placeholder
            if not ok: dec.write('X')

        i+=1
    return ok

random = [None]*16
random[0] = 103
random[1] = 145

random[3] = 58
random[4] = 168
random[5] = 78
random[6] = 141

random[8] = 110

random[14] = 107
random[15] = 162
decrypt(random)
```

![Decrypt Attempt 1](/images/ThreeLineCryptoFirst.png)

Damn, that wasn't very helpful :sob: At least parts of sentences make sense. (I fixed my code for this writeup, my original code was wrong and provided even less info :skull:)

However, I turned my attention to the start of the passage. `W` is a very useful char to start with. I first tried testing `Welcome to` which perfectly lined up with the number of `X`'s but that didn't work. So, I decided to write a script to test if a plain text could match with the encoded text:

```py
# print(s[:40])
enc = b'0\x9c\x0f\xe5\x88\nr\xfa\xaa=\x1a\x13\xc0\x0bn\x04U\xd0\x86\r\xe4\xc0"\xf5\x88\x14Wz\xc9\xf4u\xef\x05\x8d \x0e/\xe5\xc0N'

# Test start
plain = "Wh"

masterkey = [
    103, #0
    145, #1
    None, #2
    58, #3
    168, #4
    78, #5
    141, #6
    None, #7
    110, #8
    None, #9
    None, #10
    None, #11
    None, #12
    None, #13
    107, #14
    162 #15
]

def test(enc, plain):
    prev = 0
    key = masterkey.copy()
    keys = []

    for i in range(len(plain)):
        # Does random[prev] exist
        if key[prev] == None:
            # Calculate new random[prev]
            key[prev] = enc[i]^ord(plain[i])
            keys.append((prev,key[prev]))
            print(prev, key[prev])
        else:
            # If random[prev] exists 
            # Check that it is consistent with the text
            print(plain[i])
            if key[prev]^ord(plain[i]) != enc[i]:
                return False

        prev = ord(plain[i])%16
    return keys

test(enc,plain)
```

I then tested `Wh` since `h` is pretty common after `W`. Doing this yields that `random[7] = 244`. Decrypting the text again we get:

`What mXXXX the corXXXXXX`

I now guessed that `mXXXX` was `makes`:

```py
...
plain = "What makes the cor"
...
assert test(enc,plain)
```

From this we get that `random[13] = 19` and `random[11] = 207`. Decoding our text now, we see the first part of the flag appear `DUCTF{when_iXXX`, nice. Unfortunately, we still dont decode past `cor`:

`What makes the corXXXXXX`

After unsuccessfully trying to guess what comes after `cor`, I wrote another script that loops through a wordlist and tests out all words with a prefix of `cor`:

```py
# Remove prints from test function
plain = "What makes the "
pref = "cor"
with open("wordlist.txt") as f:
    for line in f:
        line = line.strip()
        if line.startswith(pref):
            if test(enc, plain+line+" "):
                print(plain+line+" ")
                print(test(enc, plain+line+" "))
```

This got me:

```
What makes the cor 
[(2, 166)]
What makes the cornfield 
[(2, 232), (9, 165), (12, 145)]
What makes the cory 
[(2, 255), (9, 45)]
```

`cornfield` probably makes the most sense here. It also helps us retrieve the 2nd, 9th, and 12th random values \:D

```py
random = [None]*16
random[0] = 103
random[1] = 145
random[2] = 232
random[3] = 58
random[4] = 168
random[5] = 78
random[6] = 141
random[7] = 244
random[8] = 110
random[9] = 165
# no 10 :<
random[11] = 207
random[12] = 145
random[13] = 19
random[14] = 107
random[15] = 162
decrypt(random)
```

![Decoded](/images/ThreeLineCryptoSecond.png)

Nice, everything still makes sense! (The flag is already found but due to faulty impl during the comp I didn't get it :skull:)

Now to get the final random byte, I decided to just brute force all 256 possible values:

```py
for i in range(256):
    newkey = key.copy()
    newkey[10] = i
    # decrypt returns false if there are non-ascii chars
    if decrypt(newkey):
        print(i, open("passage.dec.txt").read()[:100])
```

```
12 What makes the cornfield smile; beneath what star
maecenas, it is meet to turn the sod
or marry elm 
28 What makes the cornfield smile; beneath what star
}aecenas, it is meet to turn the sod
r marry elm 
44 What makes the cornfield smile; beneath what star
Maecenas, it is meet to turn the sod
Or marry elm 
60 What makes the cornfield smile; beneath what star
]aecenas, it is meet to turn the sod
_r marry elm 
76 What makes the cornfield smile; beneath what star
-aecenas, it is meet to turn the sod
/r marry elm 
92 What makes the cornfield smile; beneath what star
=aecenas, it is meet to turn the sod
?r marry elm 
108 What makes the cornfield smile; beneath what star

aecenas, it is meet to turn the sod
r marry elm 
124 What makes the cornfield smile; beneath what star
aecenas, it is meet to turn the sod
r marry elm
```

Going through these, I thought `44` made the most sense. FINALLY, let's do this one last time:

```py
random[10]=44
decrypt(random)
```

![Last Time](/images/ThreeLineCryptoLast.png)

```
What makes the cornfield smile; beneath what star
Maecenas, it is meet to turn the sod
Or marry elm with vine; how tend the steer;
...
Of which one is DUCTF{when_in_doubt_xort_it_out};
...
And he, who having ploughed the fallow plain
And heaved its furrowy ridges, turns once more
Cross-wise his shattering share, with stroke on stroke
The earth assails, and makes the field his thrall.
```


Note: You could probably leak more information using punctuation, but I forgot to consider that.

### Flag

```DUCTF{when_in_doubt_xort_it_out}```