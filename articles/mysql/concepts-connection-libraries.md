---
title: Biblioteki połączeń dla bazy danych Azure dla programu MySQL
description: W tym artykule wymieniono każdej biblioteki lub sterownik programów klienckich można używany do łączenia z bazą danych Azure dla programu MySQL.
services: mysql
author: mswutao
ms.author: wutao
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: c22bb9b31e7623daa635f82256e28ed466070481
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Biblioteki połączeń dla bazy danych Azure dla programu MySQL
W tym artykule wymieniono każdej biblioteki lub sterownik programów klienckich można używany do łączenia z bazą danych Azure dla programu MySQL.

## <a name="client-interfaces"></a>Interfejsy klienta
MySQL zapewnia łączność sterownik standardowe bazy danych dotyczące korzystania z aplikacji i narzędzi, które są zgodne ze standardami branżowymi ODBC i JDBC MySQL. Każdy system, który współdziała z ODBC i JDBC można użyć MySQL.

| **Język** | **Platformy** | **Dodatkowe zasoby** | **Pobieranie** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Natywnego sterownika MySQL dla języka PHP — mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Pobieranie](http://php.net/downloads.php) |
| ODBC | Platformy systemu Windows, Linux, Mac OS X i Unix | [Przewodnik dewelopera MySQL łącznika/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Przewodnik dewelopera MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Niezależnie od platformy | [Przewodnik dewelopera łącznika/J MySQL 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Pobieranie](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Przewodnik dewelopera MySQL łącznika/Python](https://dev.mysql.com/doc/connector-python/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Przewodnik dewelopera MySQL łącznika/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Przewodnik dewelopera MySQL łącznika/C](https://dev.mysql.com/doc/connector-c/en/) | [Pobieranie](https://dev.mysql.com/downloads/connector/c/)
| Perl | Platformy systemu Windows, Linux, Mac OS X i Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Pobieranie](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Kolejne kroki
Przeczytaj te przewodniki Szybki Start na temat nawiązywania i kwerendy bazy danych platformy Azure dla programu MySQL przy użyciu języka wybór:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

