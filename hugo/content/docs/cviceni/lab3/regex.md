---
title: "Základy regulárních výrazů"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Nástroje pro zpracování textu a regulární výrazy
Regulární výraz je textový vzor reprezentující množinu řetězců, využitý pro
vyhledávání, extrakci, nebo nahrazení v textu.

Regulární výraz je řetězec skládající se z běžných znaků (např. písmena a
číslice) a tzv. metaznaků. Metaznaky jsou znaky se zvláštním významem,
umožňující reprezentovat množinu znaků. Jedná se o znaky
```bash
^ $ . + ? * { [ () | \
```
Používá se více různých specifikací regulárních výrazů, přičemž často používané
jsou:
- regulární výrazy podle normy POSIX,
- regulární výrazy jazyka Perl.

Dále jsou popsány regulární výrazy dle normy POSIX, běžně používané nástroji
dostupnými v unixových systémech.

Pokud má být metaznak interpretován jako běžný znak (bez zvláštního významu),
musí být uveden za znakem zpětného lomítka (`\`):
```bash
fel\.cvut\.cz        # odpovídá řetězci "fel.cvut.cz"
```
Nejobecnějším metaznakem je tečka (`.`), reprezentující jeden libovolný znak.
Regulární výraz:
```bash
a.c
```
tedy reprezentuje jakýkoliv řetězec délky 3 začínající “`a`” a končící “`c`”,
např.  “`aac`”, “`abc`”, “`a3c`”, nebo “`a_c`”.

Metaznak `.` je v mnoha případech příliš obecný a potřebovali bychom omezit
množinu reprezentovaných znaků. Množinu reprezentovaných znaků zapisujeme do
hranatých závorek. Následující výraz reprezentuje řetězce “`ade`”, “`bde`” a
“`cde`”:
```bash
[abc]de
```
Hranaté závorky (a jejich obsah) tedy reprezentují jeden znak z množiny. Při
specifikaci množiny je možné použít rozsahy zapsané jako počáteční a koncový
znak rozsahu oddělené pomlčkou (`-`). V rámci jedné množiny je možné kombinovat
více rozsahů:
```bash
[a-z]         # jakékoliv malé písmeno anglické abecedy
[0-9]         # jakákoliv číslice
[A-Za-z0-9]   # jakýkoliv alfanumerický znak (kromě znaků národních abeced)
```
Pokud má být součástí množiny znak “`-`”, musí být zapsán na takovém místě, aby
nemohl být považován za specifikaci rozsahu (např. jako poslední znak množiny).
Znak stříšky “`^`” zapsaný na začátku množiny mění význam závorky na doplněk k
uvedené množině.
```bash
[^A-Za-z]    # jakýkoliv znak kromě písmen anglické abecedy
```
Pro často používané množiny znaků jsou připraveny třídy znaků, zapsané jako
`[[:classname:]]`, např.
```bash
[[:blank:]] mezera nebo tabulátor
[[:space:]] mezery specifikované v "C locale" (space, tab, new line, form feed, carriage return)
[[:alpha:]] všechna písmena (včetně znaků národní abecedy)
[[:alnum:]] alfanumerické znaky
[[:digit:]] číslice (stejné jako [0-9])
[[:lower:]] malá písmena
```
Kvantifikátory umožňují reprezentovat opakující se výrazy. Jedná se o
postfixové operátory, které mají vliv na předcházející znak:
```bash
a*     libovolné opakování znaku "a" (vč. prázdného řetězce)
a\+    libovolné opakování znaku "a" (alespoň 1 výskyt)
a\?    znak "a" nebo prázdný řetězec
a\{3\}   znak "a" opakující se 3x
a\{3,5\} znak "a" opakující se 3-5x
```
Všimněte si, že kvantifikátory `+`, `?` a `{ }` je nutné zapsat za zpětné
lomítko. Důvodem je zachování kompatibility se starší specifikací regulárních
výrazů v době přidání těchto nových metaznaků. Zpětné lomítko tedy zapíná
zvláštní význam znaků. Je zřejmé, že hojné používání těchto kvantifikátorů (a
dalších později přidaných metaznaků) vede na výrazy s mnoha zpětnými lomítky,
které jsou nepřehledné a složitěji se vytváří. Proto byla později vytvořena
specifikace tzv. *rozšířených regulárních výrazů* (angl. Extended regular
expressions), které interpretují nově přidané znaky jako metaznaky bez zpětných
lomítek před nimi. Mnoho nástrojů využívajících regulární výrazy umožňují změnu
režimu mezi základními a rozšířenými výrazy.

Kvantifikátory mají vliv na bezprostředně předcházející znak. Pokud mají být
aplikovány na delší výraz, je možné uzavřít tento podvýraz do závorek:
```bash
[0-9]\+\(,[0-9]\+\)*      libovolně dlouhý seznam čísel oddělených čárkou (bez jakýchkoliv mezer)
```
Závorky musí být v základních regulárních výrazech také zapsány za zpětné
lomítko. Uvedený výraz může být zapsán jako rozšířený regulární výraz
následovně:
```bash
[0-9]+(,[0-9]+)*
```
Závorky mohou mít další význam, podle použitého nástroje. Nástroje pro
vyhledávání a náhradu textu umožňují vložit část původního textu, odpovídající
výrazu v závorce do výsledného nahrazujícího textu, vložením zpětného lomítka a
pořadového čísla příslušné závorky v hledaném výrazu. Některé nástroje pro
vyhledávání textu umožňují extrahovat části textu odpovídající závorkám pro
další využití (např. přiřadit je do samostatných proměnných).

Znakem roura (`|`) je možné specifikovat alternativy. V základních výrazech je
nutné znak `|` psát za zpětné lomítko.
```bash
color\|colour
colo\(r\|ur\)
```
Oba uvedené výrazy reprezentují řetězce “color” a “colour”. Všimněte si, že
alternativy zahrnují celé řetězce nalevo a napravo od znaku `|`. Omezení
rozsahu alternativ je možné kulatými závorkami.

Kotvy je možné použít ke specifikaci začátku nebo konce slova nebo řádky uvnitř
výrazu. Tyto metaznaky nezastupují žádný konkrétní znak, ale pouze označují
rozhraní mezi znaky slova a jinými znaky. Kotvy definované specifikací POSIX
zahrnují:
```bash
\<    začátek slova
\>    konec slova
^     začátek řádky
$     konec řádky
```
Příklady použití:
```bash
\<word\>    reprezentuje "word", ale ne "words"
\<a[a-z]*   reprezentuje jakékoliv slovo začínající na "a" napsané malými písmeny
^From:      reprezentuje začátek řádky začínající řetězcem "From:"
^[a-zA-Z0-9_]\+$ reprezentuje pouze text složený z alfanumerických znaků a podtržítek, žádné jiné znaky nesmí být před ani za korespondujícím textem
```
Specifikace regulárních výrazů podle normy POSIX je dostupná v manuálových
stránkách:
```bash
man 7 regex
```
[Regex tutorial](https://cw.felk.cvut.cz/wiki/courses/ae3b33osd/regex) by Mira Bursa.

# Standardní nástroje pro zpracování textu
## `bash`
Skriptovací jazyk bash umožňuje ověřovat, zda řetězec obsahuje podřetězec
odpovídající regulárnímu výrazu, následující konstrukcí:
```bash
if [[ "$var" =~ a[0-9] ]] ; then ...

if [[ "$a" =~ ^[[:digit:]]+$ ]] ; then
    echo "${a} is a number"
fi
```
Povšimněte si použití dvojitých hranatých závorek (narozdíl od volání příkazu
`test`). Dále pamatujte na to že regulární výrazy se neuzavírají do uvozovek
(od bashe ver. 3.2, uvozovky by byly chápány jako součást výrazu).

V proměnné `BASH_REMATCH` navíc bash poskytuje text odpovídající zadanému
výrazu.

## `perl`
Pokud potřebujete napsat skript zaměřený na zpracování textu, zvažte použití
skriptovacího jazyka *Perl*, který má řadu vestavěných funkcí pro zpracování
textu a podporu regulárních výrazů.

https://www.perl.org/

## `grep` -- tisk řádků obsahující hledaný výraz
Nástroj `grep` prohledává vstupní text a hledá řádky odpovídající zadanému
regulárnímu výrazu a tyto řádky tiskne na výstup. Ostatní řádky jsou zahozeny.
Grep zpracovává text ze souboru, který je zadán jako parametr, nebo text ze
standardního vstupu.

Příklad: (nalezení řádků obsahujících direktivu `#include` ve zdrojovém souboru
jazyka C):
```bash
grep '^[[:space:]]*#include[[:space:]]*[<"][^>"]*[>"]' source_file.c
```
Příklad: (nalezení všech souborů v adresáři se jménem délky alespoň 3 znaky)
```bash
ls | grep '^....*$'
```
Grep podporuje jak základní tak rozšířené regulární výrazy (pokud je použit
přepínač `-E`). Další užitečné přepínače jsou:

        -i (ignorace velikosti písmen při vyhledávání)
        -v (vyhledávání řetězců, které neodpovídají výrazu)
        -c (tisk počtu odpovídajících řádek, místo obsahu řádek)
        -o (tisk pouze částí řádek odpovídajících výrazu)
        -n (tisk čísla řádky před každou nalezenou řádku)

Jediným voláním příkazu je možné prohledat více souborů, nebo celou adresářovou
strukturu.

## `awk` -- jazyk pro hledání a zpracování textových vzorů

*AWK* je interpretovaný programovací jazyk navržený pro zpracování textu.
Program v jazyce *AWK* sestává z dvojic vzor-akce, zapsaných jako
```awk
vzor { akce }
```
přičemž `vzor` je typicky výraz a akce je posloupnost příkazů. Vstupní řádky
textu jsou vyhodnocovány podmínkou danou vzorem a na odpovídající řádky jsou
aplikovány akce. Pokud je `vzor` nebo akce vynechána, je použit výchozí `vzor`
(odpovídající libovolnému řádku) nebo `akce` (tisk celé řádky na výstup). Každá
řádka je rozdělena na sloupce podle zadaného oddělovače, což umožňuje
jednodušší a pohodlnější zpracování textu. Řetězce jednotlivých sloupců jsou
přístupné přes vnitřní proměnné *AWK* `$1`, `$2`, `$3`, atd., odpovídající 1.,
2., 3. ... sloupci.

Příklad: tisk vlastníků souborů v pracovním adresáři
```bash
ls -l | awk '{ print $3 }'
```
Proměnná `$0` obsahuje celou vstupní řádku.

Jazyk *AWK* poskytuje další vnitřní proměnné:

        NR (number of records) -- číslo aktuálně zpracovávaného řádku
        NF (number of fields) -- počet sloupců aktuálního řádku
        FS (field separator) -- znak použitý jako oddělovač sloupců

Vzory mohou mít podobu regulárního výrazu:
```awk
/pattern/ { action }     # všechny řádky odpovídající výrazu "pattern"
```
podmínky:
```awk
NR==10 { action }    # akce se vykoná pro 10. řádku
```
složené výrazy:
```awk
    ( $3 == "string" && NR>10 ) || NR == 1 { action }
    ! /pattern/ { action }     # negace -- všechny řádky neodpovídající výrazu "pattern"
```
Dále je možné použít vzory `BEGIN` a `END`, umožňující vykonání akcí před
zpracováním první řádky vstupu, nebo po zpracování poslední řádky.
```awk
END { print NR }    # tisk počtu řádek vstupního textu
```
Nejčastěji používaným příkazem v akcích je příkaz `print`. Alternativně je
možné použít příkaz `printf` pro formátovaný výstup, jehož syntaxe je podobná
jako u stejnojmenné funkce jazyka C.

V rámci vzorů i akcí mohou být použity vestavěné funkce. K dispozici jsou
numerické funkce (např. `sqrt`, `sin`, `exp`), nebo řetězcové funkce, např.
```awk
length(str)    # délka řetězce
index(string, substring)    # poloha podřetězce v řetězci
substr(string, position [, length])    # extrakce podřetězce z řetězce
match(string,regex)    # test zda řetězec odpovídá regulárnímu výrazu
tolower(string)    # konverze řetězce na malá písmena
```
*AWK* podporuje řetězcové a numerické proměnné a asociativní pole. Umožňuje
také definovat uživatelské funkce.

Výchozí oddělovač sloupců (sekvence mezer a podobných znaků) může být změněn
přepínačem `-F`:
```bash
awk -F':' '{ print $1 " " $3 }' /etc/passwd
```
Program v jazyce *AWK* může být napsán v samostatném souboru, nebo jako
argument příkazu `awk`:
```bash
awk -f script.awk input_file.txt
awk '/pattern/ { print $0 }' input_file.txt
```
`awk` čte text ze souboru zadaného jako parametr, nebo ze standardního vstupu,
není-li soubor zadán.
```bash
awk '/pattern/ { print $0 }' input_file.txt
cat input_file.txt | awk '/pattern/ { print $0 }'
```
[GNU awk User's Guide](https://www.gnu.org/software/gawk/manual/html_node/index.html)

## `sed` -- stream editor
`sed` je mocný a efektivní nástroj pro transformaci textu, který na řádky
vstupního textu aplikuje příkazy ve vlastním jazyce. Vstupní text je načítán ze
zadaného souboru, nebo ze standardního vstupu.

Mezi základní příkazy sedu patří

    a   append - vložení textu za zpracovávaný blok textu
    i   insert - vložení textu před zpracovávaný blok textu
    q   quit - ukončení skriptu a zpracování
    r   připojení textu z externího souboru
    c   change - přepsání zpracovávaného bloku jiným textem
    d   delete - mazání řádek textu
    h   hold - uložení řádek do odkládací paměti (hold space)
    g   vložení řádek z odkládací paměti na aktuální pozici

    s/regexp/replacement/    nahrazení řetězců odpovídajících regulárnímu výrazu jiným řetězcem

V praxi se nejčastěji používá příkaz `s`, tedy nahrazení textu jiným textem.

Většina příkazů může mít specifikovanou adresu určující na které řádky se má
příkaz aplikovat. Adresa se píše bezprostředně před příkaz. Příkazy bez adresy
jsou aplikovány na všechny vstupní řádky. Adresa může mít podobu

    číslo -- číslo konkrétní řádky
    číslo,číslo -- rozsah řádek mezi zadanými čísly řádek
    $ -- poslední řádka
    /regexp/ -- řádka odpovídající regulárnímu výrazu

Vykřičník (`!`) mezi adresou a příkazem invertuje vzor, tzn. aplikuje příkaz na
všechny řádky, které vzoru/adrese neodpovídají.

Skript v jazyce `sed` je možné číst ze samostatného souboru, nebo z argumentu:
```bash
sed -f sed_script input_data.txt
sed -e '1d' input_data.txt
```
Příklady použití:
```bash
1,3d       # smaž první 3 řádky
25h 36g    # zkopíruj řádku 25 do paměti a vlož ji na pozici řádky 36
/[0-9]/d   # smaž všechny řádky obsahující číslice
3a hello   # vlož řádku "hello" za třetí řádku
s/[^a-zA-Z0-9 ]*/_/g     # zaměň všechny sekvence nealfanumerických znaků podtržítkem
```
[sed, a stream editor](https://www.gnu.org/software/sed/manual/sed.html)

## `tr` -- translate or delete characters
`tr` je jednoduchý program, který čte ze standardního vstupu a zapistuje na
standardní výstup a umí změnit jednotlivé znaky za jiné či je mazat. Příkaz
```bash
tr abc def
```
zamění `a` za `d`, `b` za `e` atd. Příkaz
```bash
tr -d '\n'
```
smaže všechny znaky konec řádky.
