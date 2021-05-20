# Leak Spin [100 pts]

**Category:** Misc
**Solves:** 298

## Description
>We have confident insider report that one of the flags was leaked online. Can you find it?

**Hint**
* The organisers only control a limited amount of places on the internet, looks through those...

## Solution
Siden hintet hinter til at det er et begrenset antall steder Dragonsec kontrollerer, så tenkte jeg kanskje at Github var et sted å begynne:
https://github.com/DragonSecSI

Og riktig nok:
https://github.com/DragonSecSI/DCTF1-chall-leak-spin/blob/main/challenge.yml

```yml
name: "Leak Spin"
author: "Miha M."
category: Web

description: We have confident insider report that one of the flags was leaked online. Can you find it?
value: 100
type: standard

flags:
  - dctf{I_L1k3_L1evaAn_P0lkk4}

tags:
  - web

state: visible
  
version: "1.0"
```

### Flag
> dctf{I_L1k3_L1evaAn_P0lkk4}