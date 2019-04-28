---
title: Utwórz/wstawienie danych w usłudze Azure Cosmos DB Cassandra interfejs API, z platformy Spark
description: Ten artykuł szczegółowo opisuje sposób wstawić przykładowe dane w tabelach interfejsu API usługi Azure Cosmos DB Cassandra
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 03/18/2019
ms.openlocfilehash: aea646e7a390d5b53f0d4b388cfecd0c80fb19da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894049"
---
<!--Verify sucessfully-->
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Utwórz/wstawienie danych w usłudze Azure Cosmos DB Cassandra interfejs API, z platformy Spark

W tym artykule opisano sposób wstawić przykładowe dane do tabeli usługi Azure Cosmos DB Cassandra API z platformy Spark.

## <a name="cassandra-api-configuration"></a>Konfiguracja interfejsu API rozwiązania Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.cn")
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
## <a name="dataframe-api"></a>Ramka danych interfejsu API

### <a name="create-a-dataframe-with-sample-data"></a>Utwórz ramkę danych z przykładowymi danymi

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

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Utrwalanie do usługi Azure Cosmos DB Cassandra API

Podczas zapisywania danych, można również ustawić czas wygaśnięcia i ustawienia zasad zgodności, jak pokazano w poniższym przykładzie:

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

#### <a name="validate-in-cqlsh"></a>Sprawdzanie poprawności w cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Elastyczne bazy danych (RDD) interfejsu API

### <a name="create-a-rdd-with-sample-data"></a>Tworzenie RDD z przykładowymi danymi
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
> Utwórz, jeśli nie istnieje funkcja nie jest jeszcze obsługiwana.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Utrwalanie do usługi Azure Cosmos DB Cassandra API

Podczas zapisywania danych do interfejsu API rozwiązania Cassandra, można również ustawić czas wygaśnięcia i ustawienia zasad zgodności, jak pokazano w poniższym przykładzie:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Sprawdzanie poprawności w cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Kolejne kroki

Po wstawieniu danych do tabeli interfejsu API usługi Azure Cosmos DB Cassandra, przejdź do następujące artykuły, aby wykonywać inne operacje na danych przechowywanych w Cosmos DB Cassandra API:

* [operacje odczytu](cassandra-spark-read-ops.md)
* [Operacji UPSERT](cassandra-spark-upsert-ops.md)
* [Operacje usuwania](cassandra-spark-delete-ops.md)
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)
* [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->