---
title: Platforma Apache Spark nawiązywania połączenia z rozwiązania Cosmos Azure DB | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się więcej o łącznika usługi Azure Spark DB rozwiązania Cosmos, który umożliwia łączenie z Apache Spark do bazy danych rozwiązania Cosmos Azure do wykonywania rozproszonej nauki agregacji i danych na wielu dzierżawców globalnie Rozproszony system bazy danych firmy Microsoft to jest zaprojektowane dla chmury.
keywords: Platforma Apache spark
services: cosmos-db
author: dennyglee
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: denlee
ms.openlocfilehash: 3502a5af5de546de0b66521e29c37f5edfdbc6ea
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612626"
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Przyspieszenie w czasie rzeczywistym analizy danych big data w usłudze Spark do łącznika usługi Azure DB rozwiązania Cosmos

Spark do bazy danych Azure rozwiązania Cosmos łącznika umożliwia Azure DB rozwiązania Cosmos do działania jako źródło danych wejściowych lub ujście danych wyjściowych zadań Apache Spark. Łączenie [Spark](http://spark.apache.org/) do [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) przyspiesza możliwości rozwiązania szybko problemów analizy danych, których można użyć bazy danych Azure rozwiązania Cosmos szybkie utrwalić i zapytania na danych. Spark do bazy danych Azure rozwiązania Cosmos łącznika wydajnie korzysta natywnych indeksy bazy danych rozwiązania Cosmos Azure zarządzanych. Indeksy włączyć można aktualizować kolumny wykonywać analizy i rozwijana wypychania predykatu filtrowania pod kątem fast zmiany globalnie rozproszone dane, które należeć do zakresu od Internetu rzeczy (IoT) do scenariuszy nauki i analiza danych.

Dowiedz się więcej na ten film z Lewandowski Denny rozwiązania Cosmos DB główny menedżer programu Azure. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

Do pracy z Spark, GraphX i wykres Gremlin interfejsów API Azure DB rozwiązania Cosmos, zobacz [wykonywać analizy wykresu przy użyciu platformy Spark i Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Do pobrania

Aby rozpocząć pracę, należy pobrać Spark do bazy danych Azure rozwiązania Cosmos łącznika z [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) repozytorium w witrynie GitHub.

## <a name="connector-components"></a>Łącznik składników

Łącznik korzysta z następujących składników:

* [Azure DB rozwiązania Cosmos](http://documentdb.com) służy do obsługi administracyjnej i elastycznie skalować zarówno przepływność i Magazyn przez dowolną liczbę regionów geograficznych. Oferty usługi:
   * Włącz klucz [dystrybucji globalnego](distribute-data-globally.md) i skalowanie w poziomie
   * Gwarantowane pojedynczą cyfrą opóźnienia w 99-ty percentyl
   * [Wiele modeli dobrze zdefiniowany spójności](consistency-levels.md)
   * Zagwarantować wysokiej dostępności z możliwości wielu
   * Wszystkie funkcje bazują na branży, kompleksowe [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA).

* [Platforma Apache Spark](http://spark.apache.org/) jest aparat przetwarzania zaawansowanych typu open source, która opiera się szybkości, łatwości użycia i zaawansowanych możliwości analitycznych.

* [Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) tak, aby platforma Apache Spark w chmurze na potrzeby wdrożeń krytycznym można wdrożyć za pomocą [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Oficjalnie obsługiwane wersje:

| Składnik | Wersja |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| Scala| 2.10, 2.11|
| Azure rozwiązania Cosmos bazy danych SQL w języku Java SDK | 1.14.0, 1.15.0 |

W tym artykule opisano, jak uruchomić niektóre proste przykłady przy użyciu języka Python (za pośrednictwem pakiet pyDocumentDB) i interfejsy Scala.

Istnieją dwa podejścia do połączenia Apache Spark i bazy danych rozwiązania Cosmos Azure:
- Użyj pydocumentdb — za pomocą [zestaw SDK Python SQL Azure rozwiązania Cosmos DB](https://github.com/Azure/azure-documentdb-python).
- Utwórz oparte na języku Java platformy Spark do bazy danych Azure rozwiązania Cosmos łącznika przy użyciu [zestawu Java SDK usługi Azure rozwiązania Cosmos bazy danych SQL](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Pakiet pyDocumentDB implementacji
Bieżący [pydocumentdb — zestaw SDK](https://github.com/Azure/azure-documentdb-python) umożliwia podłączenie do bazy danych Azure rozwiązania Cosmos Spark, jak pokazano na poniższym diagramie:

![Platforma Spark dla bazy danych Azure rozwiązania Cosmos przepływu danych za pośrednictwem pydocumentdb — bazy danych](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Przepływ danych implementacji pakiet pyDocumentDB

Przepływ danych jest następujący:

1. Węzeł główny Spark łączy do węzła bramy bazy danych rozwiązania Cosmos Azure za pośrednictwem pakiet pyDocumentDB. Użytkownik określa tylko połączenia Spark i bazy danych Azure rozwiązania Cosmos. Połączenia dla odpowiednich węzłów master i bramy są niewidoczne dla użytkownika.
2. Węzeł bramy sprawia, że zapytanie bazy danych z rozwiązania Cosmos platformy Azure, gdzie zapytanie później jest uruchamiana dla kolekcji partycji w węzły danych. Odpowiedzi na te zapytania są wysyłane do węzła bramy, a ten zestaw wyników jest zwracana do węzła głównego Spark.
3. Kolejne zapytania (na przykład przed Spark DataFrame) są wysyłane do węzłów procesu roboczego Spark do przetwarzania.

Komunikacja między Spark i bazy danych Azure rozwiązania Cosmos jest ograniczone do węzła głównego Spark i bazy danych Azure rozwiązania Cosmos węzłów bramy.  Zapytania go tak szybko, jak warstwa transportu między tymi dwoma węzłami umożliwia.

### <a name="install-pydocumentdb"></a>Zainstaluj pakiet pyDocumentDB
Można zainstalować pakiet pyDocumentDB w węźle sterowników za pomocą **pip**, na przykład:

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Nawiązanie połączenia z Spark bazy danych Azure rozwiązania Cosmos przez pakiet pyDocumentDB
Prostota transportu komunikacji sprawia, że wykonanie zapytania z Spark do bazy danych Azure rozwiązania Cosmos przy użyciu pakiet pyDocumentDB stosunkowo proste.

Poniższy fragment kodu przedstawia sposób użycia pydocumentdb — w kontekście Spark.

```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Jak wspomniano we fragmencie kodu:

* Zestaw SDK Python usługi Azure rozwiązania Cosmos bazy danych (`pyDocumentDB`) zawiera wszystkie niezbędne parametry połączenia. Na przykład parametr preferowane lokalizacje wybiera kolejność odczytu repliki i priorytet.
*  Zaimportuj wymagane biblioteki i skonfiguruj sieci **umożliwia** i **hosta** do tworzenia bazy danych rozwiązania Cosmos Azure *klienta* (**pydocumentdb.document_client** ).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Wykonywanie zapytań Spark za pośrednictwem pakiet pyDocumentDB
W poniższych przykładach użyto wystąpienia bazy danych Azure rozwiązania Cosmos, który został utworzony w poprzedniej fragment kodu za pomocą określone klucze tylko do odczytu. Poniższy fragment kodu, które łączy się z **airports.codes** kolekcji na koncie DoctorWho jako określony wcześniej i uruchamia zapytanie w celu wyodrębnienia miast lotnisku w stanie Waszyngton.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Po wykonaniu zapytania za pomocą **zapytania**, wynikiem jest **query_iterable. QueryIterable** który jest konwertowany na liście Python. Lista Python można łatwo konwertować na Spark DataFrame przy użyciu następującego kodu:

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Dlaczego warto używać pakiet pyDocumentDB do nawiązania połączenia bazy danych rozwiązania Cosmos Azure Spark?
Łączenie Spark do bazy danych rozwiązania Cosmos Azure za pomocą pydocumentdb — jest zwykle w scenariuszach, gdzie:

* Chcesz użyć języka Python.
* Zwracany jest stosunkowo mały zestawu wyników z bazy danych usługi Azure rozwiązania Cosmos do Spark. Należy pamiętać, że źródłowy zestawu danych w usłudze Azure DB rozwiązania Cosmos może być dość duży. Chcesz zastosować filtry, to znaczy uruchamiania filtrów predykatów źródła bazy danych Azure rozwiązania Cosmos.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Platforma Spark jest łącznikiem bazy danych Azure rozwiązania Cosmos

Korzysta z platformy Spark do łącznika usługi Azure DB rozwiązania Cosmos [zestawu Java SDK usługi Azure rozwiązania Cosmos bazy danych SQL](https://github.com/Azure/azure-documentdb-java) i przenosi dane między węzłami procesów roboczych Spark i bazy danych Azure rozwiązania Cosmos, jak pokazano na poniższym diagramie:

![Przepływ danych w Spark do łącznika usługi Azure DB rozwiązania Cosmos](./media/spark-connector/spark-connector.png)

Przepływ danych jest następujący:

1. Węzeł główny Spark łączy do węzła bramy bazy danych Azure rozwiązania Cosmos można uzyskać mapy partycji. Użytkownik określa tylko połączenia Spark i bazy danych Azure rozwiązania Cosmos. Połączenia dla odpowiednich węzłów master i bramy są niewidoczne dla użytkownika.
2. Te informacje są dostarczane do węzła głównego Spark.  W tym momencie można przeanalizować zapytania w celu ustalenia partycji i ich lokalizacje w usłudze Azure DB rozwiązania Cosmos, który chcesz uzyskać dostęp.
3. Te informacje są przesyłane do węzłów procesu roboczego platformy Spark.
4. Węzłów procesu roboczego Spark nawiązać partycji bazy danych Azure rozwiązania Cosmos bezpośrednio do wyodrębniania danych i zwrócić dane do partycji Spark w węzłów procesu roboczego platformy Spark.

Komunikacja między Spark i bazy danych Azure rozwiązania Cosmos jest znacznie szybsze ponieważ przenoszenia danych między węzłami procesów roboczych Spark i węzły danych bazy danych Azure rozwiązania Cosmos (partycje).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Tworzenie Spark do łącznika usługi Azure DB rozwiązania Cosmos
Obecnie projektu łącznik używa maven. Aby utworzyć łącznik bez zależności, możesz uruchomić:
```
mvn clean package
```
Możesz również pobrać najnowsze wersje JAR z [ *zwalnia* folderu](https://github.com/Azure/azure-cosmosdb-spark/releases).

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Obejmują Spark DB Azure rozwiązania Cosmos JAR
Przed wykonaniem dowolny kod, należy dołączyć JAR Spark DB rozwiązania Cosmos Azure.  Jeśli używasz **spark powłoki**, wówczas można uwzględnić JAR przy użyciu **— słoików** opcji.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

Jeśli chcesz wykonać JAR bez zależności, należy użyć poniższego kodu:

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

Jeśli używasz usługi notesu, takich jak usługa notesu Azure HDInsight Jupyter, możesz użyć **spark magic** polecenia:

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**Słoików** polecenie umożliwia dołączenie dwóch słoików, które są wymagane przez **spark-azure-cosmosdb** (się i zestawu SDK usługi Azure rozwiązania Cosmos bazy danych SQL Java) i wykluczyć **scala-odzwierciedlają**tak, aby nie zakłóca wywołania Livy (notesu Jupyter > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Połącz Spark do bazy danych rozwiązania Cosmos Azure za pomocą łącznika
Mimo że transportu komunikacji jest nieco bardziej skomplikowane, wykonywanie zapytania z Spark do bazy danych rozwiązania Cosmos Azure za pomocą łącznika jest znacznie szybsze.

Poniższy fragment kodu przedstawia sposób korzystania z łącznika w sesji Spark. Zapoznaj się z `azure-cosmosdb-spark` [repozytorium GitHub](https://github.com/Azure/azure-cosmosdb-spark) dla języka Python próbek.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

Jak wspomniano we fragmencie kodu:

- **Azure — cosmosdb-spark** zawiera wszystkie niezbędne parametry połączenia, w tym preferowanych lokalizacji. Na przykład można wybrać kolejność odczytu repliki i priorytet.
- Po prostu zaimportować wymagane biblioteki i skonfiguruj umożliwia i hosta, aby utworzyć klienta usługi Azure DB rozwiązania Cosmos.

### <a name="execute-spark-queries-via-the-connector"></a>Wykonywanie zapytań Spark za pośrednictwem łącznika

W poniższym przykładzie użyto wystąpienia bazy danych Azure rozwiązania Cosmos, który został utworzony w poprzedniej fragment kodu za pomocą określone klucze tylko do odczytu. Poniższy fragment kodu łączy do kolekcji DepartureDelays.flights_pcoll (na koncie DoctorWho określone wcześniej) i przeprowadza kwerendę do wyodrębnienia informacji o lotach, które są wychodzących z Seattle opóźnienia.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Dlaczego warto używać platformy Spark implementacji łącznika Azure DB rozwiązania Cosmos?

Łączenie Spark do bazy danych rozwiązania Cosmos Azure za pomocą łącznika jest zwykle w scenariuszach, gdzie:

* Chcesz użyć, Python i/lub Scala.
* Masz dużą ilość danych do transferowania Apache Spark i bazy danych Azure rozwiązania Cosmos.

Aby daje wyobrażenie poprawę wydajności zapytań, zobacz [wiki zapytania uruchomień testów](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Przykład rozproszonej agregacji
Ta sekcja zawiera przykłady związanych z wykonaniem rozproszonej agregacji i analiza za pomocą platformy Apache Spark i bazy danych Azure rozwiązania Cosmos ze sobą. Azure DB rozwiązania Cosmos już obsługuje agregacji, w którym omówiono [planety agreguje skali z bazy danych Azure rozwiązania Cosmos blogu](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Oto, jak można przenieść je do następnego poziomu z platformy Apache Spark.

Należy pamiętać, że tych agregacji są reference do [Spark do łącznika usługi Azure rozwiązania Cosmos DB notesu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Połącz lotach przykładowe dane
Te przykłady agregacji dostępu niektóre dane dotyczące wydajności transmitowane przechowywanego w naszym **DoctorWho** bazy danych z bazy danych Azure rozwiązania Cosmos. Do nawiązania połączenia, należy korzystać z poniższy fragment kodu:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Z tym fragment zamierzamy również uruchomić kwerendę podstawowej przeniesienia odfiltrowanego zbioru danych z bazy danych rozwiązania Cosmos Azure Spark gdzie drugie można wykonywać agreguje rozproszonych. W takim przypadku prosimy w lotach, które odbiegają od Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Następujące wyniki zostały wygenerowane przez uruchomienie zapytania z usługi notesu Jupyter.  Należy pamiętać, że wszystkie fragmenty kodu są ogólny i nie jest specyficzne dla usługi.

### <a name="running-limit-and-count-queries"></a>LIMIT uruchomione i liczba zapytań
Tak jak są używane do instrukcji SQL/Spark SQL, Zacznijmy **LIMIT** zapytania:

![Spark LIMIT zapytania](./media/spark-connector/spark-sql-query.png)

Zapytanie dalej jest szybkie i proste **liczba** zapytania:

![Liczba Spark zapytania](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY zapytania
W tym zestawie dalej, można łatwo uruchomić **GROUP BY** zapytania względem naszego bazy danych Azure rozwiązania Cosmos bazy danych:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Platforma Spark Grupuj według wykresu zapytania](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINCT, ORDER BY zapytania
A Oto **DISTINCT, ORDER BY** zapytania:

![Platforma Spark Grupuj według wykresu zapytania](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Kontynuować analizę dane transmitowane
Następujące przykładowe zapytania umożliwia kontynuować analizę danych transmitowane:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Najpopularniejsze 5 opóźnione miejsca docelowe (miejscowości) z Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Wykres opóźnienia top Spark](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Obliczenia średniej opóźnienia przez miast docelowych z Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Wykres opóźnienia środkowej Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie jest jeszcze Pobierz Spark do bazy danych Azure rozwiązania Cosmos łącznika z [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) repozytorium GitHub i przejrzyj dodatkowe zasoby w repozytorium:

* [Przykłady rozproszonej agregacji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notebooki](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Można także przejrzeć [Apache Spark SQL, przewodnik zestawów danych i DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) i [Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
