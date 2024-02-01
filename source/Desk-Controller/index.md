---
title: Desk Controller
date: 2024-02-01 20:49:40
---

# Desk Controller

Projekt, którym aktualnie się zajmuję.  
Jest to monitor biurka/PC, który wyświetla dane na Raspberry Pi.  
Frontend do niego jest tworzony w qml, a backend w C++ z użyciem biblioteki Qt.

Ta aplikacja będzie się łączyć z System-Monitor-Client, która będzie działać na PC, którego obciążenie ma być śledzone. Będzie nawiązywać połączenie po socketach do Desk Controllera i wysyłać dane o aktualnym obciążeniu PC.

## Technologie:

- C++20
- CMake
- QML
- Qt6
