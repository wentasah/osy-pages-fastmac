---
title: "10. NOVA System call"
weight: 2
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# NOVA -- Systémová volání a správa paměti
Na tomto cvičení se seznámíte s jádrem miniaturního OS NOVA a
implementujete do něj systémové volání `brk`. [NOVA][] je
mikrohypervizor původně vyvíjený na [Drážďanské univerzitě][1],
později ve firmě Intel a nyní firmami [GENODE labs][2], [BedRock
Systems][4] a [Cyberus Technology][3]. Na cvičeních však nebudete
pracovat s kompletní verzí jádra NOVA, ale se zjednodušenou verzí pro
výuku, která má pouze 2 tisíce řádek kódu.

[NOVA]: http://hypervisor.org/
[1]: http://os.inf.tu-dresden.de/
[2]: https://genode.org/
[3]: https://gitlab.com/cyberus/NOVA
[4]: https://bedrocksystems.com/

## Domácí příprava
Pro toto cvičení budete potřebovat následující:

- aspoň [trochu rozumět kódu v C++][4] (viz také [7. přednáška][l7]) a [inline
  assembleru][ia] (viz [8. cvičení][lab8])
- vědět, co to jsou [systémová volání][5] a proč/k čemu se používají (viz
  přednášky)
- popis instrukce [sysenter][6] a [sysexit][]
- vědět, co dělá systémové volání [brk][] v Linuxu
- rozumět rozdílu mezi [uživatelským prostorem a prostorem jádra][7] (viz
  přednášky)
- vědět, jak CPU překládá virtuální adresy na fyzické (i386 používá
  dvoustupňové mapování 10-10-12), viz přednášky, APO,
  https://www.youtube.com/watch?v=KNUJhZCQZ9c, případně další
- stáhnout, rozbalit a ideálně i vyzkoušet (`make run`) [operační systém
  NOVA][NOVA]
  - na vlastním PC (Debian/Ubuntu) budete potřeboval balíčky:
    - `libc6-dev-i386`
    - `qemu-system-i386` nebo `qemu-system-x86`
  - přečíst hlavní `Makefile`
  - přečíst funkci `Ec::syscall_handler`
  - přečíst funkci `Ptab::insert_mapping` (byla na přednáškách)
  - podívat se na třídu `Kalloc`
  - při práci přes vzdálený přístup použijte
    ```bash
    ssh -X «login»@postel.felk.cvut.cz
    ```
    přepínač `-X` umožní spouštět i grafické aplikace (QEMU)


[4]: http://kmlinux.fjfi.cvut.cz/~fabiadav/cecko/poznamky-k-jazyku-c_plus_plus/uvod-do-c_plus_plus
[l7]: /docs/prednasky/pdf/lekce07.pdf
[ia]: https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html
[lab8]: /docs/cviceni/lab8
[5]: https://en.wikipedia.org/wiki/System_call
[6]: https://c9x.me/x86/html/file_module_x86_id_313.html
[sysexit]: https://c9x.me/x86/html/file_module_x86_id_314.html
[brk]: http://man7.org/linux/man-pages/man2/brk.2.html
[7]: https://blog.codinghorror.com/understanding-user-and-kernel-mode/
[NOVA]: https://gitlab.fel.cvut.cz/osy/osy.pages.fel.cvut.cz/-/archive/master/nova.zip?path=nova

# Zadání úlohy
Implementujte systémové volání `brk` s prototypem:
```C
void *brk(void *address)
```

Toto systémové volání nastaví konec datového segmentu v adresním prostoru
procesu (tzv. *program break* nebo jen *break*) na adresu danou parametrem
`address`. Tím se zvětší nebo zmenší množství alokované paměti, které může
program využívat ke svému běhu. Break je první adresa za koncem namapovaného
datového segmentu.

Vaše řešení by mělo splňovat následující požadavky:

- Po úspěšném návratu ze systémového volání je break nastaven na
  hodnotu `address`. To znamená, že uživatelský program může používat
  paměť od adresy `0x1000` do adresy o jedna menší než `address`.
- Přístup na stránky začínající na adrese vyšší či rovné `address`
  nebude programu dovolen.
- Break nesmí jít nastavit na nižší hodnotu, než je jeho hodnota při spuštění
  programu. Tím by se program připravil o část svého kódu, dat nebo zásobníku.
- Break nesmí jít nastavit na vyšší hodnotu než `0xC0000000`. Tím by aplikace
  mohla přepsat jádro, které je namapováno od této adresy výš.
- Při úspěšném dokončení je vrácena původní hodnota break před vykonáním
  systémového volání. Při chybě je vrácena hodnota `0`.
- Pokud je `address` rovno `NULL` (`0`), nejedná se o chybu a hodnota break se
  nemění. Toto volání slouží pouze ke zjištění aktuální hodnoty break.
- Při žádném volání `brk` nesmí dojít k „pádu“ systému.
- *ABI* systémového volání bude následující:
  - vstup: `AL=3`, `ESI=address`,
  - výstup: `EAX=návratová hodnota`.
- Nově alokovaná paměť bude inicializována na nulu.
- Při snižování hodnoty break bude nepřístupná paměť dealokována (a
  odmapována), aby mohla být opět alokována později.
- Při chybě alokace paměti v průběhu systémového volání nebude adresní prostor
  uživatelské aplikace změněn a částečně alokovaná paměť bude dealokována.
- Při kompilaci nevypisuje kompilátor žádná varování.

Odevzdává se archiv se souborem `ec_syscall.cc` obsahující vaši implementaci,
ideálně vytvořený pomocí:
```bash
make hw10
```

# Nápověda
- Operační systém NOVA s testovací aplikací `user/hello.c` můžete nabootovat
  buď na fyzickém počítači pomocí zavaděče podporujícího specifikaci
  [multiboot][9] (např. [GRUB 2][10]), ale pravděpodobně bude efektivnější ho
  pouštět jako virtuální stroj například v emulátoru Qemu. Pro to stačí spustit
  příkaz:
  ```bash
  make run
  ```

- Při zvětšování hodnoty „program break“ musíte v jádře alokovat paměť a
  namapovat ji do adresního prostoru uživatelské aplikace modifikováním
  stránkovacích tabulek. Inspirací vám může být funkce `Ec::root_invoke()`,
  která připravuje paměť pro spouštěný program. Funkce čte hlavičky z binárky
  aplikace, které si můžete zobrazit příkazem `readelf --program-headers
  hello`.

  Na konci funkce nastaví proměnné `Ec::break_min` a `Ec::break_current`, které
  pravděpodobně budete potřebovat ve své implementaci.

- Při snižování hodnoty „program break“ naopak musíte mapování zrušit a paměť
  dealokovat.

- Při startu uživatelského programu (např. `hello`) je jeho paměťová mapa
  následující (trochu zjednodušeno):

  | Adresy                      | Obsah                                                                    |
  |:----------------------------|:-------------------------------------------------------------------------|
  | `0x00001000 – 0x00001fff`   | zásobník (4 kB); počáteční hodnota registru   `ESP` je `0x2000`          |
  | `0x00002000 – 0xXXXXX000-1` | data programu, viz `.data` ve výstupu příkazu `readelf --sections hello` |
  | `0xXXXXX000 – 0xYYYYY000-1` | kód programu, viz `.text` ve výstupu příkazu `readelf --sections hello`  |
  | `0xYYYYY000`                | program break                                                            |

- Ke kódu jádra NOVA není žádná dokumentace, ale části, které budete
  potřebovat, jsou tak jednoduché, že byste měli být schopni jim
  porozumět na základě čtení kódu (a komentářů). Pokud však i přes
  vaší snahu něčemu nerozumíte, klidně se [zeptejte](https://gitlab.fel.cvut.cz/osy/osy.pages.fel.cvut.cz/issues/new?issue[title]=Otázka+ke+zdrojovým+kódům+OS+NOVA).

[9]: https://www.gnu.org/software/grub/manual/multiboot/multiboot.html
[10]: https://www.gnu.org/software/grub/
[forum]: https://cw.felk.cvut.cz/forum/thread-4098.html

# Ladění
Při vývoji operačního systému nelze používat debugger tak jednoduše, jak jste
zvyklí při vývoji aplikací. Ladit váš kód můžete přidáváním příkazů `printf()`
na potřebná místa v kódu. Pokud vám to nestačí můžete použít parametr `-gdb`
(případně zkratku `-s`) emulátoru Qemu.

Abychom vám ladění usnadnili, v hlavním `Makefile` jsou připravena pravidla jak
pro spouštění Qemu se zmiňovanými parametry, tak pro spouštění debuggeru `gdb`
tak, aby šel ladit kód běžící v Qemu:
- V jednom okně spusťte příkaz
  ```sh
  make rd
  ```
  který spustí Qemu, které po startu počká na připojení debuggeru.
- V druhém okně pak spusťte
  ```sh
  make du
  ```
  (pokud chcete ladit uživatelský program (nápř. `hello‘)) nebo
  ```sh
  make dk
  ```
  pokud chcete ladit jádro.

  Můžete v `Makefile` změnit argument příkazu `break` tak, aby se
  vykonávání programu zastavilo na funkci (či řádku), který
  potřebujete odladit.

## Užitečné příkazy `gdb`

Příkazy se většinou dají zkrátit na první znak.

- Běh programu: `next` (`n`), `step` (`s`), `continue` (`c`)
- Výpis proměnných: `print` (např. `p initialized_var` nebo hexadecimálně: `p/x
  initialized_var`)
- Výpis paměti: `x` (např: `x/16 0x2000` vypíše 16 slov od adresy `0x200`,
  `x/16 &initialized_var` vypíše 16 slov začínající na adrese proměnné
  `initialized_var`.
- Informace: `info registers` vypíše hodnoty registrů, `info locals` vypíše
  hodnoty lokálních proměnných atd.
- Zobrazení: `layout src` zobrazí zdrojový kód i příkazové okno, `Ctrl-L`
  překreslí obrazovku, `tui disable` vypne zobrazování zdrojového kódu.

## Monitorovací konzole Qemu

Pro ladění můžete používat i monitorovací konzoli Qemu, která umožňuje zobrazit
stav emulovaného CPU.
- Do konzole se přepnete stiskem `Ctrl-Alt-2` v grafickém okně (nebo stiskem
  `Ctrl-a c` pokud spouštíte `qemu` s přepínačem `-nographic`)
- Příkazy `info mem` nebo `info tlb` vypíší informace o mapování virtuálních
  adres na fyzické (tj. stránkovací tabulku).
- `info registers` vypíše hodnoty registrů

# Příklad
- Předpokládejme, že `break_start` je na začátku Vašeho procesu nastaven na
  `0x5000`.
- Při volání funkce `break(0x9000)` dojde k alokování stránek `0x5000-0x5FFF`,
  `0x6000-0x6FFF`, `0x7000-0x7FFF` a `0x8000-0x8FFF`.
- Při dalším volání `break(0x7555)` dojde k uvolnění a odmapování
  stránky `0x8000-0x8FFF`.
- Při dalším volání `break(0x7666)` se neprovádí žádná alokace, ale
  vynuluje se paměť `0x7555-0x7665`.
- Při dalším volání `break(0xA000)` se alokuje nová stránka pro `0x8000-0x8FFF`
  a `0x9000-0x9FFF`, a vynuluje se paměť `0x7666-0x7FFF` (předpokládáme, že
  nově alokované stránky jsou už vynulované).

# Materiály
- [NOVA Introduction][11]
- [Testovací programy][12]
- [Zdrojové kódy OS NOVA][NOVA]

[11]: pdf/osy-nova-intro.pdf
[12]: misc/brktest.tgz
