---
title: QT linker problem
date: 2023-09-05 20:56:03
tags: C++
categories:
- programowanie
- problemy programisty
author: Sidewinder22
---

# Opis

Podczas tworzenia kodu do jednego z moich projektów, natrafiłem na problem związany z linkowaniem. Chwilę mi zajęło znalezienie rozwiązania w internecie, więc pomyślałem, że przedstawię je pokrótce w tym poście.

Projekt jest wykonany w C++ przy użyciu biblioteki QT.
Poniżej są logi przedstawiające błąd linkowania:

```bash
g++ -Wl,-O1 -o Side-Crypto-Communicator Application.o main.o HLine.o InputPanel.o Menu.o OutputPanel.o Panel.o StatusBar.o ToolBar.o UIManager.o UserAction.o Window.o Logger.o NetworkManager.o TcpClient.o TcpServer.o moc_InputPanel.o moc_Menu.o moc_OutputPanel.o moc_Panel.o moc_StatusBar.o moc_ToolBar.o moc_UIManager.o moc_UserAction.o moc_Window.o moc_NetworkManager.o   /usr/lib/libQt5Widgets.so /usr/lib/libQt5Gui.so /usr/lib/libQt5Core.so -lGL -lpthread   
/usr/bin/ld: Application.o: in function `Application::Application()':
Application.cpp:(.text+0x4e): undefined reference to `vtable for Application'
/usr/bin/ld: main.o: in function `Application::~Application()':
main.cpp:(.text._ZN11ApplicationD2Ev[_ZN11ApplicationD5Ev]+0x3): undefined reference to `vtable for Application'
/usr/bin/ld: main.o: in function `Application::~Application()':
main.cpp:(.text._ZN11ApplicationD0Ev[_ZN11ApplicationD5Ev]+0x3): undefined reference to `vtable for Application'
collect2: error: ld returned 1 exit status
make: *** [Makefile:451: Side-Crypto-Communicator] Błąd 1
```

## Rozwiązanie

Problem z linkowaniem pojawił się po dodaniu makra `Q_OBJECT` do pliku nagłówkowego `Application.hpp`.
Była to jedyna wprowadzona zmiana. Nie wykorzystałem jeszcze niczego z biblioteki QT.

Okazało się, że nie zaktualizowałem sekcji `HEADERS` w pliku projektowym QT `build.pro`.

Wystarczyło tylko dodać folder src, w którym właśnie znajduje się plik nagłówkowy `Application.hpp`, do tej sekcji:
```bash
HEADERS         +=  ../src/*.hpp
```

I wtedy wszystko się pięknie zlinkowało.

## Podsumowanie

To taki krótki wpis z seri <em>problemy programisty</em>.  
Mam nadzieję, że komuś się przyda. Możliwe, że nawet mi! ;)

Jeśli macie jakieś pytania, to piszcie śmiało!

Pozdrawiam,  
{\\\_Sidewinder22\_/}
