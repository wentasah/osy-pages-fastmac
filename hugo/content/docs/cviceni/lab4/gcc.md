---
title: "Překladač GCC"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Přepínače překladače GCC
## Přepínače pro určení výstupu
Pokud jsou vstupem gcc zdrojové soubory, nejdříve je překládá a poté implicitně
spouští linker, aby z nich vytvořil spustitelný soubor. Toto chování lze měnit
následujícími přepínači:

    -c          kompilace do objektového souboru (vypnutí linkeru, který se spouští implicitně
    -E          ukončení po preprocesoru
    -S          ukončení po překladu do assembleru
    -shared     výstupem gcc je sdílená knihovna (.so)

## Obecné přepínače

    -o  NAZEV   určení názvu výstupního souboru

## Přepínače pro kompilátor

    -g          zapnutí ukládání debug-informací do přeložených souborů
    -W  LEVEL   nastavení úrovně varování, doporučeno nastavovat na -Wall
    -D  MAKRO   definice makra (anaogie #define) z příkazové řádky - možnost modifikace kódu v okamžiku překladu
    -I  CESTA   přidání cesty k adresáři, kde se hledají hlavičkové (.h) soubory; parametr -I se opakuje pro každý adresář

## Přepínače pro linker

    -l  LIB     přidání knihovny s daným názvem; přepínač -lknihovna hledá soubor libknihovna.so (popř. libknihovna.a)
    -L  CESTA   přidání cesty k adresáři, kde se hledají knihovny
