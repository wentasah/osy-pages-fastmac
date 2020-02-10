---
title: "5. Vlákna a synchronizace"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Vlákna a synchronizace
Na tomto cvičení byste si měli vyzkoušet jak vytvořit vlákno v jazyce C s
využitím knihovny `pthread` a jak vlákna synchronizovat tak, aby nedošlo k
poškození dat se kterými se pracuje z více vláken.

## Domácí příprava
Pro toto cvičení budete potřebovat znalosti o tom
- co jsou vlákna, mutexy a [semafory][1],
- jak tyto prostředky vytvoříte v jazyce C s využitím knihovny [pthread][2],
- jak se vlákna vytvářejí a ukončují,
- jaké problémy mohou nastávat při paralelním běhů vláken a
- jak psát programy tak, aby tyto problémy nenastaly.

Potřebná teorie byla vyložena na přednášce, včetně ukázek použití funkcí
knihovny `pthread`. Před cvičením je doporučeno podívat se na manuálové stránky
potřebných funkcí, zejména
- [`pthread_create`][3], [`pthread_join`][4],
- [`pthread_mutex_init`][5], `pthread_mutex_destroy`,
  [`pthread_mutex_lock`][6], `pthread_mutex_unlock`
- [`sem_init`][7], `sem_destroy`, [`sem_wait`][8], [`sem_post`][9].


[1]: http://man7.org/linux/man-pages/man7/sem_overview.7.html
[2]: http://man7.org/linux/man-pages/man7/pthreads.7.html
[3]: http://man7.org/linux/man-pages/man3/pthread_create.3.html
[4]: http://man7.org/linux/man-pages/man3/pthread_join.3.html
[5]: http://man7.org/linux/man-pages/man3/pthread_mutex_destroy.3p.html
[6]: http://man7.org/linux/man-pages/man3/pthread_mutex_lock.3p.html
[7]: http://man7.org/linux/man-pages/man3/sem_init.3.html
[8]: http://man7.org/linux/man-pages/man3/sem_wait.3.html
[9]: http://man7.org/linux/man-pages/man3/sem_post.3.html

# Zadání úlohy
Implementujte vícevláknový program `prod-cons` splňující následující požadavky:

- V hlavním vlákně (funkce `main()`) se vytvoří jedno vlákno, kterému budeme
  říkat *producent* a dále `N` vláken *konzument*.

- Hodnota `N` bude zadávána jako parametr při spouštění programu (`argv[1]`).
  Pokud žádný parametr nebude zadán, bude `N` rovno `1`.

- Předpokládejte, že `N` bude v rozmezí 1 až počet CPU v systému
  (`sysconf(_SC_NPROCESSORS_ONLN)`). Pokud bude zadána jiná hodnota, program
  skončí s návratovým kódem `1`.

- Producent bude splňovat následující:

    - Bude číst ze standardního vstupu “příkazy” ve formě dvojic “`<X>
      <slovo>`”, kde `<X>` je celé nezáporné číslo a `<slovo>` je libovolná
      neprázdná sekvence znaků (kromě whitespace). `X` od slova může, ale
      nemusí, být odděleno bílými znaky (whitespace), jednotlivé příkazy jsou
      od sebe vždy odděleny jedním či více bílými znaky (mezera, konec řádku).
      Délka slova je omezená pouze velikostí dostupné paměti.

      K načítání příkazů doporučujeme použít následující kód:
      ```C
      int ret, x;
      char *text;
      while ((ret = scanf("%d %ms", &x, &text)) == 2) {
          ...
      }
      ```
      Direktiva `%ms` (malloc string) způsobí, že `scanf` dynamicky alokuje
      takové množství paměti, které je potřeba pro uložení načítaného slova.
      Nezapomeňte potom tuto paměť uvolnit funkcí `free()`.

    - Pokud bude zadán neplatný příkaz (tj. neodpovídající předchozímu bodu),
      program skončí s návratovým kódem `1`.

    - Pro každý přečtený příkaz dynamicky alokuje (`malloc()`, `new`, …)
      datovou strukturu, uloží do ní `X` a `slovo` a zařadí jí na konec
      spojového seznamu.

- Každý konzument bude splňovat následující:

    - Bude ze začátku spojového seznamu vybírat položky vkládané producentem.

    - Pokud v seznamu žádná položka není, bude čekat, až tam producent něco
      přidá (bez spotřeby výpočetního času, žádný polling).

    - Pokud producent přidá `P` položek, vzbudí se maximálně `P` konzumentů,
      ostatní budou dále čekat.

    - Pro každou vyzvednutou položku konzument vypíše na standardní výstup
      řetězec “`Thread n: slovo slovo slovo...`”, kde `n` je číslo konzumenta
      (pořadí vytvoření konzumenta v rozsahu `1-N`) a slovo se opakuje X-krát
      (informace od producenta). Tento řetězec bude celý na jedné řádce
      ukončené `\n`.

- Pouze producent bude číst ze standardního vstupu.

- Pouze konzumenti budou zapisovat na standardní výstup.

- Standardní chybový výstup můžete použít k ladícím výpisům.

- Uzavření standardního vstupu je požadavkem na ukončení programu. Pokud není
  řečeno jinak, návratový kód bude `0`.

- Všechny platné “příkazy” zaslané na standardní vstup budou mít odpovídající
  řádku na standardním výstupu (nic se neztratí).

- Žádné čekání ve vašem programu by nemělo být implementováno formou pollingu
  (periodická kontrola, že se něco stalo).

- Program před ukončením dealokuje všechnu dynamicky alokovanou paměť.

Do odevzdávacího systému nahrajte svůj zdrojový kód a `Makefile`, který
vygeneruje program `prod-cons` ve stejném adresáři jako `Makefile`. Program
překládejte s příznaky `-Wall -g -O2` a navíc s příznaky v proměnné
`EXTRA_CFLAGS` (evaluátor ji bude nastavovat podle potřeby). Pokud tato
proměnná není definována na příkazové řádce `make`, nastavte její hodnotu na
“`-fsanitize=address -fno-omit-frame-pointer`” (viz např. [operátor `?=`][14]).
Pokud provádíte překlad a linkování odděleně, používejte příznaky v
`EXTRA_CFLAGS` také při linkování.

Překladač nesmí generovat žádná varování.

# Domácí příprava na další cvičení
Nastudujte si použití podmínkový proměnných a k tomu příslušné funkce v
knihovně `pthread`:
- `pthread_cond_init`
- [`pthread_cond_destroy`][10]
- [`pthread_cond_signal`][11]
- [`pthread_cond_broadcast`][12]
- [`pthread_cond_wait`][13]

[10]: http://man7.org/linux/man-pages/man3/pthread_cond_init.3p.html
[11]: http://man7.org/linux/man-pages/man3/pthread_cond_signal.3p.html
[12]: http://man7.org/linux/man-pages/man3/pthread_cond_broadcast.3p.html
[13]: http://man7.org/linux/man-pages/man3/pthread_cond_wait.3p.html
[14]: https://www.gnu.org/software/make/manual/html_node/Flavors.html#index-conditional-variable-assignment
