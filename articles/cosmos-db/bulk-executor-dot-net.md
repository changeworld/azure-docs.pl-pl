---
title: Użycie zbiorczego programu .NET Library do wykonywania operacji importu zbiorczego i aktualizacji w Azure Cosmos DB
description: Zbiorcze importowanie i aktualizowanie Azure Cosmos DB dokumentów przy użyciu zbiorczego modułu wykonawczego platformy .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 26e4c4edbcfc282fffd0d43673d3585cfe973848
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241285"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Użycie zbiorczej biblioteki programu .NET do wykonywania operacji zbiorczych w Azure Cosmos DB

Ten samouczek zawiera instrukcje dotyczące korzystania z biblioteki programu .NET Bulk modułu wykonawczego Azure Cosmos DB do importowania i aktualizowania dokumentów do kontenera usługi Azure Cosmos. Aby dowiedzieć się więcej o bibliotece narzędzia do wykonywania zbiorczego i jak można wykorzystać ogromną przepływność i magazyn, zobacz artykuł [Omówienie biblioteki wykonawców zbiorczych](bulk-executor-overview.md) . W tym samouczku zostanie wyświetlona Przykładowa aplikacja .NET, która zbiorczo importuje losowo wygenerowane dokumenty do kontenera usługi Azure Cosmos. Po zaimportowaniu pokazuje, jak można zbiorczo aktualizować zaimportowane dane, określając poprawki jako operacje do wykonania w określonych polach dokumentu. 

Obecnie zbiorcze wykonawca biblioteka jest obsługiwana przez interfejsu API SQL usługi Azure Cosmos DB i tylko konta interfejsu API języka Gremlin. W tym artykule opisano sposób użycia zbiorczej procedury tworzenia biblioteki .NET z kontami interfejsu API SQL. Aby dowiedzieć się więcej o korzystaniu z biblioteki .NET wykonawca zbiorcze za pomocą interfejsu API języka Gremlin, zobacz [wykonywały operacje zbiorcze w usłudze Azure Cosmos DB — interfejs API Gremlin](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać [program Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niego. Upewnij się, że włączasz Programowanie na platformie Azure podczas instalacji programu Visual Studio.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 

* Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań. Można też użyć [emulatora Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) z `https://localhost:8081` punktem końcowym. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticating-requests).

* Utwórz konto Azure Cosmos DB interfejsu API SQL, korzystając z procedury opisanej w sekcji [Tworzenie konta bazy danych](create-sql-api-dotnet.md#create-account) artykułu przewodnika Szybki Start platformy .NET. 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem, pobierając kilka przykładowych aplikacji platformy .NET z usługi GitHub. Te aplikacje wykonują operacje zbiorcze na Azure Cosmos DB danych. Aby sklonować aplikacje, Otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz je skopiować, a następnie uruchom następujące polecenie:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Sklonowane repozytorium zawiera dwa przykłady "BulkImportSample" i "BulkUpdateSample". Możesz otworzyć jedną z przykładowych aplikacji, zaktualizować parametry połączenia w pliku App. config przy użyciu parametrów połączenia konta Azure Cosmos DB, skompilować rozwiązanie i uruchomić je. 

Aplikacja "BulkImportSample" generuje losowe dokumenty i zbiorczo importuje je do Azure Cosmos DB. Aplikacja "BulkUpdateSample" zbiorczo aktualizuje zaimportowane dokumenty, określając poprawki jako operacje do wykonania w określonych polach dokumentu. W następnych sekcjach opisano kod w każdej z tych przykładowych aplikacji.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Zbiorcze importowanie danych do usługi Azure Cosmos DB

1. Przejdź do folderu "BulkImportSample" i Otwórz plik "BulkImportSample. sln".  

2. Parametry połączenia Azure Cosmos DB są pobierane z pliku App. config, jak pokazano w poniższym kodzie:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Importer zbiorczy tworzy nową bazę danych i kontener z nazwą bazy danych, nazwą kontenera i wartościami przepływności określonymi w pliku App. config. 

3. Następny obiekt DocumentClient jest inicjowany z bezpośrednim trybem połączenia TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Obiekt BulkExecutor jest inicjowany z wysoką wartością ponawiania dla żądań czasu oczekiwania i ograniczania. A następnie są ustawiane na 0, aby przekazać kontrolę przeciążenia do BulkExecutor przez jego okres istnienia.  

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

5. Aplikacja wywołuje interfejs API BulkImportAsync. Biblioteka .NET oferuje dwa przeciążenia interfejsu API importowania zbiorczego — jeden, który akceptuje listę serializowanych dokumentów JSON, a drugi akceptuje listę deserializowanych dokumentów POCO. Aby dowiedzieć się więcej na temat definicji każdej z tych przeciążonych metod, zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Metoda BulkImportAsync akceptuje następujące parametry:**
   
   |**Parametr**  |**Opis** |
   |---------|---------|
   |enableUpsert    |   Flaga umożliwiająca upserts dokumentów. Jeśli dokument o podanym identyfikatorze już istnieje, jest on aktualizowany. Domyślnie jest ustawiona wartość false.      |
   |disableAutomaticIdGeneration    |    Flagi, aby wyłączyć automatyczne generowanie identyfikatora. Domyślnie jest ustawiona na wartość true.     |
   |maxConcurrencyPerPartitionKeyRange    | Maksymalny stopień współbieżności dla zakresu kluczy partycji, ustawienie na wartość null spowoduje, że biblioteka użyje wartości domyślnej równej 20. |
   |maxInMemorySortingBatchSize     |  Maksymalna liczba dokumentów ściągniętych z modułu wyliczającego dokumenty, które są przesyłane do wywołania interfejsu API na każdym etapie.  W przypadku wstępnego przetwarzania w pamięci fazy sortowania przed zbiorczym zaimportowaniem wartość null spowoduje użycie przez bibliotekę wartości domyślnej minimum (Documents. Count, 1000000).       |
   |cancellationToken    |    Token anulowania, aby bezpiecznie zakończyć import zbiorczy.     |

   **Definicja obiektu odpowiedzi importu zbiorczego** Wynik wywołania interfejsu API importu zbiorczego zawiera następujące atrybuty:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |NumberOfDocumentsImported (Long)   |  Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z dostarczonych do zbiorczego dokumentów zaimportować wywołania interfejsu API.       |
   |TotalRequestUnitsConsumed (Double)   |   Łączna liczba jednostek żądania (RU) używane przez większość zaimportować wywołania interfejsu API.      |
   |TotalTimeTaken (TimeSpan)    |   Łączny czas za pomocą importowania zbiorczego do ukończenia wykonywania wywołań interfejsu API.      |
   |BadInputDocuments (lista\<> obiektów)   |     Lista dokumentów nieprawidłowego formatu, które nie zostały pomyślnie zaimportowane zbiorczo zaimportować wywołania interfejsu API. Użytkownik powinien rozwiązać zwróconych dokumentów i ponów próbę importowania. Sformatowana niewłaściwe dokumenty zawierają dokumentów, których wartość Identyfikatora jest ciąg (wartość null lub jakikolwiek inny typ danych jest uznawane za nieprawidłowe).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcza aktualizacja danych w usłudze Azure Cosmos DB

Należy zaktualizować istniejące dokumenty przy użyciu interfejsu API BulkUpdateAsync. W tym przykładzie zostanie ustawiona nazwa pola na nową wartość i usuń pole opisu z istniejących dokumentów. Pełny zestaw obsługiwanych operacji aktualizacji pól można znaleźć w [dokumentacji interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Przejdź do folderu "BulkUpdateSample" i Otwórz plik "BulkUpdateSample. sln".  

2. Zdefiniuj elementy aktualizacji wraz z odpowiednimi operacjami aktualizacji pola. W tym przykładzie użyjesz SetUpdateOperation aktualizuje pole nazwy i UnsetUpdateOperation, aby usunąć pole opisu z wszystkich dokumentów. Możesz także wykonywać inne operacje, takie jak zwiększenie pole dokumentu o określoną wartość, wypychanie określone wartości do pola tablicy lub usunąć określoną wartość z pola tablicy. Aby dowiedzieć się więcej o różnych metodach dostarczonych przez interfejs API aktualizacji zbiorczej, zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. Aplikacja wywołuje interfejs API BulkUpdateAsync. Aby dowiedzieć się więcej na temat definicji metody BulkUpdateAsync, zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Metoda BulkUpdateAsync akceptuje następujące parametry:**

   |**Parametr**  |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Maksymalny stopień współbieżności dla zakresu kluczy partycji, ustawienie na wartość null spowoduje, że biblioteka użyje wartości domyślnej równej 20.   |
   |maxInMemorySortingBatchSize    |    Maksymalna liczba elementów aktualizacji pobranych z numeratora elementów aktualizacji została przeniesiona do wywołania interfejsu API na każdym etapie dla fazy sortowania przed rozpoczęciem przetwarzania w pamięci przed aktualizacją zbiorczą, ustawienie wartości null spowoduje, że biblioteka użyje wartości domyślnej minimum (updateItems. Count, 1000000).     |
   | cancellationToken|Token anulowania, aby bezpiecznie zakończyć aktualizację zbiorczą. |

   **Definicja obiektu odpowiedzi aktualizacji zbiorczej** Wynik wywołania interfejsu API aktualizacji zbiorczej zawiera następujące atrybuty:

   |**Parametr**  |**Opis** |
   |---------|---------|
   |NumberOfDocumentsUpdated (Long)    |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane z tych dostarczonych do wywołania interfejsu API aktualizacji zbiorczej.      |
   |TotalRequestUnitsConsumed (Double)   |    Jednostki łączna liczba żądań (RU) używane przez aktualizacja zbiorcza wywołania interfejsu API.    |
   |TotalTimeTaken (TimeSpan)   | Łączny czas zbiorczego aktualizacji do ukończenia wykonywania wywołań interfejsu API. |
    
## <a name="performance-tips"></a>Porady dotyczące wydajności 

Korzystając z biblioteki program wykonujący zbiorcze, należy wziąć pod uwagę następujące punkty, w celu zapewnienia lepszej wydajności:

* Aby uzyskać najlepszą wydajność, uruchom aplikację z maszyny wirtualnej platformy Azure, która znajduje się w tym samym regionie, co region zapisu konta Cosmos DB.  

* Zaleca się utworzenie wystąpienia pojedynczego obiektu BulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej odpowiadającej określonemu kontenerowi Cosmos.  

* Od momentu wykonania operacji interfejsu API pojedynczej zbiorczej zużywa duże fragment komputerze klienckim procesora CPU i sieci We/Wy. Dzieje się tak wewnętrznie duplikując wielu zadań, należy unikać duplikowania wiele równoczesnych zadań w procesie aplikacji wywoływanych w każdej wykonywanie zbiorczej operacji interfejsu API. Jeśli pojedyncze wywołanie interfejsu API operacji zbiorczej działające na jednej maszynie wirtualnej nie jest w stanie wykorzystać przepływności całego kontenera (jeśli przepływność kontenera > 1 000 000 RU/s), preferowane jest utworzenie oddzielnych maszyn wirtualnych do współbieżnego wykonania wywołania interfejsu API operacji zbiorczej.  

* Upewnij się, że InitializeAsync () jest wywoływana po utworzeniu wystąpienia obiektu BulkExecutor w celu pobrania mapowanej mapy partycji kontenera Cosmos.  

* W pliku App. config aplikacji upewnij się, że **gcServer** jest włączona w celu uzyskania lepszej wydajności
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Biblioteka emituje dane śledzenia, które mogą być zbierane w pliku dziennika lub w konsoli programu. Aby włączyć oba, Dodaj następujące elementy do pliku App. config aplikacji.

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
* Aby dowiedzieć się więcej na temat szczegółów pakietu NuGet i informacji o wersji biblioteki programu .NET do zbiorczego modułu wykonawczego, zobacz[Szczegóły zestawu SDK modułu wykonawczy](sql-api-sdk-bulk-executor-dot-net.md). 
