---
title: Dostęp do usługi Azure Cosmos DB Cassandra API z platformy Spark w ramach platformy YARN z HDInsight
description: W tym artykule opisano sposób pracy z interfejsem API Cassandra DB Cosmos Azure z platformy Spark w ramach platformy YARN z HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60893706"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Dostęp do usługi Azure Cosmos DB Cassandra API z platformy Spark w ramach platformy YARN z HDInsight

W tym artykule opisano, jak uzyskać dostęp do interfejsu API usługi Azure Cosmos DB Cassandra z platformy Spark w ramach platformy YARN z platformą Spark HDInsight z powłoki aparatu spark. HDInsight to firmy Microsoft Hortonworks Hadoop PaaS platformy Azure, która korzysta z magazynu obiektów dla systemu plików HDFS i jest dostępna w tym kilka odmian systemu [Spark](../hdinsight/spark/apache-spark-overview.md).  Zawartość w tym dokumencie odwołań HDInsight Spark, ma zastosowanie do wszystkich dystrybucjach Hadoop.  

## <a name="prerequisites"></a>Wymagania wstępne

* [Aprowizowanie interfejsu API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Przejrzyj podstawowe informacje dotyczące nawiązywania połączenia z interfejsem API usługi Azure Cosmos DB Cassandra](cassandra-spark-generic.md)

* [Ustanawianie klastra HDInsight Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Przejrzyj przykłady kodu do pracy z interfejsem API bazy danych Cassandra](cassandra-spark-generic.md#next-steps)

* [Używać cqlsh do sprawdzania poprawności, jeśli więc chcesz](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfiguracja interfejsu API rozwiązania Cassandra w Spark2** — łącznik platformy Spark dla bazy danych Cassandra wymaga, że połączenia bazy danych Cassandra szczegóły, aby być inicjowane jako część kontekstu aparatu Spark. Po uruchomieniu notesu programu Jupyter, sesja platformy spark i kontekst już zainicjowany i nie jest zalecane, aby zatrzymać i ponownie zainicjować kontekstu aparatu Spark, chyba że jest to wraz z każdej konfiguracji Ustaw jako część uruchamiania notesu programu Jupyter domyślne HDInsight. Jeden obejściem jest dodać szczegóły wystąpienia bazy danych Cassandra do Ambari, Spark2 bezpośrednio konfiguracji usługi. Jest to jednorazowa działania dla klastra, która wymaga ponownego uruchomienia tej usługi Spark2.
 
  1. Przejdź do narzędzia Ambari, usługa Spark2 i wybierz konfiguracje

  2. Następnie przejdź do niestandardowych spark2 wartości domyślne i dodać nową właściwość następującym kodem i uruchom ponownie usługę Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Dostęp do usługi Azure Cassandra interfejsu API Cosmos DB z poziomu powłoki Spark

Powłoki usługi Spark jest używana do celów testowania eksploracji.

* Uruchamianie powłoki aparatu spark za pomocą zależności wymagane narzędzia maven, zgodny z wersją platformy Spark w klastrze.

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Dostęp do usługi Azure Cassandra interfejsu API Cosmos DB z notesów programu Jupyter

HDInsight — Spark jest dostarczana z usługami Notes Zeppelin i Jupyter. Są one obu środowiskach opartych na sieci web Notes, obsługujące Scala i Python. Notesy doskonale nadają się do interaktywnego analizowania poznawcze i współpracy, ale nie jest przeznaczone do procesów operacyjnych/productionized.

Następujące notesów Jupyter mogła być przekazana do klastra platformy HDInsight Spark i zapewnia przykłady gotowe do pracy z interfejsem API usługi Azure Cosmos DB Cassandra. Należy przejrzeć pierwszego notesu `1.0-ReadMe.ipynb` przejrzeć konfigurację usługi Spark do łączenia się z interfejsem API usługi Azure Cosmos DB Cassandra.

Pobierz te notesy w obszarze [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) na komputer.
  
### <a name="how-to-upload"></a>Sposób przekazywania:
Po uruchomieniu programu Jupyter, przejdź do Scala. Najpierw utwórz katalog, a następnie przekaż notesów w katalogu. Przycisk Przekaż znajduje się na górze, po prawej stronie.  

### <a name="how-to-run"></a>Jak uruchomić:
Uruchom za pomocą notesów, a każda komórka notesu sekwencyjnie.  Kliknij przycisk uruchamiania w górnej części każdego Notes, aby wykonać wszystkie komórki lub shift + enter w każdej komórce.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Dostęp za pomocą interfejsu Cassandra API usługi Azure Cosmos DB z programu Spark Scala

W przypadku zautomatyzowanych procesów w środowisku produkcyjnym programów platformy Spark są przesyłane do klastra za pomocą [skryptu spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Kolejne kroki

* [Jak tworzyć Spark Scala program w środowisku IDE i przesłać ją do klastra platformy HDInsight Spark za pomocą usługi Livy do wykonania](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Jak połączyć się z interfejsem API usługi Azure Cosmos DB Cassandra z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Pełna lista przykłady kodu dla pracy z interfejsem API bazy danych Cassandra](cassandra-spark-generic.md)
