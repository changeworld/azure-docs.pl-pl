---
title: Biblioteki połączeń dla bazy danych Azure dla PostgreSQL
description: W tym artykule opisano kilka bibliotek i sterowniki, deweloperzy mogą używać podczas kodowania aplikacji do nawiązywania połączeń i PostgreSQL kwerendy bazy danych Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7f0dd217a124f530ba009c9a5588691f604827e5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687010"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Biblioteki połączeń dla bazy danych Azure dla PostgreSQL
W tym artykule wymieniono bibliotek i sterowniki, które deweloperzy mogą używać do tworzenia aplikacji do nawiązania połączenia i wyszukiwać w bazie danych Azure PostgreSQL.

## <a name="client-interfaces"></a>Interfejsy klienta
Większość bibliotek klienckich język używany do łączenia z serwerem PostgreSQL projektów zewnętrznych i są dystrybuowane niezależnie. Biblioteki wymienione są obsługiwane na platformach Windows, Linux i komputerów Mac, do łączenia z bazą danych Azure dla PostgreSQL. Kilka przykładów szybkiego startu są wymienione w sekcji Następne kroki.

| **Język** | **Interfejs klienta** | **Dodatkowe informacje** | **Pobieranie** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Interfejs API DB 2.0 zgodne | [Pobieranie](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Rozszerzenia bazy danych | [Instalowanie](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pakiet npm PG](https://www.npmjs.com/package/pg) | Czysty JavaScript bez blokowania klienta | [Instalowanie](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Sterownik JDBC typu 4 | [Pobieranie](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Interfejs Ruby | [Pobieranie](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Przejdź | [Pakiet pq](https://godoc.org/github.com/lib/pq) | Czysty Przejdź postgres sterownika | [Instalowanie](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Dostawca danych ADO.NET | [Pobieranie](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Sterownik ODBC | [Pobieranie](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Podstawowy interfejs języka C | Dołączono |
| C++ | [libpqxx](http://pqxx.org/) | Nowy styl interfejsu C++ | [Pobieranie](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj te przewodniki Szybki Start dotyczące nawiązania połączenia i kwerendy bazy danych platformy Azure przy użyciu języka wybór PostgreSQL:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
