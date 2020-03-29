---
title: Architektura Lambda z usługą Azure Cosmos DB i Apache Spark
description: W tym artykule opisano sposób implementowania architektury lambda przy użyciu usługi Azure Cosmos DB, HDInsight i Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719742"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Usługa Azure Cosmos DB: Implementowanie architektury lambda na platformie Azure 

Architektury Lambda umożliwiają wydajne przetwarzanie danych z ogromnych zestawów danych. Architektury Lambda używają przetwarzania wsadowego, przetwarzania strumienia i warstwy obsługującej, aby zminimalizować opóźnienia związane z wykonywaniem zapytań o duże zbiory danych. 

Aby zaimplementować architekturę lambda na platformie Azure, można połączyć następujące technologie, aby przyspieszyć analizę dużych zbiorów danych w czasie rzeczywistym:
* [Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)— pierwsza w branży usługa bazy danych rozproszonych globalnie i wielomodelowych. 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)— struktura przetwarzania, która uruchamia aplikacje do analizy danych na dużą skalę
* [Kanał informacyjny zmiany](change-feed.md)usługi Azure Cosmos DB , który przesyła nowe dane do warstwy wsadowej, aby usługa HDInsight przetwarzała
* Łącznik [usługi Spark to Azure Cosmos DB](spark-connector.md)

W tym artykule opisano podstawy architektury lambda na podstawie oryginalnego projektu wielowarstwowego i korzyści z "rearchitected" architektury lambda, która upraszcza operacje.  

## <a name="what-is-a-lambda-architecture"></a>Co to jest architektura lambda?
Architektura lambda jest ogólną, skalowalną i odporną na uszkodzenia architekturą przetwarzania danych, aby rozwiązać scenariusze opóźnienia partii i szybkości, zgodnie z opisem [Nathana Marza.](https://twitter.com/nathanmarz)

![Diagram przedstawiający architekturę lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Źródło: http://lambda-architecture.net/

Podstawowe zasady architektury lambda są opisane na poprzednim diagramie [http://lambda-architecture.net](http://lambda-architecture.net/)zgodnie z .

 1. Wszystkie **dane** są wypychane *zarówno* do *warstwy wsadowej,* jak i *warstwy szybkości*.
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny, tylko do dołączanie zestaw nieprzetworzonych danych) i wstępnie oblicza widoki partii.
 3. **Warstwa obsługujących** ma widoki partii dla szybkich zapytań. 
 4. **Warstwa szybkości** kompensuje czas przetwarzania (do warstwy służącej) i dotyczy tylko najnowszych danych.
 5. Na wszystkie zapytania można odpowiedzieć, scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym lub pingując je indywidualnie.

Po dalszym przeczytaniu, będziemy mogli zaimplementować tę architekturę przy użyciu tylko następujące:

* Kontenery usługi Azure Cosmos
* Klaster HDInsight (Apache Spark 2.1)
* Złącze [Iskiery 1.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Warstwa prędkości

Z punktu widzenia operacji utrzymywanie dwóch strumieni danych przy jednoczesnym zapewnieniu prawidłowego stanu danych może być skomplikowanym przedsięwzięciem. Aby uprościć operacje, należy wykorzystać [obsługę kanału informacyjnego usługi Azure Cosmos DB,](change-feed.md) aby zachować stan *warstwy wsadowej,* jednocześnie ujawniając dziennik zmian usługi Azure Cosmos DB za pośrednictwem *interfejsu API źródła danych zmian* dla *warstwy szybkości.*  
![Diagram wyróżniający nową część danych, warstwy szybkości i głównego zestawu danych architektury lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co jest ważne w tych warstwach:

 1. Wszystkie **dane** są wypychane *tylko* do usługi Azure Cosmos DB, dzięki czemu można uniknąć wielu problemów rzutowania.
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny, tylko do dołączanie zestaw nieprzetworzonych danych) i wstępnie oblicza widoki partii.
 3. **Warstwa porcji** jest omówiona w następnej sekcji.
 4. **Warstwa szybkości** wykorzystuje hdinsight (Apache Spark) do odczytu kanału informacyjnego zmiany usługi Azure Cosmos DB. Dzięki temu można utrwalić dane, a także do zapytań i przetwarzania go jednocześnie.
 5. Na wszystkie zapytania można odpowiedzieć, scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym lub pingując je indywidualnie.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Przykład kodu: przesyłanie strumieniowe ze strukturą platformy Spark do kanału informacyjnego usługi Azure Cosmos DB
Aby uruchomić szybki prototyp kanału informacyjnego usługi Azure Cosmos DB jako część **warstwy szybkości,** można przetestować go przy użyciu danych Twitter w ramach [zmiany przetwarzania strumienia przy użyciu usługi Azure Cosmos DB Change Feed i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) przykład. Aby szybko uruchomić dane wyjściowe twittera, zobacz przykładowy kod w [kanale Strumieniowo z Twittera do Usługi Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). W poprzednim przykładzie ładujesz dane Twittera do usługi Azure Cosmos DB, a następnie możesz skonfigurować klaster usługi HDInsight (Apache Spark), aby połączyć się z kanałem informacyjnym zmian. Aby uzyskać więcej informacji na temat konfigurowania tej konfiguracji, zobacz [Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Poniższy fragment kodu pokazuje, jak `spark-shell` skonfigurować do uruchamiania zadania przesyłania strumieniowego strukturalnego, aby połączyć się z kanałem informacyjnym zmiany usługi Azure Cosmos DB, który przegląda strumień danych twitter w czasie rzeczywistym, aby wykonać liczbę interwałów uruchomionych.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Aby uzyskać pełne przykłady kodu, zobacz [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), w tym:
* [Przesyłanie strumieniowe zapytania z pliku danych usługi Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Zapytanie o znaczniki przesyłania strumieniowego z pliku danych o zmianie usługi Cosmos DB.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Dane wyjściowe tego `spark-shell` jest konsola, która stale uruchamia zadanie przesyłania strumieniowego strukturalnego, który wykonuje interwał liczyć dane Twitter z kanału informacyjnego usługi Azure Cosmos DB zmiany. Na poniższej ilustracji przedstawiono dane wyjściowe zadania strumienia i liczy interwał.

![Przesyłanie strumieniowe danych wyjściowych pokazujących liczbę interwałów względem danych twitterowych z kanału informacyjnego usługi Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Aby uzyskać więcej informacji na temat kanału informacyjnego usługi Azure Cosmos DB, zobacz:

* [Praca z obsługą kanału informacyjnego zmian w usłudze Azure Cosmos DB](change-feed.md)
* [Przedstawiamy bibliotekę procesorów kanału informacyjnego usługi Azure CosmosDB Change](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Zmiany przetwarzania strumienia: kanał informacyjny zmian usługi Azure CosmosDB + platforma Iskrowa Apache](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Warstwy partii i porcji
Ponieważ nowe dane są ładowane do usługi Azure Cosmos DB (gdzie źródło danych zmian jest używany dla warstwy prędkości), jest to, gdzie znajduje się **zestaw danych podstawowych** (niezmienne, tylko do dołączanie zestaw nieprzetworzonych danych). Od tego momentu użyj funkcji HDInsight (Apache Spark), aby wykonać funkcje pre-compute od **warstwy wsadowej** do **warstwy obsługującej,** jak pokazano na poniższej ilustracji:

![Diagram wyróżniający warstwę wsadową i warstwę obsługującą architekturę lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co jest ważne w tych warstwach:

 1. Wszystkie **dane** są wypychane tylko do usługi Azure Cosmos DB (aby uniknąć problemów z wieloma rzutami).
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny, tylko do dołączania zestaw nieprzetworzonych danych) przechowywany w usłudze Azure Cosmos DB. Za pomocą hdi spark można wstępnie obliczyć agregacje, które mają być przechowywane w obliczonych widokach wsadowych.
 3. **Warstwa obsługujących** jest bazą danych usługi Azure Cosmos z kolekcjami dla głównego zestawu danych i obliczonego widoku wsadowego.
 4. **Warstwa prędkości** została omówiona w dalszej części tego artykułu.
 5. Na wszystkie zapytania można odpowiedzieć, scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym lub pingując je indywidualnie.

### <a name="code-example-pre-computing-batch-views"></a>Przykład kodu: Wstępne obliczanie widoków wsadowych
Aby zaprezentować sposób wykonywania wstępnie obliczonych widoków względem **głównego zestawu danych** z programu Apache Spark do usługi Azure Cosmos DB, użyj następujących fragmentów kodu z tabeli architektury [Lambda Rearchitected — Warstwa wsadowa](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) i [Architektura Lambda rearchitected — partia do warstwy obsługującej](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). W tym scenariuszu użyj danych Twitter przechowywanych w usłudze Azure Cosmos DB.

Zacznijmy od utworzenia połączenia konfiguracji z danymi Twitter w usłudze Azure Cosmos DB przy użyciu kodu PySpark poniżej.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Następnie uruchommy następującą instrukcję Spark SQL, aby określić 10 najlepszych hashtagów zestawu tweetów. W przypadku tej kwerendy języka Spark SQL uruchamiamy to w notesie Jupyter bez wykresu słupkowego danych wyjściowych bezpośrednio po tym fragmentie kodu.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Wykres pokazujący liczbę tweetów na hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teraz, gdy masz zapytanie, zapiszmy ją z powrotem do kolekcji za pomocą łącznika Platformy Spark, aby zapisać dane wyjściowe w innej kolekcji.  W tym przykładzie użyj Scala, aby zaprezentować połączenie. Podobnie jak w poprzednim przykładzie, utwórz połączenie konfiguracji, aby zapisać Apache Spark DataFrame w innym kontenerze usługi Azure Cosmos.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Po `SaveMode` określeniu (wskazując, `Overwrite` `Append` czy do `tweets_bytags` lub dokumentów), należy utworzyć DataFrame podobne do spark zapytania SQL w poprzednim przykładzie.  Po `tweets_bytags` utworzeniu elementu DataFrame można `write` go zapisać przy `writeConfig`użyciu metody przy użyciu wcześniej określonego programu .

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Ta ostatnia instrukcja ma teraz zapisane Spark DataFrame w nowym kontenerze usługi Azure Cosmos; z punktu widzenia architektury lambda, jest to **widok partii** w **warstwie porcji**.
 
#### <a name="resources"></a>Resources

Aby uzyskać pełne przykłady kodu, zobacz [azure-cosmosdb-spark/lambda/samples,](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) w tym:
* Lambda Architecture Rearchitected - Warstwa wsadowa [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected - Partia do obsługi warstwy [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Warstwa prędkości
Jak wcześniej wspomniano, przy użyciu biblioteki kanału informacyjnego usługi Azure Cosmos DB umożliwia uproszczenie operacji między warstwami partii i szybkości. W tej architekturze należy użyć Apache Spark (za pośrednictwem usługi HDInsight) do wykonywania *zapytań strumieniowych strukturalnych* względem danych. Można również tymczasowo utrwalić wyniki zapytań uporządkowanej przesyłania strumieniowego, aby inne systemy mogły uzyskać dostęp do tych danych.

![Diagram podkreślający warstwę prędkości architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Aby to zrobić, należy utworzyć oddzielny kontener usługi Azure Cosmos, aby zapisać wyniki zapytań uporządkowanego przesyłania strumieniowego.  Dzięki temu można mieć inne systemy dostęp do tych informacji nie tylko Apache Spark. Oprócz funkcji czasu wygaśnięcia usługi Cosmos DB (TIME-to-Live) można skonfigurować dokumenty tak, aby były automatycznie usuwane po upływie ustawionego czasu trwania.  Aby uzyskać więcej informacji na temat funkcji TTL usługi Azure Cosmos DB, zobacz [Wygasanie danych w kontenerach usługi Azure Cosmos automatycznie z czasem wygaśnięcia](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Architektura Lambda: Rearchitected
Jak wspomniano w poprzednich sekcjach, można uprościć oryginalną architekturę lambda przy użyciu następujących składników:
* Azure Cosmos DB
* Biblioteka kanału informacyjnego usługi Azure Cosmos DB Change, aby uniknąć konieczności wielokrotnego rzutowania danych między warstwami partii i szybkości
* Apache Spark na HDInsight
* Łącznik platformy Spark dla usługi Azure Cosmos DB

![Diagram przedstawiający rearchitecture architektury lambda przy użyciu usługi Azure Cosmos DB, Spark i azure cosmos DB Change Feed API](./media/lambda-architecture/lambda-architecture-re-architected.png)

W tym projekcie potrzebujesz tylko dwóch usług zarządzanych, Azure Cosmos DB i HDInsight. Razem odnoszą się do warstw partii, obsługujących i szybkości architektury lambda. Upraszcza to nie tylko operacje, ale także przepływ danych. 
 1. Wszystkie dane są wypychane do usługi Azure Cosmos DB w celu przetworzenia
 2. Warstwa wsadowa ma główny zestaw danych (niezmienny, dołączany tylko zestaw nieprzetworzonych danych) i wstępnie oblicza widoki partii
 3. Warstwa obsługujących ma widoki partii danych dla szybkich zapytań.
 4. Warstwa szybkości kompensuje czas przetwarzania (do warstwy służącej) i dotyczy tylko najnowszych danych.
 5. Na wszystkie zapytania można odpowiedzieć, scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym.

### <a name="resources"></a>Resources

* **Nowe dane:** [Strumień strumieniowy z Twittera do usługi CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), który jest mechanizmem wypychania nowych danych do usługi Azure Cosmos DB.
* **Warstwa wsadowa:** Warstwa wsadowa składa się z *głównego zestawu danych* (niezmiennego, dołączanego zestawu nieprzetworzonych danych) oraz możliwości wstępnego obliczania widoków partii danych wypychanych do **warstwy obsługującej**.
   * **Architektura Lambda rearchitected — notes warstwy wsadowej** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) wysyła zapytanie do głównego *zestawu danych* widoków wsadowych.
* **Warstwa porcji:** **Warstwa służąca** składa się ze wstępnie obliczonych danych, co powoduje wyświetlenia wsadowe (na przykład agregacje, określone fragmentatory itp.) dla szybkich zapytań.
  * **Architektura Lambda Rearchitected — partia do obsługi notesu warstwy** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) wypycha dane partii do warstwy obsługującej; oznacza to, że spark wysyła zapytania do kolekcji wsadowej tweetów, przetwarza ją i przechowuje w innej kolekcji (obliczonej partii).
    * **Warstwa prędkości:** **Warstwa szybkości** składa się z platformy Spark korzystającej z kanału informacyjnego usługi Azure Cosmos DB, aby natychmiast odczytać i działać. Dane można również zapisać w *obliczonym RT,* dzięki czemu inne systemy mogą wysyłać zapytania do przetworzonych danych w czasie rzeczywistym, w przeciwieństwie do samodzielnego uruchamiania zapytania w czasie rzeczywistym.
  * [Kwerenda przesyłania strumieniowego z programu Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala script wykonuje kwerendę przesyłania strumieniowego z kanału informacyjnego zmiany usługi Azure Cosmos DB w celu obliczenia liczby interwałów z powłoki iskrowej.
  * [Kwerenda tagów przesyłania strumieniowego z programu Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala script wykonuje kwerendę przesyłania strumieniowego z kanału informacyjnego usługi Azure Cosmos DB, aby obliczyć liczbę interwałów tagów z powłoki iskrowej.
  
## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiłeś, pobierz łącznik platformy Spark do usługi Azure Cosmos DB z repozytorium GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) i eksploruj dodatkowe zasoby w repozytorium:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Przykłady agregacji rozproszonych](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesy](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Ustrukturyzowane pokazy przesyłania strumieniowego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Zmienianie wersji demonstracyjnych kanału informacyjnego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Przesyłanie strumieniowe przetwarzania zmian przy użyciu kanału informacyjnego Usługi Azure Cosmos DB Change Feed i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Można również przejrzeć [Apache Spark SQL, DataFrames i zestawy danych przewodnik](https://spark.apache.org/docs/latest/sql-programming-guide.html) i [Apache Spark na platformie Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
