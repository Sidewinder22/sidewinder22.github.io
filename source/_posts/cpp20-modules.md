---
title: "#5 C++20 – Moduły"
date: 2023-09-29 15:43:27
tags: c++20 c++
author:         Sidewinder22
description:    "Witajcie! 🙂 W końcu i na mnie przyszła pora na zapoznanie się z feature’mi ze standardu C++20."
---


Witajcie! 🙂
W końcu i na mnie przyszła pora na zapoznanie się z feature’mi ze standardu C++20.

Dzisiaj na pierwszy ogień idą moduły z C++20. Spróbujemy sobie napisać prosty kodzik, który wyświetli nam zawartość aktualnego katalogu (taki ls z shella).

# Wersja bez modułów

Na początku spójrzmy na kod napisany bez użycia modułów z C++20. Będą to proste klasy Filesystem i Format, plik main.cpp i cmake.
Klasa Filesystem zapewni nam metodę printCurrentDir(), w której do sformatowania wyjścia użyje metody cleanupOutput() z klasy Format.

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
