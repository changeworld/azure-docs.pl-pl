---
title: Biblioteki połączeń — usługa Azure Database for MySQL
description: W tym artykule wymieniono każdą bibliotekę lub sterownik, których programy klienckie mogą używać podczas łączenia się z usługą Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537197"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Biblioteki połączeń dla usługi Azure Database dla mysql
W tym artykule wymieniono każdą bibliotekę lub sterownik, których programy klienckie mogą używać podczas łączenia się z usługą Azure Database for MySQL.

## <a name="client-interfaces"></a>Interfejsy klienta
MySQL oferuje standardową łączność sterownika bazy danych do korzystania z MySQL z aplikacjami i narzędziami, które są zgodne ze standardami branżowymi ODBC i JDBC. Każdy system, który współpracuje z ODBC lub JDBC może używać MySQL.

| **Język** | **Platforma** | **Dodatkowy zasób** | **Pobierz** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL rodzimy sterownik dla PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Pobierz](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X i Unix | [Przewodnik po złączu MySQL/ODBC dla programistów](https://dev.mysql.com/doc/connector-odbc/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Przewodnik po złączu MySQL/dodatku Net Developer Guide](https://dev.mysql.com/doc/connector-net/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Niezależna od platformy | [Przewodnik dla programistów złącza MySQL/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Pobierz](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Przewodnik po złączu MySQL/Pythonie](https://dev.mysql.com/doc/connector-python/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Przewodnik dla programistów złączy MySQL/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Pobierz](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Przewodnik po złączu MySQL/C dla deweloperów](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Pobierz](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy Windows, Linux, Mac OS X i Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Pobierz](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Następne kroki
Przeczytaj te przewodniki Szybki start, jak łączyć się z usługą Azure Database dla mysql i wysyłać zapytania:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

