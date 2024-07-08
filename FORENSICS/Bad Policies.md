# Bad Policies

Category: Forensics (Beginner)

Points: 100

Solves: 386

>Looks like the attacker managed to access the rebels Domain Controller.
>
>Can you figure out how they got access after pulling these artifacts from one of our Outpost machines?

### Solution

Opening up the folder, it seems like we are given a bunch of users on a Windows system. Scrolling through all the files, there isn't anything interesting besides a few `.pol` files and a `Groups.xml` which contains a weird `cpassword`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}">
<User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="Backup" image="2" changed="2024-06-12 14:26:50" uid="{CE475804-94EA-4C12-8B2E-2B3FFF1A05C4}">
<Properties action="U" newName="" fullName="" description="" cpassword="B+iL/dnbBHSlVf66R8HOuAiGHAtFOVLZwXu0FYf+jQ6553UUgGNwSZucgdz98klzBuFqKtTpO1bRZIsrF8b4Hu5n6KccA7SBWlbLBWnLXAkPquHFwdC70HXBcRlz38q2" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="Backup"/>
</User>
</Groups>
```

Doing a bit of research, `.pol` files are used in Windows to store Group Policy Object (GPO) settings. These settings may contain sensitive user information as the Group Policy Preferences (GPP) feature allowed administrators to set passwords for local accounts or services. These passwords are stored in an XML format and obfuscated with a known encryption method (Base64 encode after AES ecrypt with a standardized key).

We could write a script to decode with the standardized key or we can use tools such as [gpprefdecrypt](https://github.com/reider-roque/pentest-tools/tree/master/password-cracking/gpprefdecrypt) to decode these cpasswords for us!

```shell
% ruby gpprefdecrypt.rb "B+iL/dnbBHSlVf66R8HOuAiGHAtFOVLZwXu0FYf+jQ6553UUgGNwSZucgdz98klzBuFqKtTpO1bRZIsrF8b4Hu5n6KccA7SBWlbLBWnLXAkPquHFwdC70HXBcRlz38q2"
DUCTF{D0n7_Us3_P4s5w0rds_1n_Gr0up_P0l1cy}
```


### Flag

```DUCTF{D0n7_Us3_P4s5w0rds_1n_Gr0up_P0l1cy}```