---
title: Biblioteki połączeń dla usługi Azure Database for PostgreSQL
description: W tym artykule opisano kilka bibliotek i sterowniki, które deweloperzy mogą używać podczas kodowania aplikacji do nawiązywania połączeń i zapytań usługi Azure Database for PostgreSQL.
services: postgresql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
origin.date: 02/28/2018
ms.date: 12/03/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559776"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Biblioteki połączeń dla usługi Azure Database for PostgreSQL
Ten artykuł zawiera listę bibliotek i sterowniki, które deweloperzy mogą używać do tworzenia aplikacji, aby nawiązać połączenie i odpytywanie bazy danych Azure database for PostgreSQL.

## <a name="client-interfaces"></a>Interfejsów klientów
Większość bibliotek klienta język używany do łączenia z serwerem PostgreSQL są projektów zewnętrznych i są dystrybuowane niezależnie. Biblioteki, na liście są obsługiwane na platformach Windows, Linux i Mac, do łączenia z bazą danych Azure database for PostgreSQL. Kilka przykładów szybkiego startu są wymienione w sekcji Następne kroki.

| **Język** | **Interfejs klienta** | **Dodatkowe informacje** | **Pobieranie** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Interfejs API DB zgodne w wersji 2.0 | [Pobieranie](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Rozszerzenia bazy danych | [Instalowanie](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pakiet npm PG](https://www.npmjs.com/package/pg) | Czystego kodu JavaScript niepowodującym blokowania klientem | [Instalowanie](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Sterownik JDBC typu 4 | [Pobierz](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Interfejs języka Ruby | [Pobieranie](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Przejdź | [Pakiet pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres driver | [Instalowanie](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Dostawcy danych ADO.NET | [Pobieranie](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Sterownik ODBC | [Pobieranie](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Podstawowy interfejs języka C | Dołączono |
| C++ | [libpqxx](http://pqxx.org/) | Nowy styl C++ interfejsu | [Pobieranie](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj te przewodniki Szybki Start dotyczące nawiązać połączenie i zapytanie usługi Azure Database for PostgreSQL za pomocą wybranym języku:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [przejdź](./connect-go.md)
