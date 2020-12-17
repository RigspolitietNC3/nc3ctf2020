## Den Røde Tråd II
<hr>

En klassisk Windows .EXE [crackme](https://en.wikipedia.org/wiki/Crackme), der skal reverses.
I år prøvede vi at sætte sværhedsgraden ned i forhold til sidste år, da det er sjovest hvis opgaverne bliver løst. Alligevel er denne opgave ment som den sværeste indenfor reversing, så der er noget for dem, der er med på en god udfordring.

Den Røde Tråd skal forstås på flere måder:

1. Et rød tråd, der fører en gennem opgaven
2. Rødt bånd = Jul
3. En masse CPU tråde, der samarbejder på én opgave

Opgaven fortæller om man har løst den, ved at køre i meget kort tid. Hvis input er forkert, hænger den, og bruger næsten 100% CPU.

Implementerer:

* Pseudo VM udfra custom Jobs/Tasks, der vil udføre diverse operationer på input
* Custom fetching af Windows funktioner via PEB - som "__forceinline" for at forhindre at man bare sætter et breakpoint på denne funktion, og dumper dem herfra. Ingen værdier bliver cachet - dette for at forhindre simple XREFs. Navnet på den samme funktion er derfor også krypteret med forskellige input for hvert brug af funktionen.
* Der bruges atomic instruktioner fra flere tråde på samme globale hash. Denne hash tjekkes på for at se om alle trådene er færdige eller ej. Easter-egg: XOR'es endeligt med 0x54486557695a5244 ("THeWiZRD").
* Hvis det vurderes at input er forkert, vil crackme'en begynde at slette data i dens endelige array.
* Easter-egg: Tjek compile-tiden: 1608840000 (Thu 24 December 2020 20:00:00 UTC)

Vi vil ikke gennemgå selve disassembly'en her, men mere fortælle overordnet om hvad programmet gør. Til at læse disassembly'en kan dog f.eks. bruges IDA Pro, der inkluderer en Debugger i Freeware-versionen. Denne kan evt. kombineres med decompileren i Ghidra.

Hvis opgaven startes via en debugger, vil den hurtigt lukke ned. Man kan prøve at sætte et breakpoint i "main", men dette bliver aldrig eksekveret. Dette sker, da opgaven indeholder et TLS-callback, der eksekveres inden main.

Dette er et lille nik til sidste års udgave af opgaven, der indeholdt utallige anti-debug, anti-VM og anti-dump teknikker for at modstå analyse. I år nøjes vi dog med dette lille trick, der alligevel gør at man hele tiden har i baghovedet, at der kunne være endnu et anti-* trick.

I dette konkrete TLS callback er der et tjek på om man kører i en debugger. Dette tjek er simpelt, og kan nemt patches ud. Men det leder én til en dekrypteringsalgoritme, der er ment som en lille opstart, men ikke har nogen betydning for den egentlige opgave. Denne lille troll, giver:

```text
nc3 ... Sæt stemningen. Vi foreslår: rebrand.ly/q2wq6 ... eller: rebrand.ly/9wsmj ... :)
```

De 2 links fører til Rick Ashley og Gulddreng (sidstnævnte er vores jule-svar på en [RickRoll](https://da.wikipedia.org/wiki/Rickrolling)).


Den egentlige opgave starter i main, hvor der først startes 64 tråde (hint til vores Commodore 64 opgave i år), der hver lægger jobs i køen til deres worker tråd. Disse jobs udfører linært, og kan være:


Der er en stor array, men kun hver 640'ne byte bliver brugt (tallet 64 igen) - de øvrige bytes er random. De brugte bytes er en klassisk check-array, dvs. hvis vi krypterer flaget, så får vi disse bytes. Her kan man så vælge at brute force sig til dette check-array, eller man kan rent faktisk reverse algoritmen. Lad os gøre det sidste:



Find bytes med:


0x5c, 0x7, 0xb2, 0x39, 0xe9, 0x1, 0xb, 0x21, 0xa5, 0x14 :

```text
.data:00000001400229A0 unk_1400229A0   db  5Ch ; \             ; DATA XREF: sub_1400015A0↑o
.data:00000001400229A0                                         ; sub_140001830+28↑o
.data:00000001400229A1                 db    7
.data:00000001400229A2                 db 0B2h ; ²
.data:00000001400229A3                 db  39h ; 9
.data:00000001400229A4                 db 0E9h ; é
.data:00000001400229A5                 db    1
.data:00000001400229A6                 db  0Bh
.data:00000001400229A7                 db  21h ; !
.data:00000001400229A8                 db 0A5h ; ¥
...
.data:000000014002C9D8                 db 0AAh ; ª
.data:000000014002C9D9                 db 0E9h ; é
.data:000000014002C9DA                 db  1Dh
.data:000000014002C9DB                 db  8Dh
.data:000000014002C9DC                 db  1Dh
.data:000000014002C9DD                 db  4Ah ; J
.data:000000014002C9DE                 db  7Ah ; z
.data:000000014002C9DF                 db  1Fh
```

Brug IDA Pro's export som "raw" til en fil, der bliver 41,024 bytes - kald den f.eks.:

```text
nc3ctf2020_den_røde_tråd2_exported_data.bin
```

Det følgende script vil dekode denne fil. Vi har inkluderet enkodningen også, da denne typisk først implementeres, så man kan tjekke sin Python-implementation op imod target filens implementation:


```python
def XorLists(input1, input2) :
    l1 = len(input1)
    l2 = len(input2)
    l = max(l1, l2)
    r = []
    for i in range(0, l) :
        c1 = input1[i % l1]
        c2 = input2[i % l2]
        c = c1 ^ c2
        r.append(c)
    return r


def UseNextAddOnListsLimited(l1) :
    l = len(l1)
    r = []
    for i in range(0, l) :
        c1 = l1[ i ]
        c2 = 0
        if i+1 < l :
            c2 = l1[i + 1]
        else:
            c2 = c1
        c = (c1 + c2) & 0xFF
        r.append(c)
    return r


def UseNextAddOnListsLimited_Rev(l1) :
    l = len(l1)
    r = l1
    r[l-1] = int(r[l-1] / 2)
    for i in range(l-2, -1, -1) :
        c1 = l1[ i ]
        c2 = l1[i + 1]
        c = (c1 - c2) & 0xFF
        r[i] = c
    return r


def rotation_value(value, rotations, widht=32):
    if int(rotations) != abs(int(rotations)):
        rotations = widht + int(rotations)
    return (int(value)<<(widht-(rotations%widht)) | (int(value)>>(rotations%widht))) & ((1<<widht)-1)


def DoMagicOnList(input) :
    l = len(input)
    r = []
    for i in range(0, l) :
        c = input[ i ]
        c = (c + 64) & 0xFF
        c = rotation_value(c, 3, 8) & 0xFF
        c = (c ^ 64) & 0xFF
        r.append( c )
    return r


def DoMagicOnList_Rev(input) :
    l = len(input)
    r = []
    for i in range(0, l) :
        c = input[ i ]
        c = (c ^ 64) & 0xFF
        c = rotation_value(c, -3, 8) & 0xFF
        c = (c - 64) & 0xFF
        r.append( c )
    return r


def EncodeSimpleNoKey(input) :
    print("EncodeSimpleNoKey:")

    r1 = DoMagicOnList(input)
    r2 = XorLists([64], r1)
    r3 = DoMagicOnList(r2)
    r4 = XorLists([128], r3)
    r5 = UseNextAddOnListsLimited(r4)
    r6 = XorLists([ord(c) for c in 'nc3ctf2020'], r5)

    return r6


def DecodeSimpleNoKey(input) :
    print("DecodeSimpleNoKey:")

    r6 = XorLists([ord(c) for c in 'nc3ctf2020'], input)
    r5 = UseNextAddOnListsLimited_Rev(r6)
    r4 = XorLists(r5, [128])
    r3 = DoMagicOnList_Rev(r4)
    r2 = XorLists(r3, [64])
    r1 = DoMagicOnList_Rev(r2)

    return r1



encoded = []
f = open("nc3ctf2020_den_røde_tråd2_exported_data.bin", "rb")
for i in range(0, 64) :
    f.read(640)
    b = f.read(1)
    encoded.append(b[0])


decoded = DecodeSimpleNoKey(encoded)
print(''.join((chr(x)) for x in decoded))

```

Det endelige flag er en 4. måde at fortolke opgavens navn på: en hyldest til Shu-bi-dua's Den Røde Tråd; til minde om [Michael Bundesen](https://da.wikipedia.org/wiki/Michael_Bundesen), der desværre faldt bort i 2020.

<details>
<summary>Spoiler alert - Flaget:</summary>
nc3{glædelig_jul_du_fulgte_den_lange_røde_tråd_rebrand.ly/hkstn}
</details>