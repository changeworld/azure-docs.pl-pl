---
title: Sterowniki MySQL i zgodność z narzędziami narzędzia do zarządzania
description: W tym artykule opisano MySQL sterowników i narzędzi do zarządzania, które są zgodne z usługą Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9e56c2bd65f8a9a517a7cdebe02a1d051c689df6
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985896"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL sterowników i narzędzi do zarządzania zgodne z usługą Azure Database for MySQL
W tym artykule opisano, sterowników i narzędzi do zarządzania, które są zgodne z usługą Azure Database for MySQL.

## <a name="mysql-drivers"></a>Sterowniki MySQL
Usługa Azure Database for MySQL — używa najpopularniejszych wersji community na świecie bazy danych MySQL. Dlatego jest zgodny z szerokiej gamy języków i sterowniki programowania. Celem jest zapewnienie pomocy technicznej trzy najnowsze wersje sterowników MySQL i kontynuować wysiłków autorów ze społeczności "open source" w celu stale funkcjonalność i użyteczność sterowniki MySQL. Lista sterowników, które zostały sprawdzone i ocenione jako zgodne z usługą Azure Database for MySQL 5.6 i 5.7 znajduje się w poniższej tabeli:

| **Sterownik** | **Łącza** | **Zgodne wersje** | **Wersje niezgodną** | **Uwagi** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Środowisko PHP 7.0 połączenia z MySQLi protokołu SSL należy dodać MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT w parametrach połączenia. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Zestaw PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opcji na wartość false.|
| .Net | [MySqlConnector w witrynie GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Instalacja pakietu Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 oraz po | 0.26.5 i przed | |
| Nodejs |  [MySQLjs w witrynie GitHub](https://github.com/mysqljs/mysql/releases) <br> Pakiet instalacyjny z poziomu narzędzia NPM:<br> Uruchom `npm install mysql` z poziomu narzędzia NPM | 2.15 | 2.14.1 i przed | |
| PRZEJDŹ | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 i przed | Użyj allowNativePasswords = true w parametrach połączenia |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 i przed | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 i przed | |

## <a name="management-tools"></a>Narzędzia do zarządzania
Zaletą zgodności obejmuje również narzędzia do zarządzania bazą danych. Istniejącymi narzędziami powinny nadal działać z usługą Azure Database for MySQL, tak długo, jak Manipulowanie bazą danych działa w granicach uprawnień użytkownika. Trzy wspólnej bazy danych narzędzi do zarządzania, które zostały sprawdzone i ocenione jako zgodne z usługą Azure Database for MySQL 5.6 i 5.7 są wymienione w poniższej tabeli:

|                                     | **Aplikację MySQL Workbench 6.x i nowsze** | **Navicat 12** | **Narzędzia PHPMyAdmin 4.x i nowsze** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Create, Update, Read, Write, Delete | X | X | X |
| Połączenia SSL | X | X | X |
| Okno Autouzupełnianie zapytań SQL | X | X |  |
| Importowanie i eksportowanie danych | X | X | X |
| Eksportowanie do wielu formatów | X | X | X |
| Wykonywanie kopii zapasowych i przywracanie |  | X |  |
| Wyświetl parametry serwera | X | X | X |
| Wyświetlanie połączeń klienta | X | X | X |
