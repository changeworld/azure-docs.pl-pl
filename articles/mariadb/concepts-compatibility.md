---
title: Azure Database dla sterowników MariaDB oraz zgodność z narzędziami narzędzia do zarządzania
description: W tym artykule opisano MariaDB sterowników i narzędzi do zarządzania, które są zgodne z usługą Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386821"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB sterowników i narzędzi do zarządzania zgodne z usługą Azure Database dla serwera MariaDB

W tym artykule opisano, sterowników i narzędzi do zarządzania, które są zgodne z usługą Azure Database dla serwera MariaDB.

## <a name="mariadb-drivers"></a>Sterowniki MariaDB

Azure Database dla serwera MariaDB korzysta z wersji community serwera MariaDB. Dlatego jest zgodny z szerokiej gamy języków i sterowniki programowania. Firmy MariaDB interfejsu API i protokół są zgodne z tych używanych przez MySQL. Oznacza to, że łączników, które działają z bazą danych MySQL, również powinny współpracować z MariaDB.

Celem jest zapewnienie pomocy technicznej trzy najnowsze wersje sterowników MariaDB, i Kontynuuj wysiłków autorów ze społeczności "open source" w celu stale funkcjonalność i użyteczność MariaDB sterowników. Lista sterowników, które zostały sprawdzone i ocenione jako zgodne z usługą Azure Database dla MariaDB 10.2 znajduje się w poniższej tabeli:

**Sterownik** | **Łącza** | **Zgodne wersje** | **Niezgodne wersje** | **Uwagi**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Środowisko PHP 7.0 połączenia z MySQLi protokołu SSL należy dodać MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT w parametrach połączenia. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Zestaw PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opcji na wartość false.
.NET | [MySqlConnector w witrynie GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Instalacja pakietu Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 oraz po | 0.26.5 i przed |
MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Kodowanie błędów może spowodować połączeń w niektórych systemach Windows bez UTF8.
Node.js |  [MySQLjs w witrynie GitHub](https://github.com/mysqljs/mysql/) <br> Pakiet instalacyjny z poziomu narzędzia NPM:<br> Uruchom `npm install mysql` z poziomu narzędzia NPM | 2.15 | 2.14.1 i przed
JĘZYK GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 i przed | Użyj `allowNativePasswords=true` w parametrach połączenia dla wersji 1.3. W wersji 1.4 zawiera poprawki i `allowNativePasswords=true` nie jest już wymagane.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 i przed |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 i przed |

## <a name="management-tools"></a>Narzędzia do zarządzania

Zaletą zgodności obejmuje również narzędzia do zarządzania bazą danych. Z istniejących narzędzi będą nadal działać z usługą Azure Database dla serwera MariaDB, tak długo, jak Manipulowanie bazą danych działa w granicach uprawnień użytkownika. Trzy wspólnej bazy danych narzędzi do zarządzania, które zostały sprawdzone i ocenione jako zgodne z usługą Azure Database dla MariaDB 10.2 są wymienione w poniższej tabeli:

| | **Aplikację MySQL Workbench 6.x i nowsze** | **Navicat 12** | **Narzędzia PHPMyAdmin 4.x i nowsze**
---|---|---|---
Create, Update, Read, Write, Delete | X | X | X
Połączenia SSL | X | X | X
Okno Autouzupełnianie zapytań SQL | X | X |
Importowanie i eksportowanie danych | X | X | X
Eksportowanie do wielu formatów | X | X | X
Wykonywanie kopii zapasowych i przywracanie |  | X |
Wyświetl parametry serwera | X | X | X
Wyświetlanie połączeń klienta | X | X | X

## <a name="next-steps"></a>Kolejne kroki

- [Rozwiązywanie problemów z połączeniem do usługi Azure Database dla serwera MariaDB](howto-troubleshoot-common-connection-issues.md)