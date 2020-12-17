## Kludetæppet
<hr>

```python
ting = ["nc3", "gjort", "{", "specielt", "}", "_", "selv", "vel", "jule", "er", "med", "gaver", "guld"]

opskrift = [1, 3, 7, 2, 6, 10, 6, 8, 2, 6, 4, 6, 11, 6, 9, 12, 5]

kludeTaeppe = ''
for tal in opskrift :
    t = ting[tal]
    kludeTaeppe += t

print kludeTaeppe
```

<hr>

En programmeringsopgave, hvor man skal finde fejlen: Der skal ændres i loopet: 

```python
    t = ting[tal - 1]
```


<details>
<summary>Spoiler alert - Flaget:</summary>
nc3{selvgjort_er_velgjort_specielt_med_julegaver}
</details>