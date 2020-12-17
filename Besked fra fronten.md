## Besked fra fronten
<hr>>

```text
nc3{JiM2ODsmIzgyOyYjNzM7JiM3NjsmIzc2OyYjNjk7JiM3ODsmIzczOyYjODM7JiM4MzsmIzY5OyYjODM7JiM0ODsmIzEwODsmIzc3OyYjODQ7JiMxMjE7JiM2NjsmIzY2OyYjODQ7JiM2OTsmIzkwOyYjNjY7JiM3MzsmIzY5OyYjMTIwOyYjNzQ7JiM4NDsmIzg1OyYjNjk7JiMxMDM7JiM4MjsmIzY5OyYjODY7JiM3NzsmIzg2OyYjNjk7JiM2OTsmIzEwMzsmIzgyOyYjODU7JiM3ODsmIzczOyYjODQ7JiMxMjE7JiM2NjsmIzgzOyYjODQ7JiM0ODsmIzQ5OyYjNzA7JiM4NDsmIzEyMTsmIzY2OyYjNzE7JiM4NjsmIzg1OyYjMTIwOyYjNzc7JiM3MzsmIzcwOyYjNzg7JiM4NTsmIzg0OyYjNDk7JiM2NTsmIzEwMzsmIzgxOyYjODU7JiMxMjA7JiM3MTsmIzgxOyYjODM7JiM2NjsmIzc3OyYjODM7JiM4NTsmIzQ5OyYjNjY7JiM3MzsmIzY5OyYjMTIwOyYjNzQ7JiM4NDsmIzg1OyYjNjk7JiMxMDM7JiM4MjsmIzg1OyYjNzg7JiM3MzsmIzg0OyYjMTIxOyYjNjY7JiM3MTsmIzg2OyYjODU7JiMxMjA7JiM3NzsmIzczOyYjNzA7JiM3ODsmIzg1OyYjODQ7JiM0OTsmIzY1OyYjMTAzOyYjODU7JiMxMDc7JiM1NzsmIzc4OyYjODI7JiM4NTsmIzU2OyYjMTAzOyYjODE7JiM4NTsmIzEyMDsmIzcxOyYjODE7JiM4MzsmIzY2OyYjODQ7JiM4MzsmIzg1OyYjODY7JiM4MzsmIzg1OyYjMTA3OyYjNjk7JiMxMDM7JiM4MzsmIzQ4OyYjMTA4OyYjNzc7JiM4NDsmIzEyMTsmIzY2OyYjNzA7JiM4MTsmIzQ4OyYjMTA0OyYjODA7JiM3MzsmIzY5OyYjOTA7JiM4NjsmIzg0OyYjNjk7JiMxMTk7JiMxMDM7JiM4NTsmIzQ5OyYjODI7JiM4MDsmIzg1OyYjNjc7JiM2NjsmIzc5OyYjODQ7JiM0OTsmIzkwOyYjNzA7JiM4NDsmIzg1OyYjNzQ7JiM3MDsmIzg1OyYjMTA1OyYjNjY7JiM3NDsmIzg0OyYjMTA3OyYjODI7JiM3NDsmIzgxOyYjODM7JiM2NjsmIzg0OyYjODM7JiM4NTsmIzg2OyYjODM7JiM4NTsmIzEwNzsmIzY5OyYjMTAzOyYjODU7JiM0ODsmIzEwODsmIzcwOyYjODU7JiMxMDg7JiM3NDsmIzY2OyYjNzM7JiM2OTsmIzg2OyYjNjg7JiM4MzsmIzY5OyYjNTY7JiMxMDM7JiM4NTsmIzEwNzsmIzU3OyYjNzg7JiM4MjsmIzg1OyYjNTY7JiMxMDM7}
```

Hvis man har været fast deltager i NC3CTF de senere år, ved man at vi godt kan lide [CyberChef](https://gchq.github.io/CyberChef/), og at der nok vil komme en eller flere opgaver, der kan løses via dette smarte værktøj. Dette år er selvfølgelig ingen undtagelse, og der ses hurtigt at JiM2.. osv. er Base64, der nemt dekodes:

```text
&#68;&#82;&#73;&#76;&#76;&#69;&#78;&#73;&#83;&#83;&#69;&#83;&#48;&#108;&#77;&#84;&#121;&#66;&#66;&#84;&#69;&#90;&#66;&#73;&#69;&#120;&#74;&#84;&#85;&#69;&#103;&#82;&#69;&#86;&#77;&#86;&#69;&#69;&#103;&#82;&#85;&#78;&#73;&#84;&#121;&#66;&#83;&#84;&#48;&#49;&#70;&#84;&#121;&#66;&#71;&#86;&#85;&#120;&#77;&#73;&#70;&#78;&#85;&#84;&#49;&#65;&#103;&#81;&#85;&#120;&#71;&#81;&#83;&#66;&#77;&#83;&#85;&#49;&#66;&#73;&#69;&#120;&#74;&#84;&#85;&#69;&#103;&#82;&#85;&#78;&#73;&#84;&#121;&#66;&#71;&#86;&#85;&#120;&#77;&#73;&#70;&#78;&#85;&#84;&#49;&#65;&#103;&#85;&#107;&#57;&#78;&#82;&#85;&#56;&#103;&#81;&#85;&#120;&#71;&#81;&#83;&#66;&#84;&#83;&#85;&#86;&#83;&#85;&#107;&#69;&#103;&#83;&#48;&#108;&#77;&#84;&#121;&#66;&#70;&#81;&#48;&#104;&#80;&#73;&#69;&#90;&#86;&#84;&#69;&#119;&#103;&#85;&#49;&#82;&#80;&#85;&#67;&#66;&#79;&#84;&#49;&#90;&#70;&#84;&#85;&#74;&#70;&#85;&#105;&#66;&#74;&#84;&#107;&#82;&#74;&#81;&#83;&#66;&#84;&#83;&#85;&#86;&#83;&#85;&#107;&#69;&#103;&#85;&#48;&#108;&#70;&#85;&#108;&#74;&#66;&#73;&#69;&#86;&#68;&#83;&#69;&#56;&#103;&#85;&#107;&#57;&#78;&#82;&#85;&#56;&#103;
```

Dette er HTML Entity, og bruges til at skrive ASCII værdier i HTML:

```text
DRILLENISSES0lMTyBBTEZBIExJTUEgREVMVEEgRUNITyBST01FTyBGVUxMIFNUT1AgQUxGQSBMSU1BIExJTUEgRUNITyBGVUxMIFNUT1AgUk9NRU8gQUxGQSBTSUVSUkEgS0lMTyBFQ0hPIEZVTEwgU1RPUCBOT1ZFTUJFUiBJTkRJQSBTSUVSUkEgU0lFUlJBIEVDSE8gUk9NRU8g
```

Hov, nu kan Cyber Chef ikke dekode mere. Men man kan se at en DRILLENISSE har sneget sig ind, så når denne fjernes (et hint til dette kom hurtigt ud), får vi en ren Base64, der dekoder til:

```text
KILO ALFA LIMA DELTA ECHO ROMEO FULL STOP ALFA LIMA LIMA ECHO FULL STOP ROMEO ALFA SIERRA KILO ECHO FULL STOP NOVEMBER INDIA SIERRA SIERRA ECHO ROMEO 
```

Hvis man søger lidt på nettet, finder man ["NATO's fonetiske alfabet"](https://da.wikipedia.org/wiki/NATO%27s_fonetiske_alfabet), der viser at man bare tager første bogstav i hvert ord:

```text
kalder.alle.raske.nisser
```

<details>
<summary>Spoiler alert - Flaget:</summary>
nc3{kalder.alle.raske.nisser}
</details>
