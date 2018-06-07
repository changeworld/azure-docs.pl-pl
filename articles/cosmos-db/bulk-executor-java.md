---
title: Za pomocą języka BulkExecutor Java biblioteki do wykonywania operacji zbiorczej w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Biblioteka języka BulkExecutor Java DB rozwiązania Cosmos Azure umożliwia zbiorczego importu i aktualizowanie dokumentów do bazy danych Azure rozwiązania Cosmos kolekcji.
keywords: Moduł wykonujący zbiorczego języka Java
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 77135ec5f62636d1dd634361da345b00d98ad918
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610246"
---
# <a name="use-bulkexecutor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Biblioteka BulkExecutor Java służy do wykonywania operacji zbiorczych danych bazy danych Azure rozwiązania Cosmos

W tym samouczku instrukcje na temat używania biblioteka języka Java Moduł wykonujący zbiorczego Azure DB rozwiązania Cosmos do importowania i aktualizowania dokumentów bazy danych Azure rozwiązania Cosmos. Aby dowiedzieć się więcej o BulkExecutor biblioteki i jej można wykorzystać ogromnej przepływności i magazynu, zobacz [Przegląd biblioteki BulkExecutor](bulk-executor-overview.md) artykułu. W tym samouczku tworzenia aplikacji Java, która generuje losowe dokumentów i są importowane do bazy danych Azure rozwiązania Cosmos kolekcji zbiorczego. Po zaimportowaniu, będzie zbiorczego zaktualizować niektóre właściwości dokumentu. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).  

* Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań. Można użyć [Azure rozwiązania Cosmos DB emulatora](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z `https://localhost:8081` identyfikatora URI. Klucz podstawowy znajduje się w [uwierzytelniania żądań](local-emulator.md#authenticating-requests).  

* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
  - W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.  

  - Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.

* [Pobierz](http://maven.apache.org/download.cgi) i [zainstaluj](http://maven.apache.org/install.html) archiwum binarne [Maven](http://maven.apache.org/)  
  
  - W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.

* Utwórz konto interfejsu API Azure rozwiązania Cosmos bazy danych SQL przy użyciu procedury opisanej w [Tworzenie konta bazy danych](create-sql-api-java.md#create-a-database-account) artykułu szybkiego startu Java.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz załóżmy przełączyć się do pracy z kodem, Pobierz dokument zawierający przykładową aplikację Java z usługi GitHub. Ta aplikacja wykonuje operacje zbiorcze danych bazy danych Azure rozwiązania Cosmos. Klonowanie aplikacji, otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz skopiować aplikację i uruchom następujące polecenie:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Sklonowanego repozytorium zawiera dwa przykłady "elementów bulkimport" i "bulkupdate" odnoszącego się do folderu "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikacja "elementów bulkimport" generuje losowe dokumentów i importowane do bazy danych Azure rozwiązania Cosmos. Aplikacja "bulkupdate" aktualizuje niektórych dokumentów w usłudze Azure DB rozwiązania Cosmos. W kolejnych sekcjach Zapoznamy się z kodu w każdym z tych przykładowych aplikacji. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Zbiorcze importowanie danych do bazy danych Azure rozwiązania Cosmos

1. Parametry połączenia w usłudze Azure DB rozwiązania Cosmos jako argumenty do odczytu i przypisane do zmienne zdefiniowane w pliku CmdLineConfiguration.java.  

2. Obok obiektu DocumentClient została zainicjowana przy użyciu następujących instrukcji:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Obiekt DocumentBulkExecutor jest inicjowany z wartościami wysokiej ponownych prób dla czas oczekiwania i ograniczenie żądań. A następnie są one ustawione na 0 do przekazania kontroli przeciążenia DocumentBulkExecutor jego czas ich istnienia.  

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
     offerThroughput) // throughput you want to allocate for bulk import out of the collection's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. Wywołanie wartość importAll interfejsu API, który generuje losowe dokumentów do zbiorczego importu do kolekcji bazy danych Azure rozwiązania Cosmos. Można skonfigurować w pliku CmdLineConfiguration.java konfiguracje wiersza polecenia.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   Interfejs API importowania zbiorczego akceptuje kolekcji serializacji JSON dokumentów i składa się z następującej składni, aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoda wartość importAll przyjmuje następujące parametry:
 
   |**Parametr**  |**Opis**  |
   |---------|---------|
   |isUpsert    |   Flaga włączenia upsert dokumentów. Jeśli dokument o podanym identyfikatorze już istnieje, jest on aktualizowany.  |
   |disableAutomaticIdGeneration     |   Flagi, aby wyłączyć automatyczne generowanie identyfikatora. Domyślnie jest ustawiona na true.   |
   |maxConcurrencyPerPartitionRange    |  Maksymalny stopień współbieżności na zakresem kluczy partycji. Wartość domyślna to 20.  |

   **Definicja obiektu odpowiedzi importowania zbiorczego** następujące metody get zawiera wynik importowania zbiorczego wywołanie interfejsu API:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane poza dokumentów do zbiorczego zaimportować wywołanie interfejsu API.      |
   |Podwójna getTotalRequestUnitsConsumed()   |  Jednostki całkowita liczba żądań (RU) używane przez większość zaimportować wywołanie interfejsu API.       |
   |Czas trwania getTotalTimeTaken()   |    Całkowity czas wykonywania za pomocą importowania zbiorczego wywołanie interfejsu API, aby ukończyć wykonywania.     |
   |Lista<Exception> getErrors() |  Pobiera listę błędów niektórych dokumentów poza partii dostarczony do zbiorczego importu nie można pobrać dodaje wywołanie interfejsu API.       |
   |Lista<Object> getBadInputDocuments()  |    Lista zły format dokumentów, które nie zostały pomyślnie zaimportowane w zbiorczego importowanie wywołanie interfejsu API. Użytkownik powinien Usuń dokumenty zwrócone i spróbuj ponownie import. Sformatowany zły dokumenty zawierają dokumentów, których wartość Identyfikatora nie jest ciąg (wartość null lub jakikolwiek inny typ danych jest uznawane za nieprawidłowe).     |

5. Po utworzeniu zbiorczego importowania aplikacji gotowych kompilacji narzędzia wiersza polecenia ze źródła za pomocą polecenia "mvn czystą pakietu". To polecenie generuje plik jar w folderze docelowym:  

   ```java
   mvn clean package
   ```

6. Po wygenerowaniu zależności docelowych, można wywołać aplikacji importera zbiorczo za pomocą następującego polecenia:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Importer zbiorczego tworzy nową bazę danych i kolekcję o nazwie bazy danych, nazwę kolekcji i przepływności wartości określone w pliku App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcze danych aktualizacji w usłudze Azure DB rozwiązania Cosmos

Należy zaktualizować istniejące dokumenty przy użyciu interfejsu API BulkUpdateAsync. W tym przykładzie zostanie Ustaw pole nazwy na nową wartość i usunąć pola Opis z istniejących dokumentów. Pełny zestaw pól obsługiwane operacje aktualizacji, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definiuje elementy aktualizacji wraz z odpowiednich operacji aktualizowania pola. W tym przykładzie użyjesz SetUpdateOperation można zaktualizować pola nazwy i UnsetUpdateOperation, aby usunąć pole opisu z wszystkie dokumenty. Można również wykonywać innych operacji, takich jak przyrostu pole dokumentu przez określoną wartość, push określone wartości do pola tablicy lub usuń określoną wartość z pola tablicy. Informacje na temat różnych metod udostępniane przez interfejs API aktualizacji zbiorczej, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Wywołanie updateAll interfejsu API, który generuje losowe dokumentów można następnie zbiorczego importowane do bazy danych Azure rozwiązania Cosmos kolekcji. Można skonfigurować konfiguracje wiersza polecenia do przekazania pliku CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Interfejs API aktualizacji zbiorczej akceptuje kolekcję elementów do zaktualizowania. Każdy element aktualizacji określa listę operacje aktualizacji pola do wykonania w dokumencie identyfikowany przez identyfikator i wartość klucza partycji. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll — metoda przyjmuje następujące parametry:

   |**Parametr** |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maksymalny stopień współbieżności na zakresem kluczy partycji. Wartość domyślna to 20.  |
 
   **Definicja obiektu odpowiedzi importowania zbiorczego** następujące metody get zawiera wynik importowania zbiorczego wywołanie interfejsu API:

   |**Parametr** |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane poza dokumentów do wywołania interfejsu API aktualizacji zbiorczej.      |
   |Podwójna getTotalRequestUnitsConsumed() |  Całkowita liczba jednostek żądania (RU) używane przez aktualizację zbiorczą wywołanie interfejsu API.       |
   |Czas trwania getTotalTimeTaken()  |   Łączny czas zbiorczego aktualizacji wywołanie interfejsu API, aby ukończyć wykonywania.      |
   |Lista<Exception> getErrors()   |     Pobiera listę błędów, jeśli niektórych dokumentów poza partii dostarczony do wywołania interfejsu API zbiorcze aktualizacji nie można pobrać wstawiony.      |

3. Po utworzeniu zbiorczego aktualizacji aplikacji gotowych kompilacji narzędzia wiersza polecenia ze źródła za pomocą polecenia "mvn czystą pakietu". To polecenie generuje plik jar w folderze docelowym:  

   ```
   mvn clean package
   ```

4. Po wygenerowaniu zależności docelowych aplikacji aktualizacji zbiorczej można wywołać za pomocą następującego polecenia:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Porady dotyczące wydajności 

Należy wziąć pod uwagę następujące kwestie w celu poprawy wydajności podczas korzystania z biblioteki BulkExecutor:

* Aby uzyskać najlepszą wydajność uruchomienie aplikacji z maszyny Wirtualnej platformy Azure, w tym samym regionie co regionu zapisu konta DB rozwiązania Cosmos.  
* Do osiągnięcia wyższej przepustowości:  

   * Ustaw rozmiar sterty JVM wystarczająco dużą liczbą w celu uniknięcia jakikolwiek problem pamięci podczas obsługi dużej liczby dokumentów. Sugerowany rozmiar sterty: max (3GB, 3 * sizeof (wszystkie dokumenty przekazany do zbiorczego importowanie interfejsu API w jednym zadaniu wsadowym)).  
   * Brak czasu przetwarzania wstępnego, z powodu którego otrzymasz wyższej przepustowości podczas wykonywania operacji zbiorczej z dużą liczbą dokumentów. Tak Jeśli chcesz zaimportować 10 000 000 dokumentów, uruchamianie importowania zbiorczego 10 razy na 10 zbiorczego dokumentów poszczególnych o rozmiarze 1 000 000 jest lepiej niż uruchamianie importowania zbiorczego 100 razy na 100 zbiorczego dokumentów poszczególnych rozmiar 100 000 dokumentów.  

* Zalecane jest utworzenie wystąpienia pojedynczego obiektu DocumentBulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej, która odnosi się do określonej kolekcji bazy danych Azure rozwiązania Cosmos.  

* Ponieważ wykonanie operacji interfejsu API pojedynczego zbiorczego zużywa duże fragmentu komputer kliencki procesora CPU i sieci We/Wy. Dzieje się to przez wewnętrznie duplikowanie wielu zadań, należy unikać duplikowania wiele równoczesnych zadań w procesie aplikacji każdego wykonywanego interfejsu API operacji zbiorczej wywoływanych przez. Jeśli połączenie operacji interfejsu API pojedynczego zbiorczego uruchomionych na jednej maszynie wirtualnej jest nie może wykorzystać całą kolekcję przepływności (jeśli swojej kolekcji przepływność > 1 mln RU/s), zaleca się tworzenie oddzielnych maszyn wirtualnych, które można wykonać jednocześnie zbiorczego wywołania operacji interfejsu API.

    
## <a name="next-steps"></a>Kolejne kroki
* Aby poznać szczegóły pakietu maven i wersji biblioteki BulkExecutor Java, zobacz[szczegóły BulkExecutor SDK](sql-api-sdk-bulk-executor-java.md).


