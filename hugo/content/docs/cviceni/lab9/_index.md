---
title: "9. Přetečení zásobníku"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Útok pomocí přetečení zásobníku
## Domácí příprava
Většina informací potřebných pro řešení této úlohy zazněla na přednášce. Dále
se vám může hodit vědět následující:
- Jak funguje protokol [HTTP 1.1][1] ([specifikace][2])
- K čemu slouží příkaz `nm`.
- [Základní příkazy debuggeru gdb][3]: `run`, `break`, `print`, `next`, `step`,
  `nexti`, `stepi`, `layout asm`, `layout regs`. Kromě `gdb` můžete použít i
  jiné debuggery, či grafické nadstavby.
- Pokud budete potřebovat přeložit assembler do strojového kódu,
  můžete použít příkaz `as --32`, či `gcc -m32 source.s` (`gcc`
  automaticky kompiluje přípony `.s` a `.S` pouze assemblerem).
- Příkazem `objcopy` můžete vykopírovat “holé” strojové instrukce z programu.
  Např. příkaz
  ```bash
  objcopy -O binary --only-section=.text --only-section=.rodata prog prog.bin
  ```
  vykopíruje z programu `prog` kód (`.text`) a read-only data (globální
  proměnné deklarované jako `const`).
- Disassemblovat “holou” binárku (např. soubor `prog.bin` vytvořený výše
  uvedeným příkazem `objcopy`) můžete příkazem
  ```bash
  objdump -D -b binary -m i386 prog.bin
  ```
  nebo
  ```bash
  ndisasm -b 32 prog.bin
  ```
- Ke komunikaci s webovým serverem nemusíte používat jen webový prohlížeč, ale
  třeba i nástroje `nc`, `wget` nebo `curl`.

[1]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_message
[2]: https://tools.ietf.org/html/rfc7230#section-3
[3]: https://sourceware.org/gdb/current/onlinedocs/gdb/Command-and-Variable-Index.html#Command-and-Variable-Index

# Zadání úlohy
{{< hint info >}}
Tato úloha není povinná.
{{< /hint >}}

Vytvořte útok na [tento webový server][4]. Můžete si vybrat z několika různě
složitých variant útoku:
1. Způsobte, že server vypíše na svůj standardní výstup řádku “`pwned!`” (bez
   uvozovek) -- 4 body.
2. Zjistěte, co je na serveru v souboru `/tmp/secret.txt` -- 5 bodů.
3. Změňte obrázek na [produkčním serveru][4] -- více jak 5 bodů (záleží na
   domluvě se cvičícím).

Do odevzdávacího systému nahrávejte archiv, který bude obsahovat všechny vaše
zdrojové soubory a spustitelný soubor `exploit`. Ten bude spuštěn a jako
parametr mu bude předáno URL webového serveru, na který má zaútočit -- např.
`./exploit http://localhost:8080`. V případě 1. varianty zadání nebude na
stdout vypsáno nic (`pwned!` se vypíše na straně serveru), u 2. varianty se tam
vypíše obsah souboru `/tmp/secret.txt`. 3. variantu odevzdávací systém
netestuje, počítá se pouze změna obrázku na produkčním serveru. Soubor
`exploit` může být např.  skript, který přeloží, co je potřeba, a spustí
vlastní útok.

[4]: http://rtime.felk.cvut.cz:8080/

# Nápověda
- Doporučujeme obstarat si binárku serveru a vyvíjet útok lokálně. Když
  uspějete lokálně, vyzkoušejte to na [našem serveru][4] (smysl má pouze
  varianta 2) a pak svůj útok nahrajte do upload systému.
- Budete potřebovat znát alespoň [základy HTTP protokolu][5].
- Server je 32bitový program, ale to byste určitě zjistili i sami.
- Abychom vám hackování trochu ulehčili, podstatné části kódu serveru,
  zpracovávající HTTP požadavky, nepracují s nulou ukončenými řetězci.
- Může se vám hodit nástroj [radare][], který používá mnoho profesionálů.
  Základní informace o použití radare jako debuggeru najdete [zde][6] či
  [zde][7].
- Další nápovědu můžete získat na [fóru][forum].

[5]: https://cs.wikipedia.org/wiki/Hypertext_Transfer_Protocol
[radare]: http://www.radare.org/r/
[6]: https://github.com/radare/radare2/blob/master/doc/intro.md#debugger
[7]: https://radare.gitbooks.io/radare2book/first_steps/basic_debugger_session.html
[forum]: https://cw.felk.cvut.cz/forum/thread-4120.html
