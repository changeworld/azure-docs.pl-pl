---
title: Użycie zbiorczej wykonawcze biblioteki środowiska Java w Azure Cosmos DB do wykonywania operacji importu zbiorczego i aktualizacji
description: Zbiorcze importowanie i aktualizowanie Azure Cosmos DB dokumentów przy użyciu biblioteki środowiska wykonawczego w języku Java
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: bf2a2385b3129ddf24ede7f6d851701186b0e33c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445712"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Wykonywały operacje zbiorcze na danych usługi Azure Cosmos DB za pomocą biblioteki języka Java przetwarzania zbiorczego

Ten samouczek zawiera instrukcje na temat korzystania z usługi Azure Cosmos DB zbiorcze wykonawca Java biblioteki do zaimportowania, a następnie zaktualizować dokumenty usługi Azure Cosmos DB. Aby dowiedzieć się o biblioteki wykonawca zbiorcze oraz jak pomaga w ogromnej przepustowości i magazynu, zobacz [zbiorczo Przegląd biblioteki wykonawca](bulk-executor-overview.md) artykułu. W tym samouczku utworzysz aplikację Java, która generuje losowe dokumenty i są one zbiorczo importowane do kontenera usługi Azure Cosmos. Po zaimportowaniu będą zbiorczo zaktualizować niektóre właściwości dokumentu. 

Obecnie Biblioteka wykonawców zbiorczych jest obsługiwana tylko przez Azure Cosmos DB z INTERFEJSem API SQL i kontami interfejsu API Gremlin. W tym artykule opisano sposób użycia zbiorczej procedury tworzenia biblioteki Java z kontami interfejsu API SQL. Aby dowiedzieć się więcej o korzystaniu z biblioteki .NET wykonawca zbiorcze za pomocą interfejsu API języka Gremlin, zobacz [wykonywały operacje zbiorcze w usłudze Azure Cosmos DB — interfejs API Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).  

* Możesz bezpłatnie [wypróbować Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można też użyć [emulatora Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z punktem końcowym `https://localhost:8081`. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticating-requests).  

* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.  

  - Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.

* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne [Maven](https://maven.apache.org/)  
  
  - W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.

* Utwórz konto Azure Cosmos DB interfejsu API SQL, korzystając z procedury opisanej w sekcji [Tworzenie konta bazy danych](create-sql-api-java.md#create-a-database-account) artykułu przewodnika Szybki Start w języku Java.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem pobierając przykładowej aplikacji Java z usługi GitHub. Ta aplikacja wykonuje operacje zbiorcze na danych usługi Azure Cosmos DB. Aby sklonować aplikację, otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz skopiować aplikację i uruchom następujące polecenie:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Sklonowanego repozytorium zawiera dwa przykłady "elementów bulkimport" i "bulkupdate" względną do folderu "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikacja "elementów bulkimport" generuje losowe dokumentów i importuje je do usługi Azure Cosmos DB. Aplikacja "bulkupdate" aktualizuje niektóre dokumenty w usłudze Azure Cosmos DB. W kolejnych sekcjach omówimy kod w każdym z tych przykładowych aplikacji. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Zbiorcze importowanie danych do usługi Azure Cosmos DB

1. Parametry połączenia usługi Azure Cosmos DB są odczytywane jako argumentów i przypisane zmienne zdefiniowane w pliku CmdLineConfiguration.java.  

2. Obok obiektu DocumentClient jest inicjowany za pomocą następujących instrukcji:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Obiekt DocumentBulkExecutor jest inicjowany za pomocą ponownych prób wysoki czas oczekiwania i ograniczenia żądań. A następnie są one ustawione na 0 do przekazywania kontroli przeciążenia do DocumentBulkExecutor na cały okres ich istnienia.  

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

4. Wywołaj interfejs API, który generuje losowe dokumenty do zbiorczego importowania do kontenera usługi Azure Cosmos. Można skonfigurować konfiguracje wiersza polecenia w pliku CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Interfejs API importu zbiorczego akceptuje kolekcji serializacji JSON dokumentów i składa się z następującej składni, aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

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
   |disableAutomaticIdGeneration     |   Flagi, aby wyłączyć automatyczne generowanie identyfikatora. Domyślnie jest ustawiona na wartość true.   |
   |maxConcurrencyPerPartitionRange    |  Maksymalny stopień współbieżności na zakres kluczy partycji. Wartość domyślna to 20.  |

   **Definicja obiektu odpowiedzi importu zbiorczego** wynikiem importowania zbiorczego wywołania interfejsu API zawiera następujące metody get:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z dostarczonych do zbiorczego dokumentów zaimportować wywołania interfejsu API.      |
   |Podwójna getTotalRequestUnitsConsumed()   |  Łączna liczba jednostek żądania (RU) używane przez większość zaimportować wywołania interfejsu API.       |
   |Czas trwania getTotalTimeTaken()   |    Łączny czas za pomocą importowania zbiorczego do ukończenia wykonywania wywołań interfejsu API.     |
   |Lista wyjątków\<> GetErrors () |  Pobiera listę błędów, jeśli niektóre dokumenty z usługi batch do zbiorczego zaimportować wywołania interfejsu API nie można pobrać wstawiony.       |
   |Lista\<obiektów > getBadInputDocuments ()  |    Lista dokumentów nieprawidłowego formatu, które nie zostały pomyślnie zaimportowane zbiorczo zaimportować wywołania interfejsu API. Użytkownik powinien rozwiązać zwróconych dokumentów i ponów próbę importowania. Sformatowana niewłaściwe dokumenty zawierają dokumentów, których wartość Identyfikatora jest ciąg (wartość null lub jakikolwiek inny typ danych jest uznawane za nieprawidłowe).     |

5. Po umieszczeniu zbiorcze importowanie gotowych aplikacji kompilacji narzędzia wiersza polecenia ze źródła przy użyciu polecenia "mvn czyste pakiet". To polecenie spowoduje wygenerowanie pliku jar w folderze docelowym:  

   ```java
   mvn clean package
   ```

6. Po wygenerowaniu miejsce docelowe zależności aplikacji importera zbiorczego można wywołać za pomocą następującego polecenia:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Importer zbiorcze tworzy nową bazę danych i kolekcji przy użyciu nazwy bazy danych, nazwę kolekcji i wartości przepływności, określone w pliku App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcza aktualizacja danych w usłudze Azure Cosmos DB

Należy zaktualizować istniejące dokumenty przy użyciu interfejsu API BulkUpdateAsync. W tym przykładzie zostanie ustawiona nazwa pola na nową wartość i usuń pole opisu z istniejących dokumentów. Aby uzyskać pełny zestaw obsługiwanych pól operacje aktualizacji, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definiuje elementy aktualizacji oraz odpowiednie operacje aktualizacji pola. W tym przykładzie użyjesz SetUpdateOperation aktualizuje pole nazwy i UnsetUpdateOperation, aby usunąć pole opisu z wszystkich dokumentów. Możesz także wykonywać inne operacje, takie jak zwiększenie pole dokumentu o określoną wartość, wypychanie określone wartości do pola tablicy lub usunąć określoną wartość z pola tablicy. Aby dowiedzieć się więcej na temat różnych metod dostarczonych przez interfejs API aktualizacji zbiorczej, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Wywołaj interfejs API updateAll, który generuje losowe dokumenty, które mają zostać zbiorczo zaimportowane do kontenera usługi Azure Cosmos. Można skonfigurować konfiguracje wiersza polecenia do przekazania w pliku CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Aktualizacja zbiorcza interfejs API akceptuje kolekcję elementów do zaktualizowania. Każdy element aktualizacji określa listę pól aktualizacji operacji wykonywanej na dokument identyfikowany przez identyfikator i wartość klucza partycji. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll — metoda przyjmuje następujące parametry:

   |**Parametr** |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maksymalny stopień współbieżności na zakres kluczy partycji. Wartość domyślna to 20.  |
 
   **Definicja obiektu odpowiedzi importu zbiorczego** wynikiem importowania zbiorczego wywołania interfejsu API zawiera następujące metody get:

   |**Parametr** |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane poza dokumentów dostarczonych wywołania interfejsu API pakietu zbiorczego aktualizacji.      |
   |Podwójna getTotalRequestUnitsConsumed() |  Jednostki łączna liczba żądań (RU) używane przez aktualizacja zbiorcza wywołania interfejsu API.       |
   |Czas trwania getTotalTimeTaken()  |   Łączny czas zbiorczego aktualizacji do ukończenia wykonywania wywołań interfejsu API.      |
   |Lista wyjątków\<> GetErrors ()   |    Pobiera listę błędów, jeśli niektóre dokumenty, poza partii dostarczane do wywołania interfejsu API zbiorcze aktualizacji nie można pobrać wstawiony.      |

3. Po umieszczeniu zbiorczej aktualizacji gotowych aplikacji kompilacji narzędzia wiersza polecenia ze źródła przy użyciu polecenia "mvn czyste pakiet". To polecenie spowoduje wygenerowanie pliku jar w folderze docelowym:  

   ```
   mvn clean package
   ```

4. Po wygenerowaniu miejsce docelowe zależności, można wywołać zbiorcze aktualizowanie aplikacji przy użyciu następującego polecenia:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Porady dotyczące wydajności 

Korzystając z biblioteki program wykonujący zbiorcze, należy wziąć pod uwagę następujące punkty, w celu zapewnienia lepszej wydajności:

* Aby uzyskać najlepszą wydajność Uruchom aplikację w maszynie Wirtualnej platformy Azure, w tym samym regionie, co region zapisu konta usługi Cosmos DB.  
* Do osiągnięcia wyższej przepustowości:  

   * Ustaw rozmiar sterty JVM numerowi wystarczająco duży, aby uniknąć wszelkich problemu pamięci podczas obsługi dużej liczby dokumentów. Sugerowany rozmiar sterty: maksymalny (3GB, 3 * sizeof (wszystkie dokumenty przekazany do zbiorczego importowanie interfejsu API w jednej partii)).  
   * Brak czasu przetwarzania wstępnego, z powodu którego uzyskasz większą przepływność podczas wykonywania operacji zbiorczych z dużą liczbą dokumentów. Tak Jeśli chcesz zaimportować 10 000 000 dokumentów, uruchomienie importu zbiorczego 10 razy na 10 zbiorcze dokumentów każdego o rozmiarze 1 000 000 jest korzystniejsze niż Uruchamianie importu zbiorczego 100 razy na 100 zbiorcze dokumentów każdej wielkości 100 000 dokumentów.  

* Zaleca się utworzenie wystąpienia pojedynczego obiektu DocumentBulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej odpowiadającej określonemu kontenerowi usługi Azure Cosmos.  

* Od momentu wykonania operacji interfejsu API pojedynczej zbiorczej zużywa duże fragment komputerze klienckim procesora CPU i sieci We/Wy. Dzieje się tak wewnętrznie duplikując wielu zadań, należy unikać duplikowania wiele równoczesnych zadań w procesie aplikacji wywoływanych w każdej wykonywanie zbiorczej operacji interfejsu API. W przypadku wywołania operacji interfejsu API pojedynczej zbiorczej, uruchomiony w jednej maszynie wirtualnej nie można użyć całego kontenera przepływności (Jeśli Twój kontener przepływności > 1 mln jednostek RU/s), lepiej jest utworzyć osobne maszyny wirtualne, aby jednocześnie wykonać zbiorcze wywołania operacji interfejsu API.

    
## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o szczegóły pakietu maven i biblioteki języka Java wykonawca zbiorcze informacje o wersji, zobacz[zbiorczo Szczegóły zestawu SDK funkcji wykonawczej](sql-api-sdk-bulk-executor-java.md).


