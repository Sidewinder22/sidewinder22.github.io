---
date:           2021-05-26 15:00:00 +0000
tags:           projects SFe
categories:     development
author:         Sidewinder22
title:          "#1 Projekt prostego edytora tekstowego SideFileEditor"
header:
  teaser: /assets/images/sfe_screenshot.png
---

Opis mojego projektu prostego edytora tekstowego napisanego w C++ i QT.

## Motywacja

Żeby umieć programować - trzeba programować.
To bardzo proste stwierdzenie jest prawdziwe.
Z programowaniem jest tak samo jak z grą na instrumencie - po prostu trening czyni mistrza.

Dlatego aby doskonalić moje umiejętności programowania, zrobiłem projekt prostego edytora tekstowego SideFileEditor (SFe).
Moim założeniem było rozwijanie umiejętności programowania w C++ i nauka tworznie aplikacj z UI.

## Opis

### Cele projektu

* nauka projektowania oprogramowania w języku C++,
* nauka nowych standardów C++ (11/14/17) w praktyce,
* zdobycie wiedzy o libliotece QT5.

### Features

* otwieranie / wyświetlanie zawartości / zamykwanie plików,
* tworzenie i zapisywanie nowych plików,
* zapisywanie zmian w istniejących plikach,
* dock wyświetlający otwarte bufory i pliki.

## Design

Do stworzenie aplikacji został wykorzystany wzorzec MVC (Model-View-controller).

Model jest wyrażony jako bufory.
W tym edytorze wszystko jest buforem. Kojarzycie podejście znane z systemów uniksowych?
Tam (prawie) wszystko jest plikiem.

View to po prostu kilka klas reprezentujących interfejs użytkownika.

Controller łączy za pomocą slotów i sygnałów (z biblioteki QT) partie View i Model.

[Link do projektu](https://github.com/Sidewinder22/SideFileEditor)

![sfe_screenshot](/assets/images/sfe_screenshot.png){: width="700" }

Pozdrawiam,  
Sidewinder22
