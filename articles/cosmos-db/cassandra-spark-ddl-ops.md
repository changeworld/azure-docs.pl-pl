---
title: Operacje DDL w interfejsie API Cassandra usługi Azure Cosmos DB firmy Spark
description: W tym artykule opisano operacje przestrzeni kluczy i tabeli DDL względem interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c0df05eff5dc84ef24e1ed5afcaf705d99f447ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622575"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operacje DDL w interfejsie API Cassandra usługi Azure Cosmos DB firmy Spark

W tym artykule opisano operacje przestrzeni kluczy i tabeli DDL względem interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark.

## <a name="cassandra-api-related-configuration"></a>Konfiguracja związana z api cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operacje DDL przestrzeni kluczy

### <a name="create-a-keyspace"></a>Tworzenie przestrzeni kluczy

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Sprawdź poprawność w cqlsh

Uruchom następujące polecenie w cqlsh i powinieneś zobaczyć przestrzeń kluczy utworzoną wcześniej.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Upuść przestrzeń kluczy

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Sprawdź poprawność w cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operacje DDL tabeli

**Zagadnienia do rozważenia:**  

- Przepływność można przypisać na poziomie tabeli przy użyciu instrukcji tworzenie tabeli.  
- Jeden klucz partycji może przechowywać 20 GB danych.  
- Jeden rekord może przechowywać maksymalnie 2 MB danych.  
- Jeden zakres kluczy partycji może przechowywać wiele kluczy partycji.

### <a name="create-a-table"></a>Tworzenie tabeli

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Sprawdź poprawność w cqlsh

Uruchom następujące polecenie w cqlsh i powinien zobaczyć tabelę o nazwie "książki: 

```bash
USE books_ks;
DESCRIBE books;
```

Aprowizowana przepływność i domyślne wartości czasu wygaśnięcia nie są wyświetlane w danych wyjściowych poprzedniego polecenia, można uzyskać te wartości z portalu.

### <a name="alter-table"></a>Zmienianie tabeli

Następujące wartości można zmienić za pomocą polecenia alter table:

* aprowizowana przepływność 
* wartość czasu do żywo
<br>Zmiany kolumn nie są obecnie obsługiwane.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Tabela upuszczania

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Sprawdź poprawność w cqlsh

Uruchom następujące polecenie w cqlsh i powinieneś zobaczyć, że tabela "książki" nie jest już dostępna:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu przestrzeni kluczy i tabeli przejdź do następujących artykułów dla operacji CRUD i nie tylko:
 
* [Tworzenie/wstawianie operacji](cassandra-spark-create-ops.md)  
* [Operacje odczytu](cassandra-spark-read-ops.md)  
* [Operacje upsert](cassandra-spark-upsert-ops.md)  
* [Usuwanie operacji](cassandra-spark-delete-ops.md)  
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)  
* [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)  
