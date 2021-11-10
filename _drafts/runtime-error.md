---
layout:     post
date:       2021-11-10 10:00:00 +0100
tags:       problemy_programisty development C++ 
author:     Sidewinder22
title:      "#4 Runtime error"
short_desc: "Runtime error w przypadku użycia std::dynamic_pointer_cast"
---

## Plan

* RUntime error
* ICommunicator interface
* std::dynamic_pointer_cast
* ptr do serwera nie działął, bo klasa TcpServer'a nie dziedziczy po interfejsie ICommunicator
