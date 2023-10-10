---
title: C++20 – Moduły
date: 2023-09-29 15:43:27
tags: 
- C++20
- C++
categories: 
- programowanie
- moduły
author:         Sidewinder22
description:    "Witajcie! 🙂 W końcu i na mnie przyszła pora na zapoznanie się z feature’mi ze standardu C++20."
---


Witajcie! 🙂
W końcu i na mnie przyszła pora na zapoznanie się z feature’mi ze standardu C++20.

Dzisiaj na pierwszy ogień idą moduły z C++20. Spróbujemy sobie napisać prosty kodzik, który wyświetli nam zawartość aktualnego katalogu (taki ls z shella).

# Wersja bez modułów

Na początku spójrzmy na kod napisany bez użycia modułów z C++20. Będą to proste klasy <em>Filesystem</em> i <em>Format</em>, plik <em>main.cpp</em> i <em>cmake</em>.
Klasa Filesystem zapewni nam metodę `printCurrentDir()`, w której do sformatowania wyjścia użyje metody `cleanupOutput()` z klasy Format.

## Kod

```cpp
// Filesystem.hpp

#ifndef FILESYSTEM_HPP_
#define FILESYSTEM_HPP_

class Filesystem
{
public:
    void printCurrentDir();

private:
    static constexpr auto currentPath_ = (".");
};

#endif // FILESYSTEM_HPP_
```

```cpp
// Filesystem.cpp

#include <iostream>
#include <filesystem>
#include "Format.hpp"
#include "Filesystem.hpp"

void Filesystem::printCurrentDir()
{
    std::vector<std::string> output;
    
    for (auto && entry : std::filesystem::directory_iterator{currentPath_})
    {
        output.push_back(entry.path().c_str());
    }

    Format format;
    auto results = format.cleanupOutput(output);

    for (auto && result : results)
    {
        std::cout << result << " ";
    }

    std::cout << std::endl;
}
```

```cpp
// Format.hpp

#ifndef FORMAT_HPP_
#define FORMAT_HPP_

#include <string>
#include <vector>

class Format
{
public:
    std::vector<std::string> cleanupOutput(std::vector<std::string>& output);
};

#endif // FORMAT_HPP_
```

```cpp
// Format.cpp

#include <algorithm>
#include "Format.hpp"

std::vector<std::string> Format::cleanupOutput(std::vector<std::string>& output)
{
    std::vector<std::string> result;

    std::transform(output.begin(), output.end(), output.begin(),
        [](std::string entry){ return entry.substr(2); });

    return output;
}
```



W pliku <em>main.cpp</em> stworzymy sobie obiekt klasy Tools i wywołamy na nim metodę: <em>printCurrentDir()</em>, która wyświetli nam zawartość aktualnego katalogu.

```cpp
// main.cpp

#include <iostream>
#include "Filesystem.hpp"

using namespace std;

int main()
{
    Filesystem filesystem;
    filesystem.printCurrentDir();

    return 0;
}
```

```cmake
# CmakeLists.txt

cmake_minimum_required(VERSION 3.5)

project(Side-ls LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "-Wall -Wextra")

add_library(tools SHARED
    Filesystem.cpp
    Format.cpp
)

add_executable(Side-ls main.cpp)
target_link_libraries(Side-ls PRIVATE tools)

```

# Kompilacja

Teraz skompilujmy nasz kod:

```bash
mkdir build && cd build && cmake ..
make
./Side-ls
```

A oto output z konsoli:

![cpp_without_modules](/assets/images/cpp20_without_modules.png)


# Wersja z modułami

## Kompilacja najnowszej wersji clang’a

Aby zapewnić dobre wsparcie do budowania modułów, zbudujemy sobie najnowszą wersję kompilatora clang. Poniżej przedstawiam komendy, za pomocą których możemy pobrać repozytorium z kodem kompilatora, a następnie go zbudować.

```bash
git clone https://github.com/llvm/llvm-project

cd llvm-project
mkdir build
cd build
cmake -DLLVM_ENABLE_PROJECTS=clang -DCMAKE_BUILD_TYPE=Release -G "Unix Makefiles" ../llvm
make -j$(nproc)

# Sprawdź, czy wszystko się dobrze zbudowało
make -j$(nproc) check-clang
```

Mój output z testów:

![CLang tests output](/assets/images/cmake_test.png)

## Moduły C++20

To teraz opowiedzmy sobie trochę o modułach w C++. To nowość wprowadzona w standardzie C++20.  
Wcześniej deklaracje klas, funkcji i zmiennych szły do plików nagłówkowych `*.hpp` (ang. header). Ich minusem jest to, że ich zawartość jest po prostu wklejana do jednostek kompilacji (generalnie to pliki `*.cpp`), przez co wydłuża się czas samej kompilacji.
Stosując je trzeba także zadbać o to, aby ich zawartość nie była dołączana podwójnie, przez np. inny plik nagłówkowy, który także includuje ten sam plik. Aby temu zapobiec używa się dyrektyw preprocesora `#ifndef`, `#define` i `#endif`.

```cpp
#ifndef TOOLS_HPP_
#define TOOLS_HPP_

// deklaracje klas, funkcji i zmiennych

#endif // TOOLS_HPP_
```

Moduły natomiast są takimi oddzielnymi jednostkami, które nie są tak zależne między sobą – są pojedynczymi bytami. Jeśli w danymi module nie było zmian, to nie musi on być rekompilowany. Dzięki temu możemy szybciej skompilować nasz projekt.
Ułatwiają także organizację kodu. Gdy moduł jest gotowy, programista musi go tylko zaimportować i może z niego korzystać. Zobaczymy to później na przykładzie.

W przypadku modułów wygląda to troszkę inaczej:

```cpp
module; // Deklaracja która jest niezbędna do dodania plików nagłówkowych w starym stylu

// Tu idą include'y z plikami nagłówkowymi

export module [module-name]; // Deklaracja która eksportuje nasz moduł

export class [class-name]	// Eksport naszej klasy
{
};
```

## Kod

Pora na kod. Implementację klas podzieliłem na dwa oddzielne pliki. Plik z rozszerzeniem `*.cppm` to w przypadku kompilator clang pliki interfejsu modułu.

```cpp
// Format.cppm

module;

#include <string>
#include <vector>

export module Format;

export class Format
{
public:
    std::vector<std::string> cleanupOutput(std::vector<std::string>& output);
};

```

Tu w implementacji metody cleanupOutput() jest mały bonusik z C++20. 

Ponieważ z klasy Filesystem dostaję wpisy z formacie: `./filename`, to aby je wypisać tak jak narzędzie `ls`, muszę usunąć 2 pierwsze znaki. Aby zmodyfikować każdy wpis używam metody `std::ranges::transform()` wprowadzonej w C++20, która na każdym wywołuje metodę `substr()` w celu usunięcia znaków: „./”.

```cpp
// Format-impl.cpp

module;

#include <algorithm>
#include <iostream>
#include <vector>

module Format;

std::vector<std::string> Format::cleanupOutput(std::vector<std::string>& output)
{
    std::vector<std::string> result;

    std::ranges::transform(output, std::back_inserter(result),
        [](std::string entry){ return entry.substr(2); });

    return result;
}
```

Podobnie dla klasy <em>Filesystem</em>.

```cpp
// Filesystem.cppm
export module Filesystem;

export class Filesystem
{
public:
    void printCurrentDir();

private:
    static constexpr auto currentPath_ = ".";
};
```

```cpp
// Filesystem-impl.cpp

module;

#include <iostream>
#include <filesystem>
#include <string>
#include <vector>

module Filesystem;
import Format;

void Filesystem::printCurrentDir()
{
    std::vector<std::string> output;
    
    for (auto && entry : std::filesystem::directory_iterator{currentPath_})
    {
        output.push_back(entry.path().c_str());
    }

    Format format;
    auto results = format.cleanupOutput(output);

    for (auto && result : results)
    {
        std::cout << result << " ";
    }

    std::cout << std::endl;
}
```

```cpp
// main.cpp
#include <iostream>

import Filesystem;

using namespace std;

int main()
{
    Filesystem filesystem;
    filesystem.printCurrentDir();

    return 0;
}
```

Ostatni element – plik <em>makefile</em>. W przykładzie bez modułów użyłem cmake.
Ale tutaj dla łatwiejszej kompilacji klas, które są rozdzielone na pliki `*.cppm` i `cpp`, używam zwykłego pliku <em>Makefile</em>, w którym są ręcznie napisane komendy do kompilacji poszczególnych plików.

Wyjaśnijmy sobie dla przykładu kompilację klasy Filesystem.  
Najpierw mamy kompilację interfejsu modułu do formatu pośredniego z roszerzeniem `*.pcm`:

    Filesystem.pcm: Filesystem.cppm
	    $(CC) $(CFLAGS) Filesystem.cppm --precompile  -o Filesystem.pcm

Następnie z pliku pośredniego tworzymy plik obiektowy:

    Filesystem.o: Filesystem.pcm
	    $(CC) $(CFLAGS) Filesystem.pcm -c -o Filesystem.o

I na końcu kompiluję plik z implementacją <em>Filesystem-impl.cpp</em> do pliku obiektowego <em>Filesystem-impl.o</em>. Ponieważ w tym pliku z implementację importujemy moduł <em>Filesystem</em>, to musimy podać plik pośredni, który został wygenerowany z naszej deklaracji modułu w formacie: `-fmodule-file=<module-name>=<path/to/*.pcm>`.
W metodzie <em>printCurrentDir()</em> korzystamy też z modułu <em>Format</em>, dlatego musimy także dodać ścieżkę do skompilowanych modułów za pomocą opcji: `-fprebuilt-module-path=<path>`.

    Filesystem-impl.o: Filesystem-impl.cpp Filesystem.pcm
	    $(CC) $(CFLAGS) Filesystem-impl.cpp -fmodule-file=Filesystem=Filesystem.pcm -fprebuilt-module-path=. -c -o Filesystem-impl.o    

Spójrzmy teraz na całą zawartość pliku <em>Makefile</em>. Na początku zdefiniowałem stałą <em>clang</em>, w której podaję ścieżkę do narzędzia <em>clang++</em>, które wcześniej skompilowaliśmy.

```make
clang="[PATH_TO_COMPILED_LLVM]/llvm-project/build/bin/clang++"

# Variables
CC = $(clang)
CFLAGS = -Wall -Wextra -std=c++20
TARGET = Side-ls

all: $(TARGET)

$(TARGET): Format.o Format-impl.o Filesystem.o Filesystem-impl.o main.o
	$(CC) main.o Format.o Format-impl.o Filesystem.o Filesystem-impl.o -o $(TARGET) 

# Main
main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp -fprebuilt-module-path=. -c -o main.o

# Format
Format-impl.o: Format-impl.cpp Format.pcm
	$(CC) $(CFLAGS) Format-impl.cpp -fmodule-file=Format=Format.pcm -c -o Format-impl.o

Format.o: Format.pcm
	$(CC) $(CFLAGS) Format.pcm -c -o Format.o

Format.pcm: Format.cppm
	$(CC) $(CFLAGS)	Format.cppm --precompile -o Format.pcm

# Filesystem
Filesystem-impl.o: Filesystem-impl.cpp Filesystem.pcm
	$(CC) $(CFLAGS) Filesystem-impl.cpp -fmodule-file=Filesystem=Filesystem.pcm -fprebuilt-module-path=. -c -o Filesystem-impl.o

Filesystem.o: Filesystem.pcm
	$(CC) $(CFLAGS) Filesystem.pcm -c -o Filesystem.o

Filesystem.pcm: Filesystem.cppm
	$(CC) $(CFLAGS) Filesystem.cppm --precompile  -o Filesystem.pcm


# Clean
clean:
	rm *.pcm *.o Side-ls
```

## Kompilacja

    make
    ./Side-ls

A oto output z konsoli:

![Cpp20 Modules Output](/assets/images/cpp20_modules_output.png)

# Podsumowanie

Jak widać na powyższym przykładzie, używanie modułów z C++ nie jest takie straszne 🙂 Najbardziej problematyczne może być pisanie plików makefile. Trzeba tylko zrozumieć, jakie zależności musimy podać w którym miejscu.

Było mi bardzo przyjemnie w końcu zapoznać się z pierwszą rzeczą ze standardu C++20. To pierwszy wpis z tej serii – mam nadzieję, że niedługo powstanie kolejny.

Trzymajcie się!

Link do projektu: https://github.com/Sidewinder22/Side-ls

# Bibliografia

- https://clang.llvm.org/docs/StandardCPlusPlusModules.html
- https://github.com/llvm/llvm-project
- https://clang.llvm.org/get_started.html
- https://en.cppreference.com/w/cpp/algorithm/ranges/transform

Pozdrawiam, 
{\\\_Sidewinder22\_/}
