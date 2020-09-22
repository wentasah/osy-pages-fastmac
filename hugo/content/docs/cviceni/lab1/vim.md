---
title: "Textový editor VIM"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Základy používání textového editoru VIM
Vim je mocný textový editor, který se vyvíjí již řadu desetiletí a každý
UNIX-ový uživatel by měl mít alespoň základní povědomí o jeho použití a
možnostech. Pro vim existuje řada užitečných pluginů, které rozšiřují jeho
základní funkce a umožňují každému uživateli přizpůsobt si jej k valstním
potřebám. Řada programátorů proto vim používá jako plnohodnotnou a efektivní
náhradu za rozsáhlá a náročná integrovaná vývojová prostředí (IDE).

Ovládání editoru vim je navrženo tak, aby umožňovalo efektivní a rychlou práci,
což může být pro začínajícího uživatele matoucí a zdánlivě komplikované.

Nejdůležitější vlastnost editoru vim, která ho také odlišuje od většiny
ostatních editorů je, že má **3 režimy** práce:
- příkazový (normální) režim,
- vkládací režim,
- režim příkazové řádky.

**Příkazový režim**, který je výchozí po spuštění vimu, umožňuje zadávání
příkazů. Příkazy jsou reprezentovány jedním znakem, resp. stiskem klávesy. Pro
zadávání textu je nutné přepnout vim do **vkládacího režimu** vhodným příkazem,
např. 'i' (insert). Vkládací režim ukončujeme stiskem klávesy ESC. Do režimu
**příkazové řádky** se dostaneme příkazem '`:`' (dvojtečka). V režimu příkazové
řádky je možné zadávat komplikovanější příkazy s parametry. Stiskem klávesy
`ENTER` se příkaz vykoná a vim se vrací do příkazového režimu.

Aktuální režim poznáte podle stavové řádky dole: ve vkládacím režimu tam
uvidíte “`-- INSERT --`”, v režimu příkazové řádky tam bude “`:`” a za ní
blikající kurzor.

{{< hint info >}}
Editor vim se ukončuje příkazem `quit` (zkratka `q`) v režimu příkazové řádky
(tedy stiskem `:` + `q` + `ENTER`).
{{< /hint >}}

# Základní příkazy v příkazovém režimu

    i (insert) — vložení textu na aktuální pozici kurzoru
    a (append) — vložení textu za aktuální pozici kurzoru
    I — vložit text na začátek aktuálního řádku
    A — vložit text na konec aktuálního řádku
    x — smazat znak na pozici kurzoru
    r — nahraď znak pod kurzorem (následuje nový znak); nepřepíná režim
    D — smazat vše od kurzoru do konce řádku
    w — posun kurzoru na začátek dalšího slova
    b — posun kurzoru na konec předchozího slova
    { — posun kurzoru na předchozí prázdnou řádku (začátek odstavce)
    } — posun kurzoru na následující prázdnou řádku
    gg — navigace na začátek souboru
    G — navigace na konec souboru
    % — navigace kurzoru na odpovídající párovou závorku
    / — hledání textu (po stisku / je nutné zadat hledaný text)
    n — opakování posledního hledání
    u — undo — vrácení poslední změny (zrušení posledního příkazu)
    <CTRL>+r — redo
    . — zopakování posledního provedeného příkazu

Navigace kurzoru je možná kurzorovými šipkami, ovšem pozor, někdy nemusí
fungovat správně (např. při špatném nastavení terminálu při vzdáleném
připojení). Proto je dobré znát původní navigační klávesy:

    h — doleva
    j — dolů
    k — nahoru
    l — doprava

Ve vkládacím režimu je zpravidla možné používat pro navigaci kurzorové šipky
podobně jako v jiných editorech. Pokud však vlivem nastavení nefungují, je
potřeba se pro změnu pozice kurzoru vrátit do příkazového režimu.

# Základní příkazy příkazové řádky
Většinu základních příkazů je možné volat jejich zkratkami — v následujícím
seznamu jsou uvedeny v závorce.

    quit (q) — ukončení editoru. Pokud nechcete ukládat upravený soubor, použijte q!
    write (w) — uložení aktualního souboru
    edit (e) — otevření jiného souboru (název souboru následuje za příkazem)
    !<příkaz> — spuštění systémového příkazu
    redo — vrácení příkazu zrušeného přes “undo”

Chování vimu je možné změnit řadou jeho nastavení, která je možné změnit
příkazem set:

    set number — zapnutí číslování řádek
    set nonumber — vypnutí číslování řádek
    set wrap / set nowrap — zapnutí/vypnutí zalamování dlouhých řádků

# Komplikovanější příkazy příkazového režimu
Některé příkazy se vykonávají stiskem více kláves po sobě.

Stiskem čísla před klávesou příkazu se zadává počet opakování příkazu. Např.
stiskem kombinace “`20a*<ESC>`” se za pozici kurzoru vloží 20 znaků “`*`”.

    d — mazání; je nutná další klávesa specifikující co se má smazat:
    dd — smaž celý aktuální řádek
    dw — smaž aktuální slovo pod kurzorem
    dgg — smaž všechny řádky od začátkusouboru po kurzor
    dG — smaž všechny řádky od kurzoru do konce souboru
    c — nahraď text; další klávesa říká co se nahrazuje; příkaz přepíná do vkládacího režimu
    cw — nahraď slovo pod kurzorem

# Odkazy
- https://vim.sourceforge.io/about.php
- http://www.viemu.com/vi-vim-cheat-sheet.gif
- http://www.abclinuxu.cz/clanky/navody/editor-vim-prakticky-i
- http://fotomobilpc.i-prehled.cz/vim/
- https://www.fprintf.net/vimCheatSheet.html
