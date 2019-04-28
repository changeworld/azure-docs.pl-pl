---
title: Operacje języka DDL w usługi Azure Cosmos DB Cassandra API z platformy Spark
description: Ten artykuł szczegółowo opisuje operacje języka DDL w przestrzeni kluczy i tabeli dla platformy Azure Cosmos DB bazy danych Cassandra API z platformy Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c12787cd6e0df19fd842dd44da49aa5ea97aa05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898886"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operacje języka DDL w usługi Azure Cosmos DB Cassandra API z platformy Spark

Ten artykuł szczegółowo opisuje operacje języka DDL w przestrzeni kluczy i tabeli dla platformy Azure Cosmos DB bazy danych Cassandra API z platformy Spark.

## <a name="cassandra-api-related-configuration"></a>Konfiguracji odnoszące się do interfejsu API rozwiązania Cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operacje języka DDL przestrzeń kluczy

### <a name="create-a-keyspace"></a>Tworzenie przestrzeni kluczy

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Sprawdzanie poprawności w cqlsh

Uruchom następujące polecenie w cqlsh i powinna zostać wyświetlona przestrzeń kluczy, która została utworzona wcześniej.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Upuść przestrzeń kluczy

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Sprawdzanie poprawności w cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operacje języka DDL tabeli

**Kwestie:**  

- Przepływności można przypisywać na poziomie tabeli za pomocą instrukcji create table.  
- Jeden klucz partycji może przechowywać 10 GB danych.  
- Jeden rekord może przechowywać maksymalnie 2 MB danych.  
- Jeden zakres kluczy partycji można przechowywać wiele kluczy partycji.

### <a name="create-a-table"></a>Tworzenie tabeli

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Sprawdzanie poprawności w cqlsh

Uruchom następujące polecenie w cqlsh i powinien zostać wyświetlony tabelę o nazwie "książek: 

```bash
USE books_ks;
DESCRIBE books;
```

Aprowizowana przepływność i domyślne wartości TTL nie są wyświetlane w danych wyjściowych poprzedniego polecenia, z poziomu portalu można uzyskać te wartości.

### <a name="alter-table"></a>Instrukcja ALTER table

Za pomocą polecenia alter table, można zmienić następujące wartości:

* aprowizowana przepływność 
* wartość czasu wygaśnięcia
<br>Zmiany kolumn nie są obecnie obsługiwane.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Usunięcie tabeli

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Sprawdzanie poprawności w cqlsh

Uruchom następujące polecenie w cqlsh i powinien zostać wyświetlony, że w tabeli "books" nie jest już dostępne:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu przestrzeni kluczy i tabeli, przejdź do następujących artykułów, aby operacje CRUD i inne:
 
* [Utwórz/insert operacji](cassandra-spark-create-ops.md)  
* [operacje odczytu](cassandra-spark-read-ops.md)  
* [Operacji UPSERT](cassandra-spark-upsert-ops.md)  
* [Operacje usuwania](cassandra-spark-delete-ops.md)  
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)  
* [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)  
