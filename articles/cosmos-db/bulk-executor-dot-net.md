---
title: Użyj zbiorczej biblioteki egzekutora .NET w usłudze Azure Cosmos DB do zbiorczego importowania i aktualizowania operacji
description: Zbiorcze importowanie i aktualizowanie dokumentów usługi Azure Cosmos DB przy użyciu zbiorczego executora biblioteki .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246879"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Użyj zbiorczej biblioteki .NET executor do wykonywania operacji zbiorczych w usłudze Azure Cosmos DB

Ten samouczek zawiera instrukcje dotyczące używania zbiorczego executora biblioteki .NET do importowania i aktualizowania dokumentów do kontenera usługi Azure Cosmos. Aby dowiedzieć się więcej o zbiorczej bibliotece executor i jak pomaga wykorzystać ogromną przepływność i magazyn, zobacz zbiorczy artykuł [omówienia biblioteki executor.](bulk-executor-overview.md) W tym samouczku zostanie wyświetlone przykładowa aplikacja .NET, która zbiorczo importuje losowo generowane dokumenty do kontenera usługi Azure Cosmos. Po zaimportowaniu pokazuje, jak można zbiorczo aktualizować importowane dane, określając poprawki jako operacje do wykonania w określonych polach dokumentu.

Obecnie biblioteka zbiorczego executora jest obsługiwana tylko przez interfejs API SQL usługi Azure Cosmos DB i konta interfejsu API Gremlin. W tym artykule opisano sposób używania zbiorczej biblioteki egzekutora .NET z kontami interfejsu API SQL. Aby dowiedzieć się więcej na temat używania zbiorczej biblioteki .NET z kontami interfejsu API Gremlin, zobacz [wykonywanie operacji zbiorczych w interfejsie API gremlin usługi Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze zainstalowanej programu Visual Studio 2019, możesz pobrać i używać [programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Upewnij się, że włączysz "Tworzenie platformy Azure" podczas konfiguracji programu Visual Studio.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

* Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez bezpłatnej subskrypcji platformy Azure i zobowiązań. Lub można użyć [emulatora usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z punktem `https://localhost:8081` końcowym. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticating-requests).

* Utwórz konto interfejsu API SQL usługi Azure Cosmos DB przy użyciu kroków opisanych w sekcji [tworzenia konta bazy danych](create-sql-api-dotnet.md#create-account) w artykule szybki start platformy .NET.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem, pobierając przykładową aplikację .NET z gitHub. Ta aplikacja wykonuje operacje zbiorcze na danych przechowywanych na koncie usługi Azure Cosmos. Aby sklonować aplikację, otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz go skopiować, i uruchom następujące polecenie:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Sklonowane repozytorium zawiera dwie próbki "BulkImportSample" i "BulkUpdateSample". Można otworzyć jedną z przykładowych aplikacji, zaktualizować parametry połączenia w pliku App.config za pomocą ciągów połączeń konta usługi Azure Cosmos DB, utworzyć rozwiązanie i uruchomić je.

Aplikacja "BulkImportSample" generuje losowe dokumenty i zbiorczo importuje je do konta usługi Azure Cosmos. Zbiorcza aktualizacja importowanych dokumentów przez aplikację "BulkUpdateSample" aktualizuje importowane dokumenty, określając poprawki jako operacje do wykonania w określonych polach dokumentu. W następnych sekcjach przejrzysz kod w każdej z tych przykładowych aplikacji.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Zbiorcze importowanie danych do konta usługi Azure Cosmos

1. Przejdź do folderu "BulkImportSample" i otwórz plik "BulkImportSample.sln".  

2. Parametry połączenia usługi Azure Cosmos DB są pobierane z pliku App.config, jak pokazano w poniższym kodzie:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Importer zbiorczy tworzy nową bazę danych i kontener z nazwą bazy danych, nazwą kontenera i wartościami przepływności określonymi w pliku App.config.

3. Następnie obiekt DocumentClient jest inicjowany w trybie bezpośredniego połączenia TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor obiekt jest inicjowany z wysoką wartość ponawiania dla czasu oczekiwania i ograniczone żądania. A następnie są one ustawione na 0, aby przekazać kontrolę przeciążenia do BulkExecutor przez cały okres jego istnienia.  

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

5. Aplikacja wywołuje interfejs API BulkImportAsync. Biblioteka .NET udostępnia dwa przeciążenia interfejsu API importu zbiorczego — jeden, który akceptuje listę szeregowanych dokumentów JSON, a drugi, który akceptuje listę deserializowanych dokumentów POCO. Aby dowiedzieć się więcej o definicjach każdej z tych przeciążonych metod, zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync metoda akceptuje następujące parametry:**
   
   |**Parametr**  |**Opis** |
   |---------|---------|
   |enableUpsert    |   Flaga umożliwiająca operacje upsert na dokumentach. Jeśli dokument o podanym identyfikatorze już istnieje, jest aktualizowany. Domyślnie jest ustawiona na false.      |
   |disableAutomaticIdGeneration    |    Flaga wyłączania automatycznego generowania identyfikatora. Domyślnie jest ustawiona na true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maksymalny stopień współbieżności na zakres klucza partycji, ustawienie wartości null spowoduje, że biblioteka użyje domyślnej wartości 20. |
   |maxInMemorySortingBatchSize     |  Maksymalna liczba dokumentów, które są pobierane z wylicznika dokumentu, który jest przekazywany do wywołania interfejsu API na każdym etapie. W przypadku fazy sortowania w pamięci, która ma miejsce przed importem zbiorczym, ustawienie tego parametru na wartość null spowoduje, że biblioteka użyje domyślnej wartości minimalnej (documents.count, 1000000).       |
   |Cancellationtoken    |    Token anulowania bezpiecznie zakończyć operację importu zbiorczego.     |

   **Definicja obiektu odpowiedzi zbiorczej na import** Wynik wywołania interfejsu API importu zbiorczego zawiera następujące atrybuty:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |NumberOfDocumentsImportowane (długie)   |  Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z całkowitej liczby dokumentów dostarczonych do wywołania interfejsu API importu zbiorczego.       |
   |TotalRequestUnitsConsumed (podwójnie)   |   Łączna liczba jednostek żądań (RU) zużytych przez wywołanie interfejsu API importu zbiorczego.      |
   |TotalTimeTaken (TimeSpan)    |   Całkowity czas wykonany przez wywołanie interfejsu API importu zbiorczego, aby zakończyć wykonanie.      |
   |BadInputDocuments (lista\<obiektu>)   |     Lista dokumentów w złym formacie, które nie zostały pomyślnie zaimportowane w wywołaniu interfejsu API importu zbiorczego. Napraw zwrócone dokumenty i ponów próbę importu. Dokumenty w złym formacie obejmują dokumenty, których wartość identyfikatora nie jest ciągiem znaków (null lub inny typ danych jest uznawany za nieprawidłowy).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Zbiorcze dane aktualizacji na koncie usługi Azure Cosmos

Istniejące dokumenty można zaktualizować za pomocą interfejsu API BulkUpdateAsync. W tym przykładzie ustawisz `Name` pole na nową `Description` wartość i usuniesz pole z istniejących dokumentów. Pełny zestaw obsługiwanych operacji aktualizacji można znaleźć w [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Przejdź do folderu "BulkUpdateSample" i otwórz plik "BulkUpdateSample.sln".  

2. Zdefiniuj elementy aktualizacji wraz z odpowiednimi operacjami aktualizacji pola. W tym przykładzie `SetUpdateOperation` można zaktualizować `Name` pole `UnsetUpdateOperation` i `Description` usunąć pole ze wszystkich dokumentów. Można również wykonywać inne operacje, takie jak zwiększanie pola dokumentu o określoną wartość, wypychanie określonych wartości do pola tablicy lub usuwanie określonej wartości z pola tablicy. Aby dowiedzieć się więcej o różnych metodach dostarczanych przez interfejs API aktualizacji zbiorczej, zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Aplikacja wywołuje interfejs API BulkUpdateAsync. Aby dowiedzieć się więcej o definicji metody BulkUpdateAsync, zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync metoda akceptuje następujące parametry:**

   |**Parametr**  |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maksymalny stopień współbieżności na zakres klucza partycji, ustawienie tego parametru na null spowoduje, że biblioteka będzie używać wartości domyślnej(20).   |
   |maxInMemorySortingBatchSize    |    Maksymalna liczba elementów aktualizacji pobranych z wyliczacza elementów aktualizacji przekazywane do wywołania interfejsu API na każdym etapie. W przypadku fazy sortowania w pamięci, która ma miejsce przed aktualizacją zbiorczą, ustawienie tego parametru na wartość null spowoduje, że biblioteka użyje domyślnej wartości minimalnej(updateItems.count, 1000000).     |
   | Cancellationtoken|Token anulowania bezpiecznie zakończyć operację aktualizacji zbiorczej. |

   **Definicja obiektu odpowiedzi na aktualizację zbiorczą** Wynik wywołania interfejsu API aktualizacji zbiorczej zawiera następujące atrybuty:

   |**Parametr**  |**Opis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (długi)    |   Liczba dokumentów, które zostały pomyślnie zaktualizowane z całkowitej liczby dokumentów dostarczonych do wywołania interfejsu API aktualizacji zbiorczej.      |
   |TotalRequestUnitsConsumed (podwójnie)   |    Łączna liczba jednostek żądań (RUs) wykorzystanych przez wywołanie interfejsu API aktualizacji zbiorczej.    |
   |TotalTimeTaken (TimeSpan)   | Całkowity czas wykonany przez wywołanie interfejsu API aktualizacji zbiorczej, aby zakończyć wykonanie. |
    
## <a name="performance-tips"></a>Porady dotyczące wydajności 

Należy wziąć pod uwagę następujące punkty dla lepszej wydajności podczas korzystania z biblioteki wykonawca zbiorczej:

* Aby uzyskać najlepszą wydajność, uruchom aplikację z maszyny wirtualnej platformy Azure, która znajduje się w tym samym regionie co region zapisu konta usługi Azure Cosmos.  

* Zaleca się wystąpienie pojedynczego `BulkExecutor` obiektu dla całej aplikacji w ramach jednej maszyny wirtualnej, która odpowiada określonemu kontenerowi usługi Azure Cosmos.  

* Ponieważ wykonanie interfejsu API pojedynczej operacji zbiorczej zużywa duży fragment procesora CPU i we/wy komputera klienckiego (dzieje się tak przez tworzenie wielu zadań wewnętrznie). Należy unikać tworzenia wielu równoczesnych zadań w ramach procesu aplikacji, które wykonują wywołania interfejsu API operacji zbiorczej. Jeśli wywołanie interfejsu API pojedynczej operacji zbiorczej uruchomionej na jednej maszynie wirtualnej nie może korzystać z przepływności całego kontenera (jeśli przepływność kontenera > 1 mln jednostek RU/s), zaleca się utworzenie oddzielnych maszyn wirtualnych w celu jednoczesnego wykonania wywołań interfejsu API operacji zbiorczej.  

* Upewnij `InitializeAsync()` się, że metoda jest wywoływana po wystąpieniu obiektu BulkExecutor, aby pobrać mapę partycji docelowego kontenera Cosmos.  

* W aplikacji App.Config upewnij **się, że gcServer** jest włączony dla lepszej wydajności
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteka emituje ślady, które mogą być zbierane do pliku dziennika lub na konsoli. Aby włączyć oba, dodaj następujący kod do pliku App.Config aplikacji.

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

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o szczegółach pakietu Nuget i informacjach o wersji, zobacz [szczegóły zestawu SDK wykonawcy zbiorczego](sql-api-sdk-bulk-executor-dot-net.md).
