---
description: 'category: misc'
---

# Trapped in Plain Sight 2

**Platform:** UTCTF2025

**Difficulty:&#x20;**<mark style="color:green;">**easy**</mark>

***

### Description

_Only the chosen may see._

The password is for ssh is `password`.

### Resolution

<figure><img src="../.gitbook/assets/Screenshot from 2025-03-14 23-55-57.png" alt=""><figcaption></figcaption></figure>

I connected to the ssh connection and found the flag was already in the current directory. It was strange, so I checked the file user privileges into that file.

<figure><img src="../.gitbook/assets/Screenshot from 2025-03-14 23-55-39.png" alt=""><figcaption></figcaption></figure>

`getfacl` shows which users/groups have special permissions on that file.

`secretuser`can read the file

From this, my task is likely to become `secretuser`.

I tried to see if there’s a `setuid` root binary that could drop privileges to read `flag.txt`

Since that info was not that useful, I looked for passwords in config files with the command

```r
grep -Ri "secretuser" /etc 2>/dev/null
grep -Ri "secretuser" /home 2>/dev/null
```

From this, we can see the password was set as “hunter2”.

After making `sudo` as `secretuser` with the previous password, we can now read the file `flag.txt`.

<figure><img src="../.gitbook/assets/Screenshot from 2025-03-14 23-55-19.png" alt=""><figcaption></figcaption></figure>
