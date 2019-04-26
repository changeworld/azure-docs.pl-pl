---
title: Platforma Spark jest łącznika przy użyciu usługi Azure SQL Database i programu SQL Server | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z łącznika Spark dla usługi Azure SQL Database i programu SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 8e531de34302ef8aee571c960955d33a4832aa11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331528"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Przyspiesz analizę danych big Data w czasie rzeczywistym za pomocą łącznika usługi Spark dla usługi Azure SQL Database i programu SQL Server

Łącznik platformy Spark dla usługi Azure SQL Database i programu SQL Server umożliwia baz danych SQL, w tym usługi Azure SQL Database i programu SQL Server do działania jako dane wejściowe źródło lub danych wyjściowych ujście dla zadań platformy Spark. Umożliwia korzystanie z danych transakcyjnych w czasie rzeczywistym w analizy danych big data i utrzymują się wyniki zapytania ad hoc i raportowania. W porównaniu do wbudowanego łącznika JDBC, ten łącznik umożliwia zbiorcze Wstawianie danych do bazy danych SQL. Można go przewyższyć wiersz po wierszu wstawiania x 10 do 20 x szybsze działanie. Łącznik platformy Spark dla usługi Azure SQL Database i programu SQL Server obsługuje również uwierzytelnianie w usłudze AAD. Umożliwia bezpieczne nawiązywanie połączenia usługi Azure SQL database z usługi Azure Databricks przy użyciu swojego konta usługi AAD. Za pomocą wbudowanego łącznika JDBC zapewnia podobne interfejsy. Jest łatwą migrację istniejących zadań platformy Spark używanie tego nowego łącznika.

## <a name="download"></a>Do pobrania
Aby rozpocząć pracę, Pobierz platformy Spark do łącznika bazy danych SQL z [repozytorium azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) w witrynie GitHub.

## <a name="official-supported-versions"></a>Oficjalna obsługiwane wersje

| Składnik                            |Wersja                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 lub nowszy           |
| Scala                                |2.10 lub nowszy            |
| Sterownik JDBC firmy Microsoft dla programu SQL Server |6.2 lub nowszej             |
| Microsoft SQL Server                 |Program SQL Server 2008 lub nowszy |
| Azure SQL Database                   |Obsługiwane                |

Łącznik platformy Spark dla usługi Azure SQL Database i programu SQL Server korzysta z sterownik JDBC firmy Microsoft dla programu SQL Server do przenoszenia danych między węzłami procesów roboczych platformy Spark i baz danych SQL:
 
Przepływ danych jest w następujący sposób:
1. Węzeł główny Spark łączy się z programu SQL Server lub usługi Azure SQL Database i ładuje dane z określonej tabeli lub przy użyciu określonego zapytania SQL
2. Węzeł główny Spark rozprowadza dane z węzłami procesu roboczego dla transformacji. 
3. Węzeł procesu roboczego łączy się z programu SQL Server lub usługi Azure SQL Database i zapisuje dane w bazie danych. Użytkownik może zdecydować użyć wstawiania wiersz po wierszu lub zbiorczego wstawiania.

Na poniższym diagramie przedstawiono przepływ danych.

   ![architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Tworzenie platformy Spark do łącznika usługi SQL DB
Obecnie projekt łącznik korzysta z narzędzia maven. Aby skompilować łącznik bez zależności, można uruchomić:

- Wyczyść pakietu mvn
- Pobierz najnowsze wersje plik JAR z folderu wydania
- Obejmują bazy danych SQL platformy Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Platforma Spark nawiązać połączenie z bazą danych SQL za pomocą łącznika
Możesz połączyć się z usługi Azure SQL Database lub SQL Server z zadań platformy Spark, odczytu lub zapisu danych. Można również uruchomić zapytania DML lub DDL w bazie danych Azure SQL lub bazy danych programu SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Odczytywanie danych z usługi Azure SQL Database lub SQL Server

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Odczytywanie danych z usługi Azure SQL Database lub SQL Server przy użyciu określonego zapytania SQL
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

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Uruchamianie zapytania DML lub DDL w usłudze Azure SQL Database lub SQL Server
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

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Platforma Spark nawiązać połączenie z usługi Azure SQL Database przy użyciu uwierzytelniania usługi AAD
Można połączyć z usługą Azure SQL Database przy użyciu uwierzytelniania usługi Azure Active Directory (AAD). Uwierzytelnianie w usłudze AAD umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych oraz jako alternatywę dla uwierzytelniania programu SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Łączenie za pomocą ActiveDirectoryPassword tryb uwierzytelniania
#### <a name="setup-requirement"></a>Wymagania instalacji
Jeśli używasz trybu uwierzytelniania ActiveDirectoryPassword należy pobrać [azure-activedirectory biblioteki do java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jego zależności i dołączyć je ścieżka kompilacji języka Java.

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
Jeśli używasz trybu uwierzytelniania opartego na tokenach dostępu, musisz pobrać [azure-activedirectory biblioteki do java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jego zależności i dołączyć je ścieżka kompilacji języka Java.

Zobacz [użycia Azure uwierzytelnianie usługi Active Directory do uwierzytelniania w usłudze SQL Database](sql-database-aad-authentication.md) dowiesz się, jak można uzyskać dostęp do tokenu do usługi Azure SQL database.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zapisać danych do bazy danych Azure SQL database lub SQL Server przy użyciu Bulk Insert
Łącznik tradycyjnych jdbc zapisuje dane do bazy danych Azure SQL database lub SQL Server przy użyciu wstawiania wiersz po wierszu. Spark do łącznika usługi SQL DB służy do zapisywania danych do usługi SQL database przy użyciu wstawiania zbiorczego. Znacznie zwiększa wydajność zapisu podczas ładowania dużych zestawów danych i ładowania danych do tabel, gdy indeks magazynu kolumn jest używany.

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
Jeśli jeszcze nie, Pobierz łącznik platformy Spark dla usługi Azure SQL Database i programu SQL Server z [repozytorium GitHub usługi azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) i dodatkowe zasoby w repozytorium:

-   [Przykładowe notesów usługi Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Przykładowe skrypty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Możesz również przejrzeć [Apache Spark SQL, przewodnik zestawy danych i elementy Dataframe](https://spark.apache.org/docs/latest/sql-programming-guide.html) i [dokumentacji usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

