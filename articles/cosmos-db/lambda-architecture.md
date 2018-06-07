---
title: Architektura lambda z bazy danych Azure rozwiązania Cosmos i HDInsight (Apache Spark) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób implementacja architektury lambda przy użyciu bazy danych Azure rozwiązania Cosmos, HDInsight i Spark
keywords: lambda-architecture
services: cosmos-db
author: dennyglee
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: a8cdee6e8e7c338be278449a31426824f6e805d6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611861"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure DB rozwiązania Cosmos: Implementacja architektury lambda na platformie Azure 

Lambda architektury Włącz wydajność przetwarzania danych duże zbiory danych. Lambda architektury użyć przetwarzania wsadowego, przetwarzania strumienia i warstwy obsługi aby zminimalizować opóźnienie objętego zapytań danych big data. 

Aby zaimplementować to architektura lambda na platformie Azure, można połączyć następujące technologie w celu przyspieszenia analizy w czasie rzeczywistym danych big data:
* [Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/), branży pierwszej usługi globalnie rozproszone i wiele modeli bazy danych. 
* [Platforma Apache Spark w usłudze Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), ramy przetwarzania, które są uruchomione aplikacje analizy danych na dużą skalę
* Azure DB rozwiązania Cosmos [zmienić źródło](change-feed.md), które strumieni nowych danych do warstwy partii dla usługi HDInsight do przetworzenia
* [Spark do łącznika usługi Azure rozwiązania Cosmos bazy danych](spark-connector.md)

W tym artykule opisano podstawy architektury lambda, na podstawie oryginalnego projektu wielowarstwową i zalety architektury lambda "rearchitected", który upraszcza czynności.  

Omówienie architektury lambda i zasoby dostępne w przykładowym architektura lambda Obejrzyj następujące:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Co to jest to architektura lambda?
Architektura lambda jest rodzajowy, skalowalnych i przetwarzania architektura adres odpornej na uszkodzenia danych partii i szybkości opóźnienia scenariuszy, w sposób opisany w [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram przedstawiający architektura lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Źródło: http://lambda-architecture.net/

Podstawowe zasady architektura lambda są opisane na powyższym diagramie zgodnie [ https://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Wszystkie **danych** spoczywa na *zarówno* *warstwy partii* i *warstwy szybkości*.
 2. **Warstwy partii** ma głównego zestawu danych (niezmienne i tylko Dołącz zestaw danych pierwotnych) i oblicza wstępnie widoków partii.
 3. **Warstwy obsługi** widokami partii szybkiego zapytań. 
 4. **Warstwy szybkości** kompensuje czas przetwarzania (do warstwy usług) i dotyczy tylko najnowsze dane.
 5. Wszystkie zapytania można odpowiedzi przez wyniki scalania z widoków partii i widoków w czasie rzeczywistym lub ping je pojedynczo.

Na dalsze informacje, firma Microsoft będzie wdrożenia tej architektury przy użyciu tylko następujące elementy:

* Azure DB rozwiązania Cosmos kolekcji
* Klaster usługi HDInsight (Apache Spark 2.1)
* Platforma Spark jest łącznik [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Szybkość warstwy

Z perspektywy operacji obsługi dwóch strumieni danych przy jednoczesnym zapewnieniu faktyczny stan danych może być skomplikowane pozwala. Upraszczają operacje, należy skorzystać [zmiany bazy danych Azure rozwiązania Cosmos źródła pomocy technicznej](change-feed.md) Aby zachować stan *warstwy partii* podczas ujawniania dziennik zmian bazy danych rozwiązania Cosmos Azure za pośrednictwem *zmienić źródła danych interfejsu API* dla Twojego *warstwy szybkości*.  
![Diagram Wyróżnianie nowych danych, szybkość warstwy i główny zestaw danych część architektura lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co to jest ważne w tych warstwach:

 1. Wszystkie **danych** spoczywa *tylko* do bazy danych rozwiązania Cosmos platformy Azure, w związku z tym można uniknąć problemów z wieloma rzutowania.
 2. **Warstwy partii** ma głównego zestawu danych (niezmienne i tylko Dołącz zestaw danych pierwotnych) i oblicza wstępnie widoków partii.
 3. **Warstwy obsługi** została szczegółowo opisana w następnej sekcji.
 4. **Warstwy szybkości** korzysta z usługi HDInsight (Apache Spark) do odczytu źródła zmiany bazy danych Azure rozwiązania Cosmos. Dzięki temu można utrwalić danych oraz do wykonywania zapytań i jednocześnie go przetworzyć.
 5. Wszystkie zapytania można odpowiedzi przez wyniki scalania z widoków partii i widoków w czasie rzeczywistym lub ping je pojedynczo.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Przykład kodu: Spark strukturę przesyłania strumieniowego do zmiany bazy danych Azure rozwiązania Cosmos źródła danych
Do uruchamiania szybkiego prototypu zmiany bazy danych Azure rozwiązania Cosmos źródła danych jako część **warstwy szybkości**, można przetestować go przy użyciu danych Twitter jako część [strumienia przetwarzania zmian przy użyciu usługi Azure rozwiązania Cosmos DB zmienić źródła danych i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)przykład. Aby szybko rozpocząć danych wyjściowych Twitter, zobacz przykładowy kod w [strumienia źródła danych z serwisem Twitter DB rozwiązania Cosmos](https://github.com/tknandu/TwitterCosmosDBFeed). Z poprzednim przykładzie ładowania Twitter danych do bazy danych Azure rozwiązania Cosmos, a następnie służą do tworzenia klastra usługi HDInsight (Apache Spark) do nawiązania połączenia zmiany źródła danych. Aby uzyskać więcej informacji na temat tej konfiguracji, zobacz [Apache Spark do Instalatora łącznika DB rozwiązania Cosmos Azure](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Poniższy fragment kodu przedstawia sposób konfigurowania `spark-shell` do uruchomienia strukturalnych zadanie przesyłania strumieniowego do nawiązania połączenia zmiany bazy danych Azure rozwiązania Cosmos źródła danych, który monitoruje w czasie rzeczywistym strumienia danych Twitter, aby wykonać aktualną liczbę interwał.

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

Przykłady kompletny kod dla [azure-cosmosdb-spark/lambda/przykłady](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), takie jak:
* [Zapytanie przesyłania strumieniowego z Feed.scala zmiany DB rozwiązania Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Przesyłania strumieniowego kwerendy tagów z Feed.scala zmiany DB rozwiązania Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Dane wyjściowe tego `spark-shell` konsoli, co jest wykonywane nieprzerwanie strukturalnych zadanie przesyłania strumieniowego, które wykonuje liczba interwale z danymi usługi Twitter z zmiany bazy danych Azure rozwiązania Cosmos źródła danych. Na poniższej ilustracji przedstawiono dane wyjściowe zadania strumienia i interwał liczby.

![Przesyłanie strumieniowe danych wyjściowych przedstawiający liczba interwale danych Twitter ze źródła strumieniowego zmiany bazy danych Azure rozwiązania Cosmos](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Więcej informacji dotyczących bazy danych Azure rozwiązania Cosmos zmian źródła danych, zobacz:

* [Praca z zmiany źródła pomocy technicznej w usłudze Azure DB rozwiązania Cosmos](change-feed.md)
* [Introducing Azure zmiany CosmosDB źródła danych biblioteki procesora](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Strumienia przetwarzania zmian: Źródła danych zmian Azure CosmosDB + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Partii i obsługująca warstwy
Ponieważ nowych danych jest ładowany do rozwiązania Cosmos Azure DB (gdzie zmiany źródła danych jest używane dla warstwy szybkości), to jest, gdy **głównego zestawu danych** znajduje się (niezmienne i tylko Dołącz zestaw danych pierwotnych). Z tego punktu i jego nowszych wersjach, używać HDInsight (Apache Spark) do wykonywania zadań wstępnych obliczeń z **warstwy partii** do **warstwy obsługi**, jak pokazano na poniższej ilustracji:

![Diagram wyróżnianie partii i warstwy obsługi architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co to jest ważne w tych warstwach:

 1. Wszystkie **danych** zostanie przypisany tylko do bazy danych Azure rozwiązania Cosmos (Aby uniknąć problemów z multiemisji).
 2. **Warstwy partii** ma głównego zestawu danych (niezmienne i tylko Dołącz zestaw danych pierwotnych) przechowywanych w usłudze Azure DB rozwiązania Cosmos. Przy użyciu HDI Spark, można wstępnie obliczeniowe z agregacji mają być przechowywane w widoków obliczona partii.
 3. **Warstwy obsługi** znajduje się baza danych bazy danych Azure rozwiązania Cosmos z kolekcjami do głównego zestawu danych i obliczonych widoku partii.
 4. **Warstwy szybkości** omówione w dalszej części tego artykułu.
 5. Wszystkie zapytania można odpowiedzi przez scalanie wyników z widoków partii i w czasie rzeczywistym lub ping je pojedynczo.

### <a name="code-example-pre-computing-batch-views"></a>Przykład kodu: wstępnie obliczanie partii widoków
Aby pokazują, jak wykonać wstępnie obliczeniowej widoków z **głównego zestawu danych** Apache Spark do bazy danych rozwiązania Cosmos Azure, użyj poniższej wstawki kodu z notesów [Lambda architektura Rearchitected - warstwy partii ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) i [Rearchitected architektura Lambda - partii obsługująca warstwy](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). W tym scenariuszu należy użyć danych Twitter przechowywanych w usłudze Azure DB rozwiązania Cosmos.

Zacznijmy od utworzenia konfiguracji połączenia danych Twitter w ramach bazy danych rozwiązania Cosmos Azure przy użyciu kodu PySpark poniżej.

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

Następnie umożliwia Uruchom następującą instrukcję Spark SQL ustalenie hashtagów 10 pierwszych zestawu tweetów. Dla tego zapytania Spark SQL prowadzimy to notesu Jupyter bez na wykresie słupkowym dane wyjściowe bezpośrednio po następujący fragment kodu.

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

![Wykres przedstawiający liczbę tweetów na hasztagiem](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teraz, gdy masz kwerendy, Zapisz dokument do kolekcji przy użyciu łącznika programu Spark można zapisać danych wyjściowych do innej kolekcji.  W tym przykładzie należy użyć Scala prezentować połączenia. Podobny do poprzedniego przykładu, Utwórz połączenie konfiguracji można zapisać DataFrame Apache Spark w innej kolekcji bazy danych Azure rozwiązania Cosmos.

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

Po określeniu `SaveMode` (wskazująca, czy `Overwrite` lub `Append` dokumenty), tworzenie `tweets_bytags` DataFrame podobne do zapytań Spark SQL w poprzednim przykładzie.  Z `tweets_bytags` DataFrame utworzony, możesz zapisać go za pomocą `write` metody za pomocą wcześniej określonego `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Teraz ostatniej instrukcji zapisał DataFrame Twojego Spark do nowej kolekcji z bazy danych Azure rozwiązania Cosmos; z punktu widzenia architektura lambda jest to Twoje **partii widoku** w **warstwy obsługi**.
 
#### <a name="resources"></a>Zasoby

Przykłady kompletny kod dla [azure-cosmosdb-spark/lambda/przykłady](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) w tym:
* Lambda architektura Rearchitected - warstwy partii [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda architektura Rearchitected - partii do warstwy obsługująca [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Szybkość warstwy
Wcześniej dostrzeżone za pomocą biblioteki Azure rozwiązania Cosmos DB zmiany źródła danych umożliwia upraszczają operacje między warstwami partii i szybkości. W ramach tej architektury Apache Spark (za pomocą usługi HDInsight) należy używać do wykonywania *strukturę przesyłania strumieniowego* zapytań dotyczących danych. Można również do tymczasowego utrzymania wyników strukturalnych zapytań przesyłania strumieniowego więc innych systemów można uzyskać dostęp do tych danych.

![Diagram wyróżnianie szybkości warstwy architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

W tym celu utwórz kolekcję bazy danych Azure rozwiązania Cosmos oddzielne można zapisać wyników strukturalnych zapytań strumieniowych.  Dzięki temu można korzystać z innych systemów te informacje nie tylko Apache Spark. Również funkcją rozwiązania Cosmos DB czas wygaśnięcia (TTL), można skonfigurować dokumentów, aby automatycznie usunięte po ustaw czas trwania.  Aby uzyskać więcej informacji o funkcji Azure rozwiązania Cosmos DB TTL, zobacz [wygaśnie danych w kolekcjach bazy danych rozwiązania Cosmos Azure automatycznie z czasu wygaśnięcia](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Architektura lambda: Rearchitected
Zgodnie z opisem w poprzedniej sekcji, można uprościć oryginalnego architektura lambda za pomocą następujących składników:
* Azure Cosmos DB
* Biblioteki usługi Azure rozwiązania Cosmos DB zmiany źródła Aby uniknąć konieczności obsługą multiemisji danych między warstwami partii i szybkość
* Platforma Apache Spark w usłudze HDInsight
* Łącznik programu Spark dla rozwiązania Cosmos Azure DB

![Diagram przedstawiający rearchitecture architektury lambda przy użyciu bazy danych Azure rozwiązania Cosmos, Spark i rozwiązania Cosmos DB zmiany źródła danych interfejsu API Azure](./media/lambda-architecture/lambda-architecture-re-architected.png)

Ten projekt musisz tylko dwóch zarządzanych usług Azure DB rozwiązania Cosmos i HDInsight. Razem odnoszą się do partii, obsługi i szybkość warstwy architektury lambda. Upraszcza to nie tylko działania, ale także przepływu danych. 
 1. Wszystkie dane zostanie przypisany do bazy danych Azure rozwiązania Cosmos do przetworzenia
 2. Warstwy usługi partia zadań ma głównego zestawu danych (niezmienne i tylko Dołącz zestaw danych pierwotnych) i wstępnie oblicza widoków partii
 3. Warstwa obsługi ma partii widoki danych szybkiego zapytań.
 4. Warstwa szybkości kompensuje czas przetwarzania (do warstwy usług) i dotyczy tylko najnowsze dane.
 5. Wszystkie zapytania można odpowiedzi przez scalenie wyników z widoków partii i w czasie rzeczywistym.

### <a name="resources"></a>Zasoby

 * **Nowe dane**: [strumienia strumieniowe źródło z serwisem Twitter CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), która jest mechanizm przekazywania nowych danych do bazy danych Azure rozwiązania Cosmos.
 * **Warstwa partii:** warstwy partii składa się z *główny zestaw danych* (niezmienne i tylko Dołącz zestaw danych pierwotnych) oraz możliwość wstępnie obliczanie partii widoków danych, które są przenoszone do **warstwy usług** .
    * **Rearchitected architektura Lambda - warstwy partii** notesu [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) zapytania *główny zestaw danych* zestaw widoków partii.
 * **Warstwa obsługi:** **warstwy obsługi** składa się z wstępnie obliczone dane wynikowe w widokach partii (na przykład agregacji, określonych fragmentatory itp.) dla szybkiego zapytań.
    * **Rearchitected architektura Lambda - partii do warstwy obsługująca** notesu [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) wypychanie partii danych do warstwy obsługi; oznacza to, Spark wysyła zapytanie do kolekcji partii tweetów, procesy i zapisze go w innej kolekcji (obliczona partii).
* **Szybkość warstwy:** **warstwy szybkości** składa się z Spark przy użyciu zmian bazy danych Azure rozwiązania Cosmos źródła danych do odczytu i działają na natychmiast. Można także zapisać dane *obliczana RT* tak, aby inne systemy można zbadać przetworzone dane w czasie rzeczywistym w przeciwieństwie do uruchomienia w czasie rzeczywistym zapytania samodzielnie.
    * [Przesyłania strumieniowego zapytania z rozwiązania Cosmos zmienić źródła danych DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) skryptu języka scala wykonuje zapytanie przesyłania strumieniowego ze źródła danych do obliczenia liczby interwał, z poziomu powłoki spark zmiany bazy danych Azure rozwiązania Cosmos.
    * [Przesyłania strumieniowego kwerendy tagów z rozwiązania Cosmos zmienić źródła danych bazy danych](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) skryptu języka scala wykonuje zapytanie przesyłania strumieniowego ze źródła danych do obliczenia interwał liczba tagów z powłoki spark zmiany bazy danych Azure rozwiązania Cosmos.
  
## <a name="next-steps"></a>Kolejne kroki
Jeśli nie jest jeszcze Pobierz Spark do bazy danych Azure rozwiązania Cosmos łącznika z [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) repozytorium GitHub i przejrzyj dodatkowe zasoby w repozytorium:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Przykłady rozproszonej agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notebooki](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturalne pokazy przesyłania strumieniowego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Zmiana źródła pokazy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strumień przetwarzania zmian za pomocą usługi Azure rozwiązania Cosmos DB zmiany źródła danych i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Można także przejrzeć [Apache Spark SQL, przewodnik zestawów danych i DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) i [Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
