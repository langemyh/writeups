# XtraORdinary [250 pts]

**Category:** Cryptography

**Solves:** 64

## Description
>Check out my new, never-before-seen method of encryption! I totally invented it myself. I added so many for loops that I don't even know what it does. It's extraordinarily secure!
> 
> CHALLENGE ENDPOINTS
> 
> Download output.txt - https://artifacts.picoctf.net/picoMini+by+redpwn/Cryptography/xtraordinary/output.txt
>
> Download encrypt.py - https://artifacts.picoctf.net/picoMini+by+redpwn/Cryptography/xtraordinary/encrypt.py
>
> AUTHOR: BOOLEAN

**Hint**
* (None)

## Solution
XOR is pretty reversible, given that:
`a ^ b ^ b = a`

So, no matter how many times you run xor, it will just be two possible outcomes: Either you get a new value or reverse to the original. With this in mind, one may reverse the massive number of xoring going on, and end up with the original cipher. The original key may be found with bruteforce, since we know the start of the flag `picoCTF`:

```python
#!/usr/bin/env python3
from random import randint
from pwn import *
import string

with open('output.txt', 'r') as f:
    glaf = bytes.fromhex(f.read())

random_strs = [
    b'my encryption method',
    b'is absolutely impenetrable',
    b'and you will never',
    b'ever',
    b'ever',
    b'ever',
    b'ever',
    b'ever',
    b'ever',
    b'break it'
]
def encrypt(ptxt, key):
    ctxt = b''
    for i in range(len(ptxt)):
        a = ptxt[i]
        b = key[i % len(key)]
        ctxt += bytes([a ^ b])
    return ctxt

# Finding all combinations of random_strs
xors = []
length = len(random_strs)
for i in range(2**length):
    p = f'{i:0{length}b}'
    tmp = []
    for i in range(len(p)):
        if p[i] == '1':
            tmp.append(random_strs[i])
    xors.append(tmp)

# Finding possible original ciphertexts
def find_ctxts(ctxt, xors):
    ctxts = []
    for x in xors:
        for key in x:
            ctxt = encrypt(ctxt, key)
            ctxts.append(ctxt)
    return ctxts

# Finding possible keys
def find_key(ctxt):
    flag = b'picoCTF'
    key = ''
    index = 0
    for x in ctxt[:len(flag)]:
        for l in string.printable:
            ptxt = bytes([x ^ ord(l)])
            if ptxt[0] == flag[index]:
                key += l
                break
        index += 1
    if flag.isascii():
        return key
    return False

# Finding actual original ciphertext and key
for ctxt in find_ctxts(glaf, xors):
    key = find_key(ctxt)
    if key:
        flag = encrypt(ctxt, key.encode())
        if flag.endswith(b'}'):
            log.success(f'Flag: {flag.decode()} with {key}')
            exit()
```

Here the `encrypt`function is just reused from the given source, and so is the list with `random_strs`. To optimalize the code, it's possible to remove the duplicate `ever`items from the list.

To find every possible combination of `random_strs`, I just used binary numbers converted to strings and looped through "turning" the `random_strs` "on" and "off". Guessing there's prettier and more efficient ways of doing that bit.

### Flag
> picoCTF{w41t_s0_1_d1dnt_1nv3nt_x0r???}
