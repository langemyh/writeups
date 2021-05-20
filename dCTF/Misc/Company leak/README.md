# Company leak [500 pts]

**Category:** Misc
**Solves:** 54

## Description
>Someone hacked and leaked some very important data from an evil company. Find their dirty secrets and expose them!

**Hint**
* -

## Solution
Her følger det med en zip-fil:
```zsh
$ unzip leaked.zip 
Archive:  leaked.zip
  inflating: README.md               
  inflating: super_secret.zip        
```

```zsh
$ unzip super_secret.zip 
Archive:  super_secret.zip
[super_secret.zip] README.md password: 
````

Her trenger man tydeligvis et passord.

Denne trengte jeg virkelig en writeup på:
https://github.com/yulyachert/DCTF-writeups/blob/main/CompanyLeak.md

I denne brukes et verktøy som kalles for `bkcrack`:
https://github.com/kimci86/bkcrack

Som utnytter en sårbarhet om kjent plaintext. Både `leakded.zip`og `super_secret.zip`har begge to en `README.md`. Dette kan utnyttes for å klare å hente ut det vi trenger:

```zsh
$ binwalk leaked.zip 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Zip archive data, at least v2.0 to extract, compressed size: 289, uncompressed size: 481, name: README.md
328           0x148           Zip archive data, at least v2.0 to extract, compressed size: 1224, uncompressed size: 1342, name: super_secret.zip
1787          0x6FB           End of Zip archive, footer length: 22

$ binwalk super_secret.zip 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Zip archive data, encrypted at least v2.0 to extract, compressed size: 305, uncompressed size: 481, name: README.md
388           0x184           Zip archive data, encrypted at least v2.0 to extract, compressed size: 681, uncompressed size: 1347, name: top_secret.txt
1320          0x528           End of Zip archive, footer length: 22

```

Det viktigste her er: `uncompressed size: 481`.

Ziper `README.md`:
```zsh
$ zip plain.zip README.md 
  adding: README.md (deflated 39%)
````

Så begynner jobben med `bkcrack`:
```zsh
$ bkcrack -C super_secret.zip -c README.md -P plain.zip -p README.md

bkcrack 1.2.2 - 2021-04-15
[15:08:38] Z reduction using 285 bytes of known plaintext
100.0 % (285 / 285)
[15:08:39] Attack on 27016 Z values at index 7
Keys: a33fbdc6 5b49420e 6589766e
66.8 % (18048 / 27016)
[15:08:54] Keys
a33fbdc6 5b49420e 6589766e

$ bkcrack -C super_secret.zip -c README.md -k a33fbdc6 5b49420e 6589766e -d README.secret.txt
bkcrack 1.2.2 - 2021-04-15
[15:09:51] Writing deciphered data README.secret.txt (maybe compressed)
Wrote deciphered data.

$ cat README.secret.txt 
]n0
   y2t1ڥ~X,"mC}R$wc]B(TR\S&TZXrm9%OZE]*/Ke᭘Y.n+R        -@(2v{LBzB&m
'x^\i}\Uy/cg\f}Rƍ]^|DP{F%Qdwn)2aRPslS80+8&JG#
HLlep~u|/4⽲w>7C%     
```

Dette gir jo ikke mening, men `bkcrack`got your back:
```zsh
$ tools/inflate.py < flag.txt > result.txt
```

```zsh
$ cat result.txt 
I'd just like to interject for a moment. What you're referring to as Linux, is in fact, GNU/Linux, or as I've recently taken to calling it, GNU plus Linux. Linux is not an operating system unto itself, but rather another free component of a fully functioning GNU system made useful by the GNU corelibs, shell utilities and vital system components comprising a full OS as defined by POSIX.

dctf{wew_lad_y0u_aCtually_d1d_it}

Many computer users run a modified version of the GNU system every day, without realizing it. Through a peculiar turn of events, the version of GNU which is widely used today is often called "Linux", and many of its users are not aware that it is basically the GNU system, developed by the GNU Project.

There really is a Linux, and these people are using it, but it is just a part of the system they use. Linux is the kernel: the program in the system that allocates the machine's resources to the other programs that you run. The kernel is an essential part of an operating system, but useless by itself; it can only function in the context of a complete operating system. Linux is normally used in combination with the GNU operating system: the whole system is basically GNU with Linux added, or GNU/Linux. All the so-called "Linux" distributions are really distributions of GNU/Linux.

(text above is a joke)
```

### Flag
> dctf{wew_lad_y0u_aCtually_d1d_it}
