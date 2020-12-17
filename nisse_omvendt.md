## nisse_omvendt.py
<hr>

```python

Ja, så skete det: Lillenisse lavede en sindsyg krypterings algoritme, og var SÅ stolt. Dejligt for ham. Men pludselig skete det, der bare ikke må ske: Hans computer crashede imens han ændrede i koden! Hans dekryptering er væk!! For altid?!




kodet_besked.bin:

8F 57 F0 B7 A2 9D AC AD AE 9B 93 98 98 93 95 96
A3 A0 9D 9E AB AE AF A8 9C AC A5 AF B3 AF C7 BC
A7 B2 B5 B3 AA A5 B3 B8 BE CA C3 C8 CB BF D0 DA




nisse_omvendt.py:

import sys

besked = sys.argv[1]

filnavn = 'kodet_besked.bin'

kodet_besked = ''
for i in range(0, len(besked)):
    c = ord(besked[i])
    nc = ord(besked[(i + 1) % len(besked)])
    c = (c ^ 64) & 0xFF
    c = (c -  1) & 0xFF
    c = (c ^  1) & 0xFF
    c = (c +  i) & 0xFF
    c = (c +  nc) & 0xFF
    kodet_besked += chr(c)

f = open(filnavn, 'w')
f.write(kodet_besked)
f.close()


f = open(filnavn, 'r')
kodet_besked = f.read()
f.close()

dekodet_besked = ''
for i in range(0, len(kodet_besked)):
    c = ord(kodet_besked[i])
$$2133248hfdsfBIIIIPP!!!123213123dddddCRASH!
````

Finten i denne opgave er at den enkelte tegn baserer sig på det næste - og når man når det sidste tegn, så baserer det sig på det første.

Her er den nemmeste måde at brute force beskeden. Vi kender jo starten af flaget:

```text
nc3{
```

og vi kender slutningen:

```text
}
```

Egentlig er kun det første og sidste bogstav, vi behøver. Men det er fint at have 3 yderligere tegn til at verificere algoritmen.

Vores løsning:

```python
import string


def EncodeChar(c, i, nextPlainTextChar) :
    c = ord(c)
    c = (c ^ 64) & 0xFF
    c = (c - 1) & 0xFF
    c = (c ^ 1) & 0xFF
    c = (c + i) & 0xFF

    nc = ord(nextPlainTextChar)
    c = (c + nc) & 0xFF

    return c


def BruteForce(kodet_besked) :
    length = len(kodet_besked)

    i = 0
    c = EncodeChar('n', i, 'c')
    assert( chr(c) == kodet_besked[i] )

    i = 1
    c = EncodeChar('c', i, '3')
    assert( chr(c) == kodet_besked[i] )

    i = 2
    c = EncodeChar('3', i, '{')
    assert( chr(c) == kodet_besked[i] )

    i = length - 1
    c = EncodeChar('}', i, 'n')
    assert( chr(c) == kodet_besked[i] )


    dekodet_besked = list('A' * length)
    dekodet_besked[0] = 'n'
    dekodet_besked[1] = 'c'
    dekodet_besked[2] = '3'
    dekodet_besked[3] = '{'
    dekodet_besked[i] = '}'

    possible = string.printable
    for i in range(length - 1, 0, -1) :
        for tryMe in possible :

            c = EncodeChar(tryMe, i, dekodet_besked[(i + 1) % length])

            if chr(c) == kodet_besked[i] :
                dekodet_besked[i] = tryMe

    return ''.join(dekodet_besked)



filnavn = 'kodet_besked.bin'

f = open(filnavn, 'r')
kodet_besked = f.read()
f.close()


dekodet_besked = BruteForce(kodet_besked)

print("dekodet_besked: " + dekodet_besked)
```

<details>
<summary>Spoiler alert - Flaget:</summary>
nc3{yessss_jeg_har_lavet_en_ubrydelig_algoritme}
</details>