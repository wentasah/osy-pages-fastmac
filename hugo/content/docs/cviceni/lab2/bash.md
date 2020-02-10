---
title: "Základy jazyka BASH"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Základy skriptovacího jazyka BASH
Podklady pro výuku předmětu Operační systémy.

[Bash][] (Bourne Again SHell) je interpret příkazů (shell) projektu [GNU][]
kompatibilní se starším [Bourne shell][]-em (sh), přidávající užitečné funkce
převzaté z Korn shellu (ksh) a C shellu (csh). Shell představuje základní
rozhraní pro komunikaci uživatele s operačním systémem počítače. Jeho základní
funkcí je spouštění zadaných příkazů a programů, navíc poskytuje řadu vlastních
příkazů umožňujících efektivní práci se systémem. Bash je tedy možné používat:
- interaktivně (realizuje příkazovou řádku operačního systému),
- jako interpret skriptů.

V interaktivním režimu čte Bash příkazy ze standardního vstupu (od uživatele)
místo ze souboru, jinak je jeho chování v obou režimech stejné. Skript v jazyce
bash je textový soubor obsahující příkazy jazyka bash, oddělené novou řádkou,
nebo středníkem. Na první řádku skriptu je vhodné napsat hlavičku, která umožní
jádru OS rozpoznat, že se jedná o skript a jakým programem má být skript
interpretován. Na operačním systému Linux bude mít tato hlavička tvar
```bash
#!/bin/bash
```
Znaky `#!` na začátku první řádky (POZOR, opravdu to musí být první řádka, tzn.
před hlavičkou nesmí být ani prázdná řádka) říkají, že se jedná o skript, za
nimi následuje plná cesta k interpretu příkazů (v našem případě /bin/bash). Zde
je vhodné si uvědomit, že po přenesení skriptu na jiný systém, kde se interpret
bashe nachází v jiném adresáři (např. ve FreeBSD to může být
`/usr/local/bin/bash`) nemusí skript fungovat podle předpokladu. Hlavička je
důležitá v případě, že skript spouštíme pouze zadáním jeho cesty (skript musí
být v tomto případě nastaven jako spustitelný). Pokud spouštíme skript jako
argument příkazu bash, např.
```bash
/bin/bash ./muj_skript.sh
```
hlavička není pro spuštění nezbytná. Přesto je vhodné ji do souboru uvést pro
pozdější jednoduchou identifikaci o jaký soubor se jedná.

Základní funkcí shellu je spouštění externích programů a skriptů, přičemž
syntaxe je absolutní nebo relativní cesta k souboru obsahujícímu binární kód,
nebo jiný skript. Příkazy se oddělují koncem řádky, nebo středníkem. Znakem `#`
je možné do kódu vkládat komentáře -- cokoliv za tímto znakem interpret
ignoruje.

Pokud budeme chtít od bashe něco víc, budeme potřebovat proměnné.

[Bash]: http://www.gnu.org/software/bash/
[GNU]: http://www.gnu.org/
[Bourne shell]: http://en.wikipedia.org/wiki/Bourne_shell

# Proměnné
Bash nemá datové typy proměnných -- resp. s obsahem proměnné se vždy pracuje
jako s řetězcem. Proměnná je identifikována názvem, který se může skládat z
malých a velkých písmen anglické abecedy, podtržítek a číslic, přičemž nesmí
číslicí začínat. V názvech proměnných záleží na velikosti písmen, což znamená,
že `promenna` a `PROMENNA` jsou dvě různé proměnné.

Přiřazení hodnoty proměnné se provádí operátorem `=`, např.
```bash
A=24
```
přiřadí do proměnné `A` řetězec “`24`”. Při přiřazování je důležité myslet na
to, že před operátorem `=` ani za ním nesmí být mezera, jinak je příkaz
interpretován chybně:
```bash
A =24   # pokus o spuštění příkazu "A" s argumentem "=24"
A= 24   # pokus o spuštění příkazu "24" s přednastavenou hodnotou proměnné "A" na prázdný řetězec (viz export níže)
A = 24  # pokus o spuštění příkazu "A" se dvěma parametry "=" a "24"
```
Pokud potřebujeme pracovat s hodnotou proměnné (předat ji jako parametr, nebo
přiřadit jako hodnotu do jiné proměnné), musíme proměnnou zapsat se znakem
dolaru, tedy např.
```bash
echo $A     # výpis obsahu proměnné na standardní výstup
```
Alternativní způsob zápisu využívá složené závorky kolem názvu proměnné:
```bash
echo ${A}
```
To je výhodné, pokud např. bezprostředně za hodnotou proměnné následují znaky,
které by jinak byly považovány za součást názvu proměnné:
```bash
echo x$Ax   # vytiskne znak "x" + hodnotu proměnné "Ax"
echo x${A}x # vytiskne znak "x" + hodnotu proměnné "A" + znak "x"
```
# Pole
Kromě obyčejných proměnných umožňuje bash pracovat s poli. Pole vytvoříme buď
deklarací příkazem
```bash
declare -a POLE
```
který vytvoří prázdné pole, nebo přímo přiřazením prvků pole do proměnné. Prvky
pole se zapisují do kulatých závorek:
```bash
PRAZDNE_POLE=()     # vytvoření prázdného pole v proměnné PRAZDNE_POLE
BARVY=(cervena zelena modra)        # vytvoření pole se třemi prvky "cervena", "zelena" a "modra" v proměnné BARVYÀ
```
Při přístupu k prvkům pole se používá zápis
```bash
${BARVY[0]} # vrátí první položku pole
```
přičemž v hranatých závorkách je číselný index položky, číslování je od 0.
Pokud bychom na index a hranaté závorky zapomněli, dostaneme první prvek pole.
Následující zápisy nám umožní zjistit délku pole (počet položek), nebo získat
všechny položky pole najednou:
```bash
${#BARVY[*]}    # vrátí počet položek pole BARVY
${BARVY[*]}     # vrátí všechny položky pole BARVY jako jeden řetězec, ve kterém jsou položky pole vzájemně odděleny mezerou
${BARVY[@]}     # vrátí všechny položky pole BARVY samostatně (pro správnou funkci je potřeba správně použít uvozovky)
```
Přidávat prvky k existujícímu poli můžeme např. operátorem `+=`:
```bash
BARVY+=(cerna bila)
```
# export
Viditelnost proměnné v bashi je omezena na instanci interpretu vykonávajícího
aktuální skript. Pokud chceme hodnotu proměnné přenést do synovského procesu
(pokud spouštíme z jednoho skriptu jiný program nebo skript), můžeme použít
příkaz
```bash
export PROMENNA
```
kterým říkáme, že se má proměnná zkopírovat do proměnných spuštěných procesů. V
běžných programech spuštěných z aktuálního skriptu jsou takovéto proměnné
viditelné jako “proměnné prostředí”.

Pokud je přiřazení proměnné uvedeno před příkazem, je proměnná “exportována”
automaticky, ale jen pro spouštěný příkaz:
```bash
A=1
A=2 bash -c 'echo $A' # vypíše 2
echo $A               # vypíše 1
```
# Uvozovky
Řetězce v bashi je důrazně doporučeno uzavírat do uvozovek, ačkoliv to syntaxe
jazyka striktně nevyžaduje. Uvozovky pomohou především v případech, kdy obsah
proměnné obsahuje mezery nebo některé znaky se zvláštní interpretací. Podívejme
se na příklad:
```bash
A="text s mezerami"
program $A
program "$A"
```
okud bychom vynechali uvozovky u přiřazení do proměnné `A`, obdržíme zřejmě
chybové hlášení o neznámém příkazu “`s`” -- interpret nemá jak odlišit zda se
jedná o pokračování řetězce, nebo příkaz který chcete spustit s přednastavenou
proměnnou `A` na hodnotu “text”. Druhá řádka se interpretuje jako program text
s mezerami což znamená, že spuštěný program obdrží tři samostatné argumenty
“text”, “s” a “mezerami”, což zřejmě není náš záměr. Třetí řádek pracuje tak,
jak požadujeme, tj. předá programu jediný argument bez ohledu na jeho obsah.

okud budu chtít předat celé pole jako parametry spuštěného programu (každý
prvek jako samostatný argument), musím výraz uzavřít do uvozovek:
```bash
program "${POLE[@]}"
```
jinak mezery v řetězcích způsobí rozdělení řetězce na více samostatných
argumentů.

Bash umožňuje uzavírat řetězce kromě “dvojitých uvozovek” také do 'apostrofů',
přičemž základní rozdíl mezi nimi je ten, že uvnitř apostrofů se neprovádí
překlad proměnných na jejich hodnoty, tedy např.
```bash
echo "Obsah proměnné je $A"
```
vytiskne hodnotu proměnné, ale
```bash
echo 'promenna $A'
```
vytiskne řetězec "`promenna $A`". Uvozovky a apostrofy můžeme jinak s výhodou
zaměňovat, pokud potřebujeme vytvořit řetězec obsahující právě apostrof nebo
uvozovky.

{{< hint info >}}
**Doporučení**: zvykněte si vždy uzavírat hodnoty proměnné do uvozovek. Vyhnete
se tak neočekávanému chování skriptu a chyb, jejichž důvod nemusí být na první
pohled zřejmý.
{{< /hint >}}

Kromě uvozovek a apostrofů umožňuje syntaxe jazyka použití `obrácených
apostrofů`, které však neslouží k definici řetězce, ale řetězec uvnitř
apostrofů se interpretuje jako příkaz který je vykonán a standardní výstup
tohoto příkazu je vložen na místo těchto uvozovek, např.
```bash
ADRESAR=`pwd`       # spustí příkaz "pwd" (print working directory) a výstup (cestu k aktuálnímu pracovnímu adresáři) uloží do proměnné ADRESAR
```
Stejného výsledku je možné dosáhnout i alternativním zápisem bez obrácených
apostrofů:
```bash
ADRESAR=$(pwd)
```
Výhoda tohoto zápisu je, že lze více nahrazení vkládat do sebe. Např:
```bash
SOUBORY=$(ls $(pwd))
```
Pokud budete chtít uložit jednotlivá slova ve výstupu příkazu pole, můžete
použít zápis
```bash
SOUBORY=( `ls` )    # spustí příkaz "ls" a každé slovo v jeho výstupu uloží do jednoho prvku pole SOUBORY (pozor, v případě mezer v názvech souborů nemusí příkaz fungovat jak očekáváte)
```
# Zpracování argumentů
Často požadujeme parametrizaci chování skriptu prostřednictvím argumentů
zadaných při spuštění. V některých případech jsou obsahem argumentů
zpracovávaná data. Bash umožňuje číst jednotlivé argumenty prostřednictvím
speciálních proměnných
```bash
$1 # hodnota 1. argumentu
$2 # hodnota 2. argumentu
$3 # hodnota 3. argumentu
atd...
```
V proměnné `$0` je potom uložen příkaz, kterým byl skript spuštěn (cesta ke
skriptu). Proměnné `$*` a `$@` nám umožní pracovat se všemi parametry buď
najednou, jako s jedním řetězcem, ve kterém jsou argumenty odděleny mezerou,
nebo se seznamem samostatných argumentů (rozdíl mezi `*` a `@` je stejný jako u
práce s celým polem). V proměnné `$#` najdeme počet zadaných argumentů.

Uvedený způsob můžeme použít pouze pro argumenty 1-9, pro přečtení dalších
argumentů je nutné použít zápis se složenými závorkami `${11}`, `${12}`, atd.
Velmi často však požadujeme možnost proměnlivého počtu parametrů skriptu, které
nemusí mít pevné pořadí (např. skript pracující se skupinou zadaných souborů,
kterých může být libovolný počet). V takovém případě s výhodou využijeme příkaz
`shift`, který odstraní 1. argument a všechny následující posune o jedno místo
dopředu (obsahem proměnné `$1` pak bude hodnota 2. argumentu). S využitím cyklů
a opakovaného volání příkazu `shift` potom můžeme postupně zpracovat libovolný
počet parametrů. Příkaz `shift` ovlivňuje i hodnoty proměnných `$#` a `$*`,
resp. `$@`.

# Speciální proměnné
Bash nám nabízí několik speciálních proměnných, které nám umožňují získat
užitečné informace, nebo ovlivňovat chování interpretu.

Kromě proměnných pro přístup k argumentům je to proměnná `$?`, která obsahuje
návratovou hodnotu posledního spuštěného příkazu. V případě, že poslední příkaz
obsahoval roury a spouštěl tedy více paralelních procesů, obsahuje proměnná
`$?` návratovou hodnotu posledního příkazu v kaskádě. Návratové hodnoty
předchozích příkazů můžeme získat z proměnné `PIPESTATUS`, která obsahuje pole
návratových hodnot jednotlivých procesů v kaskádě.

Další užitečnou proměnnou je `$PWD`, která obsahuje cestu k aktuálnímu
pracovnímu adresáři (nezaměňovat s příkazem `pwd`).

Velmi užitečnou proměnnou je `$IFS` (internal field separator), která ovlivňuje
rozdělování a spojování řetězců při mnoha různých operacích. Obsahem proměnné
`$IFS` je seznam znaků, které jsou považovány za oddělovače. V případě volání
příkazu, který rozděluje vstupní řetězec na pole nebo seznam argumentů je tento
řetězec rozdělován v místech výskytu těchto oddělovačů. V případě spojování
více řetězců do jednoho jsou spojené řetězce odděleny prvním znakem této
proměnné (např. při použití výrazu `$*` nebo `${POLE[*]}`). Výchozí nastavení
proměnné `$IFS` obsahuje 3 znaky: mezeru, tabulátor a konec řádku.  Pokud tedy
například budeme číst seznam souborů z výstupu programu `ls` a ukládat je do
položek pole, je vhodné předem nastavit proměnnou pouze na konec řádku, čímž
zabráníme rozdělení souborů s mezerami v názvu na více položek:
```bash
OLD_IFS=$IFS    # uschování původního nastavení IFS
IFS=$'\n'       # tento zápis uloží do proměnné IFS znak "new-line"
SOUBORY=( `ls` )        # přečtení názvů souborů v aktuálním adresáři do pole "SOUBORY"
IFS=$OLD_IFS    # je rozumné nastavit proměnnou IFS na původní nastavení s ohledem na chování ostatních příkazů
```
# Globy (wildcards)
Při práci se soubory, zejména v interaktivním uživatelském režimu, často
oceníme tzv. *globy* -- vzory umožňující jednoduše vytvořit seznamy souborů s
podobnými názvy. Tyto vzory mohou obsahovat následující znaky:
- `*` (hvězička) -- reprezentuje libovolný řetězec
- `?` (otazník) -- reprezentuje jeden libovolný znak
- `[...]` (seznam znaků v hranatých závorkách) -- reprezentuje jeden znak ze
  zadané množiny

V případě že interpret bashe narazí na řetězec obsahující tyto znaky, jsou
nalezeny všechny soubory, jejichž názvy odpovídají danému výrazu a seznam názvů
těchto souborů je vložen na místo původního výrazu. Pozor, toto nahrazení se
používá pouze v případě, že uvedený výraz NENÍ uzavřen v žádných uvozovkách --
v opačném případě se výraz použije jako obyčejný řetězec tak jak je. Z toho je
zřejmé že použití globů uvnitř skriptu nemusí být rozumné s ohledem na možné
nežádoucí chování, bude-li skript pracovat se soubory obsahujícími mezery.

# Vstup a výstup
Běžící skript má, stejně jako každý jiný proces, k dispozici svůj standardní
vstup, výstup a chybový výstup, jejichž prostřednictvím si vyměňuje data s
jinými procesy nebo uživatelem. Nejjednodušší způsob výpisu řetězce na
standardní výstup umožňuje příkaz `echo`. Častěji však využíváme výstupu jiných
programů spuštěných z našeho skriptu -- pokud jejich výstup explicitně
nepřesměrujeme, je automaticky napojen na výstup procesu běžícího skriptu.
Výpisu na chybový výstup docílíme prostým přesměrováním výstupu příkazu echo
(nebo libovolného jiného příkazu):
```bash
echo "Chybove hlaseni" >&2
```
Pro čtení jedné řádky standardního vstupu můžeme použít příkaz `read`. Syntaxe
jeho volání je
```bash
read PROMENNA
```
Uvedený příkaz přečte jednu řádku ze vstupu (pokud vstupní buffer neobsahuje
žádná data, příkaz zablokuje proces a čeká na vstup) a uloží ji do proměnné s
názvem `PROMENNA`. V případě chyby, nebo konce souboru přesměrovaného na vstup,
vrací příkaz `read` nenulovou návratovou hodnotu. Pro zpracování víceřádkového
vstupu je tedy nutné volat příkaz `read` opakovaně.

# Řízení běhu programu, podmínky a cykly
Bezpodmínečné ukončení běhu skriptu je možné příkazem exit. Nepovinným
parametrem je návratová hodnota, která je vrácena rodičovskému procesu, výchozí
návratová hodnota je `0`.

## Podmínky
Podmínky jsou v bashi realizovány příkazem if, jehož syntaxe je
```bash
if <příkaz>
then
    <příkaz1>
    <příkaz2>
    ...
else
    <příkaz3>
    ...
fi
```
Za klíčovým slovem `if` následuje příkaz, který je spuštěn, a na základě jeho
návratové hodnoty probíhá interpretace kódu prováděním příkazů za klíčovým
slovem `then` (návratová hodnota `0`), nebo `else` (nenulová návratová
hodnota). Blok “`else`” je nepovinný. Pro vyhodnocení podmínek s využitím
operátorů nad hodnotami proměnných slouží příkaz `test`. Ten umožňuje provádět
operace nad řetězci (porovnávání, test prázdného řetězce), čísly (základní
algebraické operace) a soubory (existence souboru, typ souboru, přístupová
práva apod.). Příkaz
```bash
test "$A" = "abcd"
```
tedy vrátí návratovou hodnotu `0`, pokud je obsahem proměnné `A` řetězec
“`abcd`”. Pro praktické použití příkazu `test` je výhodnější jeho úspornější a
přehlednější zápis hranatými závorkami:
```bash
if [ "$A" = "abcd" ]; then
    # podmínka splněna
fi
```
Příkaz test očekává všechny operandy a operátory jako samostatné argumenty. Při
použití zápisu v hranatých závorkách si proto dejte dobrý pozor na důsledné
oddělení všech operátorů a operandů mezerami. Pokud tyto mezery vynecháte, jako
v tomto případě:
```bash
if [ "$A"="abcd" ]; then ... ; fi
```
obdrží příkaz test jediný parametr “`...=abcd`” a vrátí vždy `0`, protože se
jedná o neprázdný řetězec (výchozí test, pokud není zadán žádný operátor). Dále
důsledně dbejte uzavírání hodnot proměnných do uvozovek, jinak můžete narazit
na problémy v případě proměnné obsahující prázdný řetězec:
```bash
if [ $A = "abcd" ]; then ... ; fi
```
Uvedený příkaz bude fungovat bez problémů jen tehdy, bude li obsah proměnné `A`
neprázdný. V opačném případě obdrží příkaz test pouze argumenty “`=`” a
“`abcd`” a skončí chybou vyžadující dva operandy pro operátor “`=`”. Užitečné
operátory příkazu test jsou:
```bash
-n STRING ... řetězec je neprázdný
-z STRING ... řetězec je prázdný
STRING1 = STRING2 ... dva řetězce jsou shodné
STRING1 != STRING2 ... dva řetězce jsou rozdílné
INTEGER1 -eq INTEGER2 ... dvě čísla jsou si rovná
INTEGER1 -ne INTEGER2 ... dvě čísla si nejsou rovná
INTEGER1 -gt INTEGER2 ... INTEGER1 je větší než INTEGER2
INTEGER1 -ge INTEGER2 ... INTEGER1 je větší nebo rovno INTEGER2
INTEGER1 -lt INTEGER2 ... INTEGER1 je menší než INTEGER2
INTEGER1 -le INTEGER2 ... INTEGER1 je menší nebo rovno INTEGER2
-e FILE ... existuje soubor s názvem FILE
-f FILE ... existuje obyčejný soubor s názvem FILE
-d FILE ... existuje adresář s názvem FILE
-r FILE ... existuje soubor s názvem FILE a mám práva pro čtení
-w FILE ... existuje soubor s názvem FILE a mám práva pro zápis
-L FILE ... soubor FILE je existující symbolický odkaz
```
Příkaz `test` dále umožňuje konjunkce a disjunkce výrazů operátory `-a` (AND) a
`-o` (OR) a uzavírání dílčích výrazů do závorek.

V případě, že potřebujeme hodnotu proměnné porovnávat s více jinými hodnotami,
můžeme použít příkaz `case`, jehož syntaxe je
```bash
case $PROMENNA in
hodnota1)
    <příkaz>
    <příkaz>
    ...
    ;;
hodnota2)
    <příkaz>
    ;;
hodnota3|hodnota4)
    <příkaz>
    ;;
*)
    <příkaz>
    ;;
esac
```
Hodnota v zadané proměnné je porovnávána s jednotlivými zadanými hodnotami (což
může být i glob) a v případě shody se provádí blok příslušných příkazů až do
dvojice středníků ukončujících blok. Svislítkem `|` je možné oddělit více
hodnot, pro které se má vykonávat stejný blok příkazů. V případě, že žádná
hodnota neodpovídá, provádí se blok `*)`, pokud existuje.

## Seznamy příkazů
Bash nabízí operátory `&&` a `||`, umožnující spojování příkazů do seznamů (též
řetězení příkazů) s podmíněným vykonáváním následujících příkazů. Interpretace
seznamu příkazů
```bash
<příkaz1> && <příkaz2>
```
vykoná `příkaz1` a pokud ten vrátí nulovou návratovou hodnotu, vykoná se i
`příkaz2`. Operátor `||` se používá obdobně s tím rozdílem, že následující
příkaz se provede pouze pokud je návratová hodnota nenulová. Uvedené operátory
umožňují zkrátit zápis podmínek zejména v případech, kdy na základě vyhodnocení
podmínky chceme (nebo nechceme) spustit jediný příkaz. Operátory je možné
libovolně řetězit a vzájemně kombinovat.

## Cykly
Bash nabízí tři druhy cyklů: `for`, `while` a `until`.

Cyklus `for` slouží k procházení seznamu hodnot a jeho syntaxe je
```bash
for PROMENNA in seznam_hodnot
do
    <příkaz>
    ...
done
```
Příkaz `for` postupně přiřazuje položky ze seznamu (řetězec hodnot oddělených
`$IFS` nebo pole) do proměnné `PROMENNA` a pro každé přiřazení vykoná seznam
příkazů uvnitř bloku `do ... done`.

Cykly `while` a `until` mají shodnou syntaxi a rozdíl mezi nimi je pouze ve
vyhodnocení podmínky:
```bash
while <příkaz> # nebo until <příkaz>
do
    <příkaz>
    ...
done
```
Na začátku každého průchodu cyklem se spustí příkaz následující klíčové slovo
`while` resp. `until`. Rozdíl mezi nimi je ten, že cyklus `while` pokračuje,
dokud je návratová hodnota spuštěného příkazu nulová, cyklus `until` pokračuje,
dokud je návratová hodnota nenulová. Cykly `while`/`until` je výhodné
kombinovat s příkazem `test`, stejně jako u podmínek.

Uvnitř všech cyklů (tedy uvnitř bloku `do ... done`) je možné používat příkazy
pro řízení průchodu cyklem `break` a `continue`. Příkaz `break` způsobí
okamžité ukončení cyklu. Příkaz `continue` přeskočí zbytek příkazů uvnitř cyklu
a skočí zpět na začátek cyklu.

# Aritmetické operace
Proměnné v bashi jsou řetězce. Pokud do proměnné uložíme celé číslo, uloží se
jako řetězec jednotlivých dekadických číslic. Příkaz `expr` umí převést zadané
řetězcové argumenty na čísla a provádět s nimi základní aritmetické operace.
Parametry příkazu `expr` jsou seznamem operandů, operátorů, případně závorek
aritmetického výrazu. Výsledek výpočtu vypisuje `expr` na standardní výstup.

Nejužitečnější operátory jsou sčítání (`+`), odečítání (`-`), násobení (`*`),
celočíselné dělení (`/`) a zbytek po celočíselném dělení (`%`). Kromě těchto
operátorů nabízí navíc relační operátory (ty už ale umíme realizovat příkazem
`test`) a dále několik operací pro práci s řetězci (ty však budeme schopni
provést jednodušeji jiným způsobem).

Při zadávání parametrů příkazu `expr` je nutné důsledně oddělovat mezerami
operátory a operandy, jinak je příkaz nerozpozná správně. Volání externího
příkazu `expr` není v rámci skriptu příliš pohodlné a přehledné, proto bash
nabízí zkrácený zápis `$(( výraz ))`. Tento zápis navíc poskytuje lepší parser
vstupního výrazu, nevadí mu chybějící mezery mezi operátory a operandy a navíc
automaticky nahrazuje jména proměnných jejich hodnotami. Inkrementace
celočíselné proměnné tak může vypadat takto:
```bash
A=$(( A+1 ))
```
Další možnosti aritmetických výpočtů nabízí vnitřní příkaz bashe `let`.
Poskytuje navíc mj. logické a bitové operátory a operátory inkrementace a
dekrementace. Inkrementaci proměnné je možné zapsat velmi úsporně
```bash
let A++
```
# Skupinové příkazy
Skupinu příkazů je možné uzavřít do složených závorek, což umožňuje např.
hromadné přesměrování vstupu/výstupu pro všechny příkazy:
```bash
{
    <příkaz>
    <příkaz>
    ...
} > soubor
```
Kromě složených závorek je možné použít také kulaté závorky, které navíc
způsobí, že příkazy uvnitř se vykonávají v samostatné instanci (nový proces)
interpretu bash. Proměnné nastavené v novém procesu si v původním procesu
nechávají svou hodnotu.

# Operace s řetězci
Bash nabízí některé základní operace nad řetězci uloženými v proměnné. Délku
řetězce v proměnné `RETEZEC` je možné získat zápisem
```bash
echo ${#RETEZEC}
```
Vytvoření pod-řetězce na základě počátečního indexu (první znak má index `0`) a
délky je možné
```bash
echo ${RETEZEC:<index>:<délka>}
```
Bash dále podporuje odřezávání začátků a konců řetězců na základě jejich
obsahu:
```bash
echo ${RETEZEC#zacatek} # odřízne na začátku řetězce $RETEZEC řetězec "zacatek"
                        # (pokud $RETEZEC tímto řetězcem začíná) a vrátí zbytek
echo ${RETEZEC%konec} # odřízne z konce řetězce $RETEZEC řetězec "konec"
                      # (pokud $RETEZEC tímto řetězcem končí) a vrátí zbytek
```
Síla těchto operací vynikne zejména při použití znaku `*`, reprezentujícícho
libovolný řetězec uvnitř hledaného výrazu. Zdvojením znaku `#` na `##`, resp
`%` na `%%` je pak možné ovlivnit hladovost výrazu obsahujícího `*`:
```bash
A="abcdefghabcdefgh"
echo ${A#*cde}     # vypíše "fghabcdefgh"
echo ${A##*cde}     # vypíše "fgh"
echo ${A%cde*}     # vypíše "abcdefghab"
echo ${A%%cde*}     # vypíše "ab"
```
Jednoduché nahrazení části řetězce za jiný řetězec je možné
```bash
echo ${RETEZEC/vzor/nahrazeni}   # vypíše řetězec z proměnné RETEZEC, přičemž řetězec "vzor" bude nahrazen řetězcem "nahrazeni"
```
Tento způsob umožňuje nahradit pouze první výskyt řetězce. Podporuje navíc znak
`*` zastupující libovolný řetězec.

Další možnosti práce s řetězci a texty nabízejí externí nástroje (viz příští
cvičení). Nástroj `cut` umožňuje ze vstupní řádky vyříznout znaky, slova, nebo
řetězce oddělené specifikovaným oddělovačem na základě jejich pořadí. Pro
hromadné nahrazení znaků za jiné znaky se hodí příkaz `tr`. Příkaz `tr` dostává
jako parametry dvě stejně dlouhé množiny znaků zadané jako řetězce a nahrazuje
ve vstupním textu znaky z první množiny odpovídajícími znaky z druhé množiny.
Pro spočítání znaků, slov, nebo řádek ve vstupním textu je možné použít příkaz
`wc` (word count).

# Funkce
Definice funkce v jazyce bash je možná dvěma způsoby:
```bash
function nazev_funkce
{
    <příkaz>
    <příkaz>
    ...
}
```
nebo
```bash
nazev_funkce()
{
    <příkaz>
    ...
}
```
Funkce nemá pevný počet parametrů. K jednotlivým parametrům se uvnitř funkce
dostanete přes proměnné `$1`, `$2`, atd., podobně jako mimo funkce pracujete s
parametry skriptu. Analogicky se uvnitř funkce chovají proměnné `$#`, `$*`,
`$@` a příkaz `shift`. Návratová hodnota funkce je dána návratovou hodnotou
posledního příkazu. Provádění funkce je možné ukončit příkazem `return`, jehož
nepovinným parametrem je návratová hodnota. Funkce se volá prostým uvedením
jejího jména, následovaného parametry, stejně jako se volá externí program nebo
skript.

Proměnné uvnitř funkcí mají globální viditelnost. Pokud chceme mít ve funkci
proměnnou pouze lokální (tak, aby nebyly ovlivňovány proměnné stejného jména
vně funkce), je možné explicitně deklarovat funkci jako lokální:
```bash
function funkce
{
    local A
    A=1
}
A=2
funkce
echo $A      # vypíše "2"
```
# Zpracování přepínačů v argumentech skriptu
Syntaxe volání většiny nástrojů v unixových systémech se řídí doporučeními
podle standardu POSIX. Při zpracování jednopísmenných přepínačů dle této
syntaxe vám pomůže příkaz `getopts`. Syntaxe příkazu je
```bash
getopts options proměnná
```
První parametr (`options`) je řetězec obsahující seznam písmen podporovaných
přepínačů. Pokud má daný přepínač argument, následuje za písmenem dvojtečka.
Dvojtečka na začátku řetězce nastavuje tichý mód, ve kterém se nevypisují chyby
při zadání neplatného přepínače. Druhým parametrem je název proměnné, do které
se uloží písmeno rozpoznaného přepínače. Každé volání příkazu `getopts`
zpracuje jediný argument skriptu, proto je nutné jej volat v cyklu. Použití
příkazu je zřejmé z následujícího příkladu:
```bash
while getopts ":abchn:s:" opt; do
    case $opt in
    a|b|c) echo "Byl aktivovan prepinac -${opt}"
        ;;
    n) echo "Prepinac -n ma hodnotu ${OPTARG}"
        ;;
    s) echo "Prepinac -s ma hodnotu ${OPTARG}"
        ;;
    h) echo "Pouziti skriptu:"
        echo "`basename $0` [-a] [-b] [-c] [-h] [-n <argument>] [-s <argument>]"
        ;;
    ?) echo "Byl zadan neplatny prepinac -${OPTARG}"
        ;;
    esac
done
shift $(($OPTIND - 1))
if [ $# -gt 0 ]; then
    echo "Ostatni parametry skriptu: $*"
fi
```
Uvedený skript rozpoznává přepínače `-a`, `-b`, `-c` a `-h`, dále přepínače
`-n` a `-s`, které vyžadují argument, a dále libovolný počet dalších parametrů,
které nezačínají znakem '`-`' (uvedená implementace vyžaduje, aby byly
přepínače ve tvaru `-písmeno` uvedeny před ostatními parametry). Jednotlivé
přepínače mohou být zadány v libovolném pořadí. Příkaz `getopts` nastavuje
proměnné `$OPTARG` (hodnota argumentu aktuálního přepínače) a `$OPTIND` (index
aktuálně zpracovávaného parametru skriptu).

Varianta příkazu s názvem `getopt` umí zpracovávat i volby v dlouhém formátu
(např. `--help`).

# Další užitečné příkazy a externí nástroje
## `eval`
Příkaz `eval` interpretuje řetězec zadaný parametrem jako příkaz, který vykoná.
Využijete jej tam, kde si potřebujete připravit složitější příkaz (např. podle
zadaných parametrů skriptu) a pak jej vykonat.

## `cat`
Nástroj `cat` slouží ke spojení obsahu více souborů, zadaných jako parametry,
do jednoho výstupu. Pokud není zadán žádný vstupní soubor, čte `cat` data ze
standardního vstupu. Tohoto chování je možné s výhodou využít, pokud píšeme
skript, který čte data buď ze zadaného souboru, nebo ze standardního vstupu,
není-li soubor zadán.

## `find`
Příkaz `find` slouží k rekurzivnímu procházení stromové struktury zadaného
adresáře. Nalezené soubory buď vypisuje na standardní výstup, nebo nad nimi
vykoná zadaný příkaz. Parametry příkazu `find` umožňují filtrovat výsledky
podle mnoha kritérií (např. jméno nebo typ souboru). Použitím příkazu `find` se
vyhnete implementaci rekurze při procházení podadresářů.

## `dirname`
Příkaz `dirname` vrátí cestu k nadřazenému adresáři pro zadaný soubor nebo
adresář. V případě, že zadaná cesta je relativní, vrací také relativní cestu.

## `basename`
Příkaz `basename` odstraní z cesty k zadanému souboru či adresáři cestu k
nadřazenému adresáři a vrátí pouze jeho jméno.

## `readlink`
Příkaz `readlink` umožňuje zjistit, na jaký soubor odkazuje symbolický odkaz.
Užitečný je jeho parametr `-f`, se kterým příkaz vrací absolutní cestu a to i v
případě, že není aplikován na odkaz.

## `stat`
Příkaz `stat` umožňuje získat dostupné informace o souboru -- velikost, čas
změny, oprávnění a další informace uložené v *I-node*. Jeho parametry umožňují
libovolné formátování požadovaných informací.

## `diff`
Příkaz `diff` slouží k porovnání obsahu dvou textových souborů. Na výstup
vypisuje nalezené změny ve formátu závisejícím na použitých parametrech.

## `seq`
Příkaz `seq` generuje posloupnosti čísel v zadaném rozsahu a zadaným krokem.
Hodí se např. pro implementaci cyklu `for`.

## `head` a `tail`
Příkazy `head` a `tail` vrací `N` prvních resp. posledních řádek zadaného
souboru, nebo textu na standardním vstupu, kde `N` je číslo zadané jako
argument parametru `-N`.

## `sort`
Příkaz `sort` slouží k seřazení řádků textu (ze souboru nebo standardního
vstupu) podle abecedy. Parametry příkazu umožňují změnit způsob řazení.

## `uniq`
Příkaz `uniq` kopíruje řádky standardního vstupu na výstup s vynecháním
bezprostředně následujících duplicitních řádků. Parametr `-c` umožňuje sčítat
počty duplicitních řádků.

## `grep`
`grep` je užitečný nástroj pro filtrování řádek textu podle jejich obsahu.
Povinným parametrem příkazu je *vzor*, se kterým jsou porovnávány vstupní řádky
a ty odpovídající jsou vytištěny na výstup. *Vzor* pro porovnávání je regulární
výraz.

## `awk`
`awk` je nástroj pro zpracování vstupního textu řádku po řádce, přičemž na
každou řádku se aplikuje skript napsaný ve vlastním jazyce. Tento skript se
skládá z řádek ve tvaru `<vzor> { <akce> }`. *Vzor* představuje regulární
výraz, nebo podmínku, která se vyhodnocuje pro každou řádku vstupu a v případě
jejího splnění je na řádek aplikována zadaná *akce* -- příkaz jazyka `awk`.
`awk` automaticky rozděluje vstupní text na řetězce podle zadaného oddělovače,
což dále usnadňuje jeho zpracování. Podporuje proměnné, pole, aritmetické
operace a programové konstrukce podobně jako jazyk `C` (`if`, `while`, `for`,
...). Dále poskytuje užitečné funkce pro zpracování textu nebo matematické
funkce.

## `sed`
`sed` je nástroj pro zpracování vstupního textu podle programu napsaného ve
vlastním jazyce. Jedná se o velmi efektivní nástroj s širokými možnostmi
použití. Velmi výhodná je jeho schopnost pracovat s regulárními výrazy pro
porovnávání nebo nahrazování textu.

# Odkazy a další materiály
- [Advance Bash Scripting Guide](http://tldp.org/LDP/abs/html/)
- [BASH -- manuálová stránka](http://linux.die.net/man/1/bash)
- [Vestavěné příkazy bash](http://www.gnu.org/software/bash/manual/html_node/Bourne-Shell-Builtins.html)
