---
layout:     post
date:       2021-12-04 10:00:00 +0100
tags:       problemy_programisty development C++ 
author:     Sidewinder22
title:      "#4 Cpp runtime error"
description: "Runtime error w spowodowany użyciem std::dynamic_pointer_cast"
---

## Wprowadzenie

Ostatnio trafiłem na ciekawy problem.
To dobry materiał na krótki wpis.
Podczas debugowania odkryłem `runtime error` w moim kodzie.

### Wspólny interfejs

Gdy
 



### Plan

* RUntime error
* ICommunicator interface
* std::dynamic_pointer_cast
* ptr do serwera nie działął, bo klasa TcpServer'a nie dziedziczy po interfejsie ICommunicator
