---
title: Użyj zbiorczej biblioteki java executor w usłudze Azure Cosmos DB do wykonywania operacji importowania zbiorczego i aktualizacji
description: Zbiorcze importowanie i aktualizowanie dokumentów usługi Azure Cosmos DB przy użyciu zbiorczej biblioteki java wykonawca
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: bf2a2385b3129ddf24ede7f6d851701186b0e33c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445712"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Wykonywanie operacji zbiorczych w usłudze Azure Cosmos DB przy użyciu biblioteki funkcji wykonawczej Java operacji zbiorczych

Ten samouczek zawiera instrukcje dotyczące importowania i aktualizowania dokumentów usługi Azure Cosmos DB w celu zaimportowania i aktualizowania dokumentów usługi Azure Cosmos DB. Aby dowiedzieć się więcej o zbiorczej bibliotece executor i jak pomaga wykorzystać ogromną przepływność i magazyn, zobacz [zbiorczy artykuł omówienia biblioteki executora.](bulk-executor-overview.md) W tym samouczku tworzysz aplikację Java, która generuje losowe dokumenty i są one zbiorczo importowane do kontenera usługi Azure Cosmos. Po zaimportowaniu można zbiorczo zaktualizować niektóre właściwości dokumentu. 

Obecnie biblioteka zbiorczego executora jest obsługiwana tylko przez konta interfejsu API SQL usługi Azure Cosmos DB i interfejsu API Gremlin. W tym artykule opisano sposób używania zbiorczej biblioteki java executor z kontami interfejsu API SQL. Aby dowiedzieć się więcej na temat używania zbiorczej biblioteki .NET w interfejsie API gremlin, zobacz [wykonywanie operacji zbiorczych w interfejsie API gremlin usługi Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.  

* Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez bezpłatnej subskrypcji platformy Azure i zobowiązań. Lub można użyć [emulatora usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z punktem `https://localhost:8081` końcowym. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticating-requests).  

* [Zestaw java development (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.  

  - Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.

* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne [Maven](https://maven.apache.org/)  
  
  - W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.

* Utwórz konto interfejsu API SQL usługi Azure Cosmos DB przy użyciu kroków opisanych w sekcji [tworzenia konta bazy danych](create-sql-api-java.md#create-a-database-account) w artykule Szybki start języka Java.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem, pobierając przykładową aplikację Java z GitHub. Ta aplikacja wykonuje operacje zbiorcze na danych usługi Azure Cosmos DB. Aby sklonować aplikację, otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz skopiować aplikację, i uruchom następujące polecenie:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Sklonowane repozytorium zawiera dwie przykłady "bulkimport" i "bulkupdate" względem folderu "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikacja "bulkimport" generuje losowe dokumenty i importuje je do usługi Azure Cosmos DB. Aplikacja "bulkupdate" aktualizuje niektóre dokumenty w usłudze Azure Cosmos DB. W następnych sekcjach przejrzymy kod w każdej z tych przykładowych aplikacji. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Zbiorcze importowanie danych do usługi Azure Cosmos DB

1. Parametry połączenia usługi Azure Cosmos DB są odczytywane jako argumenty i przypisywane do zmiennych zdefiniowanych w pliku CmdLineConfiguration.java.  

2. Następnie obiekt DocumentClient jest inicjowany przy użyciu następujących instrukcji:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Obiekt DocumentBulkExecutor jest inicjowany z wysokimi wartościami ponawiania dla czasu oczekiwania i ograniczonych żądań. A następnie są one ustawione na 0, aby przekazać kontrolę przeciążenia do DocumentBulkExecutor przez cały okres jego istnienia.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Wywołanie importAll API, który generuje losowe dokumenty do zbiorczego importu do kontenera usługi Azure Cosmos. Konfiguracje wiersza polecenia można skonfigurować w pliku CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Interfejs API importu zbiorczego akceptuje kolekcję dokumentów seryjnych JSON i ma następującą składnię, aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoda importAll akceptuje następujące parametry:
 
   |**Parametr**  |**Opis**  |
   |---------|---------|
   |isUpsert (isUpsert)    |   Flaga umożliwiająca upsert dokumentów. Jeśli dokument o podanym identyfikatorze już istnieje, jest aktualizowany.  |
   |disableAutomaticIdGeneration     |   Flaga wyłączania automatycznego generowania identyfikatora. Domyślnie jest ustawiona na true.   |
   |maxConcurrencyPerPartitionRange    |  Maksymalny stopień współbieżności na zakres klucza partycji. Wartość domyślna to 20.  |

   **Definicja obiektu odpowiedzi zbiorczej na import** Wynik wywołania interfejsu API importu zbiorczego zawiera następujące metody get:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z dokumentów dostarczonych do wywołania interfejsu API importu zbiorczego.      |
   |podwójne getTotalRequestUnitsConsumed()   |  Łączna liczba jednostek żądań (RU) zużytych przez wywołanie interfejsu API importu zbiorczego.       |
   |Czas trwania getTotalTimeTaken()   |    Całkowity czas wykonany przez wywołanie interfejsu API importu zbiorczego, aby zakończyć wykonanie.     |
   |Lista\<wyjątków> getErrors() |  Pobiera listę błędów, jeśli niektóre dokumenty z partii dostarczone do wywołania interfejsu API importu zbiorczego nie można wstawić.       |
   |Lista\<obiektów> getBadInputDocuments()  |    Lista dokumentów w złym formacie, które nie zostały pomyślnie zaimportowane w wywołaniu interfejsu API importu zbiorczego. Użytkownik powinien naprawić zwrócone dokumenty i ponowić próbę importu. Dokumenty w złym formacie obejmują dokumenty, których wartość identyfikatora nie jest ciągiem znaków (null lub inny typ danych jest uznawany za nieprawidłowy).     |

5. Po przygotowaniu aplikacji importu zbiorczego skompiluj narzędzie wiersza polecenia ze źródła za pomocą polecenia "mvn clean package". To polecenie generuje plik jar w folderze docelowym:  

   ```java
   mvn clean package
   ```

6. Po wygenerowaniu zależności docelowych można wywołać aplikację importera zbiorczego za pomocą następującego polecenia:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Importer zbiorczy tworzy nową bazę danych i kolekcję z nazwą bazy danych, nazwą kolekcji i wartościami przepływności określonymi w pliku App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcze dane aktualizacji w usłudze Azure Cosmos DB

Istniejące dokumenty można zaktualizować za pomocą interfejsu API BulkUpdateAsync. W tym przykładzie ustawisz pole Nazwa na nową wartość i usuniesz pole Opis z istniejących dokumentów. Aby uzyskać pełny zestaw obsługiwanych operacji aktualizacji pola, zobacz [dokumentację interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definiuje elementy aktualizacji wraz z odpowiednimi operacjami aktualizacji pola. W tym przykładzie użyjesz SetUpdateOperation, aby zaktualizować pole Nazwa i UnsetUpdateOperation, aby usunąć pole Opis ze wszystkich dokumentów. Można również wykonywać inne operacje, takie jak zwiększanie pola dokumentu o określoną wartość, wypychanie określonych wartości do pola tablicy lub usuwanie określonej wartości z pola tablicy. Aby dowiedzieć się więcej o różnych metodach dostarczanych przez interfejs API aktualizacji zbiorczej, zobacz [dokumentację interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Wywołanie updateAll API, który generuje losowe dokumenty, które mają być następnie zbiorczo importowane do kontenera usługi Azure Cosmos. Konfiguracje wiersza polecenia można skonfigurować w pliku CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Interfejs API aktualizacji zbiorczej akceptuje kolekcję elementów, które mają zostać zaktualizowane. Każdy element aktualizacji określa listę operacji aktualizacji pola, które mają być wykonywane w dokumencie oznaczonym identyfikatorem i wartością klucza partycji. aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll Metoda akceptuje następujące parametry:

   |**Parametr** |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maksymalny stopień współbieżności na zakres klucza partycji. Wartość domyślna to 20.  |
 
   **Definicja obiektu odpowiedzi zbiorczej na import** Wynik wywołania interfejsu API importu zbiorczego zawiera następujące metody get:

   |**Parametr** |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane z dokumentów dostarczonych do wywołania interfejsu API aktualizacji zbiorczej.      |
   |podwójne getTotalRequestUnitsConsumed() |  Łączna liczba jednostek żądań (RU) zużytych przez wywołanie interfejsu API aktualizacji zbiorczej.       |
   |Czas trwania getTotalTimeTaken()  |   Całkowity czas wykonany przez wywołanie interfejsu API aktualizacji zbiorczej w celu ukończenia wykonania.      |
   |Lista\<wyjątków> getErrors()   |    Pobiera listę błędów, jeśli niektóre dokumenty z partii dostarczone do wywołania interfejsu API aktualizacji zbiorczej nie można wstawić.      |

3. Po przygotowaniu aplikacji aktualizacji zbiorczej skompiluj narzędzie wiersza polecenia ze źródła za pomocą polecenia "mvn clean package". To polecenie generuje plik jar w folderze docelowym:  

   ```
   mvn clean package
   ```

4. Po wygenerowaniu zależności docelowych można wywołać aplikację aktualizacji zbiorczej za pomocą następującego polecenia:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Porady dotyczące wydajności 

Należy wziąć pod uwagę następujące punkty dla lepszej wydajności podczas korzystania z biblioteki wykonawca luzem:

* Aby uzyskać najlepszą wydajność, uruchom aplikację z maszyny Wirtualnej platformy Azure w tym samym regionie co region zapisu konta usługi Cosmos DB.  
* Aby osiągnąć wyższą przepustowość:  

   * Ustaw rozmiar sterty JVM na wystarczająco dużą liczbę, aby uniknąć problemów z pamięcią podczas obsługi dużej liczby dokumentów. Sugerowany rozmiar sterty: max(3GB, 3 * sizeof (wszystkie dokumenty przekazywane do interfejsu API importu zbiorczego w jednej partii)).  
   * Istnieje czas wstępnego przetwarzania, dzięki czemu otrzymasz wyższą przepływność podczas wykonywania operacji zbiorczych z dużą liczbą dokumentów. Tak więc, jeśli chcesz zaimportować 10 000 000 dokumentów, uruchamianie importu zbiorczego 10 razy na 10 większości dokumentów każdego rozmiaru 1 000 000 jest lepsze niż uruchamianie importu zbiorczego 100 razy na 100 większości dokumentów o rozmiarze 100 000 dokumentów.  

* Zaleca się wystąpienie pojedynczego obiektu DocumentBulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej, która odpowiada określonemu kontenerowi usługi Azure Cosmos.  

* Ponieważ wykonanie interfejsu API pojedynczej operacji zbiorczej zużywa duży fragment procesora CPU i we/wy komputera klienckiego. Dzieje się tak poprzez tarło wielu zadań wewnętrznie, uniknąć tarło wiele równoczesnych zadań w ramach procesu aplikacji każdego wykonywania wywołań interfejsu API operacji zbiorczej. Jeśli wywołanie interfejsu API pojedynczej operacji zbiorczej uruchomione na jednej maszynie wirtualnej nie może zużywać przepływności całego kontenera (jeśli przepływność kontenera > 1 mln jednostek RU/s), zaleca się utworzenie oddzielnych maszyn wirtualnych do równoczesnych wykonywania wywołań interfejsu API operacji zbiorczych.

    
## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o szczegółach pakietu maven i informacjach o wydaniu biblioteki Java, zobacz[szczegóły zestawu SDK wykonawcy zbiorczego.](sql-api-sdk-bulk-executor-java.md)


