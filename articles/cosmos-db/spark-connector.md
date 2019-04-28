---
title: Platforma Apache Spark nawiązać połączenie z usługi Azure Cosmos DB
description: Dowiedz się więcej na temat łącznika usługi Azure Cosmos DB Spark, która umożliwia łączenie platformy Apache Spark w usłudze Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: ramkris
ms.openlocfilehash: 60afd8128224050d456699e798d814a259e106ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330250"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Przyspiesz analizy danych big data przy użyciu platformy Apache Spark do łącznika usługi Azure Cosmos DB

Możesz uruchomić [Spark](https://spark.apache.org/) zadania z danymi przechowywanymi w usłudze Azure Cosmos DB przy użyciu łącznika usługi Cosmos DB Spark. Cosmos może służyć do usługi batch i przetwarzania strumienia i jako warstwy obsługi na dostępie z małymi opóźnieniami.

Można użyć łącznika z [usługi Azure Databricks](https://azure.microsoft.com/services/databricks) lub [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), które zawierają zarządzane klastry Spark na platformie Azure. W poniższej tabeli przedstawiono obsługiwane platformy Spark w wersji.

| Składnik | Wersja |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x i 2.1.x |
| Scala | 2.11 |
| Wersja środowiska uruchomieniowego usługi Azure Databricks | > 3.4 |

> [!WARNING]
> Ten łącznik obsługuje podstawowe (SQL) interfejsu API usługi Azure Cosmos DB.
> W przypadku usługi Cosmos DB dla interfejsu API usługi MongoDB, użyj [łącznika usługi Spark bazy danych MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Cosmos DB Cassandra API, można użyć [łącznika usługi Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Szybki start

* Wykonaj kroki opisane w temacie [wprowadzenie do zestawu SDK Java](sql-api-async-java-get-started.md) do konfigurowania konta usługi Cosmos DB, a następnie wprowadź niektóre dane.
* Wykonaj kroki opisane w temacie [wprowadzenie w usłudze Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) do skonfigurowania obszaru roboczego usługi Azure Databricks oraz klaster.
* Możesz teraz utworzyć nowych w notesach i zaimportować Biblioteka łącznika usługi Cosmos DB. Przejdź do [Praca z łącznikiem usługi Cosmos DB](#bk_working_with_connector) szczegółowe informacje dotyczące sposobu konfigurowania obszaru roboczego.
* Poniższa sekcja ma fragmenty jak odczytywanie i zapisywanie za pomocą łącznika.

### <a name="reading-from-cosmos-db"></a>Odczyt z usługi Cosmos DB

Poniższy fragment kodu przedstawia sposób tworzenia Spark DataFrame do odczytu z usługi Cosmos DB w PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

I tym samym fragmencie kodu w języku Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="writing-to-cosmos-db"></a>Zapisywanie do usługi Cosmos DB

Poniższy fragment kodu przedstawia sposób zapisania ramki danych do usługi Cosmos DB w PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

I tym samym fragmencie kodu w języku Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Więcej fragmentów kodu i kompletne przykłady, zobacz [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Praca z łącznikiem usługi

Tworzenie łącznika ze źródła w usłudze Github lub pobrać plikach JAR uber z narzędzia Maven, poniższe linki.

| platforma Spark | Scala | Najnowsza wersja |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Za pomocą elementów Databricks notebook

Utworzyć bibliotekę przy użyciu obszaru roboczego usługi Databricks, postępując zgodnie ze wskazówkami w podręczniku usługi Azure Databricks > [korzystania z łącznika usługi Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Uwaga: **używać łącznika usługi Azure Cosmos DB Spark** obecnie strona nie jest aktualny. Zamiast pobierania sześć oddzielnych plikach JAR do sześciu różnych bibliotek, możesz pobrać plik jar uber z narzędzia maven w https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) i zainstalować ten plik jar/bibliotekami.
> 

### <a name="using-spark-cli"></a>Za pomocą platformy spark — interfejs wiersza polecenia

Do pracy z łącznika przy użyciu platformy spark — interfejs wiersza polecenia (czyli `spark-shell`, `pyspark`, `spark-submit`), możesz użyć `--packages` parametru z łącznikiem usługi [współrzędnych maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Za pomocą notesów programu Jupyter

Jeśli używasz notesów programu Jupyter w HDInsight, możesz użyć spark magic `%%configure` komórkę, aby określić współrzędne maven łącznika.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Należy pamiętać, włączenie `spark.jars.excludes` dotyczy Usuń potencjalnych konfliktów między łącznika, Apache Spark i usługi Livy.

### <a name="build-the-connector"></a>Tworzenie łącznika

Obecnie ten projekt łącznik używa `maven` tak, aby skompilować bez zależności, można uruchomić:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Praca z naszych przykładów

[Cosmos DB platformy Spark w usłudze GitHub, repozytorium](https://github.com/Azure/azure-cosmosdb-spark) ma następujące przykładowe notesów i skrypty, które możesz wypróbować.

* **Wydajność lotu na czas za pomocą platformy Spark i Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Platforma Spark nawiązać połączenie Cosmos DB przy użyciu usługa notesu HDInsight Jupyter, aby zaprezentować Spark SQL, GraphFrames i prognozowanie opóźnień lotów przy użyciu potoków uczenia Maszynowego.
* **[Łączenie z platformy Spark za pomocą zestawienia zmian usługi DB Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Szybkie pokazy dotyczące sposobu nawiązywania połączenia platformy Spark do kanału informacyjnego zmian bazy danych Cosmos.
* **Źródło przy użyciu platformy Apache Spark i Azure Cosmos DB kanału informacyjnego zmian w usłudze Twitter**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Przy użyciu platformy Apache Spark do zapytania Cosmos DB wykresy**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Łączenie usługi Azure Databricks z usługi Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  przy użyciu `azure-cosmosdb-spark`.  Połączone w tym miejscu jest również wersja usługi Azure Databricks [notesu lotu punktualności lotów](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architektury lambda przy użyciu usługi Azure Cosmos DB i HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Można zmniejszyć nakłady operacyjne związane z obsługą potoków danych big Data za pomocą usługi Cosmos DB i platformy Spark.

## <a name="more-information"></a>Więcej informacji

Mamy więcej informacji w `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) w tym:

* [Podręcznik użytkownika łącznik Spark dla usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Przykłady agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguracja i instalacja

* [Konfiguracja łącznika Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Platforma Spark umożliwia instalację łącznika usługi Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (w trakcie wykonywania)
* [Konfigurowanie dodatku Power Query Power BI bezpośrednio do usługi Azure Cosmos DB przy użyciu platformy Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Za pomocą Cosmos DB agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Znane problemy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Wydajność

* [Porady dotyczące wydajności](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Przebiegi testowe zapytanie](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Pisanie testów](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Zestawienie zmian

* [Stream przetwarzania zmian przy użyciu usługi Azure Cosmos DB kanału informacyjnego zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Pokazy kanału informacyjnego zmian](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Pokazy Stream ze strukturą](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorowanie

* [Monitorowanie zadań usługi Spark w usłudze application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze nie, Pobierz platformy Spark do łącznika usługi Azure Cosmos DB z [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) repozytorium GitHub. Zapoznaj się z następujących zasobach dodatkowych: w repozytorium:

* [Przykłady agregacji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesów](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Możesz również przejrzeć [Apache Spark SQL, przewodnik zestawy danych i elementy Dataframe](https://spark.apache.org/docs/latest/sql-programming-guide.html)i [platformy Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
