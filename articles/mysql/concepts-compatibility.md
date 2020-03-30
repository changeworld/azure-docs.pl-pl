---
title: Zgodność ze sterownikami i narzędziami — usługa Azure Database for MySQL
description: W tym artykule opisano sterowniki MySQL i narzędzia do zarządzania, które są zgodne z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537214"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Sterowniki MySQL i narzędzia do zarządzania zgodne z usługą Azure Database for MySQL
W tym artykule opisano sterowniki i narzędzia do zarządzania, które są zgodne z usługą Azure Database for MySQL.

## <a name="mysql-drivers"></a>Sterowniki MySQL
Usługa Azure Database for MySQL korzysta z najpopularniejszej na świecie wersji bazy danych MySQL. W związku z tym jest kompatybilny z szeroką gamą języków programowania i sterowników. Celem jest wspieranie trzech najnowszych wersji sterowników MySQL, a wysiłki z autorami ze społeczności open source, aby stale ulepszać funkcjonalność i użyteczność sterowników MySQL, są kontynuowane. Lista sterowników, które zostały przetestowane i uznane za zgodne z usługą Azure Database dla MySQL 5.6 i 5.7 znajduje się w poniższej tabeli:

| **Język programowania** | **Sterownik** | **Linki** | **Kompatybilne wersje** | **Niezgodne wersje** | **Uwagi** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | W przypadku połączenia PHP 7.0 z SSL MySQLi dodaj MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT w ciągu połączenia. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Zestaw ChNP: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opcja false.|
| .NET | Łącznik Async MySQL dla platformy .NET | https://github.com/mysql-net/MySqlConnector <br> [Pakiet instalacyjny firmy Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 i po | 0.26.5 i przed | |
| .NET | Złącze MySQL/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Błąd kodowania może spowodować niepowodzenie połączeń w niektórych systemach Windows innych niż UTF8. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Pakiet instalacyjny od NPM:<br> Uruchom `npm install mysql` z NPM | 2.15 | 2.14.1 i przed | |
| Node.js | węzeł-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Przejdź | Przejdź sterownik MySQL | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 i przed | Użyj `allowNativePasswords=true` w ciągu połączenia dla wersji 1.3. Wersja 1.4 zawiera `allowNativePasswords=true` poprawkę i nie jest już wymagana. |
| Python | Łącznik MySQL/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, użyj 8.0.16+ z MySQL 8.0  | 1.2.2 i przed | |
| Python | PyMySQL ( PyMySQL ) | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3+ | 0,9,0 - 0,9,2 (regresja w web2py) | |
| Java | Złącze MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 i przed | | 
| Java | Złącze MySQL/J | https://github.com/mysql/mysql-connector-j | 5.1.21+, użyj 8.0.17+ z MySQL 8.0 | 5.1.20 i poniżej | |
| C | Złącze MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C | Złącze MySQL/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | Złącze MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 i poniżej | | 
| C++ | MySQL++| https://tangentsoft.net/mysql++ | 3.2.3+ | | |
| Ruby | mysql2 ( mysql2 ) | https://github.com/brianmario/mysql2 | 0.4.10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16+ | | |
| Swift | mysql-szybki | https://github.com/novi/mysql-swift | 0.7.2+ | | |
| Swift | para/mysql | https://github.com/vapor/mysql-kit | 2.0.1+ | | |

## <a name="management-tools"></a>Narzędzia do zarządzania
Zaletą zgodności rozciąga się również narzędzia do zarządzania bazami danych. Istniejące narzędzia powinny nadal współpracować z usługą Azure Database for MySQL, o ile manipulacja bazą danych działa w granicach uprawnień użytkownika. Trzy typowe narzędzia do zarządzania bazami danych, które zostały przetestowane i uznane za zgodne z usługą Azure Database dla mysql 5.6 i 5.7 są wymienione w poniższej tabeli:

|                                     | **MySQL Workbench 6.x i do góry** | **Okręg wyborczy Navicat 12** | **PHPMyAdmin 4.x i do góry** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Tworzenie, aktualizowanie, odczytywanie, pisanie, usuwanie | X | X | X |
| Połączenie SSL | X | X | X |
| Automatyczne uzupełnianie kwerendy SQL | X | X |  |
| Importowanie i eksportowanie danych | X | X | X | 
| Eksportowanie do wielu formatów | X | X | X |
| Wykonywanie kopii zapasowych i przywracanie |  | X |  |
| Wyświetl parametry serwera | X | X | X |
| Wyświetlanie połączeń klientów | X | X | X |

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)