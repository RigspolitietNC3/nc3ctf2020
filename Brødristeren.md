

## Brødristeren
<hr>

Retro når det er bedst: Commodore 64 intro med rasterbars, musik og selvmodificerende kode.


<figure class="video_container">
  <video controls="true" allowfullscreen="true" poster="nc3ctf2020_brodristeren_poster.png">
    <source src="nc3ctf2020_brodristeren_small.webm" type="video/webm">
  </video>
</figure>


Selve koden er skrevet i 6502 assembler, og er derved en kærkommen mulighed for deltagerne til enten at sætte sig ind i denne udbredte arkitektur fra fordumstid - eller måske genopfriske minder om en mere simpel tid, da CPU'er ikke var multi-tråede og havde Out-Of-Order execution.

Som de fleste C64 programmer, er dette også pakket - kaldet "crunched" dengang. Og strategien for at udpakke koden, kan derfor med fordel være at lade decruncheren køre, og derefter dumpe hele RAM:

Man kan med fordel bruge "C64 Debugger":

https://sourceforge.net/projects/c64-debugger/

Så snart introen starter, kan man dumpe al RAM til en fil med Ctrl + U. Og ja; den er ikke stor :)

Denne .BIN fil kan hentes ind i Ghidra - vælg CPU: 6502.

Tryk D for at fortælle Ghidra at det her er kode. Nu skal der læses 6502 assembly som i gamle dage!

Disse links kunne være nyttige:

Memory map:

https://sta.c64.org/cbm64mem.html

Opcodes:

http://www.6502.org/tutorials/6502opcodes.html

<br>
Vi finder noget data, der bliver opereret på:

```assembly
DAT_1a0b                                        XREF[1]:     FUN_087f:09c3(R)  
1a0b 60              ??         60h    `
1a0c 61              ??         61h    a
1a0d 60              ??         60h    `
1a0e 60              ??         60h    `
1a0f 60              ??         60h    `
1a10 61              ??         61h    a
1a11 60              ??         60h    `
1a12 61              ??         61h    a
1a13 62              ??         62h    b
1a14 63              ??         63h    c
1a15 63              ??         63h    c
1a16 63              ??         63h    c
1a17 63              ??         63h    c
1a18 62              ??         62h    b
1a19 63              ??         63h    c
1a1a 62              ??         62h    b
1a1b 64              ??         64h    d
1a1c 65              ??         65h    e
1a1d 65              ??         65h    e
1a1e 64              ??         64h    d
1a1f 64              ??         64h    d
1a20 64              ??         64h    d
1a21 65              ??         65h    e
1a22 65              ??         65h    e
1a23 66              ??         66h    f
1a24 66              ??         66h    f
1a25 67              ??         67h    g
1a26 67              ??         67h    g
1a27 67              ??         67h    g
1a28 67              ??         67h    g
1a29 66              ??         66h    f
1a2a 67              ??         67h    g
1a2b 68              ??         68h    h
1a2c 69              ??         69h    i
1a2d 69              ??         69h    i
1a2e 68              ??         68h    h
1a2f 68              ??         68h    h
1a30 68              ??         68h    h
1a31 68              ??         68h    h
1a32 68              ??         68h    h
1a33 6a              ??         6Ah    j
1a34 6a              ??         6Ah    j
1a35 6b              ??         6Bh    k
1a36 6b              ??         6Bh    k
1a37 6b              ??         6Bh    k
1a38 6b              ??         6Bh    k
1a39 6a              ??         6Ah    j
1a3a 6b              ??         6Bh    k
1a3b 6c              ??         6Ch    l
1a3c 6d              ??         6Dh    m
1a3d 6d              ??         6Dh    m
1a3e 6c              ??         6Ch    l
1a3f 6c              ??         6Ch    l
1a40 6c              ??         6Ch    l
1a41 6c              ??         6Ch    l
1a42 6c              ??         6Ch    l
```

Vi tyder algoritmen, og vender den om:

```python
import ctypes

def Decode(input):
    r = ''
    for i in range(0, len(input)) :
        c = (input[i])
        key = (i + 64) >> 2

        c = ctypes.c_uint8(c - 64)
        c = c.value ^ ctypes.c_uint8(key).value
    
        r += chr(c)
    
    return r


encodedInput = [0x60, 0x61, 0x60, 0x60, 0x60, 0x61, 0x60, 0x61, 0x62, 0x63, 0x63, 0x63, 0x63, 0x62, 0x63, 0x62, 0x64, 0x65, 0x65, 0x64, 0x64, 0x64, 0x65, 0x65, 0x66, 0x66, 0x67, 0x67, 0x67, 0x67, 0x66, 0x67, 0x68, 0x69, 0x69, 0x68, 0x68, 0x68, 0x68, 0x68, 0x6a, 0x6a, 0x6b, 0x6b, 0x6b, 0x6b, 0x6a, 0x6b, 0x6c, 0x6d, 0x6d, 0x6c, 0x6c, 0x6c, 0x6c, 0x6c]

decodedInput = Decode(encodedInput)

print decodedInput
```

==>

```
01001010011101010110110000110010011011110011001001101111
```

Dette indtastes, og vi får flaget:



<details>
<summary>Spoiler alert - Flaget:</summary>
nc3{sid_6502_vic_det_hele_spiller}
</details>
