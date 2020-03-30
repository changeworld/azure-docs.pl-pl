---
title: Tworzenie lub wstawianie danych do interfejsu API cassandra usługi Azure Cosmos DB z platformy Spark
description: W tym artykule opisano, jak wstawić przykładowe dane do tabel interfejsu API usługi Azure Cosmos DB Cassandra
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3eb23a3d8b1098110bd8b75faa22cc483637d183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442118"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Tworzenie/wstawianie danych do interfejsu API cassandra usługi Azure Cosmos DB z platformy Spark
 
W tym artykule opisano sposób wstawiania przykładowych danych do tabeli w interfejsie API Cassandra usługi Azure Cosmos DB z platformy Spark.

## <a name="cassandra-api-configuration"></a>Konfiguracja interfejsu API Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Interfejs API dla ram danych

### <a name="create-a-dataframe-with-sample-data"></a>Tworzenie elementu dataframe z przykładowymi danymi

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> Funkcja "Utwórz, jeśli nie istnieje", na poziomie wiersza nie jest jeszcze obsługiwana.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Utrwalić się w interfejsie API Cassandra usługi Azure Cosmos DB

Podczas zapisywania danych można również ustawić ustawienia zasad czas do żywo i spójności, jak pokazano w poniższym przykładzie:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Czas wygaśnięcia na poziomie kolumny nie jest jeszcze obsługiwany.

#### <a name="validate-in-cqlsh"></a>Sprawdź poprawność w cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Interfejs API odpornej rozproszonej bazy danych (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Tworzenie rdd z przykładowymi danymi
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Tworzenie, jeśli nie istnieje funkcjonalność nie jest jeszcze obsługiwana.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Utrwalić się w interfejsie API Cassandra usługi Azure Cosmos DB

Podczas zapisywania danych w interfejsie API Cassandra można również ustawić ustawienia zasad czas do żywo i spójności, jak pokazano w poniższym przykładzie:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Sprawdź poprawność w cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Następne kroki

Po wstawieniu danych do tabeli interfejsu API usługi Azure Cosmos DB Cassandra przejdź do następujących artykułów, aby wykonać inne operacje na danych przechowywanych w interfejsie API Cassandra usługi Cosmos DB:
 
* [Operacje odczytu](cassandra-spark-read-ops.md)
* [Operacje upsert](cassandra-spark-upsert-ops.md)
* [Usuwanie operacji](cassandra-spark-delete-ops.md)
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)
* [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)

