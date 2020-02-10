---
title: "8. Systémová volání"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Systémová volání a inline assembler
Cílem tohoto cvičení je detailně se seznámit s tím, jak aplikace volá služby
operačního systému a jak lze systémová volání volat přímo, bez použití knihoven
jako `libc`. Tato znalost se vám bude hodit v dalších cvičeních.

## Domácí příprava
Seznamte se se základními instrukcemi architektury `x86` a způsobem, jakým
vkládat instrukce assembleru přímo do zdrojového kódu v jazyce C/C+ +. Projděte
si [prezentaci o inline assembler][ia] a připomeňte si [první][l1] a [druhou
přednášku][l2].

Dále se seznamte s nástroji [strace][] a [ltrace][]. Doporučujeme si oba
nástroje vyzkoušet na následujícím programu:

```C
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("Hello world\n");
    return 0;
}
```

Pokud vás zajímají podrobnosti o kombinovaní assembleru a C, podívejte se do
[dokumentace překladače GCC][gcc]. Mimo jiné tam najdete popisy [obecných
omezení][1] a [omezení závislých na architektuře CPU][2] (hledejte sekci “x86
family”).

[ia]: pdf/inline_assembler.pdf
[l1]: /docs/prednasky/pdf/lekce01.pdf
[l2]: /docs/prednasky/pdf/lekce02.pdf
[strace]: https://linux.die.net/man/1/strace
[ltrace]: https://linux.die.net/man/1/ltrace
[gcc]: https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html
[1]: https://gcc.gnu.org/onlinedocs/gcc/Simple-Constraints.html
[2]: https://gcc.gnu.org/onlinedocs/gcc/Machine-Constraints.html

# Zadání úlohy
Vytvořte program, který čte ze standardního vstupu celá nezáporná dekadická
čísla oddělená mezerami nebo konci řádků a vypíše je na standardní výstup v
hexadecimální podobě oddělená koncem řádku. Program nesmí používat žádnou
standardní knihovnu jako např. `libc`. Předpokládejte, že maximální hodnota
čísla bude `2^32-1`.

Jinými slovy vytvořte program fungující podobně jako program níže, ale tak, aby
šel přeložit s přepínači kompilátoru

    -nostdlib -nostdinc -static -m32 -Wall -g -O2

```C
#include <stdio.h>

int main()
{
    unsigned num;
    while (scanf("%u", &num) == 1)
        printf("0x%x\n", num);
    return 0;
}
```

Do BRUTE nahrávejte soubor `hexconv.c` se svou implementací.

# Nápověda
- Pro vyvolání služeb jádra potřebujete znát *ABI* (application binary
  interface) jádra OS. To se dočtete v [man syscall][] v řádcích architektury
  i386.
- Čísla jednotlivých systémových volání najdete v souboru
  `/usr/include/i386-linux-gnu/asm/unistd_32.h`.
  {{< hint warning >}}
  Pozor! Jak ABI, tak čísla systémových volání se liší mezi 32- a 64-bitových
  jádrem. 64-bitové jádro je možné volat pomocí obou ABI, ale v této úloze
  používejte 32-bitové ABI, protože program je kompilován s přepínačem `-m32`.
  {{< /hint >}}
- Můžete vyjít z kódu níže, který již obsahuje náhradu funkce `scanf`.
- Pro tisk i načítání můžete použít pole pevné délky např. 20 (maximální výstup
  má 8 hexadecimálních znaků).

```C
#include <unistd.h> /* TODO: write your own system call wrappers */
                    /*       for read, write, exit */
#include <stdio.h>  /* TODO: sprintf -- convert number to hex string */
#include <string.h> /* TODO: strlen -- length of output for write */

int isnum(char ch)
{
    return ch >= '0' && ch <= '9';
}

int isspc(char ch)
{
    return ch == ' ' || ch == '\n';
}

static void print(unsigned num)
{
    char buf[20];
    /* TODO: Get rid of sprintf() and strlen() */
    sprintf(buf, "0x%x\n", num);
    int ret = write(STDOUT_FILENO, buf, strlen(buf));
    if (ret == -1)
        _exit(1); // TODO your new exit
}

/* TODO: main() is called by libc. Real entry point is _start(). */
int main()
{
    char buf[20];
    unsigned num = 0;
    int i;
    int num_digits = 0;
    unsigned chars_in_buffer = 0;

    for (/* no init */; /* no end condition */; i++, chars_in_buffer--) {
        if (chars_in_buffer == 0) {
            int ret = read(STDIN_FILENO, buf, sizeof(buf));
            if (ret < 0)
                return 1; // TODO replace by exit
            i = 0;
            chars_in_buffer = ret;
        }
        if (
            num_digits > 0
            && (chars_in_buffer == 0 /* EOF */ || !isnum(buf[i]))
        ) {
            print(num);
            num_digits = 0;
            num = 0;
        }
        if (
            chars_in_buffer == 0 /* EOF */
            || (!isspc(buf[i]) && !isnum(buf[i]))
        )
            return 0; // TODO: replace by exit

        if (isnum(buf[i])) {
        num = num * 10 + buf[i] - '0';
        num_digits++;
        }
    }
}
```

# Materiály
- [inline assembler][ia]

[man syscall]: http://man7.org/linux/man-pages/man2/syscall.2.html
