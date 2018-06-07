---
title: Za pomocą biblioteki BulkExecutor .NET do wykonywania operacji zbiorczej w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Biblioteka BulkExecutor .NET Azure rozwiązania Cosmos DB służy do importowania zbiorczego i aktualizowanie dokumentów do bazy danych Azure rozwiązania Cosmos kolekcji.
keywords: Moduł wykonujący zbiorczego .net
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 0e8c5f9a848eaa1543ce9d58895b035e23d9f335
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611164"
---
# <a name="using-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Za pomocą biblioteki BulkExecutor .NET do wykonywania operacji zbiorczej w usłudze Azure DB rozwiązania Cosmos

W tym samouczku instrukcje na temat używania biblioteki BulkExecutor .NET DB rozwiązania Cosmos Azure do importowania i aktualizowania dokumentów do kolekcji bazy danych Azure rozwiązania Cosmos. Aby dowiedzieć się więcej o BulkExecutor biblioteki i jej można wykorzystać ogromnej przepływności i magazynu, zobacz [Przegląd biblioteki BulkExecutor](bulk-executor-overview.md) artykułu. Ten samouczek przeprowadzi użytkownika przykładową aplikację .NET zbiorcze importów losowo generowany dokumentów w kolekcji usługi Azure DB rozwiązania Cosmos. Po zaimportowaniu, pokazuje sposób można zbiorczego aktualizacji importowanych danych, określając poprawki jako operacje do wykonania w polach określonego dokumentu.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze programu Visual Studio 2017 r zainstalowany, możesz pobrać i użyć [programu Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Upewnij się, należy włączyć programowanie Azure podczas instalacji programu Visual Studio.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 

* Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań. Można użyć [Azure rozwiązania Cosmos DB emulatora](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z `https://localhost:8081` identyfikatora URI. Klucz podstawowy znajduje się w [uwierzytelniania żądań](local-emulator.md#authenticating-requests).

* Utwórz konto interfejsu API Azure rozwiązania Cosmos bazy danych SQL przy użyciu procedury opisanej w [Tworzenie konta bazy danych](create-sql-api-dotnet.md#create-a-database-account) artykułu szybkiego startu .NET. 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz załóżmy przełączyć się do pracy z kodem, pobierając niektóre przykładowe aplikacje .NET z usługi GitHub. Te aplikacje operacji zbiorczych danych bazy danych Azure rozwiązania Cosmos. Klonowanie aplikacje, otwórz wiersz polecenia, przejdź do katalogu, w którym ma zostać skopiuj je i uruchom następujące polecenie:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Sklonowanego repozytorium zawiera dwa przykłady "BulkImportSample" i "BulkUpdateSample." Można otworzyć jednego z przykładowych aplikacji, zaktualizuj parametry połączenia w pliku App.config z parametrów połączenia konta bazy danych Azure rozwiązania Cosmos, Skompiluj rozwiązanie i uruchom go. 

Aplikacja "BulkImportSample" generuje losowe dokumentów i zbiorczo importowane do bazy danych Azure rozwiązania Cosmos. Większość aplikacji "BulkUpdateSample" aktualizuje importowanych dokumenty, określając poprawki jako operacje do wykonania w polach określonego dokumentu. W kolejnych sekcjach należy przejrzeć kod w każdym z tych przykładowych aplikacji.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Zbiorcze importowanie danych do bazy danych Azure rozwiązania Cosmos

1. Przejdź do folderu "BulkImportSample", a następnie otwórz plik "BulkImportSample.sln".  

2. Azure DB rozwiązania Cosmos parametry połączenia są pobierane z pliku App.config, jak pokazano w poniższym kodzie:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Importer zbiorczego tworzy nową bazę danych i kolekcję o nazwie bazy danych, nazwę kolekcji i przepływności wartości określone w pliku App.config. 

3. Obok obiektu DocumentClient jest inicjowany z trybu połączenia TCP bezpośrednie:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Obiekt BulkExecutor jest inicjowany z wartościami wysokiej ponownych prób dla czas oczekiwania i ograniczenie żądań. A następnie są one ustawione na 0 do przekazania kontroli przeciążenia BulkExecutor jego czas ich istnienia.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Aplikacja wywołuje interfejs API BulkImportAsync. Biblioteka .NET udostępnia dwa przeciążenia zbiorczego importowanie interfejsu API -, który akceptuje listę serializacji dokumentów JSON i innych akceptuje listy dokumentów POCO zdeserializowany. Aby poznać definicje każdej z tych metod przeciążone, zapoznaj się [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Metoda BulkImportAsync przyjmuje następujące parametry:**
   
   |**Parametr**  |**Opis** |
   |---------|---------|
   |enableUpsert    |   Flaga włączenia upsert dokumentów. Jeśli dokument o podanym identyfikatorze już istnieje, jest on aktualizowany. Domyślnie jest ustawiona na wartość false.      |
   |disableAutomaticIdGeneration    |    Flagi, aby wyłączyć automatyczne generowanie identyfikatora. Domyślnie jest ustawiona na true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maksymalny stopień współbieżności na zakresem kluczy partycji, ustawienie na wartość null spowoduje, że biblioteki użyć domyślnej wartości 20. |
   |maxInMemorySortingBatchSize     |  Maksymalna liczba dokumentów pobierane z wyliczający dokumentu, który zostanie przekazany do interfejsu API wywołać na każdym etapie.  W pamięci przetwarzania wstępnego sortowania fazy przed importowania zbiorczego ustawienie na wartość null spowoduje, że biblioteki użyć domyślnej wartości min (documents.count 1000000).       |
   |CancellationToken    |    Token anulowania, aby bezpiecznie zamknąć importowania zbiorczego.     |

   **Definicja obiektu odpowiedzi importowania zbiorczego** wynikiem importowania zbiorczego wywołanie interfejsu API zawiera następujące atrybuty:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |NumberOfDocumentsImported (Liczba długa)   |  Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane poza dokumentów do zbiorczego zaimportować wywołanie interfejsu API.       |
   |TotalRequestUnitsConsumed (o podwójnej precyzji)   |   Jednostki całkowita liczba żądań (RU) używane przez większość zaimportować wywołanie interfejsu API.      |
   |TotalTimeTaken (TimeSpan)    |   Całkowity czas wykonywania za pomocą importowania zbiorczego wywołanie interfejsu API, aby ukończyć wykonywania.      |
   |BadInputDocuments (listy<object>)   |     Lista zły format dokumentów, które nie zostały pomyślnie zaimportowane w zbiorczego importowanie wywołanie interfejsu API. Użytkownik powinien Usuń dokumenty zwrócone i spróbuj ponownie import. Sformatowany zły dokumenty zawierają dokumentów, których wartość Identyfikatora nie jest ciąg (wartość null lub jakikolwiek inny typ danych jest uznawane za nieprawidłowe).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcze danych aktualizacji w usłudze Azure DB rozwiązania Cosmos

Należy zaktualizować istniejące dokumenty przy użyciu interfejsu API BulkUpdateAsync. W tym przykładzie zostanie Ustaw pole nazwy na nową wartość i usunąć pola Opis z istniejących dokumentów. Pełny zestaw pól obsługiwane operacje aktualizacji, zapoznaj się [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Przejdź do folderu "BulkUpdateSample", a następnie otwórz plik "BulkUpdateSample.sln".  

2. Definiowanie elementów aktualizacji wraz z odpowiednich operacji aktualizowania pola. W tym przykładzie użyjesz SetUpdateOperation można zaktualizować pola nazwy i UnsetUpdateOperation, aby usunąć pole opisu z wszystkie dokumenty. Można również wykonywać innych operacji, takich jak przyrostu pole dokumentu przez określoną wartość, push określone wartości do pola tablicy lub usuń określoną wartość z pola tablicy. Informacje na temat różnych metod udostępniane przez interfejs API aktualizacji zbiorczej, zapoznaj się [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Aplikacja wywołuje interfejs API BulkUpdateAsync. Aby uzyskać informacje dotyczące definicji metody BulkUpdateAsync, zapoznaj się [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Metoda BulkUpdateAsync przyjmuje następujące parametry:**

   |**Parametr**  |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maksymalny stopień współbieżności na zakresem kluczy partycji, ustawienie na wartość null spowoduje, że biblioteki użyć domyślnej wartości 20.   |
   |maxInMemorySortingBatchSize    |    Maksymalna liczba aktualizacji elementów pobierane z modułu wyliczającego elementy aktualizacji przekazane do wywołania interfejsu API w każdym z etapów przetwarzania wstępnego fazy sortowania w pamięci przed zaktualizowaniem zbiorczego, ustawienie na wartość null spowoduje, że biblioteki użyć domyślnej wartości min (updateItems.count, 1000000).     |
   | CancellationToken|Token anulowania, aby bezpiecznie zamknąć aktualizacji zbiorczej. |

   **Definicja obiektu odpowiedzi aktualizacji zbiorczej** wynik aktualizację zbiorczą wywołanie interfejsu API zawiera następujące atrybuty:

   |**Parametr**  |**Opis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (Liczba długa)    |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane poza te dostarczane do wywołania interfejsu API aktualizacji zbiorczej.      |
   |TotalRequestUnitsConsumed (o podwójnej precyzji)   |    Całkowita liczba jednostek żądania (RU) używane przez aktualizację zbiorczą wywołanie interfejsu API.    |
   |TotalTimeTaken (TimeSpan)   | Łączny czas zbiorczego aktualizacji wywołanie interfejsu API, aby ukończyć wykonywania. |
    
## <a name="performance-tips"></a>Porady dotyczące wydajności 

Należy wziąć pod uwagę następujące kwestie w celu poprawy wydajności podczas korzystania z biblioteki BulkExecutor:

* Aby uzyskać najlepszą wydajność uruchomienie aplikacji z maszyny wirtualnej platformy Azure, który znajduje się w tym samym regionie co regionu zapisu konta DB rozwiązania Cosmos.  

* Zalecane jest uruchamianie pojedynczego obiektu BulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej odpowiadający określonej kolekcji DB rozwiązania Cosmos.  

* Ponieważ wykonanie operacji interfejsu API pojedynczego zbiorczego zużywa duże fragmentu komputer kliencki procesora CPU i sieci We/Wy. Dzieje się to przez wewnętrznie duplikowanie wielu zadań, należy unikać duplikowania wiele równoczesnych zadań w procesie aplikacji każdego wykonywanego interfejsu API operacji zbiorczej wywoływanych przez. Jeśli połączenie operacji interfejsu API pojedynczego zbiorczego uruchomionych na jednej maszynie wirtualnej jest nie może wykorzystać całą kolekcję przepływności (jeśli swojej kolekcji przepływność > 1 mln RU/s), zaleca się tworzenie oddzielnych maszyn wirtualnych, które można wykonać jednocześnie zbiorczego wywołania operacji interfejsu API.  

* Upewnij się, że InitializeAsync() jest wywoływana po tworzenia wystąpienia obiektu BulkExecutor można pobrać docelowa Mapa partycji kolekcji DB rozwiązania Cosmos.  

* W pliku App.Config aplikacji, upewnij się, **gcserver —** jest włączona w celu zapewnienia lepszej wydajności
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteka emituje dane śledzenia, które mogą być zbierane w pliku dziennika lub w konsoli. Aby włączyć zarówno, należy dodać następujące do pliku App.Config aplikacji.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
```

## <a name="next-steps"></a>Kolejne kroki
* Aby poznać szczegóły pakietu Nuget i wersji biblioteki BulkExecutor .net, zobacz[szczegóły BulkExecutor SDK](sql-api-sdk-bulk-executor-dot-net.md). 
