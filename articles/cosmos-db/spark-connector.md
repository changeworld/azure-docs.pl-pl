---
title: Platforma Apache Spark nawiązać połączenie z usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat łącznika usługi Azure Cosmos DB Spark, która umożliwia łączenie platformy Apache Spark w usłudze Azure Cosmos DB. Agregacje rozproszonych można wykonać w systemie wielodostępnym, globalnie rozproszona baza danych firmy Microsoft.
keywords: Platforma Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 4fa28e2d3f5d94d7ab47ec3b1e1e3240e5c770de
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042986"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Przyspiesz analizy danych big data przy użyciu platformy Apache Spark do łącznika usługi Azure Cosmos DB
 
Platforma Apache Spark do łącznika usługi Azure Cosmos DB umożliwia usłudze Azure Cosmos DB jako dane wejściowe lub wyjściowe zadań platformy Apache Spark. Łączenie [Spark](http://spark.apache.org/) do [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) przyspiesza rozwiązania zmieniających problemów do nauki o danych. Azure Cosmos DB umożliwia szybkie zostaną zachowane, a wykonywanie zapytań o dane. Łącznik efektywnie używa natywnych indeksów zarządzanych usługi Azure Cosmos DB. Indeksy Włącz kolumn z możliwością aktualizowania podczas wykonywania predykatu wypychania szczegółów filtrowania względem szybko zmieniających się globalnie rozproszonych danych i analizy. Tego rodzaju danych może wynosić od Internetu rzeczy (IoT) w scenariuszach analizy i analizy danych.

## <a name="connector-components"></a>Składniki łącznika

Platforma Spark może łącznika usługi Azure Cosmos DB zawiera następujące składniki:

* [Usługa Azure Cosmos DB](http://documentdb.com) pozwala aprowizować i elastycznie Skaluj przepływność i Magazyn, w dowolnej liczbie regionów geograficznych.  

* [Platforma Apache Spark](http://spark.apache.org/) to aparat przetwarzania zaawansowane "open source", zbudowane wokół szybkość i łatwość użycia i zaawansowanych możliwości analitycznych.  

* [Klaster Apache Spark w usłudze Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) pozwala na uruchamianie zadania Spark w klastrze Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Platforma Apache Spark nawiązać połączenie z usługi Azure Cosmos DB

Dostępne są dwie opcje do łączenia z platformy Apache Spark i Azure Cosmos DB:

- [Usługa Azure Cosmos DB SQL zestawu SDK języka Python](https://github.com/Azure/azure-documentdb-python), łącznik oparta na środowisku Python, który jest również nazywany *pyDocumentDB*.  

- [Usługa Azure Cosmos DB SQL zestawu Java SDK](https://github.com/Azure/azure-documentdb-java), łącznik opartych na języku Java.


**Obsługiwane wersje**

| Składnik | Wersja |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Wersja środowiska uruchomieniowego usługi Azure Databricks | > 3.4 |
| Azure Cosmos DB SQL w języku Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Połącz przy użyciu języka Python lub pakiet pyDocumentDB zestawu SDK

Na poniższym diagramie przedstawiono architekturę wdrożenia zestawu SDK pakietu pyDocumentDB:

![Diagram platformy Spark dla usługi Azure Cosmos DB przepływu danych za pośrednictwem pakietu pyDocumentDB bazy danych](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Przepływ danych

Przepływ danych w implementacji pakiet pyDocumentDB jest następująca:

* Łączy się węzeł bramy usługi Azure Cosmos DB za pomocą pakietu pyDocumentDB z węzła głównego platformy Spark. Należy określić tylko połączenia platformy Spark i Azure Cosmos DB. Połączenia z odpowiadającymi im węzłami główny i brama są niewidoczne.  

* Węzeł bramy powoduje, że zapytania względem usługi Azure Cosmos DB, gdzie później uruchomieniu zapytania względem z partycjami kolekcji w węzłów danych. Odpowiedzi na te zapytania są wysyłane z powrotem do węzła bramy, a ten zestaw wyników jest zwracany do węzła głównego platformy Spark.  

* Kolejne zapytań (na przykład rozwiązania Spark data frame) są wysyłane z węzłami procesu roboczego platformy Spark do przetwarzania.  

Komunikacja między platformami Spark i usługa Azure Cosmos DB jest ograniczona do węzła głównego do platformy Spark i węzły bramy usługi Azure Cosmos DB. Zapytania go tak szybko, jak warstwa transportu między tymi dwoma węzłami umożliwia.

Uruchom następujące kroki, aby nawiązać połączenie z platformy Spark usługi Azure Cosmos DB przy użyciu pakietu pyDocumentDB zestawu SDK:

1. Tworzenie [obszaru roboczego usługi Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) i [klastra Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Usługa Azure Databricks w wersji 4.0 środowiska uruchomieniowego zawiera Apache Spark 2.3.0 i Scala 2.11 w tym obszarze roboczym.  

2. Gdy klaster zostanie utworzony i działa, przejdź do **obszaru roboczego** > **Utwórz** > **biblioteki**.  
3. W oknie dialogowym Nowa biblioteka, wybierz **przekazywanie Egg języka Python lub PyPi** jako źródło. Podaj **pydocumentdb** jako nazwę, a następnie wybierz **zainstalować biblioteki**. Pakiet pyDocumentdb SDK została już opublikowana na pakiety pip, dzięki czemu można znaleźć i zainstalować ją. 

   ![Zrzut ekranu przedstawiający okno dialogowe nowej biblioteki](./media/spark-connector/create-library.png)

4. Po zainstalowaniu biblioteki należy dołączyć go do klastra, która została utworzona wcześniej.  

5. Przejdź do **obszaru roboczego** > **tworzenie** > **notesu**.  

6. W **tworzenie notesu** okno dialogowe, wprowadź nazwę przyjazną dla użytkownika, a następnie wybierz **Python** jako język. Z listy rozwijanej wybierz wcześniej utworzony klaster, a następnie wybierz **Utwórz**.  

7. Uruchom kilka zapytań Spark przy użyciu loty przykładowe dane hostowane w "doctorwho" konta usługi Azure Cosmos DB. (To konto jest publicznie dostępny). [Azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) repozytorium GitHub znajduje się wersja Notes w formacie HTML. Pobieranie plików repozytorium, a następnie przejdź do `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`. Można zaimportować Notes do swojego konta usługi Azure Databricks i uruchom go. W poniższej sekcji omówiono funkcje bloków kodu szczegółowo.

Poniższy fragment kodu pokazuje, jak zaimportować pakiet pyDocumentDB zestawu SDK w celu uruchomienia zapytania w kontekście aparatu Spark. Jak wspomniano we fragmencie kodu, pydocumentdb — zestaw SDK zawiera parametry połączenia wymagane do połączenia z kontem usługi Azure Cosmos DB. Importuje wymaganych bibliotek i konfiguruje klucz główny i hosta, aby utworzyć klienta usługi Azure Cosmos DB (pydocumentdb.document_client).


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

Następnie można uruchomić zapytania. Poniższy fragment kodu łączy się z kolekcją airports.codes na koncie doctorwho i uruchamia zapytanie w celu wyodrębnienia miast port lotniczy w stanie Waszyngton. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

Po uruchomieniu kwerendy, wynikiem jest "query_iterable. QueryIterable"który jest konwertowany na listę języka Python. Tej listy, z kolei jest konwertowana do rozwiązania Spark data frame. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Uwagi dotyczące korzystania z pakietu pyDocumentDB zestawu SDK
Nawiązywanie połączenia platformy Spark usługi Azure Cosmos DB przy użyciu pakietu pyDocumentDB, zestaw SDK jest zalecane w następujących scenariuszach:

* Chcesz przy użyciu języka Python.  

* Zwracany jest stosunkowo mały zestawu wyników z usługi Azure Cosmos DB do platformy Spark. Pamiętaj, że bazowego zestawu danych w usłudze Azure Cosmos DB może być dość duży i stosowanie filtrów lub działających filtrów predykatów w odniesieniu do źródła danych usługi Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Połącz przy użyciu zestawu SDK języka Java

Na poniższym diagramie przedstawiono architekturę wdrożenia zestawu Java SDK usługi Azure Cosmos DB SQL, a dane są przenoszone między węzłami procesu roboczego platformy Spark:

![Diagram przepływu danych na platformie Spark do łącznika usługi Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Przepływ danych

Przepływ danych w implementacji zestawu SDK języka Java jest następująca:

* Węzeł główny Spark łączy do węzła bramy usługi Azure Cosmos DB, aby uzyskać mapę partycji. Należy określić tylko połączenia platformy Spark i Azure Cosmos DB. Połączenia z odpowiadającymi im węzłami główny i brama są niewidoczne.  

* Te informacje są dostarczane do węzła głównego platformy Spark. W tym momencie można przeanalizować zapytania w celu ustalenia partycje i ich lokalizacji w usłudze Azure Cosmos DB, który wymagany jest dostęp do.  

* Te informacje są przesyłane do węzłów procesu roboczego platformy Spark.  

* Węzły procesu roboczego platformy Spark partycji usługi Azure Cosmos DB bezpośrednie połączenie, aby wyodrębnić i zwrócić danych z partycjami platformy Spark w węzłach procesu roboczego.  

Komunikacja między platformami Spark i usługa Azure Cosmos DB jest znacznie szybsze, ponieważ przenoszenia danych między węzłami procesu roboczego platformy Spark a węzłów danych usługi Azure Cosmos DB (partycji). W tym przykładzie możesz wysłać przykładowe dane usługi Twitter do usługi Azure Cosmos DB i uruchamianie zapytań Spark przy użyciu tych danych. Poniższe kroki umożliwiają zapisu przykładowych danych z usługi Twitter do usługi Azure Cosmos DB:

1. Tworzenie [konta interfejsu API SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) i [dodania kolekcji](create-sql-api-dotnet.md#add-a-collection) do konta.  

2. Pobierz [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) próbki z usługi GitHub. To źródło danych umożliwia pisanie przykładowych danych z usługi Twitter do usługi Azure Cosmos DB.  

3. Otwórz wiersz polecenia i zainstaluj moduły Tweepy i pyDocumentdb, uruchamiając następujące polecenia:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Wyodrębnij zawartość przykładowej kanale w serwisie Twitter, a następnie otwórz plik config.py. Zaktualizuj wartości masterKey, host, databaseId, collectionId i preferredLocations.  

5. Przejdź do `http://apps.twitter.com/`i zarejestruj kanale aplikacji w serwisie Twitter. Po wybraniu nazwy aplikacji, będziesz otrzymywać z **konsumenta, klucz tajny klienta, token dostępu i klucz tajny tokenu dostępu**. Skopiuj te wartości i aktualizować je w pliku config.py w celu udostępnienia w serwisie Twitter application programowy dostęp do usługi Twitter.   

6. Zapisz plik config.py. Otwórz wiersz polecenia i uruchom aplikację w języku Python za pomocą następującego polecenia:

   ```bash
   Python driver.py
   ```

7. Przejdź do kolekcji usługi Azure Cosmos DB w portalu i sprawdź, czy dane usługi Twitter, są zapisywane do kolekcji.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Znajdowanie i dołączyć zestawu SDK Java do klastra Spark

1. Tworzenie [obszaru roboczego usługi Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) i [klastra Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Usługa Azure Databricks w wersji 4.0 środowiska uruchomieniowego zawiera Apache Spark 2.3.0 i Scala 2.11 w tym obszarze roboczym.  

2. Gdy klaster zostanie utworzony i działa, przejdź do **obszaru roboczego** > **Utwórz** > **biblioteki**.  

3. Z **nową bibliotekę** okna dialogowego wybierz **Współrzędna Maven** jako źródło. Podaj wartość współrzędnych **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**i wybierz **Utwórz bibliotekę**. Zależności Maven są rozwiązywane i pakiet zostanie dodany do obszaru roboczego. W poprzednim formacie współrzędnych Maven 2.3.0 reprezentuje wersji platformy Spark, 2.11 reprezentuje wersji języka Scala i 1.2.0 reprezentuje wersja łącznika usługi Azure Cosmos DB. 

4. Po zainstalowaniu biblioteki należy dołączyć go do klastra, która została utworzona wcześniej. 

W tym artykule przedstawiono użycie łącznika Spark zestawu SDK języka Java w następujących scenariuszach:

* Odczyt danych z usługi Twitter z usługi Azure Cosmos DB.  

* Dane odczytu w usłudze Twitter, który przesyła strumieniowo do usługi Azure Cosmos DB.  

* Zapis danych serwisu Twitter do usługi Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Odczyt danych z usługi Twitter z usługi Azure Cosmos DB
 
W tej sekcji uruchamiasz Spark zapytania, aby odczytać partii danych z usługi Twitter z usługi Azure Cosmos DB. [Azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) repozytorium GitHub znajduje się wersja Notes w formacie HTML. Pobieranie plików repozytorium, a następnie przejdź do `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html`. Możesz zaimportować Notes do swojego konta usługi Azure Databricks i zaktualizuj konto identyfikatora URI, klucz główny, bazy danych i nazwy kolekcji. Można uruchomić Notes, lub utwórz go w następujący sposób:

1. Przejdź na swoje konto usługi Azure Databricks, a następnie wybierz pozycję **obszaru roboczego** > **Utwórz** > **notesu**. 

2. W **tworzenie notesu** okno dialogowe, wprowadź nazwę przyjazną dla użytkownika, a następnie wybierz **Python** jako język. Z listy rozwijanej wybierz wcześniej utworzony klaster, a następnie wybierz **Utwórz**.  

3. Zaktualizuj punkt końcowy, klucz główny, bazy danych i kolekcji wartości, aby połączyć się z kontem. Odczytywanie tweetów za pomocą polecenia spark.read.format().

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Uruchom zapytanie, aby uzyskać liczbę tweety za pomocą różnych hasztagi z pamięci podręcznej danych. 

   ```python
   %sql
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

Zestaw SDK platformy Java obsługuje następujące wartości dla mapowania konfiguracji: 

|Ustawienie  |Opis  |
|---------|---------|
|query_maxdegreeofparallelism  | Ustawia liczbę równoczesnych operacji wykonywania po stronie klienta podczas równoległego wykonywania zapytań. Jeśli jest ustawiona na wartość, która jest większa niż 0, ogranicza liczbę jednoczesnych operacji przypisanej wartości. Jeśli jest ustawiona na wartość mniejszą niż 0, system automatycznie decyduje, liczby równoczesnych operacji do uruchomienia. Ponieważ łącznik jest mapowany każda partycja kolekcji za pomocą funkcji wykonawczej, ta wartość będzie mieć żadnego efektu o nieudanej operacji odczytu.        |
|query_maxbuffereditemcount     |    Ustawia maksymalną liczbę elementów, które mogą być buforowane po stronie klienta podczas równoległego wykonywania zapytań. Jeśli jest ustawiona na wartość, która jest większa niż 0, ogranicza liczbę elementów, które buforowanego mają przypisaną wartością. Jeśli jest ustawiona na wartość mniejszą niż 0, system automatycznie decyduje o liczbę elementów do buforu.     |
|query_enablescan    |   Ustawia opcję, aby włączyć skanowanie w zapytania, które nie może być obsługiwana, ponieważ indeksowanie został wyłączony w żądanej ścieżki.       |
|query_disableruperminuteusage  |  Wyłącza żądania jednostek (ru) / wyczerpaniu minuty przygotowanie wydajności niezbędnej do obsługi zapytań, jeśli zwykłe jednostek żądań na sekundę.       |
|query_emitverbosetraces   |   Ustawia opcję, aby umożliwić zapytania, aby emitować się pełne dane śledzenia dla badania.      |
|query_pagesize  |   Ustawia rozmiar strony wyników kwerendy dla każdego żądania zapytania. W celu zoptymalizowania przepływności, należy użyć dużego rozmiaru strony w celu zmniejszenia liczby rund do pobierania wyników.      |
|query_custom  |  Ustawia zapytanie usługi Azure Cosmos DB, aby zastąpić domyślne zapytanie podczas pobierania danych z usługi Azure Cosmos DB. Należy pamiętać, podając tę wartość, jest używany zamiast zapytanie o przekazana predykatów także.     |

W zależności od scenariusza należy używać wartości z innej konfiguracji, aby zoptymalizować wydajność i przepływność. Należy pamiętać, że klucz konfiguracji jest obecnie bez uwzględniania wielkości liter, a wartość konfiguracji to zawsze ciąg.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Twitter odczytu danych, który przesyła strumieniowo do usługi Azure Cosmos DB

W tej sekcji uruchamiasz Spark zapytań na odczytywanie zestawienia zmian usługi Twitter danych przesyłanych strumieniowo. Podczas działania zapytania w tej sekcji, upewnij się, że źródło danych aplikacji w usłudze Twitter działa i przekazywanie danych do usługi Azure Cosmos DB. [Azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) repozytorium GitHub znajduje się wersja Notes w formacie HTML. Pobieranie plików repozytorium, a następnie przejdź do `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`. Możesz zaimportować Notes do swojego konta usługi Azure Databricks i zaktualizuj konto identyfikatora URI, klucz główny, bazy danych i nazwy kolekcji. Można uruchomić Notes, lub utwórz go w następujący sposób:

1. Przejdź na swoje konto usługi Azure Databricks, a następnie wybierz pozycję **obszaru roboczego** > **Utwórz** > **notesu**.  

2. W **tworzenie notesu** okno dialogowe, wprowadź nazwę przyjazną dla użytkownika, a następnie wybierz **Scala** jako język. Z listy rozwijanej wybierz wcześniej utworzony klaster, a następnie wybierz **Utwórz**.  

3. Zaktualizuj punkt końcowy, klucz główny, bazy danych i kolekcji wartości, aby połączyć się z kontem.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Rozpocznij odczytywanie zmian źródła danych jako strumienia za pomocą polecenia spark.readStream.format():

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Rozpocznij przesyłanie strumieniowe zapytania do konsoli:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Zestaw SDK platformy Java obsługuje następujące wartości dla mapowania konfiguracji:

|Ustawienie  |Opis  |
|---------|---------|
|readchangefeed   |  Wskazuje, że zawartość kolekcji jest pobierana z kanału informacyjnego zmian bazy danych cosmos DB. Wartość domyślna to false.       |
|changefeedqueryname |   Niestandardowy ciąg do identyfikowania zapytania. Łącznik przechowuje informacje o kolekcji tokenów kontynuacji dla różnych zmian źródła danych zapytania oddzielnie. Jeśli readchangefeed ma wartość true, to wymagana konfiguracja, który nie może przyjąć wartość pustą.      |
|changefeedcheckpointlocation  |   Ścieżka do lokalnego magazynu plików do utrwalenia tokenów kontynuacji w przypadku awarii węzła.      |
|changefeedstartfromthebeginning  |  Określa, czy kanał informacyjny zmian należy rozpocząć od początku (PRAWDA) lub z bieżącego punktu (false). Domyślnie rozpoczyna się od bieżącego (false).       |
|rollingchangefeed  |   Wartość logiczna wskazująca, czy kanału informacyjnego zmian powinna być z ostatnich zapytania. Wartość domyślna to false, co oznacza, że zmiany są liczone od pierwszego odczytu kolekcji.      |
|changefeedusenexttoken  |   Wartość logiczna służąca do obsługi scenariuszy niepowodzenia przetwarzania. Oznacza to, że bieżąca zmiana źródła danych usługi batch został obsłużony bez problemu zmieniała. Zestaw Resiliant rozproszonych danych należy używać dalej tokenów kontynuacji można pobrać partii kolejnych zmian.      |
| InferStreamSchema | Wartość logiczna wskazująca, czy schemat danych przesyłanych strumieniowo próbkowania na początku strumienia. Domyślnie ta wartość jest ustawiona na wartość true. Jeśli ten parametr ma wartość true, a dane przesyłane strumieniowo zmiany schematu, po próbkowania danych, właściwości nowo dodanych zostanie porzucony w ramce danych przesyłania strumieniowego. <br/><br/> Jeśli chcesz, aby ramki danych przesyłania strumieniowego jako niezależnej od schematu, ustaw ten parametr na wartość false. W tym trybie treści dokumentów odczytywanie zestawienia zmian usługi Azure Cosmos DB są opakowane w właściwości treści. Ta właściwość jest wynikowe przesyłania strumieniowego danych ramki, oprócz wartości właściwości systemu.
 |

### <a name="connection-settings"></a>Ustawienia połączenia

Zestaw SDK platformy Java obsługuje poniższych ustawień połączenia:

|Ustawienie  |Opis  |
|---------|---------|
|connectionmode   |  Ustawia tryb połączenia, która wewnętrznego obiektu DocumentClient powinna być używana do komunikacji z usługą Azure Cosmos DB. Dozwolone wartości to **DirectHttps** (wartość domyślna) i **bramy**. Tryb połączenia DirectHttps przekierowywać żądania bezpośrednio do partycji bazy danych cosmos DB i zapewnia pewne korzyści opóźnienia.       |
|connectionmaxpoolsize   |  Ustawia wartość rozmiaru puli połączeń, używanego przez wewnętrzny DocumentClient. Wartość domyślna to 100.       |
|connectionidletimeout  |  Ustawia wartość limitu czasu w sekundach dla bezczynnych połączeń. Wartość domyślna to 60.       |
|query_maxretryattemptsonthrottledrequests    |  Ustawia maksymalną liczbę ponownych prób. Będzie ona używana w razie awarii żądania z powodu ograniczania na kliencie szybkości. Jeśli nie zostanie określony, wartość domyślna to 1000 ponownych prób.       |
|query_maxretrywaittimeinseconds   |  Ustawia maksymalny czas ponawiania w sekundach. Domyślnie jest 1 000 sekund.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Zapis danych serwisu Twitter do usługi Azure Cosmos DB 

W tej sekcji uruchamiasz Spark zapytania, aby zapisać wsad danych serwisu Twitter do nowej kolekcji w tej samej bazy danych. [Azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) repozytorium GitHub znajduje się wersja Notes w formacie HTML. Pobieranie plików repozytorium, a następnie przejdź do `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`. Możesz zaimportować Notes do swojego konta usługi Azure Databricks i zaktualizuj konto identyfikatora URI, klucz główny, bazy danych i nazwy kolekcji. Można uruchomić Notes, lub utwórz go w następujący sposób:

1. Przejdź na swoje konto usługi Azure Databricks, a następnie wybierz pozycję **obszaru roboczego** > **Utwórz** > **notesu**.  

2. W **tworzenie notesu** okno dialogowe, wprowadź nazwę przyjazną dla użytkownika, a następnie wybierz **Scala** jako język. Z listy rozwijanej wybierz wcześniej utworzony klaster, a następnie wybierz **Utwórz**.  

3. Zaktualizuj punkt końcowy, klucz główny, bazy danych i kolekcji wartości, aby nawiązać połączenie z kolekcji bazy danych do odczytu i zapisu danych z usługi Twitter.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Uruchom zapytanie, aby uzyskać liczbę tweety za pomocą różnych hasztagi z pamięci podręcznej danych. 

   ```scala
   %sql
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

5. Utwórz nową ramkę danych tagów tweetów i zapisać dane do nowej kolekcji. Po uruchomieniu następujący kod, można powrócić do portalu i sprawdź, czy dane są zapisywane do kolekcji. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Zestaw SDK platformy Java obsługuje następujące wartości dla mapowania konfiguracji:

|Ustawienie  |Opis  |
|---------|---------|
| Elementów BulkImport | Wartość logiczna wskazująca, czy przy użyciu biblioteki BulkExecutor można zaimportować danych. Domyślnie ta wartość jest ustawiona na wartość true. |
|WritingBatchSize  |   Wskazuje rozmiar partii do użycia podczas zapisywania danych zawartości do kolekcji usługi Azure Cosmos DB. <br/><br/> Jeśli elementów BulkImport parametr ma wartość true, a następnie WritingBatchSize parametr wskazuje rozmiar partii podana jako dane wejściowe na wartość importAll API Biblioteka BulkExecutor dokumentów. Domyślnie ta wartość jest równa 100 tys. <br/><br/> Jeśli parametr elementów BulkImport jest ustawiony na wartość false, parametr WritingBatchSize wskazuje rozmiar partii do użycia podczas pisania w kolekcji usługi Azure Cosmos DB. Łącznik wysyła żądania createDocument i upsertDocument asynchronicznie w usłudze batch. Im większy rozmiar partii, więcej przepływności, który można osiągnąć, tak długo, jak zasoby klastra są dostępne. Z drugiej strony Określ numer mniejszego rozmiaru partii do ograniczania szybkości i zużycia jednostek RU. Domyślnie zapisywanie rozmiar partii wynosi 500.  |
|UPSERT   |  Ciąg, wartość logiczna wskazująca, czy ma być używany zamiast CreateDocument upsertDocument, gdy tworzysz do kolekcji usługi Azure Cosmos DB.   |
| WriteThroughputBudget |  Ciąg będący liczbą całkowitą reprezentującą liczbę RU\s, którą chcesz przydzielić do zbiorczego pozyskiwania zadania Spark poza łącznej przepływności przydzielone do kolekcji. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Zapis danych z usługi Twitter, który przesyła strumieniowo do usługi Azure Cosmos DB 

W tej sekcji uruchamiasz Spark zapytania, aby zapisać kanał informacyjny zmian danych usługi Twitter do nowej kolekcji w tej samej bazy danych przesyłanych strumieniowo. [Azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) repozytorium GitHub znajduje się wersja Notes w formacie HTML. Pobieranie plików repozytorium, a następnie przejdź do `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`. Możesz zaimportować Notes do swojego konta usługi Azure Databricks i zaktualizuj konto identyfikatora URI, klucz główny, bazy danych i nazwy kolekcji. Można uruchomić Notes, lub utwórz go w następujący sposób:

1. Przejdź na swoje konto usługi Azure Databricks, a następnie wybierz pozycję **obszaru roboczego** > **Utwórz** > **notesu**.  

2. W **tworzenie notesu** okno dialogowe, wprowadź nazwę przyjazną dla użytkownika, a następnie wybierz **Scala** jako język. Z listy rozwijanej wybierz wcześniej utworzony klaster, a następnie wybierz **Utwórz**.  

3. Zaktualizuj punkt końcowy, klucz główny, bazy danych i kolekcji wartości, aby nawiązać połączenie z kolekcji bazy danych do odczytu i zapisu danych z usługi Twitter.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Rozpocznij odczytywanie zmian źródła danych jako strumienia za pomocą polecenia spark.readStream.format():
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Definiowanie konfiguracji kolekcji docelowej, a następnie uruchomić zadanie przesyłania strumieniowego przy użyciu metody writeStream.format():

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Zestaw SDK platformy Java obsługuje następujące wartości dla mapowania konfiguracji:

|Ustawienie  |Opis  |
|---------|---------|
|UPSERT   |  Ciąg, wartość logiczna wskazująca, czy ma być używany zamiast CreateDocument upsertDocument, gdy tworzysz do kolekcji usługi Azure Cosmos DB.   |
|checkpointlocation  |   Ścieżka do lokalnego magazynu plików do utrwalenia tokenów kontynuacji w przypadku awarii węzła.   |
|WritingBatchSize  |  Wskazuje rozmiar partii do użycia podczas zapisywania danych do kolekcji usługi Azure Cosmos DB. Łącznik wysyła żądania createDocument i upsertDocument asynchronicznie w usłudze batch. Im większy rozmiar partii, więcej przepływności, który można osiągnąć, tak długo, jak zasoby klastra są dostępne. Z drugiej strony Określ numer mniejszego rozmiaru partii do ograniczania szybkości i zużycia jednostek RU. Domyślnie zapisywanie rozmiar partii wynosi 500.  |


### <a name="considerations-when-using-java-sdk"></a>Uwagi dotyczące korzystania z zestawu Java SDK

Nawiązywanie połączenia z platformy Spark usługi Azure Cosmos DB przy użyciu zestawu SDK języka Java jest zalecane w następujących scenariuszach:

* Chcesz używać języka Python i Scala.  

* Masz dużą ilość danych do transferu między platformami Apache Spark i usługi Azure Cosmos DB. Zestaw SDK Java działa lepiej niż pakiet pyDocumentDB. Aby uzyskać więcej informacji na temat różnic w wydajności zapytania, zobacz [wiki przebiegi testowe zapytanie](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze nie, Pobierz platformy Spark do łącznika usługi Azure Cosmos DB z [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) repozytorium GitHub. Zapoznaj się z następujących zasobach dodatkowych: w repozytorium:

* [Przykłady agregacji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesów](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Możesz również przejrzeć [Apache Spark SQL, przewodnik zestawy danych i elementy Dataframe](http://spark.apache.org/docs/latest/sql-programming-guide.html)i [platformy Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
