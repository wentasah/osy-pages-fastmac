---
title: "11. NOVA Malloc"
weight: 2
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Uživatelský paměťový alokátor pro OS NOVA
Na tomto cvičení si zkusíte implementovat alokátor dynamické paměti, který bude
využívat systémové volání `brk` z minulého cvičení.

## Domácí příprava
Pro toto cvičení budete potřebovat:
- hotovou minulou úlohu
- zkontrolovat si, jestli [zdrojové kódy NOVA nebyly od minulé úlohy
  změněny][nova_log] a případně si [stáhnout aktuální verzi][nova_src]
- vědět, co je alokátor dynamické paměti (memory allocator) a způsoby
  implementace, viz:
  - [7. přednášku][l7]
  - http://arjunsreedharan.org/post/148675821737/write-a-simple-memory-allocator
- implementovat [spojové seznamy][1]
- rozumět programování v C/C++
- vědět, co dělají funkce [malloc a free][2]

[l7]: /docs/prednasky/pdf/lekce07.pdf
[1]: https://en.wikipedia.org/wiki/Linked_list
[2]: http://man7.org/linux/man-pages/man3/malloc.3.html

[nova_log]: https://gitlab.fel.cvut.cz/osy/osy.pages.fel.cvut.cz/-/commits/master/nova
[nova_src]: https://gitlab.fel.cvut.cz/osy/osy.pages.fel.cvut.cz/-/archive/master/nova.zip?path=nova


# Zadání úlohy
Implementujte jednoduchý alokátor paměti pro uživatelský prostor OS NOVA. Váš
alokátor bude implementovat funkce `my_malloc` a `my_free` s následujícími
prototypy:
```C
void *my_malloc(unsigned int size);
int my_free(void *address);
```

Funkce implementujte v souboru `user/mem_alloc.c` (C) nebo
`user/mem_alloc.cc` (C++) a na začátek souboru vložte *#include
\"[mem_alloc.h][]\"*. Makefile v adresáři user je na tento soubor
připraven a zkompiluje váš alokátor do knihovny `libc.a`. Tato
knihovna se linkuje k programům jako `hello.c`, které můžete použít
pro testování vašeho alokátoru.

[mem_alloc.h]: https://gitlab.fel.cvut.cz/osy/osy.pages.fel.cvut.cz/-/blob/master/nova/user/lib/mem_alloc.h

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
- Pokud je `my_free` zavolána na již uvolněnou paměť nebo na paměť,
  která nebyla alokována voláním `my_malloc`, jedná se o chybu a
  funkce by ji měla signalizovat návratovou hodnotou. Můžete
  předpokládat, že program používající váš alokátor nemodifikuje jinou
  paměť na heapu než tu, vrácenou funkcí `my_malloc`.
- Bude používat systémové volání `brk` pro získání paměti pro alokaci.
- Paměťová režie alokátoru pro 16bytové alokace bude maximálně 100%. Tedy pokud
  např. zavolám 1024krát `my_malloc(16)`, alokátor si od jádra vyžádá voláním
  `brk` maximálně 2×1024×16 = 32 KiB paměti.
- Paměť uvolněná voláním `my_free` půjde znovu alokovat.
- Paměť alokovaná funkcí `my_malloc` bude přístupná minimálně do doby zavolání
  odpovídajícího `my_free`.
- Žádná část paměti alokované funkcí `my_malloc` nebude znovu alokována dříve,
  než bude zavoláno odpovídající `my_free`.

Nepovinně (pro plný počet bodů) budou navíc vyžadovány následující vlastnosti:
- Po uvolnění menších sousedních bloků bude možné ve stejné oblasti alokovat
  jeden velký souvislý blok.

**Co se odevzdává**: Archiv obsahující vaši implementaci v souboru
`user/mem_alloc.c` nebo `user/mem_alloc.cc` a soubor
`kern/src/ec_syscall.cc` z minulého cvičení. Můžete ho vytvořit
následujícím příkazem spuštěným z kořenového adresáře NOVY:

```bash
make hw11
```
