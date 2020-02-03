---
title: Architektura lambda z Azure Cosmos DB i Apache Spark
description: W tym artykule opisano sposób implementacji architektury lambda przy użyciu usługi Azure Cosmos DB, HDInsight i platformy Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719742"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementowanie architektury lambda na platformie Azure 

Architektury lambda Włącz wydajnego przetwarzania danych w dużych zestawów danych. Architektury lambda Użyj przetwarzanie wsadowe, przetwarzanie strumienia i warstwę obsługi, aby zminimalizować opóźnienia związane z wykonywanie zapytań o dane big Data. 

Aby Implementowanie architektury lambda na platformie Azure, można połączyć następujące technologie, aby przyspieszyć analizy w czasie rzeczywistym danych big data:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)— najpierw dystrybuowana w branży globalnie, wielomodelowa usługa bazy danych. 
* [Apache Spark dla usługi Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), struktura przetwarzania, która uruchamia aplikacje do analizy danych na dużą skalę
* Azure Cosmos DB [źródła zmian](change-feed.md), które przesyła strumieniowo nowe dane do warstwy wsadowej usługi HDInsight do przetworzenia
* [Łącznik platformy Spark do Azure Cosmos DB](spark-connector.md)

W tym artykule opisano podstawy architektury lambda, na podstawie oryginalnego projektu wielowarstwową i zalet architektury lambda "rearchitected", która upraszcza operacje.  

## <a name="what-is-a-lambda-architecture"></a>Co to jest architektura lambda?
Architektura lambda to ogólna, skalowalna i odporna na błędy architektura przetwarzania danych, która umożliwia rozwiązywanie scenariuszy związanych z usługą Batch i szybkością opóźnienia, zgodnie z opisem w [Nathana marza](https://twitter.com/nathanmarz).

![Diagram przedstawiający architektury lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Źródło: http://lambda-architecture.net/

Podstawowe zasady architektury lambda są opisane na powyższym diagramie zgodnie z [http://lambda-architecture.net](http://lambda-architecture.net/).

 1. Wszystkie **dane** są wypychane do *warstwy* *wsadowej* i *warstwy szybkość*.
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny zestaw danych RAW) i wstępnie oblicza widoki partii.
 3. **Warstwa obsługująca** zawiera widoki wsadowe dla szybkich zapytań. 
 4. **Warstwa szybkości** pozwala kompensować czas przetwarzania (do obsługi warstwy) i obsługiwać tylko najnowsze dane.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoki i widoków w czasie rzeczywistym lub odpowiada na polecenie ping je pojedynczo.

Na dalsze informacje, firma Microsoft będzie wdrożenia tej architektury przy użyciu jedynie następujące elementy:

* Kontenery usługi Azure Cosmos
* Klaster HDInsight (Apache Spark 2.1)
* Łącznik platformy Spark [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Warstwa szybka

Z perspektywy operacje obsługi dwóch strumieni danych przy jednoczesnym zapewnieniu poprawny stan danych może być skomplikowane pozwala. Aby uprościć operacje, użyj [obsługi kanału informacyjnego zmiany Azure Cosmos DB](change-feed.md) , aby zachować stan dla *warstwy usługi Batch* podczas ujawniania Azure Cosmos DB dzienniku zmian za pomocą *interfejsu API źródła zmian* dla *warstwy szybkości*.  
![diagram wyróżniania nowych danych, warstwy szybkości i wzorca zestawu danych w architekturze lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co to jest ważne w tych warstwach:

 1. Wszystkie **dane** są wypychane *tylko* do Azure Cosmos DB, co pozwala uniknąć problemów z rozrzutem.
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny zestaw danych RAW) i wstępnie oblicza widoki partii.
 3. W następnej sekcji omówiono **obsługę warstwy** .
 4. **Warstwa szybkości** wykorzystuje usługi HDInsight (Apache Spark) do odczytywania Azure Cosmos DB źródła zmian. Dzięki temu można w celu utrwalenia Twoich danych, jak również do wykonywania zapytań i jednocześnie go przetworzyć.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoki i widoków w czasie rzeczywistym lub odpowiada na polecenie ping je pojedynczo.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Przykład kodu: Spark przesyłanie strumieniowe ze strukturą do zestawienia zmian usługi Azure Cosmos DB
Aby uruchomić szybki prototyp kanału informacyjnego zmiany Azure Cosmos DB jako część **warstwy szybkości**, można przetestować go przy użyciu danych usługi Twitter w ramach [przetwarzania strumienia przy użyciu Azure Cosmos DB zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) przykładu. Aby szybko rozpocząć pracę z danymi wyjściowymi w usłudze Twitter, zobacz przykładowy kod w [strumieniowym źródle danych z usługi Twitter do Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). W poprzednim przykładzie ładowania danych z usługi Twitter do usługi Azure Cosmos DB, a następnie służą do tworzenia klastra usługi HDInsight (Apache Spark) nawiązać zestawienia zmian. Aby uzyskać więcej informacji na temat konfigurowania tej konfiguracji, zobacz [Apache Spark do Azure Cosmos DB konfiguracji łącznika](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Poniższy fragment kodu przedstawia sposób konfigurowania `spark-shell` do uruchamiania zadania tworzenia strumieniowego w celu nawiązania Azure Cosmos DB połączenia ze strumieniowym źródłem danych w czasie rzeczywistym, aby można było wykonać liczbę uruchomionych interwałów.

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

Aby zapoznać się z kompletnymi przykładami kodu, zobacz temat [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), w tym:
* [Zapytanie przesyłane strumieniowo ze źródła danych zmian Cosmos DB. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Zapytanie o Tagi przesyłane strumieniowo ze źródła danych zmian Cosmos DB. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Danymi wyjściowymi jest konsola `spark-shell`, która ciągle uruchamia zadanie przesyłania strumieniowego ze strukturą, które wykonuje liczbę interwałów względem danych usługi Twitter ze źródła Azure Cosmos DB zmian. Na poniższej ilustracji przedstawiono dane wyjściowe zadania usługi stream i zlicza interwału.

![Dane wyjściowe przesyłania strumieniowego, przedstawiający liczbę interwał względem danych usługi Twitter z zestawienia zmian usługi Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Aby uzyskać więcej informacji na temat usługi Azure Cosmos DB zestawienia zmian, zobacz:

* [Praca z obsługą kanału informacyjnego zmian w Azure Cosmos DB](change-feed.md)
* [Wprowadzenie do biblioteki procesora kanału informacyjnego zmiany CosmosDB platformy Azure](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Zmiany przetwarzania strumienia: usługa Azure CosmosDB zmiana źródła danych i Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Usługi Batch i obsługująca warstwy
Ze względu na to, że nowe dane są ładowane do Azure Cosmos DB (gdzie Źródło zmian jest używane dla warstwy szybkości), jest to miejsce, w którym znajduje się **główny zestaw** danych (niemodyfikowalny zestaw tylko do dołączania). Od tego momentu Użyj usługi HDInsight (Apache Spark), aby wykonać funkcje wstępnego obliczania z **warstwy wsadowej** w celu obsługi **warstwy**, jak pokazano na poniższej ilustracji:

![Diagram wyróżnianie warstwy usługi batch i obsługująca warstwy architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co to jest ważne w tych warstwach:

 1. Wszystkie **dane** są wypychane tylko do Azure Cosmos dB (w celu uniknięcia problemów z multiemisją).
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny zestaw danych RAW) przechowywanych w Azure Cosmos DB. Przy użyciu platformy Spark w usłudze HDI, można wstępnie obliczeń swoje agregacje mają być przechowywane w widoków obliczanej usługi batch.
 3. **Warstwa obsługująca** to baza danych usługi Azure Cosmos z kolekcjami dla głównego zestawu danych i obliczanego widoku partii.
 4. **Warstwa szybkości** została omówiona w dalszej części tego artykułu.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoków i w czasie rzeczywistym lub odpowiada na polecenie ping je pojedynczo.

### <a name="code-example-pre-computing-batch-views"></a>Przykład kodu: wstępnie przetwarzania wsadowego widoków
Aby zaprezentować sposób wykonywania wstępnie obliczonych widoków względem **głównego zestawu danych** z Apache Spark do Azure Cosmos DB, Użyj poniższych fragmentów kodu z notesu reprezentowanych przez [architekturę lambda](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) [na podstawie](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)platformy W tym scenariuszu należy użyć danych serwisu Twitter, przechowywane w usłudze Azure Cosmos DB.

Zacznijmy od utworzenia konfiguracji połączenia danych serwisu Twitter w ramach usługi Azure Cosmos DB przy użyciu poniższego kodu PySpark.

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

Następnie uruchom następującą instrukcję programu Spark SQL, aby określić hasztagi 10 najważniejszych zestawu tweetów. Dla tego zapytania Spark SQL jest uruchomiony w notesie Jupyter bez na wykresie słupkowym dane wyjściowe, bezpośrednio po ten fragment kodu.

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

![Wykres przedstawiający liczbę tweety na hasztag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teraz, gdy zapytanie, zapisz go do kolekcji za pomocą łącznika usługi Spark można zapisać dane wyjściowe do innej kolekcji.  W tym przykładzie należy użyć Scala, aby zaprezentować połączenia. Podobnie jak w poprzednim przykładzie, Utwórz połączenie konfiguracji, aby zapisać Apache Spark ramki danych w innym kontenerze usługi Azure Cosmos.

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

Po określeniu `SaveMode` (wskazujący, czy `Overwrite` lub `Append` dokumentów) Utwórz `tweets_bytags`ą ramkę danych podobną do zapytania Spark SQL w poprzednim przykładzie.  Po utworzeniu `tweets_bytags` Dataframe można ją zapisać przy użyciu metody `write`, korzystając z wcześniej określonego `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Ta ostatnia instrukcja zapisała teraz swoją ramkę danych platformy Spark w nowym kontenerze usługi Azure Cosmos. w perspektywie architektury lambda jest to **Widok wsadu** w ramach **warstwy obsługującej**.
 
#### <a name="resources"></a>Zasoby

Aby zapoznać się z kompletnymi przykładami kodu, zobacz [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) , w tym:
* Przeprojektowana architektura lambda — warstwa wsadowa [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Przeprojektowana architektura lambda — przetwarzanie wsadowe obsługujące warstwę [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Warstwa szybka
Jak wcześniej wspomniano, przy użyciu biblioteki usługi Azure Cosmos DB zmiany źródła danych umożliwia uproszczenie operacji między warstwami usługi batch i szybkości. W tej architekturze należy używać Apache Spark (za pośrednictwem usługi HDInsight) do wykonywania zapytań *strukturalnych dotyczących przesyłania strumieniowego* na dane. Można również tymczasowo zachować wyniki zapytań ze strukturą przesyłania strumieniowego, aby inne systemy mogą uzyskiwać dostęp do tych danych.

![Diagram wyróżnianie warstwa szybka architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

W tym celu Utwórz oddzielny kontener usługi Azure Cosmos, aby zapisać wyniki zapytań dotyczących przesyłania strumieniowego ze strukturą.  Dzięki temu można mieć dostęp do innych systemów tych informacji nie tylko Apache Spark. Również funkcją usługi Cosmos DB czas wygaśnięcia (TTL), można skonfigurować dokumenty, aby automatycznie usunięte po ustaw czas trwania.  Aby uzyskać więcej informacji na temat funkcji TTL Azure Cosmos DB, zobacz [Automatyczne wygasanie danych w kontenerach usługi Azure Cosmos z użyciem czasu](time-to-live.md) wygaśnięcia

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
Jak wspomniano w poprzednich sekcjach, oryginalnym architektury lambda można uprościć za pomocą następujących składników:
* Azure Cosmos DB
* Cosmos DB zmiany źródła danych biblioteki usługi Azure w celu uniknięcia konieczności multiemisji dane między warstwami usługi batch i szybkości
* Platforma Apache Spark w HDInsight
* Łącznik platformy Spark dla usługi Azure Cosmos DB

![Diagram przedstawiający rearchitecture architektury lambda przy użyciu usługi Azure Cosmos DB, Spark i Azure Cosmos DB zmiany źródła danych interfejsu API](./media/lambda-architecture/lambda-architecture-re-architected.png)

W tym projekcie musisz tylko dwa zarządzane usługi Azure Cosmos DB i HDInsight. Razem odnoszą się do usługi batch, obsługująca i szybkości za warstwy architektury lambda. Upraszcza to nie tylko operacje, lecz także przepływu danych. 
 1. Wszystkie dane są przesyłane do usługi Azure Cosmos DB do przetworzenia
 2. Warstwa wsadowa ma główny zestaw danych (niezmienne, tylko do dołączania zestawu danych pierwotnych) i wstępnie oblicza widoków usługi batch
 3. Warstwę obsługi ma widoków partii danych na potrzeby szybkich zapytań.
 4. Warstwa szybka kompensuje czas przetwarzania (do warstwy usług) i obsługuje tylko najnowsze dane.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoków i w czasie rzeczywistym.

### <a name="resources"></a>Zasoby

* **Nowe dane**: [strumień strumieniowy z usługi Twitter do CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), który jest mechanizmem do wypychania nowych danych do Azure Cosmos DB.
* **Warstwa partii:** Warstwa wsadowa składa się z *wzorca zestawu* danych (niemodyfikowalny zestaw danych RAW) i możliwość wstępnego obliczania widoków wsadowych danych, które są przekazywane do **warstwy obsługującej**.
   * Przeprojektowana **Architektura lambda — Notes warstwy partii** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) wysyła zapytanie do zestawu *głównych zestawów danych* widoków wsadowych.
* **Obsługa warstwy:** **Warstwa obsługująca** składa się z wstępnie obliczonych danych w widokach wsadowych (na przykład agregacji, określonych fragmentatorów itp.) dla szybkich zapytań.
  * Architektura lambda przeprojektowana **— Przetwarzanie wsadowe w celu obsługi** notesu warstwy [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) wypychanie danych wsadowych do warstwy obsługującej; oznacza to, że platforma Spark wysyła zapytanie do kolekcji partii tweetów, przetwarza je i zapisuje w innej kolekcji (obliczanej partii).
    * **Warstwa szybkości:** **Warstwa szybkości** składa się z platformy Spark korzystającej ze źródła danych Azure Cosmos DB zmian w celu natychmiastowego odczytu i działania. Dane można również zapisać na *obliczonym RT* , tak aby inne systemy mogły wysyłać zapytania do przetworzonych danych w czasie rzeczywistym, zamiast wykonywać zapytania w czasie rzeczywistym.
  * [Kwerenda przesyłania strumieniowego ze źródła danych Cosmos DB zmian](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) Scala wykonuje zapytanie strumieniowe ze źródła danych Azure Cosmos DB zmiany, aby obliczyć liczbę interwałów z powłoki Spark.
  * [Zapytanie o Tagi przesyłania strumieniowego Cosmos DB ze skryptu Scala zmian kanału informacyjnego](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) wykonuje zapytanie strumieniowe ze źródła danych Azure Cosmos DB zmiany, aby obliczyć liczbę znaczników tagów z powłoki Spark.
  
## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiono, Pobierz program Spark do Azure Cosmos DB Connector z repozytorium GitHub [usługi Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) i Eksploruj dodatkowe zasoby w repozytorium:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Przykłady rozproszonej agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesy](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Pokazy strukturalnego przesyłania strumieniowego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Pokazy źródła zmian](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Przetwarzanie strumienia zmian za pomocą Azure Cosmos DB zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Warto również zapoznać się z [podręcznikiem Apache Spark SQL, Dataframes i zestawami danych](https://spark.apache.org/docs/latest/sql-programming-guide.html) oraz [Apache Spark w artykule usługi Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) .
