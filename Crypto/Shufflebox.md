# Shufflebox

Category: Crypto (Beginner)

Points: 100

Solves: 582

>I've learned that if you shuffle your text, it's elrlay hrda to tlle htaw eht nioiglra nutpi aws.

### Solution

Looks like we randomly generate a permutation of length 16 then shuffle the character according to the permuation:

```py
import random

PERM = list(range(16))
random.shuffle(PERM)

def apply_perm(s):
	assert len(s) == 16
	return ''.join(s[PERM[p]] for p in range(16))

for line in open(0):
	line = line.strip()
	print(line, '->', apply_perm(line))
```

And we are given the output of two shuffled string and we need to reverse the third one:

```
aaaabbbbccccdddd -> ccaccdabdbdbbada
abcdabcdabcdabcd -> bcaadbdcdbcdacab
???????????????? -> owuwspdgrtejiiud
```

While bruteforcing all $16!$ permutations would be too big, we can just find all permutations that match the first shuffled string, then test those on the second string. This would lead to only $(4!)^4$ permuations.

We can easily write a script to do this:

```py
#!/usr/bin/env python3

# aaaabbbbccccdddd -> ccaccdabdbdbbada
# abcdabcdabcdabcd -> bcaadbdcdbcdacab
# ???????????????? -> owuwspdgrtejiiud

s1 = "aaaabbbbccccdddd"
t1 = "ccaccdabdbdbbada"

s2 = "abcdabcdabcdabcd"
t2 = "bcaadbdcdbcdacab"

t3 = "owuwspdgrtejiiud"

pos = []

# returns a list with all indicies of char in string
def find_all(string, char):
    l = []
    for i,x in enumerate(string):
        if x == char:
            l.append(i)
    return l

import itertools

# find all permuations for a b c and d then just combine them together
# s1 is nice since its 4 a's then 4 b's etc
for perma in itertools.permutations(find_all(t1,'a')):
    for permb in itertools.permutations(find_all(t1,'b')):
        for permc in itertools.permutations(find_all(t1,'c')):
            for permd in itertools.permutations(find_all(t1,'d')):
                pos.append(perma+permb+permc+permd)

# check if perm that works for s1 works for s2
ok = []
for perm in pos:
    # use t2 since we are reversing the permuation
    if ''.join(t2[perm[p]] for p in range(16)) == s2:
        ok.append(perm)

assert len(ok) == 1

# reverse for s3
print(''.join(t3[ok[0][p]] for p in range(16)))
```

Running this we get the string: `udiditgjwowsuper`


### Flag

```DUCTF{udiditgjwowsuper}```