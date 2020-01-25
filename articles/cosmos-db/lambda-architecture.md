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
* [Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), pierwsza usługa globalnie dystrybuowanej, wielomodelowej bazy danych w branży. 
* [Platforma Apache Spark dla usługi Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), platformę przetwarzania, która umożliwia uruchamianie aplikacji do analizy danych na dużą skalę
* Usługa Azure Cosmos DB [zestawienia zmian](change-feed.md), która przesyła strumieniowo nowe dane do warstwy usługi batch dla HDInsight do przetworzenia
* [Spark do łącznika usługi Azure Cosmos DB](spark-connector.md)

W tym artykule opisano podstawy architektury lambda, na podstawie oryginalnego projektu wielowarstwową i zalet architektury lambda "rearchitected", która upraszcza operacje.  

## <a name="what-is-a-lambda-architecture"></a>Co to jest architektura lambda?
Architektura lambda jest ogólny, skalowalne, i odpornej na uszkodzenia danych, przetwarzanie architektury na adres usługi batch i szybkość scenariuszy obejmujących opóźnienie, zgodnie z opisem w [Nathana Marza](https://twitter.com/nathanmarz).

![Diagram przedstawiający architektury lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Źródło: http://lambda-architecture.net/

Podstawowe zasady architektury lambda są opisane na powyższym diagramie zgodnie [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Wszystkie **danych** są przesyłane do *zarówno* *warstwie wsadowej* i *warstwa szybka*.
 2. **Warstwie wsadowej** ma główny zestaw danych (niezmienne, tylko do dołączania zestawu danych pierwotnych) i wstępnie oblicza widoków usługi batch.
 3. **Warstwę obsługi** widoki usługi batch na potrzeby szybkich zapytań. 
 4. **Warstwa szybka** kompensuje czas przetwarzania (do warstwy usług) i obsługuje tylko najnowsze dane.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoki i widoków w czasie rzeczywistym lub odpowiada na polecenie ping je pojedynczo.

Na dalsze informacje, firma Microsoft będzie wdrożenia tej architektury przy użyciu jedynie następujące elementy:

* Kontenery usługi Azure Cosmos
* Klaster HDInsight (Apache Spark 2.1)
* Łącznik Spark [1.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Warstwa szybka

Z perspektywy operacje obsługi dwóch strumieni danych przy jednoczesnym zapewnieniu poprawny stan danych może być skomplikowane pozwala. Aby uprościć czynności, korzystanie z [Obsługa kanału informacyjnego zmian usługi Azure Cosmos DB](change-feed.md) zachować stan *warstwie wsadowej* podczas przedstawiania dziennik zmian usługi Azure Cosmos DB za pomocą *Zmień źródło danych interfejsu API* dla Twojego *warstwa szybka*.  
![Diagram wyróżnianie nowe dane, warstwa szybka i główny zestaw danych części architektury lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co to jest ważne w tych warstwach:

 1. Wszystkie **danych** wypchnięciach *tylko* do usługi Azure Cosmos DB, dlatego można uniknąć rzutowania wielu problemów.
 2. **Warstwie wsadowej** ma główny zestaw danych (niezmienne, tylko do dołączania zestawu danych pierwotnych) i wstępnie oblicza widoków usługi batch.
 3. **Warstwę obsługi** została omówiona w następnej sekcji.
 4. **Warstwa szybka** korzysta z usługi HDInsight (Apache Spark) na odczytywanie zestawienia zmian usługi Azure Cosmos DB. Dzięki temu można w celu utrwalenia Twoich danych, jak również do wykonywania zapytań i jednocześnie go przetworzyć.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoki i widoków w czasie rzeczywistym lub odpowiada na polecenie ping je pojedynczo.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Przykład kodu: Spark przesyłanie strumieniowe ze strukturą do zestawienia zmian usługi Azure Cosmos DB
Aby uruchomić szybkie prototyp zestawienia w ramach zmian do usługi Azure Cosmos DB **warstwa szybka**, można przetestować się przy użyciu danych z usługi Twitter jako część [Stream przetwarzania zmian przy użyciu usługi Azure Cosmos DB Zmień źródło danych i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)przykład. Aby szybko Rozpocznij korzystanie z danych wyjściowych usługi Twitter, zobacz przykładowy kod w [Stream źródła danych z serwisu Twitter do usługi Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). W poprzednim przykładzie ładowania danych z usługi Twitter do usługi Azure Cosmos DB, a następnie służą do tworzenia klastra usługi HDInsight (Apache Spark) nawiązać zestawienia zmian. Aby uzyskać więcej informacji na temat sposobu konfigurowania tej konfiguracji, zobacz [platforma Apache Spark umożliwia instalację łącznika usługi Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Poniższy fragment kodu przedstawia sposób konfigurowania `spark-shell` do uruchomienia strukturalne zadania przesyłania strumieniowego połączyć się do zmian usługi Azure Cosmos DB, źródła danych, która monitoruje w czasie rzeczywistym strumień danych usługi Twitter, przeprowadzić liczbę interwał.

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

Aby uzyskać przykłady kompletny kod, zobacz [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), w tym:
* [Zapytanie przesyłania strumieniowego z Feed.scala zmiany bazy danych Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Przesyłania strumieniowego zapytanie tagi z Feed.scala zmiany bazy danych Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Dane wyjściowe tego `spark-shell` konsoli, który nieprzerwanie wykonuje zadanie przesyłania strumieniowego ze strukturą przeprowadza liczba interwale względem danych z usługi Twitter z zestawienia zmian usługi Azure Cosmos DB. Na poniższej ilustracji przedstawiono dane wyjściowe zadania usługi stream i zlicza interwału.

![Dane wyjściowe przesyłania strumieniowego, przedstawiający liczbę interwał względem danych usługi Twitter z zestawienia zmian usługi Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Aby uzyskać więcej informacji na temat usługi Azure Cosmos DB zestawienia zmian, zobacz:

* [Praca ze zmianą Obsługa kanału informacyjnego w usłudze Azure Cosmos DB](change-feed.md)
* [Wprowadzenie do usługi Azure CosmosDB zmiany źródła danych z biblioteką procesora](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Stream przetwarzania zmian: Kanał informacyjny zmian Azure cosmos DB i Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Usługi Batch i obsługująca warstwy
Ponieważ nowe załadowaniu danych do usługi Azure Cosmos DB (gdzie Kanał informacyjny zmian jest używana dla warstwy szybkości), to ma miejsce **główny zestaw danych** (niezmienne, tylko do dołączania dla danych nieprzetworzonych) znajduje się zestaw. Od tej pory i nowszych wersjach użyć HDInsight (Apache Spark) do wykonywania zadań wstępne obliczeń z **warstwie wsadowej** do **warstwę obsługi**, jak pokazano na poniższej ilustracji:

![Diagram wyróżnianie warstwy usługi batch i obsługująca warstwy architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co to jest ważne w tych warstwach:

 1. Wszystkie **danych** zostanie przypisany tylko do usługi Azure Cosmos DB (Aby uniknąć problemów z multiemisji).
 2. **Warstwie wsadowej** ma głównego zestawu danych (niezmienne, tylko do dołączania zestawu danych pierwotnych) przechowywanego w usłudze Azure Cosmos DB. Przy użyciu platformy Spark w usłudze HDI, można wstępnie obliczeń swoje agregacje mają być przechowywane w widoków obliczanej usługi batch.
 3. **Warstwa obsługująca** to baza danych usługi Azure Cosmos z kolekcjami dla głównego zestawu danych i obliczanego widoku partii.
 4. **Warstwa szybka** została omówiona w dalszej części tego artykułu.
 5. Wszystkie zapytania można uzyskać przez scalanie wyników z usługi batch, widoków i w czasie rzeczywistym lub odpowiada na polecenie ping je pojedynczo.

### <a name="code-example-pre-computing-batch-views"></a>Przykład kodu: wstępnie przetwarzania wsadowego widoków
Aby zaprezentować sposób wykonywania wstępnie obliczona widoków swoje **główny zestaw danych** z platformy Apache Spark w usłudze Azure Cosmos DB, użyj poniższe fragmenty kodu z notesów [Rearchitected architektury Lambda - warstwa wsadowa ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) i [Rearchitected architektury Lambda - Batch zaspokajanie warstwy](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). W tym scenariuszu należy użyć danych serwisu Twitter, przechowywane w usłudze Azure Cosmos DB.

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

Po określeniu `SaveMode` (wskazująca, czy `Overwrite` lub `Append` dokumenty), utworzyć `tweets_bytags` ramka danych jest podobne do zapytania Spark SQL w poprzednim przykładzie.  Za pomocą `tweets_bytags` utworzona ramka danych, możesz zapisać plik za pomocą `write` metody za pomocą wcześniej określonego `writeConfig`.

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

Aby uzyskać przykłady kompletny kod, zobacz [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) w tym:
* Rearchitected architektury lambda - warstwa wsadowa [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Rearchitected architektury lambda - usługi Batch do warstwy obsługująca [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Warstwa szybka
Jak wcześniej wspomniano, przy użyciu biblioteki usługi Azure Cosmos DB zmiany źródła danych umożliwia uproszczenie operacji między warstwami usługi batch i szybkości. W tej architekturze use Apache Spark (za pośrednictwem HDInsight) do wykonywania *przesyłanie strumieniowe ze strukturą* zapytania dotyczące danych. Można również tymczasowo zachować wyniki zapytań ze strukturą przesyłania strumieniowego, aby inne systemy mogą uzyskiwać dostęp do tych danych.

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

* **Nowe dane**: [strumienia źródła danych z serwisu Twitter do usługi CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), który jest mechanizm do wypychania nowych danych do usługi Azure Cosmos DB.
* **Warstwa wsadowa:** warstwa wsadowa składa się z *główny zestaw danych* (niezmienne, tylko do dołączania zestawu danych pierwotnych) oraz możliwość wstępnie obliczeń wsadowych widoków danych, które są przekazywane do **warstwę obsługi** .
   * **Rearchitected architektury Lambda - warstwa wsadowa** notesu [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) zapytania *główny zestaw danych* zestaw widoków usługi batch.
* **Warstwę obsługi:** **warstwę obsługi** składa się z wstępnie obliczone dane w widokach partii (na przykład agregacji, fragmentatory określonych itp.) dla zapytań.
  * **Rearchitected architektury Lambda - usługi Batch do warstwy obsługująca** notesu [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) wypycha dane wsadowe warstwę obsługi; oznacza to, że platforma Spark zapytania kolekcję usługi batch tweetów, przetwarza je i zapisuje go do innej kolekcji (obliczanej usługi batch).
    * **Warstwa szybka:** **warstwa szybka** składa się z platformy Spark przy użyciu zmian usługi Azure Cosmos DB, źródła danych do odczytu i zajmującym się natychmiast. Można również zapisać dane *obliczane RT* tak, aby inne systemy mogą wysyłać zapytania przetworzone dane w czasie rzeczywistym w przeciwieństwie do uruchamiania w czasie rzeczywistym zapytania samodzielnie.
  * [Przesyłanie strumieniowe zapytania z Cosmos DB Zmień źródło danych](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala skrypt wykonuje zapytanie przesyłania strumieniowego ze źródła danych do obliczenia liczby interwał, z poziomu powłoki spark zmiany usługi Azure Cosmos DB.
  * [Przesyłania strumieniowego kwerendę tagów, Cosmos DB Zmień źródło danych](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skrypt wykonuje zapytanie przesyłania strumieniowego ze źródła danych, aby obliczyć interwał liczba tagów z poziomu powłoki spark zmiany usługi Azure Cosmos DB.
  
## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze nie, Pobierz platformy Spark do łącznika usługi Azure Cosmos DB z [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) repozytorium GitHub i dodatkowe zasoby w repozytorium:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Przykłady rozproszonych agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesów](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Pokazy przesyłania strumieniowego ze strukturą](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Pokazy kanału informacyjnego zmian](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream przetwarzania zmian za pomocą usługi Azure Cosmos DB kanału informacyjnego zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Możesz również przejrzeć [Apache Spark SQL, przewodnik zestawy danych i elementy Dataframe](https://spark.apache.org/docs/latest/sql-programming-guide.html) i [platformy Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
