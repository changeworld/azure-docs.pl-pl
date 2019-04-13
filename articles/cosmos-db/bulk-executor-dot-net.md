---
title: Za pomocą biblioteki .NET wykonawca zbiorcze można wykonać operacji importu zbiorczego operacje i aktualizacji w usłudze Azure Cosmos DB
description: Zbiorcze importowanie i aktualizowanie dokumentów usługi Azure Cosmos DB za pomocą biblioteki .NET przetwarzania zbiorczego.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: cfb90dc31635001291b1661f31ec2ee1fc378404
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523345"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Wykonywały operacje zbiorcze w usłudze Azure Cosmos DB za pomocą biblioteki .NET przetwarzania zbiorczego

Ten samouczek zawiera instrukcje na temat korzystania z usługi Azure Cosmos DB zbiorcze wykonawca biblioteki .NET do importowania i aktualizowania dokumentów do kontenera usługi Azure Cosmos DB. Aby dowiedzieć się o biblioteki wykonawca zbiorcze oraz jak pomaga w ogromnej przepustowości i magazynu, zobacz [Przegląd biblioteki wykonawca zbiorcze](bulk-executor-overview.md) artykułu. W tym samouczku zobaczysz, że przykładowej aplikacji .NET, która zbiorczo Importy losowo generowany dokumentów do kontenera usługi Azure Cosmos DB. Po zaimportowaniu go pokazuje jak można dokonywać zbiorczej aktualizacji importowanych danych, określając poprawki jako operacje do wykonania w polach określonego dokumentu. 

Obecnie zbiorcze wykonawca biblioteka jest obsługiwana przez interfejsu API SQL usługi Azure Cosmos DB i tylko konta interfejsu API języka Gremlin. W tym artykule opisano, jak biblioteka .NET wykonawca zbiorcze za pomocą konta interfejsu API SQL. Aby dowiedzieć się więcej o korzystaniu z biblioteki .NET wykonawca zbiorcze za pomocą interfejsu API języka Gremlin, zobacz [wykonywały operacje zbiorcze w usłudze Azure Cosmos DB — interfejs API Gremlin](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać i używać [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Upewnij się, że umożliwiają programowanie na platformie Azure podczas instalacji programu Visual Studio.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 

* Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań. Alternatywnie można użyć [emulatora usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z `https://localhost:8081` punktu końcowego. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticating-requests).

* Tworzenie konta interfejsu API SQL usługi Azure Cosmos DB przy użyciu kroków opisanych w [Tworzenie konta bazy danych](create-sql-api-dotnet.md#create-account) sekcji z artykułem Szybki Start platformy .NET. 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem pobierając kilka przykładowych aplikacji .NET z usługi GitHub. Te aplikacje wykonywały operacje zbiorcze na danych usługi Azure Cosmos DB. Klonowanie aplikacji, otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz skopiuj je i uruchom następujące polecenie:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Sklonowane repozytorium zawiera dwa przykłady "BulkImportSample" i "BulkUpdateSample." Można Otwórz okno dialogowe z przykładowych aplikacji, zaktualizuj parametry połączenia w pliku App.config przy użyciu parametrów połączenia konta usługi Azure Cosmos DB, skompilować rozwiązanie i uruchomić. 

Aplikacja "BulkImportSample" generuje losowe dokumentów i zbiorcze importuje je do usługi Azure Cosmos DB. Zbiorcze aplikacji "BulkUpdateSample" aktualizuje importowanych dokumenty, określając poprawki jako operacje do wykonania w polach określonego dokumentu. W kolejnych sekcjach należy przejrzeć kod w każdym z tych przykładowych aplikacji.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Zbiorcze importowanie danych do usługi Azure Cosmos DB

1. Przejdź do folderu "BulkImportSample", a następnie otwórz plik "BulkImportSample.sln".  

2. Parametry połączenia usługi Azure Cosmos DB są pobierane z pliku App.config, jak pokazano w poniższym kodzie:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Importer zbiorcze tworzy nową bazę danych i kolekcji przy użyciu nazwy bazy danych, nazwę kolekcji i wartości przepływności, określone w pliku App.config. 

3. Obok obiektu DocumentClient jest inicjowany przy użyciu trybu połączenia bezpośredniego połączenia TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Obiekt BulkExecutor jest inicjowany z wartością wysokiej ponawiania prób dla czas oczekiwania i ograniczenia żądań. A następnie są one ustawione na 0 do przekazywania kontroli przeciążenia do BulkExecutor na cały okres ich istnienia.  

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

5. Aplikacja wywołuje interfejs API BulkImportAsync. Biblioteka .NET zawiera dwa przeciążenia zbiorczego importowanie interfejsu API — taki, który akceptuje listę Zserializowany dokumentów JSON, a druga akceptuje listę zdeserializowany POCO dokumentów. Aby dowiedzieć się o definicje każdej z tych metod przeciążonych, zapoznaj się [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   |enableUpsert    |   Flaga włączenia wykonuje operację UPSERT dokumentów. Jeśli dokument o podanym identyfikatorze już istnieje, jest on aktualizowany. Domyślnie jest ustawiona na wartość false.      |
   |disableAutomaticIdGeneration    |    Flagi, aby wyłączyć automatyczne generowanie identyfikatora. Domyślnie jest ustawiona na wartość true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maksymalny stopień współbieżności na zakres kluczy partycji, ustawienie na wartość null spowoduje, że biblioteki użyć wartości domyślnej, 20. |
   |maxInMemorySortingBatchSize     |  Maksymalna liczba dokumentów pobierane z dokumentu moduł wyliczający, który jest przekazywany do interfejsu API wywołania na każdym etapie.  W pamięci przetwarzania wstępnego sortowania fazy przed zaimportowaniem zbiorcze biblioteki użyć wartości domyślnej min (documents.count 1000000) spowoduje, że ustawienie na wartość null.       |
   |Token anulowania    |    Token anulowania, aby bezpiecznie zamknąć importu zbiorczego.     |

   **Definicja obiektu odpowiedzi importu zbiorczego** wynikiem importowania zbiorczego wywołania interfejsu API zawiera następujące atrybuty:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z dostarczonych do zbiorczego dokumentów zaimportować wywołania interfejsu API.       |
   |TotalRequestUnitsConsumed (podwójny)   |   Łączna liczba jednostek żądania (RU) używane przez większość zaimportować wywołania interfejsu API.      |
   |TotalTimeTaken (TimeSpan)    |   Łączny czas za pomocą importowania zbiorczego do ukończenia wykonywania wywołań interfejsu API.      |
   |BadInputDocuments (lista\<obiektu >)   |     Lista dokumentów nieprawidłowego formatu, które nie zostały pomyślnie zaimportowane zbiorczo zaimportować wywołania interfejsu API. Użytkownik powinien rozwiązać zwróconych dokumentów i ponów próbę importowania. Sformatowana niewłaściwe dokumenty zawierają dokumentów, których wartość Identyfikatora jest ciąg (wartość null lub jakikolwiek inny typ danych jest uznawane za nieprawidłowe).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcza aktualizacja danych w usłudze Azure Cosmos DB

Należy zaktualizować istniejące dokumenty przy użyciu interfejsu API BulkUpdateAsync. W tym przykładzie zostanie ustawiona nazwa pola na nową wartość i usuń pole opisu z istniejących dokumentów. Aby uzyskać pełny zestaw obsługiwanych pól operacje aktualizacji, zapoznaj się [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Przejdź do folderu "BulkUpdateSample", a następnie otwórz plik "BulkUpdateSample.sln".  

2. Definiowanie elementów aktualizacji oraz odpowiednie operacje aktualizacji pola. W tym przykładzie użyjesz SetUpdateOperation aktualizuje pole nazwy i UnsetUpdateOperation, aby usunąć pole opisu z wszystkich dokumentów. Możesz także wykonywać inne operacje, takie jak zwiększenie pole dokumentu o określoną wartość, wypychanie określone wartości do pola tablicy lub usunąć określoną wartość z pola tablicy. Aby dowiedzieć się więcej na temat różnych metod dostarczonych przez interfejs API aktualizacji zbiorczej, zobacz [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Aplikacja wywołuje interfejs API BulkUpdateAsync. Aby dowiedzieć się więcej na temat definicji metody BulkUpdateAsync, dotyczą [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

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
   |maxConcurrencyPerPartitionKeyRange    |   Maksymalny stopień współbieżności na zakres kluczy partycji, ustawienie na wartość null spowoduje, że biblioteki użyć wartości domyślnej 20.   |
   |maxInMemorySortingBatchSize    |    Maksymalna liczba aktualizacji elementów jest pobierany z modułu wyliczającego elementów aktualizacji przekazane do wywołania interfejsu API na każdym etapie przetwarzania wstępnego fazy sortowania w pamięci przed zaktualizowaniem zbiorcze, ustawienie na wartość null spowoduje, że biblioteki użyć wartości domyślnej (updateItems.count, min 1000000).     |
   | Token anulowania|Token anulowania, aby bezpiecznie zamknąć aktualizacji zbiorczej. |

   **Definicja obiektu odpowiedzi aktualizacji zbiorczej** wynik aktualizacji zbiorczej wywołania interfejsu API zawiera następujące atrybuty:

   |**Parametr**  |**Opis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane poza tymi dostarczane do aktualizacji zbiorczej wywołania interfejsu API.      |
   |TotalRequestUnitsConsumed (podwójny)   |    Jednostki łączna liczba żądań (RU) używane przez aktualizacja zbiorcza wywołania interfejsu API.    |
   |TotalTimeTaken (TimeSpan)   | Łączny czas zbiorczego aktualizacji do ukończenia wykonywania wywołań interfejsu API. |
    
## <a name="performance-tips"></a>Porady dotyczące wydajności 

Korzystając z biblioteki program wykonujący zbiorcze, należy wziąć pod uwagę następujące punkty, w celu zapewnienia lepszej wydajności:

* Aby uzyskać najlepszą wydajność Uruchom aplikację z maszyną wirtualną platformy Azure, która znajduje się w tym samym regionie, co region zapisu konta usługi Cosmos DB.  

* Zalecane jest uruchamianie pojedynczego obiektu BulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej odpowiadający określonego kontenera usługi Cosmos DB.  

* Od momentu wykonania operacji interfejsu API pojedynczej zbiorczej zużywa duże fragment komputerze klienckim procesora CPU i sieci We/Wy. Dzieje się tak wewnętrznie duplikując wielu zadań, należy unikać duplikowania wiele równoczesnych zadań w procesie aplikacji wywoływanych w każdej wykonywanie zbiorczej operacji interfejsu API. W przypadku pojedynczej zbiorczej wywołania operacji interfejsu API, który działa na jednej maszynie wirtualnej nie można użyć całego kontenera przepływności (Jeśli Twój kontener przepływności > 1 mln jednostek RU/s), lepiej jest utworzyć osobne maszyny wirtualne, aby jednocześnie wykonać zbiorcze wywołania operacji interfejsu API.  

* Upewnij się, że InitializeAsync() jest wywoływana po utworzenie wystąpienia obiektu BulkExecutor można pobrać mapy partycji kontenera usługi Cosmos DB docelowego.  

* W pliku App.Config aplikacji, upewnij się, **gcserver —** jest włączona w celu zapewnienia lepszej wydajności
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteka emituje dane śledzenia, które mogą być zbierane do pliku dziennika lub w konsoli. Aby włączyć oba, Dodaj następujący element do pliku App.Config aplikacji.

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
* Dowiedz się więcej o szczegóły pakietu Nuget i biblioteki .NET wykonawca zbiorcze informacje o wersji, zobacz[zbiorczo Szczegóły zestawu SDK funkcji wykonawczej](sql-api-sdk-bulk-executor-dot-net.md). 
