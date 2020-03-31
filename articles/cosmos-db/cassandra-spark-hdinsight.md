---
title: Dostęp do interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark w programie YARN z programem HDInsight
description: W tym artykule opisano, jak pracować z interfejsem API Cassandra usługi Azure Cosmos DB z platformy Spark on YARN with HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887636"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Dostęp do interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark w programie YARN z programem HDInsight

W tym artykule opisano, jak uzyskać dostęp do interfejsu API cassandra usługi Azure Cosmos DB z platformy Spark on YARN z programem HDInsight-Spark z powłoki iskrowej. HDInsight to firma Microsoft Hortonworks Hadoop PaaS na platformie Azure, która wykorzystuje pamięć masową obiektów dla plików HDFS i jest dostępna w kilku smakach, w tym [Spark.](../hdinsight/spark/apache-spark-overview.md)  Podczas gdy zawartość w tym dokumencie odwołuje się do HDInsight-Spark, ma zastosowanie do wszystkich dystrybucji Hadoop.  

## <a name="prerequisites"></a>Wymagania wstępne

* [Aprowizowania interfejsu API cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Zapoznaj się z podstawami łączenia się z interfejsem API Cassandra usługi Azure Cosmos DB](cassandra-spark-generic.md)

* [Aprowizowanie klastra HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Przejrzyj przykłady kodu do pracy z interfejsem API Cassandra](cassandra-spark-generic.md#next-steps)

* [Użyj cqlsh do sprawdzania poprawności, jeśli wolisz](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfiguracja interfejsu API Cassandra w programie Spark2** — łącznik platformy Spark dla kasandry wymaga zainicjowania szczegółów połączenia Cassandra jako części kontekstu platformy Spark. Po uruchomieniu notebooka Jupyter sesja iskra i kontekst są już inicjowane i nie zaleca się zatrzymywania i ponownego inicjowania kontekstu Platformy Spark, chyba że jest on kompletny z każdym zestawem konfiguracyjnym jako część domyślnego uruchamiania notebooka Jupyter. Jednym z obejść jest dodanie szczegółów wystąpienia Cassandra do konfiguracji usługi Ambari, Spark2 bezpośrednio. Jest to jednorazowe działanie na klaster, które wymaga ponownego uruchomienia usługi Spark2.
 
  1. Przejdź do usługi Ambari, Spark2 i wybierz configs

  2. Następnie przejdź do niestandardowych spark2-defaults i dodaj nową właściwość z następującymi i uruchom ponownie usługę Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Dostęp do interfejsu API Cassandra usługi Azure Cosmos DB z powłoki Spark

Powłoka iskry jest używana do celów testowania/eksploracji.

* Uruchom powłokę iskrową z wymaganymi zależnościami maven zgodnymi z wersją platformy Spark klastra.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Wykonywanie niektórych operacji DDL i DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Uruchamianie operacji CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Dostęp do interfejsu API Cassandra usługi Azure Cosmos DB z notesów Jupyter

HDInsight-Spark jest wyposażony w usługi notebooków Zeppelin i Jupyter. Są to środowiska notesu oparte na sieci Web, które obsługują Scala i Python. Notebooki doskonale nadają się do interaktywnej analizy odkrywczej i współpracy, ale nie są przeznaczone do procesów operacyjnych/produkcyjnych.

Następujące notesy Jupyter można przekazać do klastra platformy SPARK usługi HDInsight i dostarczyć gotowych przykładów do pracy z interfejsem API Cassandra usługi Azure Cosmos DB. Pamiętaj, aby przejrzeć `1.0-ReadMe.ipynb` pierwszy notes, aby przejrzeć konfigurację usługi Spark do łączenia się z interfejsem API Cassandra usługi Azure Cosmos DB Cassandra.

Pobierz te notesy w obszarze [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) do komputera.
  
### <a name="how-to-upload"></a>Jak przesłać:
Po uruchomieniu Jupyter przejdź do Scali. Najpierw utwórz katalog, a następnie przekaż notesy do katalogu. Przycisk przesyłania znajduje się u góry, po prawej stronie.  

### <a name="how-to-run"></a>Jak uruchomić:
Uruchom przez notesy, a każda komórka notesu sekwencyjnie.  Kliknij przycisk uruchom u góry każdego notesu, aby wykonać wszystkie komórki, lub shift +enter dla każdej komórki.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Dostęp za pomocą interfejsu API Cassandra usługi Azure Cosmos DB z programu Spark Scala

W przypadku zautomatyzowanych procesów w produkcji programy Spark są przesyłane do klastra za pośrednictwem [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Następne kroki

* [Jak zbudować program Spark Scala w IDE i przesłać go do klastra HDInsight Spark za pośrednictwem Livy do wykonania](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Jak połączyć się z interfejsem API Cassandra usługi Azure Cosmos DB z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Pełna lista przykładów kodu do pracy z api Cassandra](cassandra-spark-generic.md)
