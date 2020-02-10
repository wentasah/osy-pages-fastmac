---
title: "Základní UNIXové příkazy"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Základní UNIXové příkazy
Plný popis všech příkazů lze nalézt v POSIX definici shell příkazů [Posix
standard](http://pubs.opengroup.org/onlinepubs/9699919799/).

Podrobné popisy příkazů získáte použitím příkazu: `man název_příkazu`.

{{< hint warning >}}
Unixová příkazová řádka je citlivá na velikost písmen, všechny příkazy jsou
psané malými písmeny. K zrychlení zadávání můžete použít klávesu `TAB` k
doplnění jména příkazu, případně `TAB TAB` k zobrazení možných variant příkazů
či jmen souborů.
{{< /hint >}}

# Soubory
- `ls` -- výpis souborů (list files)
- `ls -l` -- dlouhý výpis, který obsahuje podrobné informace o každém souboru
  (přístupová práva, vlastník, velikost, čas modifikace)
- `ls -a` -- vypsání všech souborů (skryté soubory začínají znakem '`.`')
- `ls` -- existuje další velké množství přepínačů, které ovlivňují typ výpisu
  souborů, např. seřadit výpis podle velkisti souborů, podle data modifikace,
  rekurzivně, apod. Použijte `man ls`.
- `more filename` -- zobrazí začátek, první stranu souboru, mezerník a enter
  umožní zobrazit další řádku, stránku souboru, `q` - konec zobrazovaní
  souboru, `/vzor` hledání vzoru v souboru.
- `emacs filename` -- editor spuštěný na otevření souboru.
- `joe filename` -- jednoduchý editor. Stiskem `ctrl+K` následovaný `H` pro
  nápovědu (help), `ctrl+C` ukončení, `ctrl+K` následovaná `X` pro uložení a
  ukončení
- `vim` -- velmi sofistikovaný editor, velmi vhodný pro použití bez myši -
  klávesové zkratky na vše
- `mv filename1 filename2` -- move a file -- přesunutí souboru (t.j. při zadání
  stejného adresáře je to přejmenování, nebo přesunutí do jiného adresáře),
  více možností
- `cp filename1 filename2` -- copy a file -- kopírování souboru, případně
  souborů -- více možností
- `rm filename` -- removes a file -- mazání souboru, souborů. Je rozumné volat
  s přepínačem `rm -i`, který se vás zeptá na potvrzení předtím, než je cokoliv
  smazáno. Lze nastavit jako výchozí přepínač vytvořením aliasu v souboru
  `~/.bashrc` či `~/.cshrc`.
- `diff filename1 filename2` -- difference -- ukáže rozdíly mezi soubory,
  vhodné pro správu verzí
- `wc filename` -- word count -- statistika souboru, kolik obsahuje slov,
  řádek, znaků
- `tr set1 set2` -- transfer characters -- zaměň znaky, čte standardní vstup a
  zamění zadané znaky za jiné (např. na velká písmena, malá písmena, odstraní
  některé znaky). Příklad `tr [a-z] [A-Z]` zamění malá písmena za velká.
- `chmod options filename` -- change mode -- změna práv v přístupu k souboru.
  Příklad `chmod o+r filename` zavede právo pro čtení pro každého uživatele;
  `chmod o-r filename` zruší právo pro všechny kromě vlastníka a stejné skupiny
  uživatelů.
- `sed` -- stream editor -- automaticky modifikuje/edituje soubor.

# Komprese souborů
- `tar -czf archiv_name list_of_files` -- zkomprimuje zadané soubory
  `list_of_files` do nového souboru `archiv_name` s použitím formátu *gziped*.
  Standardní koncovka pro tyto souboru je `.tgz` nebo `.tar.gz`.
- `tar -xzf archiv_name where` -- extrahuje soubory z archivu `archiv_name` do
  adresáře `where`. Více podrobností `man tar`.
- `gzip filename` -- zkomprimuje zadané soubory. Gzip automaticky vyrobí
  soubory s koncovkou '`.gz`' k původnímu názvu souboru.
- `gunzip filename` -- rozbalí soubory zkomprimované příkazem `gzip`.
- `gzcat filename` -- umožní využít soubory zabalené příkazem `gzip` (to samé
  jako `gunzip -c`). Například můžete vytisknout zabalené soubory příkazem
  `gzcat filename | lpr`.

# Adresáře
Adresáře sdružují soubory do hierachické struktury.

- `mkdir dirname` -- vytvoří nový adresář (make a new directory)
- `cd dirname` -- change directory - změna pracovního adresáře, to znamená
  adresáře ve kterém se vykonávají příkazy, nebo odkud směřuje relativní cesta.
  Počátečním pracovním adresářem je většinou domácí adresář. Příkaz `cd` bez
  argumentů přepne do domácího adresáře, příkaz `cd ..` přesune pracovní
  adresář o jednu úroveň výše.
- `pwd` -- vytiskne jméno pracovního adresáře (print working directory)

# Hledání souborů a v souborech
- `find` -- hledá soubory v adresářích. Velmi užitečné pokud hledáte soubor a
  nevíte ve kterém adresáři byl uložen.
- `grep string filename(s)` -- hledá zadaný řetězec - string - v zadaných
  souborech či adresářích. Velmi užitečný příkaz prohledává obsah souborů a
  hledá zadané slova. Slova lze zadávat jako regulární výraz, tedy jako šablonu
  slov.

# O uživatelích
- `w` -- who -- zobrazí kteří uživatelé jsou přihlášení na daném počítači a
  jaký program mají spuštěný.
- `who` -- podobně jako w, zobrazí i odkud jsou uživatelé připojeni
- `finger username` -- zobrazí informace o zadaném uživateli.
- `last -1 username` -- zobrazí čas a způsob posledního přihlášení do syystému.
- `write username` -- zašle jednořádkovou zprávu zadanému uživateli

# Procesy
- `ps` -- seznam běžících procesů
- `jobs` -- seznam úloh spuštěných v aktuálním terminálu na pozadí (a jejich
  stav)
- `bg` -- spuštění zastavené úlohy na pozadí
- `fg` -- spuštění úlohy na pozadí v popředí
- `kill PID` -- pošle procesu se zadaným PID signál, který zpravidla proces
  ukončí. Ukončit můžete pouze svoje procesy. Signál *KILL* (parametr `-9`)
  proces ukončí bezpodmínečně.

# Nastavení uživatele
- `whoami` -- zobrazí vaše uživatelské jméno. Užitečné při postupném
  přihlašování na mnoho počítačů pod různými jmény.
- `passwd` -- umožní změnit vaše heslo.
- `ps -u yourusername` -- seznam vašich spuštěných procesů. Obsahuje mnoho
  užitečných informací ohledně čísla procesu, využití paměti, využití
  procesoru, době běhu, prioritách, apod.
- `df` -- zobrazí jaké disky systém využívá a kolik volného místa je na nich k
  dispozici.
- `du filename` -- zobrazí kolik místa zabírají zadané soubory, nebo adresáře,
  případně i jejich podadresáře.
- `last yourusername` -- vypíše historii vašich přihlášení do systému. Tato
  služba je užitečná, pokud chcete odhalit neoprávněné přihlášení na váš účet.

# Některé klávesové zkratky
- `CTRL+D` -- konec souboru / ukončení standardního vstupu
- `CTRL+C` -- zaslání signálu *SIGINT* (INTERRUPT) aktuálnímu procesu
- `CTRL+\` -- zaslání signálu *SIGQUIT*
- `CTRL+Z` -- zastavení procesu (pozor, proces není ukončen!)
- `CTRL+S` -- pozastavení (zmrazení) výpisu (proces však běží a vypisuje dál)
- `CTRL+Q` -- pokračování pozastaveného výpisu

# Odkazy na UNIXové příkazy
Je mnoho odkazů na internetu ohledně používání UNIXu a jeho příkazů. Nejvíce
zdrojů je v angličtině.

- [tabulka](https://cw.fel.cvut.cz/wiki/_media/courses/b4b35osy/cviceni/unix_command_cheatsheet.pdf)
- [Elektronické zdroje](http://mally.stanford.edu/%7Esr/computing/el-sources.html)
- [základní příkazy](http://mally.stanford.edu/~sr/computing/basic-unix.html)
- [seznam příkazů](https://en.wikipedia.org/wiki/List_of_Unix_commands)
- [česky](https://cs.wikipedia.org/wiki/Seznam_unixov%C3%BDch_utilit)

České zdroje existují
- [práce se soubory a adresáři](https://milux.php5.cz/skoleni/skoleni-unix-2.html)
- [unixové příkazy](https://cs.wikipedia.org/wiki/%C5%A0ablona:Unixov%C3%A9_p%C5%99%C3%ADkazy)
- [Skriptování v Bournově shellu](https://cs.wikibooks.org/wiki/Skriptov%C3%A1n%C3%AD_v_Bournov%C4%9B_shellu)
