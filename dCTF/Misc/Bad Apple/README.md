# Bad Apple [200 pts]

**Category:** Misc
**Solves:** 159

## Description
>Someone stumbled upon this file in a secure server. What could it mean?

**Hint**
* Did you even listen to the song?

## Solution
Denne var morsom! Hvis man hører gjennom fila, så er det et parti i midten som bare ikke passer inn eller gir mening. Sjekker ut fila i `Audacity` og finner noe som ser mistenkelig ut som en QR-kode der støyen er! Altså mellom 1:33 og 1:44.

![[qr-noise.png]]

Trengte litt noise reduction for å få QR-koden klar nok til å kunne leses av:
![[qr-reduced-noise.png]]

Scanning av QR-koden med telefonen gav flagget:

### Flag
> dctf{sp3ctr0gr4msAreCo0l}