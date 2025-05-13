---
description: 'category: misc'
---

# Permissions

**Platform:** PICOCTF

**Difficulty:&#x20;**<mark style="color:yellow;">**medium**</mark>

***

### Description

Can you read files in the root file?

Additional details will be available after launching your challenge instance.

### Resolution

Based on the hint "What permissions do you have?", I checked what the current user is allowed to run with `sudo`.

<figure><img src=".gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

We have sudo access to the vi text editor, meaning we can run vi as root on any file.

From this we can edit `/usr/bin/vi` with `sudo`&#x20;

<figure><img src=".gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

We can execute the following script in command mode using vi. To enter command mode in the vi text editor, we press the Esc key.

Now we got root.

<figure><img src=".gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

It is now possible to search for the challenge folder containing the flag, which is located in the home directory, and read the flag.

