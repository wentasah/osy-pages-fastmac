---
title: "Make"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Make
[GNU make](https://www.gnu.org/software/make/) je nástroj pro automatizované
vytváření cílových souborů ze zdrojových souborů. Často se používá pro překlad
zdrojových souborů (např. C/C++) na spustitelné binární soubory, lze jej však s
výhodou použít pro všechny typy úloh kde se z nějakých souborů vytvářejí jiné
soubory.

Make zpracuje předpis, kterému říkáme *makefile* a který je zpravidla uložen v
souboru s názvem `Makefile`. `Makefile` je souborem pravidel v následujícím
tvaru:
```make
target: dependencies
    commands
```
- `target` je název pravidla, který se zpravidla shoduje s názvem cílového
  souboru který chceme vytvořit.
- `dependencies` (závislosti) je nepovinný seznam souborů nebo jiných pravidel,
  která jsou potřeba pro aplikaci tohoto pravidla. Pokud tyto soubory
  neexistují, make se pokusí najít pravidla pro jejich vytvoření a rekurzivně
  je vykoná. Pokud některý soubor nelze ani vytvořit, celé pravidlo selže.
- `commands` je sekvence příkazů (mohou být na více řádek), jejichž vykonáním
  dojde k provedení pravidla.

{{< hint warning >}}
Příkazy v `Makefile` musí být odsazeny **tabulátorem**! Pozor na editory které
při stisku tabulátoru vloží sekvenci mezer!
{{< /hint >}}

Make se při vykonávání pravidel chová velmi jednoduše: pokud jsou splněny
(existují) požadované *dependencies*, vykoná se sekvence příkazů. Přitom se
však nijak nekontroluje, že spuštěním uvedených příkazů se opravdu vytvoří
soubor target -- mezi hlavičkou pravidla a příkazy není žádná implicitní vazba
(ta je na autoru `Makefile` podle napsaných příkazů).

Hlavní výhodou `make` je, že provádí pouze ta pravidla, která jsou nutná a
přeskakuje ta, která by se prováděla zbytečně. Typickým případem je situace,
kdy máme projekt sestávající z více zdrojových souborů, všechny soubory již
máme přeložené (předchozím spuštěním `make`), provedeme změnu v jednom ze
souborů a chceme projekt znovu přeložit. V takovém případě `make` spustí pouze
překlad změněných souborů a ostatní ponechá přeložené od minule. Aby však tato
funkcionalita pracovala správně, je nutné aby byl `Makefile` napsán korektně,
což zahrnuje následující pravidla:
- jedno pravidlo provádí pouze jednu elementární operaci (typicky převádí právě
  jeden soubor na jiný)
- jméno pravidla které vytváří soubor se shoduje se jménem souboru
- pravidlo má korektně uvedené závislosti

Rozhodování o vykonání pravidla se provádí podle následujícího postupu:
- Pokud cílový soubor neexistuje, pravidlo se vždy provede.
- Pokud cílový soubor existuje, pravidlo se provede pokud je čas změny
  některého souboru v *dependencies* novější než čas poslední změny cílového
  souboru.

Je zřejmé, že pokud dojde k nekonzistenci časových značek souborů, make nemusí
fungovat správně. V takových případech může být nejbezpečnější všechny
vygenerované soubory smazat a nechat make vytvořit celý projekt znovu.

# Spouštění make
Make se spouští příkazem `make` v adresáři, ve kterém se nachází `Makefile`.
Pokud jej spustíme bez parametrů, provádí se první pravidlo v souboru
`Makefile`. Parametrem příkazu `make` je možné zadat název jiného pravidla,
které se má vykonat.

Pravidla v závislostech se vykonávají podle potřeby rekurzivně. Pokud
kterékoliv pravidlo selže (nejsou k dispozici závislosti, nebo některý příkaz
skončí chybou), je zastaven celý proces a make vrací chybu.

Při běhu make vypisuje všechny prováděné příkazy, podle čehož lze snadno
sledovat, která pravidla se provádějí.

V případě, že vše proběhne v pořádku, vrací make návratovou hodnotu a nemusí
vypisovat žádné hlášení na výstup.

# Triviální příklad
Mějme aplikaci sestávající ze tří souborů `src1.c`, `src2.c` a `src3.c`,
jejichž překladem má vzniknout binární spustitelný soubor. Funkce `main()` je v
jednom z nich. Součástí projektu může být i několik hlavičkových souborů
(`.h`), které však z hlediska volání kompilátoru nejsou důležité. Sestavení
aplikace vyžaduje nejprve přeložení jednotlivých zdrojových souborů na binární
`.o` soubory a jejich následné “slinkování” do spustitelného souboru
“aplikace”. `Makefile` pro sestavení této aplikace pak může vypadat následovně:
```make
aplikace: src1.o src2.o src3.o
    gcc -o aplikace src1.o src2.o src3.o

src1.o: src1.c
    gcc -c src1.c

src2.o: src2.c
    gcc -c src2.c

src3.o: src3.c
    gcc -c src3.c
```
Je zřejmé že pokud by se psal `Makefile` takto (samostatné pravidlo pro každý
soubor), bylo by to poměrně pracné a náročné na údržbu v případě dalšího
vývoje. Berte to proto pouze jako ukázku pro vysvětlení základní funkcionality.

Průběh make:

- Po spuštění příkazu `make` se provede první pravidlo “aplikace”, neboť soubor
  s názvem “aplikace” zatím neexistuje.
- Pravidlo “aplikace” je závislé na souborech `src1.o` -- `src3.o`, které také
  neexistují. Naleznou se tedy vhodná pravidla pro jejich vytvoření a ta se
  vykonají.
- Pro každý neexistující `.o` soubor se spustí odpovídající pravidlo, které jej
  vytvoří z příslušného `.c` souboru.
- Po překladu všech `.c` souborů na `.o` se může vykonat příkaz pravidla
  “aplikace”, který provede slinkování.

# Pokročilé možnosti make
## Generická pravidla
Pokud máme řadu souborů, které se zpracovávají stejným příkazem (např.
kompilace `.c` souborů), není nutné psát pravidlo pro každý soubor zvlášť, ale
je možné využít generické pravidlo ve tvaru
```make
%.o: %.c
    gcc -c -o $@ $<
```
Toto pravidlo se aplikuje vždy, když potřebujeme vytvořit soubor `.o` a
existuje soubor `.c` se stejným jménem. `$<` je proměnná `make`, za kterou se
dosadí jméno vstupního `.c` souboru, proměnná `$@` má hodnotu cíle pravidla,
tedy `.o` souboru.  Toto pravidlo v sobě navíc implicitně zahrnuje závislost
`.o` souboru na `.c` souboru.

## Proměnné v Makefile
Proměnné (též makra) v Makefile nám umožňují jej parametrizovat a pomáhají jeho
udržitelnosti. Zvláště užitečné jsou proměnné pro uložení řetězců nebo seznamů,
které se opakují na mnoha místech. Proměnné se zpravidla deklarují v úvodní
části Makefile, ještě před prvním pravidlem, ve tvaru
```make
PROMENNA=hodnota
```
Hodnotu proměnné pak mohu použít v příkazu, v závislostech, jménu pravidla,
nebo při definici jiných proměnných zápisem

    $(PROMENNA)

### Příklad
Při překladu projektu v jazyce C potřebujeme často pracovat se seznamem všech
`.o` souborů.
```make
OBJ=main.o src1.o src2.o

bin: $(OBJ)
    gcc -o bin $(OBJ)

clean:
    rm $(OBJ)
```
Následující `Makefile` slouží k překladu projektu v jazyce C. Pokud se
rozhodneme použití syntaxe jazyka C++ ve zdrojových kódech, nebo budeme chtít
změnit parametry kompilátoru, uděláme změnu pohodlně na jednom řádku, který
najdeme na začátku souboru `Makefile`.
```make
OBJ=...
CC=gcc
CFLAGS=-g -Wall -Iinclude

bin: $(OBJ)
    $(CC) -o $@ $(OBJ)

%.o: %.c
    $(CC) -o $@ -c $(CFLAGS) $<

specific.o: specific.c
    $(CC) -o $@ -c $(CFLAGS) -Dspecificka_definice $<
```
Proměnná `$@`, použitá v pravidlu bin se nahradí názvem pravidla (tj. v tomto
případě řetězcem “bin”). Pro vytvoření každého souboru se hledá
nejspecifičtější pravidlo -- v tomto případě se tedy soubor `specific.c`
překládá jiným příkazem než ostatní `.c` soubory).

# Další možnosti make
Make nám nabízí řadu funkcí a maker pro ulehčení práce.
```make
SRC=$(wildcard *.c)
```
Za makro *wildcard* `make` dosadí seznam všech souborů odpovídajících dané
masce.
```
OBJ=$(patsubst %.c,%.o,$(SRC))
```
Makro *patsubst* umožňuje provést nahrazení části názvu v seznamu souborů. 3
parametry makra jsou vzor pro nalezení, vzor pro nahrazení a vstupní seznam
souborů. Uvedený příklad nahradí všechna jména `.c` souborů v seznamu `SRC`
odpovídajícími jmény `.o` souborů a uloží nový seznam do proměnné `OBJ`.

# Pokročilé řešení závislostí při překladu kódu v C/C++
Nikde ve výše uvedených příkladech překladu C/C++ kódu se nijak neřešila
závislost na header souborech vložených ve zdrojových souborech direktivou
`#include`. Protože make nijak neanalyzuje prováděné příkazy, nemůže si být
této závislosti vědom a při změně některého `.h` souboru automaticky nevyvolá
překlad `.c` souborů které jej vkládají, jak bychom požadovali.

Toto chování je možné vynutit přidáním header souborů do závislostí pravidel
pro jednotlivé soubory:
```make
soubor1.o: soubor1.c header1.h header2.h

soubor2.o: soubor2.c header1.h header3.h header4.h
```
Tato pravidla mohou existovat i vedle stávajícího pravidla pro překlad (např.
`.c.o: ...`), není tedy nutné příkaz pro překlad u každého z nich opakovat.
Uvedený způsob je však velmi náročný na údržbu v rámci vývoje, neboť při
přidání header souboru do `.c` souboru je nutné upravit i `Makefile`.

Tento problém je možné s využitím překladače `gcc` vyřešit pomocí přepínače
`-MM`, který umožňuje vygenerovat seznam závislostí na header soubory ve tvaru
kompatibilním s formátem `Makefile`. Tento výstup můžete uložit do samostatného
souboru, který pak vložíte do vašeho `Makefile` direktivou `#include`. Funkční
a efektivní řešení na tomto principu však není triviální a před jeho použitím
doporučuji se inspirovat vhodnými příklady.

Jinou možností jak překládat komplikované projekty s korektním uvažováním
závislostí je použít sofistikovanější nástroj, určený specificky pro danou
činnost (překlad C/C++ projektů), který tyto závislosti hlídá automaticky.

Takovým nástrojem je např. Meson Build system: http://mesonbuild.com/.
