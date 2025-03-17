---
description: 'category: reverse engineering'
---

# Hunting License

**Platform:** HTB

**Difficulty:&#x20;**<mark style="color:green;">**very easy**</mark>

\
As usual, I started with the file command to see the info about this ELF.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Since the file was not stripped, I used the strings to see some possible info about flags or password

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>



Since I would like to see how and where were the functions called, I opened ghidra. Here, I noticed the questionnaire begins with a "y" response, followed by the call of exam().

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Into the exam function, we can get the first password we need which was hardcoded as "PasswordNumeroUno".

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

First password: PasswordNumeroUno



Then, the main function calls the reverse function with the following parameters: \0, t and 0xb - lenght of the password. With that we gotta se whats inside this "t".&#x20;

reverse(local\_1c,t,0xb);

By converting the bytes of information inside t, manually or with hep from ghidra, we get

0 w T d r 0 w s s 4 P

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

By reversing this with a max length of 0x11 bytes we get the second password.

Second password: P4ssw0rdTw0



Then the main function calls xor with the following parameters : null string, t2, 0x11 and 0x13. From that, we need to see what's inside t2.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Taking into account how the reverse function is coded

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Its possible to make a simple script to get the third password.

```python
p2 = [0x47, 0x7b, 0x7a, 0x61, 0x77, 0x52, 0x7d, 0x77, 0x55, 0x7a, 0x7d, 0x72, 0x7f, 0x32, 0x32, 0x32, 0x13]
p3 = 0x11
p4 = 0x13

password = bytearray(p3)

for i in range(p3):
    password[i] = p2[i] ^ p4
print(password)
```

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Third password: ThirdAndFinal!!!



From here, we just gotta **nc** this and use the previous info we got.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>
