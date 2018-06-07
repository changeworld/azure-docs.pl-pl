---
title: Platforma Spark jest łącznik z bazy danych Azure SQL i SQL Server | Dokumenty Microsoft
description: Informacje o sposobie korzystania z łącznika Spark dla usługi Azure SQL Database i programu SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: a422f65097466e4bbe5740c449d3ccf88701802b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650166"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Przyspieszenie analizy w czasie rzeczywistym danych big data z łącznikiem Spark dla usługi Azure SQL Database i programu SQL Server

Łącznik programu Spark dla usługi Azure SQL Database i programu SQL Server umożliwia baz danych SQL, łącznie z bazy danych SQL Azure i SQL Server, do działania jako danych wejściowych ujścia danych źródła lub dane wyjściowe zadań Spark. Umożliwia korzystanie w czasie rzeczywistym danych transakcyjnych w analizy danych big data i utrwalić wyniki zapytań ad hoc lub wykonywania raportu. W porównaniu do wbudowanego łącznika JDBC, ten łącznik umożliwia zbiorczego wstawiania danych do bazy danych SQL. Można go przewyższyć wiersz po wierszu wstawiania x 10 do 20 x większą wydajność. Łącznik programu Spark dla usługi Azure SQL Database i programu SQL Server obsługuje również uwierzytelniania w usłudze AAD. Pozwala ona bezpiecznego połączenia z bazą danych Azure SQL z Databricks Azure przy użyciu konta usługi AAD. Zapewnia interfejsy podobne z wbudowanego łącznika JDBC. Jest łatwy do migracji istniejącej zadań Spark używanie tego nowego łącznika.

## <a name="download"></a>Do pobrania
Aby rozpocząć pracę, należy pobrać Spark łącznikiem bazy danych SQL z [repozytorium azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) w witrynie GitHub.

## <a name="official-supported-versions"></a>Oficjalne obsługiwane wersje

| Składnik                            |Wersja                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 lub nowszy           |
| Scala                                |2.10 lub nowszy            |
| Sterownik JDBC firmy Microsoft dla programu SQL Server |6.2 lub nowszej             |
| Microsoft SQL Server                 |SQL Server 2008 lub nowszy |
| Azure SQL Database                   |Obsługiwane                |

Łącznik programu Spark dla usługi Azure SQL Database i programu SQL Server korzysta z sterownik JDBC firmy Microsoft dla programu SQL Server do przenoszenia danych między węzłami procesów roboczych Spark i bazy danych SQL:
 
Biblioteka przepływu danych jest następujący:
1. Węzeł główny Spark nawiązuje połączenie z serwerem SQL lub bazy danych SQL Azure i ładuje dane z określonej tabeli lub przy użyciu określonej kwerendy SQL
2. Węzeł główny Spark dystrybuuje danych z węzłami procesu roboczego dla transformacji. 
3. Z węzłem procesu roboczego nawiązuje połączenie z serwerem SQL lub bazy danych SQL Azure i zapisuje dane w bazie danych. Użytkownik może użyć wstawiania wiersz po wierszu lub zbiorczego wstawiania.

Na poniższym diagramie przedstawiono przepływ danych.

   ![architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Tworzenie Spark łącznikiem bazy danych SQL.
Obecnie projektu łącznik używa maven. Aby utworzyć łącznik bez zależności, możesz uruchomić:

- Pakiet czystą mvn
- Pobierz najnowsze wersje JAR w folderze zlecenia
- Obejmują Spark bazy danych SQL JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Spark nawiązać połączenia z bazą danych SQL za pomocą łącznika
Możesz nawiązać połączenie bazy danych SQL Azure lub programu SQL Server z zadań Spark, odczytu lub zapisu danych. Można również uruchomić kwerendę DML lub DDL bazy danych Azure SQL lub w bazie danych programu SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Odczyt danych z bazy danych SQL Azure lub programu SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Odczytywanie danych z bazy danych SQL Azure lub programu SQL Server z określonego zapytania SQL
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Zapisu danych do usługi Azure SQL Database lub SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Uruchom zapytanie DML lub DDL w bazie danych SQL Azure lub programu SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Platforma Spark nawiązać połączenia z bazą danych SQL Azure przy użyciu uwierzytelniania w usłudze AAD
Można połączyć z bazą danych SQL Azure przy użyciu uwierzytelniania usługi Azure Active Directory (AAD). Centralne zarządzanie tożsamościami użytkowników bazy danych i alternatywą dla uwierzytelniania programu SQL Server za pomocą uwierzytelniania usługi AAD.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Łączenie się za pomocą ActiveDirectoryPassword tryb uwierzytelniania
#### <a name="setup-requirement"></a>Wymagania instalacji
Jeśli korzystasz z trybu uwierzytelniania ActiveDirectoryPassword, należy pobrać [azure-activedirectory biblioteki do java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jego zależności i dołączyć je ścieżka kompilacji języka Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Łączenie za pomocą tokenu dostępu
#### <a name="setup-requirement"></a>Wymagania instalacji
Jeśli korzystasz z trybu uwierzytelniania opartego na tokenach dostępu, należy pobrać [azure-activedirectory biblioteki do java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jego zależności i dołączyć je ścieżka kompilacji języka Java.

Zobacz [Użyj Azure uwierzytelnianie usługi Active Directory do uwierzytelniania w usłudze SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) Aby dowiedzieć się, jak uzyskać dostęp do tokenu z bazą danych Azure SQL.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zapisu danych do bazy danych Azure SQL lub instrukcji Bulk INSERT programu SQL Server
Łącznik jdbc tradycyjnych zapisuje dane do bazy danych Azure SQL lub SQL Server przy użyciu wstawiania wiersz po wierszu. Można zapisać danych do bazy danych SQL za pomocą wstawiania zbiorczego służy Spark do łącznika bazy danych SQL. Znacznie zwiększa wydajność zapisu podczas ładowania dużych zestawów danych lub podczas ładowania danych do tabel, w których jest używany indeks magazynu kolumn.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie jest jeszcze Pobierz łącznik Spark dla usługi Azure SQL Database i programu SQL Server z [repozytorium GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) i przejrzyj dodatkowe zasoby w repozytorium:

-   [Przykładowe notesów Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Przykładowe skrypty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Można także przejrzeć [Apache Spark SQL, przewodnik zestawów danych i DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) i [Azure Databricks dokumentacji](https://docs.microsoft.com/azure/azure-databricks/).

