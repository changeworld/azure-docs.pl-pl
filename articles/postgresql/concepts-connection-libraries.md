---
title: Biblioteki połączeń — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano kilka bibliotek i sterowników, których można używać podczas kodowania aplikacji do łączenia się i wykonywania zapytań o usługę Azure Database dla postgreSql — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768898"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Biblioteki połączeń dla usługi Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule wymieniono biblioteki i sterowniki, których deweloperzy mogą używać do tworzenia aplikacji do łączenia się z usługą Azure Database i wykonywania zapytań o usługi PostgreSQL.

## <a name="client-interfaces"></a>Interfejsy klienta
Większość bibliotek klienckich używanych do łączenia się z serwerem PostgreSQL to projekty zewnętrzne i są dystrybuowane niezależnie. Wymienione biblioteki są obsługiwane na platformach Windows, Linux i Mac do łączenia się z usługą Azure Database for PostgreSQL. Kilka przykładów szybkiego startu są wymienione w następnych kroków sekcji.

| **Język** | **Interfejs klienta** | **Dodatkowe informacje** | **Pobierz** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Zgodność z interfejsem API 2.0 bazy danych | [Pobierz](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Rozszerzenie bazy danych | [Zainstaluj](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pakiet pg npm](https://www.npmjs.com/package/pg) | Klient nieblokujące czystego języka JavaScript | [Zainstaluj](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Typ 4 Sterownik JDBC | [Pobierz](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Perełka pg](https://deveiate.org/code/pg/) | Interfejs Ruby | [Pobierz](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Przejdź | [Pakiet pq](https://godoc.org/github.com/lib/pq) | Sterownik Pure Go postgres | [Zainstaluj](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Liczba zsyp](https://www.npgsql.org/) | Dostawca danych ADO.NET | [Pobierz](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Sterownik ODBC | [Pobierz](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Podstawowy interfejs języka C | Dołączono |
| C++ | [libpqxx ( libpqxx )](http://pqxx.org/) | Nowy interfejs C++ w nowym stylu | [Pobierz](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Następne kroki
Przeczytaj te przewodniki Szybki start, jak połączyć się z usługą Azure Database dla postgreSQL i wysyłać zapytania:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
