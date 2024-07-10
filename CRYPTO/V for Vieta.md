# V for Vieta

Category: Crypto (Easy)

Points: 228

Solves: 55

>Have this on loop while you solve :bop:
>
>https://www.youtube.com/watch?v=jsbVtNa5-hg


### Script

Solved by ezraft, some extreme black magic trickery:

![Sol](/images/VforVietaEzraft.png)

```py
# sage
a, b, u = var('a, b, u')
ANS = []
a = 2 * u**3 - u
for x in range(150):
    qe = ((a+b)**2 - a * b == (2 * a * b + 1) * (u**2));v = solve(qe, b); ANS.append(v[1].rhs()); a=v[1].rhs(); x

with open("Downloads/pol_array.txt", "w") as f:
    f.write("u\n")
    f.write("2*u**3 - u\n")
    for expr in ANS:
        f.write(str(expr).replace("^","**"))
        f.write("\n")
```



```py
# python
from pwn import *
import json
target = remote('2024.ductf.dev', 30018)
MAX = 2048
level = 2048

def isqrt(n):
    x = n
    y = (x + 1) // 2
    while y < x:
        x = y
        y = (x + n // x) // 2
    return x
def json_recv():
    line = target.recvline()
    print(line)
    return json.loads(line.decode())

def json_send(hsh):
    request = json.dumps(hsh).encode()
    print(request)
    target.sendline(request)

f = open("pol_array.txt", "r")
a = []
for x in f:
    a.append(x)
print(a[MAX-level])
print(target.recvline())

while True:

    received = json_recv()
    print(received)
    if "flag" in received.keys():
        print(received["flag"])
        break
    print("Received k: ")
    print(received["k"])
    print("Received level: ")
    print(received["level"])
    u = isqrt(received["k"])
    level = received["level"]
    to_send = {
        "a": eval(a[min(len(a) - 2, MAX//level)]),
        "b": eval(a[min(len(a) - 2, MAX//level) + 1])
    }
    print(len(bin(to_send["a"])))
    json_send(to_send)
```


### Flag

```DUCTF{jump1n6_4nd_fl1pp1n6_up_7h3_hyp3r8011c_14dd3r}```
