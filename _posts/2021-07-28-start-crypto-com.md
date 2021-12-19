---
date:           2021-07-28 00:08:00 +0000
tags:           development projects SCc
author:         Sidewinder22
title:          "#2 Rozpoczęcie nowego projektu"
description:    "Inaguracja nowego projektu programistycznego - szyfrowanego komunikatora"
---

## Wprowadzenie

Dzisiaj krótki wpis o nowym projekcie.

Chciałbym zdobyć praktyczne doświadczenie w tworzeniu systemów opartych o kryptografię.
Stąd (niezbyt odkrywczy) pomysł na projekt - szyfrowany komunikator.
Dzięki niemu uda się zapoznać z pojęciami szyfrowania symetrycznego i asymetrycznego.

## Status

Aktualnie mam tylko prosty projekt interfejsu użytkownika.
Mam nadzieję, że niedługo będę mógł podzielić się z Wami czymś więcej.

Chciałbym także wykorzystać nauczyć się kolejnej rzeczy (oprócz szyfrowania) - komunikacji sieciowej za pomocą `boost::asio`.
Dzięki temu uniknę używania czystych socketów, a aplikaja będzie bardziej przenośna. Zwłaszcza pomiędzy systemami Linux i Windows - ponieważ aktualnie tworzę ją na tym pierwszym.

## Technologie

* `C++` - docelowo nawet w wersji 20
* `QT` - interfejs użytkownika
* `boost::asio` - komunikacja sieciowa
* `OpenSSL` - negocjacja kluczy asymetrycznych i warstwa szyfrowania komunikacji algorytmem symetrycznym

Pozdrawiam,  
Sidewinder22
