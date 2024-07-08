# SAM I AM

Category: Forensics (Beginner)

Points: 100

Solves: 545

>The attacker managed to gain Domain Admin on our rebels Domain Controller! Looks like they managed to log on with an account using WMI and dumped some files.
>
>Can you reproduce how they got the Administrator's Password with the artifacts provided?

### Solution

We are given a `sam.bak` and `system.bak` backup files. Checking the real file type shows us that they are both [Windows Registry Hives](https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry-hives). After almost destroying my computer loading Windows Regedit, and unsuccesfully dumping all information into a txt file, I was able to get some help from the Admins.

![Txt Dump](/images/SAMIAMDump.png)

After doing a bit of searching, there was really helpful [article](https://www.thehacker.recipes/ad/movement/credentials/dumping/sam-and-lsa-secrets). Since we have the SAM (Security Account Manager) Hive, which stores local credentials, and the SYSTEM Hive, which has the info to decrypt the credentials stored in SAM, we can dump out all the secrets from SAM.

To do this we use Impacket's [secretsdump.py](https://github.com/fortra/impacket/blob/master/examples/secretsdump.py) which can dump SAM credrentials from SAM registry files with the System registry file.

```shell
% python3 secretsdump.py -sam samiam/sam.reg -system samiam/system.reg LOCAL
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Target system bootKey: 0xa88f47504785ba029e8fa532c4c9e27b
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:476b4dddbbffde29e739b618580adb1e:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
...
```

Cool! Now we have the `lm` and `nt` hashes for `Administrator`. Since `lm` hashes are not considered secure and deprecated, the default value is set to the null hash, so we only care about the `nt` hash: `476b4dddbbffde29e739b618580adb1e`

Assuming it's a common enough password we can try to crack it. Submitting this hash to https://crackstation.net/ quickly yields the admin password:

![Cracked Hash](/images/SAMIAMCrack.png)

`!checkerboard1`

Note: I tried using HashCat, but my 1 mil most common passwords wordlist didn't contain the final password.

Also thanks to DUCTF Admin Crem for being super helpful in guiding me :pray:

### Flag

```DUCTF{!checkerboard1}```