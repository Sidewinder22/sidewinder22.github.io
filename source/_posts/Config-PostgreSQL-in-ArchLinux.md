---
title: Konfiguracja bazy danych PostgreSQL w Arch Linux
date: 2023-09-15 21:05:32
tags:
- PostgreSQL
categories:
- Bazy danych
author: Sidewinder22
---

# Wstęp

Zacząłem robić nowy projekt w języku C++/QT, dla doskonalenia umiejętności – będzie to manager haseł.

Do mojego nowego projektu w języku C++, potrzebuję skonfigurować bazę danych. Postaram się to po krótce opisać w tym poście.

Pierwszą rzeczą, od której zacznę jest konfiguracja bazy danych PostgreSQL w systemie Arch Linux (dla innych systemów operacyjnych będzie podobnie – trzeba będzie przede wszystkim użyć managera paczek odpowiedniego dla wersji Linuksa, której używamy).

To zaczynajmy!

# Instalacja PostgreSQL

Zainstalujmy paczkę z bazą danych:

```bash
sudo pacman -S postgresql
```

Później przełączmy się na użytkownika `postgres`:

```bash
sudo -iu postgres
```

Następnie zainicjujemy klaster bazy danych:

```bash
[postgres@archlinux ~]$ initdb --locale pl_PL.UTF-8 -D /var/lib/postgres/data
```

Na koniec wylogujmy się z użytkownika postgresql, włączmy i wystartujmy serwis `postgresql`:

```bash
logout
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

# Zabezpieczenie bazy danych

Stwórzmy użytkownika naszej bazy danych. Użyjemy naszej nazwy użytkownika z systemu Linux. Dzięki temu nie będziemy musieli się później przełączać na użytkownika `postgres`, aby coś zrobić w naszej bazie danych.

```sql
[postgres@archlinux ~]$ createuser --interactive [USER]
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) y
Shall the new role be allowed to create more new roles? (y/n) n
```

Drugą opcję zaznaczyłem na tak, ponieważ, chciałbym, aby mój user mógł stworzyć nową bazę danych.

Stwórzmy teraz naszą bazę danych:

```bash
createdb [DataBaseName]
```

Przejdźmy do powłoki (ang. shell) naszej bazy danych:

```bash
psql -d [DataBaseName]
```

Zmieńmy hasło naszego użytkownika:

```sql
ALTER USER [USER_NAME] WITH PASSWORD '[NEW_PASSWORD]';
```

Teraz zmieńmy prawa dostępu do bazy danych.
W pliku `/var/lib/postgres/data/pg_hba.conf` zamieńmy to:

```bash
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
local   all             all                                     trust
```

Na to:

```bash
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
local   [DATABASE_NAME]             [USER]                       scram-sha-256
```

Następnie dodamy wymaganie hasła przy logowaniu do bazy danych.
W pliku: `/var/lib/postgres/data/postgresql.conf` odkomentujmy tą linijkę:


```bash
password_encryption = scram-sha-256     # scram-sha-256 or md5
```

Na koniec zrestartujmy serwis `postgresql`, aby zaaplikować zmiany:

```bash
sudo systemctl restart postgresql
```

I spróbujmy zalogować się teraz do naszej bazy danych (za pomocą komendy `psql -d [DataBaseName]`). Teraz powinniśmy zostać poproszeni o hasło, po podaniu którego pokaże nam się interaktywna powłoka PostgreSQL.

Oto zrzut mojej konsoli:

![zrzut_konsoli-postgresql-archlinux](/assets/images/zrzut-konsoli-postresql_archLinux.png)


# Podsumowanie

W tym poście przedstawiłem podstawową konfigurację bazy danych PostgreSQL. W następnym poście zajmiemy się dodaniem stworzeniem struktury tabel i zawartości, która ma być przechowywana w bazie danych.

Mam nadzieję, że post jest przydatny 🙂 W razie jakichkolwiek problemów, dawajcie znać w komentarzach!

Pozdrawiam,
{\\\_Sidewinder22\_/}
