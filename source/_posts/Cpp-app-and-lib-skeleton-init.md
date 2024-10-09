---
title: Struktura projektu w C++ (cmake/*.so/gtest)
tags:
  - C++
  - CMake
  - bibliotek współdzielona
  - Google Test
categories:
  - projekty
author: Sidewinder22
date: 2024-02-11 18:55:45
---


# Wstęp

Witajcie! :)  
Ostatnio potrzebowałem zainicjalizować projekt aplikacji w C++ z wykorzystaniem cmake'a, w którym będziemy mieli bibliotekę współdzieloną i testy jednostkowe z wykorzystaniem frameworku [Google Test](https://github.com/google/googletest).

Udało mi się to zrobić, więc chciałem się tym z Wami podzielić, a także przy okazji zanotować to sobie na przyszłość ;)  

# Struktura

Tak się przedstawia struktura plików i katalogów (zrzut ekranu z narzędzia *tree*):

![cpp-init-tree](/assets/images/cpp-init-tree.png)

## Główny katalog

Jak widać na screenshot'cie, w folderze projektu znajduje się jeden plik: `CMakeLists.txt` oraz trzy katalogi: `app`, `lib` i `test`.  
Przyjrzyjmy się zatem ich zawartości.

```cmake
# CMakeLists.txt

cmake_minimum_required(VERSION 3.22)                                        #1

project(system-monitor-client VERSION 0.0.1 LANGUAGES CXX)                  #2

set(CMAKE_CXX_STANDARD 20)                                                  #3
set(CMAKE_CXX_STANDARD_REQUIRED ON)                                         #3
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pedantic -fPIC")     #4
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)                       #5

add_subdirectory(app)                                                       #6
add_subdirectory(lib)
add_subdirectory(test)

#7
include(FetchContent)
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/03597a01ee50ed33e9dfd640b249b4be3799d395.zip
)

# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

FetchContent_MakeAvailable(googletest)
```

Wyjaśnijmy sobie zawartość tego pliku - jest go główny plik cmake naszego projektu:
* `#1` - ustawienie minimalnej wymaganej wersji projektu,
* `#2` - ustawienie nazwy projektu, 
* `#3` - dwie flagi ustawiające standard `C++20`,
* `#4` - kilka flag kompilatora z dodatkowymi sprawdzeniami i ostrzeżeniami,
* `#5` - ustawienie katalogu, w którym znajdzie się nasz zbudowana binarka,
* `#6` - dodanie podkatalogów,
* `#7` - instalacja Google Test'a (skopiowana z tej [instrukcji](https://google.github.io/googletest/quickstart-cmake.html#set-up-a-project)).

## App - aplikacja

Tutaj mamy dwa pliki - `CMakeLists.txt` i `main.cpp`.

```cmake
# app/CMakeLists.txt

set(APP ${PROJECT_NAME})        #1
set(LIB ${PROJECT_NAME}-lib)    #1

add_executable(${APP}           #2
    main.cpp
)

target_link_libraries(${APP}    #3
    PRIVATE
    ${LIB}
)
```
Opis:
* `#1` - ustawienie zmiennych `APP` i `LIB`, aby łatwiej się do nich potem odnosić,
* `#2` - instrukcja tworząca plik wykonywalny (w tym przypadku z pliku `main.cpp`),
* `#3` - instrukcja linkująca bibliotekę `LIB` do naszej aplikacji `APP`.

```c++
// app/main.cpp

#include <iostream>
#include "Application.hpp"

int main()
{
    std::cout << "### System Monitor Client ###" << std::endl;

    Application app;
    app.start();

    return 0;
}
```

To plik zawierający funkcję main naszej aplikacji. Zbyt wiele tu się nie dzieje - tworzymy tylko obiekt `Application` i wywołujemy na nim metodę start.

## Lib - biblioteka współdzielona

Główny cmake file:

```cmake
# lib/CMakeLists.txt

add_subdirectory(src)
```

Tu nie ma nic ciekawego, dodajemy tylko podkatalog.

Poniżej mamy dwa katalogi `include` i `src`. W pierwszym z nich będą znajdowały się pliki nagłówkowe, a w drugim pliki źródłowe.  
Ułatwi nam to dodanie plików nagłówkowych do naszej biblioteki.

W poniższych katalogach mamy tylko po jednym pliku nagłówkowym i źródłowym, ale w rzeczywistym projekcie, będziemy ich mieli duużo więcej :)

### include

Plik nagłówkowy:

```cpp
// lib/include/Application.hpp

#ifndef APPLICATION_HPP_
#define APPLICATION_HPP_

class Application
{
public:
    bool start();
};

#endif // APPLICATION_HPP_
```

Deklaracja klasy z jedną metodą: `bool start()`.

### src

Kod źródłowy naszej biblioteki:

```cpp
// lib/src/Application.cpp

#include <iostream>
#include "Application.hpp"

bool Application::start()
{
    std::cout << "App started" << std::endl;
    return true;
}
```

Definicja naszej metody `bool start()`.

```cmake
# lib/src/CMakeLists.txt

set(LIB ${PROJECT_NAME}-lib)

add_library(${LIB} SHARED                       #1
    Application.cpp
)

target_include_directories(${LIB}               #2
    # For the public header files               #3
    PUBLIC
    ${${PROJECT_NAME}_SOURCE_DIR}/lib/include

    # For only private header files             #4
    PRIVATE
    ${${PROJECT_NAME}_SOURCE_DIR}/lib/src
)
```

I kolejny plik cmake, w którym dzieje się magia z utworzeniem naszej biblioteki współdzielonej:
* `#1` - utworzenie biblioteki współdzielonej (`SHARED` -> współdzielona, `STATIC` -> statyczna),
* `#2` - dodanie katalogu z nagłówkami do naszej biblioteki,
* `#3` - `include` dodajemy jako `PUBLIC`, aby użytkownicy naszej biblioteki mogli z nich korzystać,
* `#4` - `src` dodajemy jako `PRIVATE`, ponieważ w tym katalogu mogą się znajdować pliki nagłówkowe, których nie chcemy udostępniać na zewnątrz poza naszą bibliotekę.

## test - testy jednostowe

I na sam koniec został nam katalog z testami jednostkowymi.

```cpp
// test/ApplicationTest.cpp

#include <gtest/gtest.h>
#include "Application.hpp"

TEST(ApplicationTest, Test)
{
    Application app;
    EXPECT_TRUE(app.start());
}
```

To jest prosty test, który sprawdza, czy powiodło się uruchomienie naszej aplikacji. 

```cmake
# test/CMakeLists.txt

set(LIB ${PROJECT_NAME}-lib)
set(TEST ${PROJECT_NAME}-tests)

enable_testing()                    #1

add_executable(${TEST}              #2
    ApplicationTest.cpp
)

target_link_libraries(${TEST}       #3
    ${LIB}
    GTest::gtest_main
)

#4
include(GoogleTest)
gtest_discover_tests(${TEST})
```

To nasz plik cmake', w którym dodajemy testy jednostkowe:
* `#1` - włączenie testów dla bieżącego katalogu,
* `#2` - dodanie pliku wykonywalnego naszego testu,
* `#3` - podlinkowanie naszej biblioteki i Google Test,
* `#4` - umożliwienie Google Test odkrycie testów zawartych w pliku wykonywalnym ([dokumentacje Google Test](https://google.github.io/googletest/quickstart-cmake.html#create-and-run-a-binary)).

# Podsumowanie

W tym poście przedstawiłem jak skonfigurować prosty projekt w C++, z biblioteką współdzieloną i testami jednostkowymi przy użyciu cmake'a.  
W wyjaśnieniach skupiłem się właśnie na komendach cmake, ponieważ to one umożliwiają nam skonfigurowanie projektu.

I co myślicie o tym wpisie? :)  
Dajcie znać w komentarzach.

Pozdrawiam,
Sidewinder22

## Linki
* [Dokumentacja cmake](https://cmake.org/cmake/help/v3.22/manual/cmake-commands.7.html)
* [Dokumencja gtest](https://google.github.io/googletest/quickstart-cmake.html)

