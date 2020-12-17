## Julegåden 3
<hr>

Der er lidt "red herring" i filen for at undgå at man bare bruger "strings".

Først man finde ud af at alle FILE0 er byttet til FlLE0. Bemærk stort "I" er sat til lille "l".

Derefter skal MFT repareres. MFT start findes i bootsector (0) på position x30 (decimal 48) og 8 bytes frem (405). 
Denne værdi skal ganges med 8 og man har start sector(3240). Gange 512 og man har offset(1658880 eller \x195000).

Reparation kan man ved at finde MFTMirror og tage de to første sektorer derfra og sætte dem ind på de 2 første sektores plads af MFTen.
De er nulled ud så de skal overskrives med det rigtige data.

MFT data findes på position \x2000, MFTMirror starter her, og der tages de første \x400 bytes (1024 decimal)
Nu kan man se hele filsystemet og i det normale data er der intet at komme efter. Kun hints.

I skraldespanden vil man kunne finde en fil for sig selv der ligger et sted den ikke burde ligge. Den hedder "Alternativ.lnk".
Filen er tom, men selve navnet hinter til at man skal finde alternative datastrømme (data streams) for filen.

Filen har 3 alternative datastrømme to gange. De er navngivet del1, del2, del3 og del1.txt, del2.txt, del3.txt.

I disse dele er der hex værdier i tekstform. Sammensættes disse i en fil vil der komme et billede frem.

Det er det samme data der vises i en teksteditor i både strømmen del1 og del1.txt filen. 

I billedet ses flaget:

<details>
<summary>Spoiler alert - Flaget:</summary>
NC3{alternative_veje_til_flaget}
</details>
