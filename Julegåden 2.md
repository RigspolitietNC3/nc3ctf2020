## Julegåden 2
<hr>

I bunder af filen ses information omkring KEY? IV og DATA. KEY oplyser at den skal sættes ind to gange.
IV er sat til 00'er og data er en længere streng på noget der er krypteret.
Ved åbning af databasen kan der ses en række i tabellen "notes".

Ved base64 af data i kolonnen "Windows Position" fås hintet "Genskab originale 4 første bytes for row2".

Nede i filen findes en b-tree page. De starter med "0D". Vores er på position \x1000 i filen. 
Man ved så at der på offset 3 og 2 byte frem er information om at der er data om antal rækker.
Der ses tallet 2, der betyder der er information om 2 rækker i denne b-tree page.

På offset 8 og 2 bytes henviser til start for første række. Offset 0A og 2 bytes henviser til start for anden række.
Informationen fra 0A og 2 bytes er 0CA6. Dette betyder vi skal starte på offset 0CA6 i filen.
På positonen finder vi nedenstånde:

```text
00 00 03 5A 8B 1F 45 08 08 00 19 08 00 00 00 00 00 00 55 55 06 00 06 5C 69 64 3D 63 61 65 35 63 39 38 65 2D 62 37 37 64 2D
```

Vi ved at headeren på en b-tree leaf cell er som følgende:

- Payload size (størrelse på al data)
- En integer nøgle, aka "rowid"
- Headersize for kolonne data info (kolonneheader)

Ved analyse af row1's header kan en ide om størrelsen på kolonneheader fremkomme. Man ser at den er 14 for row1.
Ved analyse af data for row1 ses det, at data i første række er tekst og den starter med "\". 
Der findes tegnet "\" for række 2, og går 14 bytes tilbage. På den 14. plads ses 5A.
5A skiftes ud med 14 og man har nu:

```text
00 00 03 14
```

Vi ved at det er række 2 og byten før kolonneheader ændres til 02. Man har nu:

```text
00 00 02 14
```

De første 2 bytes er payload size, størrelsen på det totale data for data om rækken. Disse 2 bytes og rowid tælles ikke med i den totale payload.
Størrelsen kan findes ved at finde slutningen af data formatet for sidste kolonne, eller i dette tilfælde slutningen af b-tree leaf pagen. 
B-tree leaf pagen slutter på 1FFF. En b-tree leaf page er generelt 4096 bytes lang.

Fra bunden og op til kolonneheader start er der 357 bytes. Dette er i binært:

```text
‭00000011 01010111‬
```

Vi ved så at den mest betydningsfulde bit bruges til at indikere om næste byte skal læses med, hvis payload overstiger 127 tegn (\x7F).
Dette er tilfældet og der indsættes en 0 bit foran de syv sidste bits i den sidste byte. 

Sidste byte er så:

```text
01010111
```

Første byte er så:

```text
00000110
```

Første byte mangler nu indikationen for at næste byte skal læses med for payloadsize og der tilføjes 1 i den mest betydningsfulde bit.
Første byte bliver så:

```text
1000 0110
```

Sammensat giver det:

```text
1000 0110 0101 0111
```

der er:

```text
\x8657
```

8657 er den totale payload size og det sættes ind og vi har headeren for celledata genskabt:

```text
86 57 02 14
```

Åbnes databasefilen kan rækken nu ses i databasen. I WindowsPostion er en ny base64 streng der dekodes til følgende: 

```text
De er blowfishnøgle
```

Nu kan koden i bunden af filen dekrypteres da man har nøglen 8657 der skal indsættes to gange altså 86578657.

```text
blowfishnøgle
86570214 86570214

d20b896149c1b535636af8fca8f8111853fbf20570e304141d668b467aab1619e313b8482d062dbb2c943034f7723b52
```

Ovenstående dekrypteres til flaget:

<details>
<summary>Spoiler alert - Flaget:</summary>
NC3{Hvad_skal_han_skabe_med_de_materialer}
</details>
