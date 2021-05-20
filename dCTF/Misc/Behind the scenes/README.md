# Behind the scenes [400 pts]

**Category:** Misc
**Solves:** 41

## Description
>Looks like a behind-the-scenes screenshot of the process behind this challenge was leaked. Can you use it to your advantage? (flag does not contain numbers)

**Hint**
* -

## Solution
Denne skjønte jeg ingenting av. Brukte en annen writeup, og fant ut at det finnes verktøy for slike ting (også!):

https://github.com/beurtschipper/Depix

Selve bildet viser at det er snakk om Windows 10 og et passord i Notepad er forsøkt sensurert. Klippet ut bare passordet:
![[bts.blurred.png]]
```zsh
python Depix/depix.py -p bts.blurred.png -s Depix/images/searchimages/debruinseq\_notepad\_Windows10\_close.png -o bts.unblurred.png
```
![[bts.unblurred.png]]

Dette gav ikke stort mer mening, men writeup som jeg lest hadde følgende:
> Then, an admin on Discord had mentioned that there some participants were retrieving incorrect outputs when following the intended solution for the challenge. I gave them a ping to see if my output was falling into that category.. it was!

![[depix.png]]

Og der er det faktisk mulig å se hva det står:
### Flag
> dctf{GotAnyMorePixels}
