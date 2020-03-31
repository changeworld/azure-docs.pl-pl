---
title: Program Spark Connector z bazą danych SQL i programem SQL Server
description: Dowiedz się, jak używać łącznika Platformy Spark dla bazy danych SQL Azure i programu SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471656"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Przyspiesz analizę dużych zbiorów danych w czasie rzeczywistym dzięki łącznikowi Spark dla usługi Azure SQL Database i SQL Server

Łącznik Platformy Spark dla usługi Azure SQL Database i SQL Server umożliwia bazom danych SQL, w tym bazy danych SQL Azure i SQL Server, działanie jako źródło danych wejściowych lub ujście danych wyjściowych dla zadań platformy Spark. Umożliwia wykorzystanie danych transakcyjnych w czasie rzeczywistym w analizie dużych zbiorów danych i utrwalanie wyników dla zapytań lub raportowania adokoc. W porównaniu do wbudowanego łącznika JDBC ten łącznik zapewnia możliwość zbiorczego wstawiania danych do baz danych SQL. Może przewyższać wstawianie wiersza po wierszu z 10x do 20x większą wydajnością. Łącznik Platformy Spark dla usługi Azure SQL Database i SQL Server obsługuje również uwierzytelnianie usługi AAD. Umożliwia bezpieczne łączenie się z bazą danych SQL platformy Azure z usługi Azure Databricks przy użyciu konta usługi AAD. Zapewnia podobne interfejsy z wbudowanym złączem JDBC. Łatwo jest przeprowadzić migrację istniejących zadań platformy Spark, aby użyć tego nowego łącznika.

## <a name="download"></a>Pobierz
Aby rozpocząć, pobierz łącznik spark do bazy danych SQL z [repozytorium azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) w usłudze GitHub.

## <a name="official-supported-versions"></a>Oficjalne obsługiwane wersje

| Składnik                            | Wersja                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 lub nowsze           |
| Scala                                | 2.10 lub nowsze            |
| Sterownik JDBC firmy Microsoft dla programu SQL Server | 6.2 lub nowsze             |
| Microsoft SQL Server                 | SQL Server 2008 lub nowsza wersja |
| Azure SQL Database                   | Obsługiwane                |

Łącznik Platformy Spark dla usługi Azure SQL Database i SQL Server wykorzystuje sterownik JDBC firmy Microsoft dla programu SQL Server do przenoszenia danych między węzłami procesu roboczego platformy Spark a bazami danych SQL:
 
Przepływ danych jest następujący:
1. Węzeł główny platformy Spark łączy się z usługą SQL Server lub Azure SQL Database i ładuje dane z określonej tabeli lub przy użyciu określonej kwerendy SQL
2. Węzeł główny platformy Spark dystrybuuje dane do węzłów procesu roboczego w celu przekształcenia. 
3. Węzeł Proces roboczy łączy się z programem SQL Server lub bazą danych SQL i zapisuje dane w bazie danych. Użytkownik może wybrać opcję wstawiania wiersza po wierszu lub wstawiania zbiorczego.

Na poniższym diagramie przedstawiono przepływ danych.

   ![architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Tworzenie łącznika spark do bazy danych SQL
Obecnie projekt łącznika używa maven. Aby utworzyć łącznik bez zależności, można uruchomić:

- mvn czysty pakiet
- Pobierz najnowsze wersje JAR z folderu release
- Dołącz SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Łączenie platformy Spark z bazą danych SQL za pomocą łącznika
Można połączyć się z usługą Azure SQL Database lub SQL Server z zadań Spark, odczytu lub zapisu danych. Można również uruchomić kwerendę DML lub DDL w bazie danych SQL azure lub bazy danych programu SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Odczytywanie danych z bazy danych SQL Azure lub programu SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Odczytywanie danych z bazy danych SQL azure lub programu SQL Server z określoną kwerendą SQL
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
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Zapisywanie danych w bazie danych SQL Database lub programie SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Uruchamianie kwerendy DML lub DDL w bazie danych SQL Azure lub programie SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Łączenie programu Spark z usługą Azure SQL Database przy użyciu uwierzytelniania usługi AAD
Możesz połączyć się z usługą Azure SQL Database przy użyciu uwierzytelniania usługi Azure Active Directory (AAD). Uwierzytelnianie AAD służy do centralnego zarządzania tożsamościami użytkowników bazy danych i jako alternatywa dla uwierzytelniania programu SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Łączenie przy użyciu trybu uwierzytelniania ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Wymagania dotyczące konfiguracji
Jeśli używasz trybu uwierzytelniania ActiveDirectoryPassword, musisz pobrać [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jego zależności i uwzględnić je w ścieżce kompilacji Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Łączenie przy użyciu tokenu dostępu
#### <a name="setup-requirement"></a>Wymagania dotyczące konfiguracji
Jeśli używasz trybu uwierzytelniania opartego na tokenie dostępu, musisz pobrać [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jej zależności i uwzględnić je w ścieżce kompilacji Java.

Zobacz [Korzystanie z uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą bazy danych SQL,](sql-database-aad-authentication.md) aby dowiedzieć się, jak uzyskać token dostępu do bazy danych SQL platformy Azure.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zapisywanie danych w bazie danych SQL platformy Azure lub programie SQL Server przy użyciu wstawiania zbiorczego
Tradycyjny łącznik jdbc zapisuje dane w bazie danych SQL platformy Azure lub programie SQL Server przy użyciu wstawiania wiersza po wierszu. Łącznik bazy danych Platformy Spark do bazy danych SQL służy do zapisywania danych w bazie danych SQL przy użyciu wstawiania zbiorczego. Znacznie poprawia wydajność zapisu podczas ładowania dużych zestawów danych lub ładowania danych do tabel, w których jest używany indeks magazynu kolumn.

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
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiłeś, pobierz łącznik platformy Spark dla usługi Azure SQL Database i SQL Server z [repozytorium GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) i eksploruj dodatkowe zasoby w repozytorium:

- [Przykładowe notesy usługi Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Przykładowe skrypty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Można również przejrzeć [Apache Spark SQL, DataFrames i Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) i [dokumentacji usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

