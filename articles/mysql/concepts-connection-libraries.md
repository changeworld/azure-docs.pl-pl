---
title: Biblioteki połączeń dla usługi Azure Database for MySQL
description: W tym artykule wymieniono każdej biblioteki lub sterownik programów klienckich można używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 14515aefe9635160cf99a630b0742d23352532cf
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985968"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Biblioteki połączeń dla usługi Azure Database for MySQL
W tym artykule wymieniono każdej biblioteki lub sterownik programów klienckich można używać podczas nawiązywania połączenia z usługą Azure Database for MySQL.

## <a name="client-interfaces"></a>Interfejsów klientów
MySQL zapewnia łączność sterownik database w warstwie standardowa przy użyciu MySQL za pomocą aplikacji i narzędzi, które są zgodne ze standardami branżowymi, ODBC i sterownika JDBC. Każdy system, który współdziała z ODBC lub JDBC służy MySQL.

| **Język** | **Platforma** | **Dodatkowym zasobem** | **Pobieranie** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Natywne sterownika MySQL dla języka PHP — mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Pobieranie](https://secure.php.net/downloads.php) |
| ODBC | Platformy Windows, Linux, Mac OS X i Unix | [Przewodnik dla deweloperów MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Przewodnik dla deweloperów MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Niezależnie od platformy | [Przewodnik dla deweloperów MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Pobieranie](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Przewodnik dla deweloperów MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Przewodnik dla deweloperów MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Przewodnik dla deweloperów MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy Windows, Linux, Mac OS X i Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Pobieranie](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Kolejne kroki
Przeczytaj te przewodniki Szybki Start dotyczące nawiązać połączenie i zapytanie usługi Azure Database for MySQL za pomocą wybranym języku:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [środowiskaNode.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [przejdź](./connect-go.md)

