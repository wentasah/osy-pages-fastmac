---
title: "Cvičení"
weight: 2
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Učebny
Cvičení probíhají v učebně
[KN:E-2, nebo T2:H1-131](https://www.fel.cvut.cz/cz/education/rozvrhy-ng.B191/public/html/predmety/46/83/p4683606.html).

# Program cvičení
| cv.   | body  | náplň
| :---: | :---: | :---
| 1.    | --    | [OS unix -- úvod](lab1)
| 2.    | 2     | [Skriptovací jazyk Bash](lab2)
| 3.    | 4     | [Zpracování textu v Bashi](lab3)
| 4.    | 5     | [Makefile a meziprocesní komunikace v C](lab4)
| 5.    | 6     | [Vlákna a synchronizace v C](lab5)
| 6.    | 12    | [Pokročilá synchronizace](lab6)
| 7.    | --    | [Synchronizace -- dokončení](lab7)
| 8.    | 5     | [Systémová volání -- Linux](lab8)
| 9.    | (5)   | [Stack overflow exploit](lab9)
| 10.   | 9     | [Systémová volání -- NOVA](lab10)
| 11.   | 7     | [Paměťový alokátor](lab11)
| 12.   | (5)   | [Přepínání vláken -- NOVA](lab12)
| 13.   | (4)   | [Linux & BusyBox](lab13)
| 14.   | --    | Zápočet
| Součet | 50 (+14) |

{{< hint info >}}
V závorce jsou bonusové body za nepovinné úkoly.
{{< /hint >}}

# Zápočet
Zápočet získáte, pokud:
- odevzdáte funkční všechny povinné úlohy,
- ze cvičení získáte minimálně 25 bodů,
- vaše účast na cvičeních bude minimálně 60%.

# Pravidla
- Od studentů se očekává domácí příprava na další cvičení.
- Body se udělují za domácí úkoly a za aktivitu při cvičení:
    - Za včas odevzdanou úlohu dostanete 2–12 bodů podle typu úlohy
    - Body za úlohy se strhávají za **pozdní odevzdání** a za další
      **nedostatky** odhalené cvičícím.
    - Na začátku každého cvičení bude možnost získat body za odpovědi na otázky
      cvičícího z **domácí přípravy**. Na jednom cvičení lze z domácí přípravy
      získat maximálně 1 bod, za semestr pak max. 10 bodů.
- Všechny úlohy se odevzdávají přes [odevzdávací systém][hw-upload]
    - Úlohy jsou vyhodnocovány automaticky, výsledek (body) se dozvíte na konci
      protokolu z automatického vyhodnocení (položka Total score).
    - Automatické vyhodnocení může být puštěno znovu i na už odevzdanou úlohu.
      Pokud se váš program nechová deterministicky, může to vést k odlišnému
      (nižšímu) hodnocení.
    - V systému se vám body objeví až po manuální kontrole učitelem. Pokud
      nebudou shledány závažné nedostatky, bude bodové ohodnocení rovno počtu
      bodů z automatického vyhodnocení zmenšeném o penalizaci za pozdní
      odevzdání.
    - Všechny úlohy budou (automaticky) kontrolovány na plagiátorství. V
      případě, že odevzdaná úloha bude označena jako plagiát, budeme postupovat
      dle instrukcí [Plagiáty a opisování][plagiat].

[hw-upload]: https://cw.felk.cvut.cz/brute/student/course/B4B35OSY
[plagiat]: https://cw.fel.cvut.cz/wiki/help/common/plagiaty_opisovani

# Vzdálený přístup k uživatelským kontům
Ke svému domovskému adresáři používanému při cvičeních se můžete dostat
odkudkoli z internetu protokolem SSH či SFTP přes server postel.felk.cvut.cz:

    ssh «login»@postel.felk.cvut.cz

Heslo je stejné jako na počítačích v laboratoři, tj. hlavní přístupové heslo.
