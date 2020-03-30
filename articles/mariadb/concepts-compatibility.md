---
title: Zgodność sterowników i narzędzi — usługa Azure Database for MariaDB
description: W tym artykule opisano sterowniki MariaDB i narzędzia do zarządzania, które są zgodne z usługą Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532352"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Sterowniki MariaDB i narzędzia do zarządzania zgodne z usługą Azure Database for MariaDB

W tym artykule opisano sterowniki i narzędzia do zarządzania, które są zgodne z usługą Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>Sterowniki MariaDB

Usługa Azure Database for MariaDB korzysta ze wspólnotowej wersji serwera MariaDB. W związku z tym jest kompatybilny z szeroką gamą języków programowania i sterowników. API i protokół MariaDB są kompatybilne z tymi używanymi przez MySQL. Oznacza to, że łączniki, które współpracują z MySQL powinny również współpracować z MariaDB.

Celem jest wspieranie trzech najnowszych wersji sterowników MariaDB, a wysiłki z autorami ze społeczności open source, aby stale ulepszać funkcjonalność i użyteczność sterowników MariaDB, są kontynuowane. Lista sterowników, które zostały przetestowane i uznane za zgodne z usługą Azure Database dla MariaDB 10.2 znajduje się w poniższej tabeli:

**Sterownik** | **Linki** | **Kompatybilne wersje** | **Niezgodne wersje** | **Uwagi**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | W przypadku połączenia PHP 7.0 z SSL MySQLi dodaj MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT w ciągu połączenia. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Zestaw ChNP: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opcja false.
.NET | [MySqlConnector na GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pakiet instalacyjny firmy Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 i po | 0.26.5 i przed |
Złącze MySQL/NET | [Złącze MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Błąd kodowania może spowodować niepowodzenie połączeń w niektórych systemach Windows innych niż UTF8.
Node.js |  [MySQLjs na GitHub](https://github.com/mysqljs/mysql/) <br> Pakiet instalacyjny od NPM:<br> Uruchom `npm install mysql` z NPM | 2.15 | 2.14.1 i przed
JĘZYK GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 i przed | Użyj `allowNativePasswords=true` w ciągu połączenia dla wersji 1.3. Wersja 1.4 zawiera `allowNativePasswords=true` poprawkę i nie jest już wymagana.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 i przed |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 i przed |

## <a name="management-tools"></a>Narzędzia do zarządzania

Zaletą zgodności rozciąga się również narzędzia do zarządzania bazami danych. Istniejące narzędzia powinny nadal współpracować z usługą Azure Database dla MariaDB, tak długo, jak manipulacja bazą danych działa w granicach uprawnień użytkownika. Trzy typowe narzędzia do zarządzania bazami danych, które zostały przetestowane i uznane za zgodne z usługą Azure Database for MariaDB 10.2 są wymienione w poniższej tabeli:

| | **MySQL Workbench 6.x i do góry** | **Okręg wyborczy Navicat 12** | **PHPMyAdmin 4.x i do góry**
---|---|---|---
Tworzenie, aktualizowanie, odczytywanie, pisanie, usuwanie | X | X | X
Połączenie SSL | X | X | X
Automatyczne uzupełnianie kwerendy SQL | X | X |
Importowanie i eksportowanie danych | X | X | X
Eksportowanie do wielu formatów | X | X | X
Wykonywanie kopii zapasowych i przywracanie |  | X |
Wyświetl parametry serwera | X | X | X
Wyświetlanie połączeń klientów | X | X | X

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)