---
title: Linux Process And Load Info
tags:
- Linux
categories:
- systemy operacyjne
author: Sidewinder22
---

Witajcie! :)

Do mojego projektu monitora systemu Linux potrzebuję odczytać dane o procesach i o obciążeniu. W tym poście pokażę, jak to zrobić.

W linuksie dane o procesach i obciążeniu znajdują się w katalogu `/proc`.  
Łatwo możemy się z tym zapoznać odwiedzając ten katalog lub otwierając jego dokumentację (`man 5 proc`).

# Dane procesów

Dane o konkretnych procesach możemy odczytać z pliku `/proc/[pid]/status`, gdzie `pid` to numer id procesu.
Przykładowa zawartość tego pliku:

## Średnie obciążenie

Odczyt pliku `/proc/loadavg`.

## Średnie obciążenie cpu

Odczyt pliku i parsowanie zawartości `/proc/stat`.





# Bibliografia
- `man 5 proc`
- [linux man page about proc](https://man7.org/linux/man-pages/man5/proc.5.html)
