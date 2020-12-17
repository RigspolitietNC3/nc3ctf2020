## DLLosaurus
<hr>

Ved reversing findes hints.
Det første er:

```text
Sie hat es angeblich in 9 Teile geteilt (Hun har delt det i 9 dele)
```

```text
Ich habe *MAL* etwas in einer exception versteckt (Jeg har *gange* gemt noget i en exception)
```

```text
Zuerst habe ich Nissa gefragt, was sie von Caesar Verschlüsselung hält, aber sie hat nur mit -2 geantwortet.
Her hintes at der bruges caeser(ROT13 eller ROT47) med -2 i parameter(Amount).
```

I main ses en position og en del af det krypterede. Regnes positionen ud ses det at det er 3. position. Vær opmærksom på escape karateren \ da der fremkommer \\ men kun en skal bruges i Base64 efterfølgende.

En metode hedder Das Ende (slutningen), der fortæller at det er slutningen af den krypterede streng.

Samles alle dele i korrekt position fås:

```text
WFuYiQES5N6Zy\rnMkFkzU7xQ5cgfsCuK|oCm{D3xDwDWfZLmTQFV5O64OOQ[y:9sixTDys
```

ROT47 med parameter -2, som er hint i teksten. 

base62, der bliver hintet i main ved at der står "62".

Der fremkommer nu hexværdier:

```text
4e 43 33 7b 69 63 68 5f 62 69 6e 5f 64 61 62 65 69 7d
```

Disse koverteres til flaget:

<details>
<summary>Spoiler alert - Flaget:</summary>
NC3{ich_bin_dabei}
</details>
