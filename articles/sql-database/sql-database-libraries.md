---
title: Biblioteki połączeń dla bazy danych SQL | Dokumentacja firmy Microsoft
description: Zawiera łącza do pobrania modułów, które umożliwiają połączenie programu SQL Server i bazy danych SQL z szerokiej gamy klienta języków programowania.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: cb9bc6dc21d043bfa2d5b278eec4d91494263515
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647531"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Łączność bibliotek i platform dla programu SQL Server

Zapoznaj się z naszym [uzyskać uruchomiona samouczki](http://aka.ms/sqldev) szybko rozpocząć pracę z języków, takich jak C#, Java, Node.js, PHP i Python programowania. Następnie należy utworzyć aplikację przy użyciu programu SQL Server w systemie Linux lub Windows lub platformy Docker na macOS.

Poniższa tabela zawiera listę bibliotek łączności lub *sterowniki* czy aplikacje klienckie mogą używać z różnych języków nawiązywania i używania programu SQL Server, uruchamiane lokalnie lub w chmurze. Można używać ich w systemie Linux, Windows lub Docker i umożliwia łączenie się z bazą danych SQL Azure i usługi Azure SQL Data Warehouse. 

| Język | Platforma | Zasoby dodatkowe | Do pobrania | Rozpoczęcie pracy |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET dla programu SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Pobieranie](https://www.microsoft.com/net/download/) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Sterownik JDBC firmy Microsoft dla programu SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Pobieranie](https://go.microsoft.com/fwlink/?linkid=852460) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Sterownik PHP SQL dla programu SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | System operacyjny: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Sterownik programu node.js dla programu SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Instalowanie](https://msdn.microsoft.com/library/mt652094.aspx) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Sterownik Python SQL](http://msdn.microsoft.com/library/mt652092.aspx) | Zainstaluj opcje: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Sterownik dopisków fonetycznych dla programu SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Instalowanie](https://msdn.microsoft.com/library/mt711041.aspx) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Sterownik ODBC firmy Microsoft dla programu SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Pobieranie](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

W poniższej tabeli wymieniono przykładowe mapowanie obiektów relacyjnych (ORM) struktury i struktur sieci web, które aplikacje klienckie można użyć z programem SQL Server, uruchamiane lokalnie lub w chmurze. Można używać struktur w systemie Linux, Windows lub Docker i umożliwia łączenie się z bazą danych SQL i magazyn danych SQL. 

| Język | Platforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Program Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernacji ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby na szyny](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Powiązane linki
- [SQL Server sterowniki](http://msdn.microsoft.com/library/mt654049.aspx) służące do połączenia z aplikacji klienta
- Połączenie z bazą danych SQL:
    - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu platformy .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka PHP](sql-database-connect-query-php.md)
    - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Node.js](sql-database-connect-query-nodejs.md)
    - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu platformy Java](sql-database-connect-query-java.md)
    - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Python](sql-database-connect-query-python.md)
    - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Ruby](sql-database-connect-query-ruby.md)
- Ponów próbę logiki przykłady kodu:
    - [Resiliently połączenia z serwerem SQL z ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Resiliently połączenia z serwerem SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

