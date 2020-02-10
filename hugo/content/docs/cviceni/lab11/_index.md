---
title: "B. NOVA Malloc"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Uživatelský paměťový alokátor pro OS NOVA
Na tomto cvičení si zkusíte implementovat alokátor dynamické paměti, který bude
využívat systémové volání brk z minulého cvičení.

## Domácí příprava
Pro toto cvičení budete potřebovat:
- hotovou minulou úlohu
- vědět co je alokátor dynamické paměti (memory allocator) a způsoby
  implementace, viz:
  - [7. přednášku][l7]
  - http://arjunsreedharan.org/post/148675821737/write-a-simple-memory-allocator
- [spojové seznamy][1]
- rozumět programování v C/C++
- připustit existenci volání [malloc a free][2]

[l7]: /docs/prednasky/pdf/lekce07.pdf
[1]: https://en.wikipedia.org/wiki/Linked_list
[2]: http://man7.org/linux/man-pages/man3/malloc.3.html

# Zadání úlohy
Implementujte jednoduchý alokátor paměti pro uživatelský prostor OS NOVA. Váš
alokátor bude implementovat funkce `my_malloc` a `my_free` s následujícími
prototypy:
```C
extern "C" void *my_malloc(unsigned int size);
extern "C" int my_free(void *address);
```

Tyto dvě řádky vložte na začátek vašeho souboru `user/mem_alloc.cc`. `extern
"C"` zajišťuje, aby funkce šla volat jak z programů v C++, tak v C. 

Od alokátoru budou očekávány následující vlastnosti:
- Bude schopen alokovat a uvolnit paměť.
- Při kompilaci nejsou generována žádná varování.
- Alokátor bude možné používat po přilinkování k aplikaci `user/hello.c` ze
  zdrojových kódů OS NOVA.
- `my_malloc` alokuje paměť velikosti `size` a vrací adresu začátku alokované
  paměti. Pokud paměť požadované velikosti nelze alokovat, `my_malloc` vrací
  `0`.
- `my_free` uvolní paměť alokovanou funkcí `my_malloc`. Parametr address je
  hodnota dříve vrácená funkcí `my_malloc`. Pokud je paměť úspěšně uvolněna,
  funkce vrátí `0`, v opačném případě je vrácen nenulový kód chyby, který si
  můžete nadefinovat jak chcete.
- Pokud je `my_free` zavolána na již uvolněnou paměť nebo na paměť, která
  nebyla alokována voláním `my_malloc`, jedná se o chybu. Můžete předpokládat,
  že program používající váš alokátor nemodifikuje jinou paměť na heapu než tu,
  vrácenou funkcí `my_malloc`.
- Bude používat systémové volání `brk` pro získání paměti pro alokaci.
- Paměťová režie alokátoru pro 16bytové alokace bude maximálně 100%. Tedy pokud
  např. zavolám 1024krát `my_malloc(16)`, alokátor si od jádra vyžádá voláním
  `brk` maximálně 32 KiB paměti.
- Paměť uvolněná voláním `my_free` půjde znovu alokovat.
- Paměť alokovaná funkcí `my_malloc` bude přístupná minimálně do doby zavolání
  `my_free`.
- Žádná část paměti alokované funkcí `my_malloc` nebude znovu alokována dříve,
  než bude zavoláno odpovídající `my_free`.

Nepovinně (pro plný počet bodů) budou navíc vyžadovány následující vlastnosti:
- Po uvolnění menších sousedních bloků bude možné ve stejné oblasti alokovat
  jeden velký souvislý blok.

**Co se odevzdává**: Archiv obsahující vaši implementaci v souboru
`user/mem_alloc.cc` a soubor `kern/src/ec_syscall.cc` z minulého cvičení.
Můžete ho vytvořit následující řádkou z kořenového adresáře NOVY (nebo si
můžete upravit pravidlo `hw11` v `Makefilu`):
```bash
zip hw11.zip kern/src/ec_syscall.cc user/mem_alloc.cc
```
