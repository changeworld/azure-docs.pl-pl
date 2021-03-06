---
title: Odczytywanie danych tabeli interfejsu API cassandra przy użyciu platformy Spark
titleSufix: Azure Cosmos DB
description: W tym artykule opisano sposób odczytywania danych z tabel interfejsu API Cassandra w usłudze Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60893404"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Odczytywanie danych z tabel interfejsu API usługi Azure Cosmos DB Cassandra przy użyciu platformy Spark

 W tym artykule opisano sposób odczytywania danych przechowywanych w interfejsie API Cassandra usługi Azure Cosmos DB z platformy Spark.

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

### <a name="read-table-using-sessionreadformat-command"></a>Przeczytaj tabelę za pomocą polecenia session.read.format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Odczyt tabeli przy użyciu pliku spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Odczytywanie określonych kolumn w tabeli

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Stosowanie filtrów

Obecnie predykatu pushdown nie jest obsługiwany, poniższe przykłady odzwierciedlają filtrowanie po stronie klienta. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>Przeczytaj tabelę
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Odczytywanie określonych kolumn w tabeli

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Widoki SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Tworzenie widoku tymczasowego z ramki danych

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Uruchamianie kwerend względem widoku

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dodatkowe artykuły dotyczące pracy z interfejsem API Cassandra usługi Azure Cosmos DB z platformy Spark:
 
 * [Operacje upsert](cassandra-spark-upsert-ops.md)
 * [Usuwanie operacji](cassandra-spark-delete-ops.md)
 * [Operacje agregacji](cassandra-spark-aggregation-ops.md)
 * [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)

