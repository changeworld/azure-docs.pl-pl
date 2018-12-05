---
title: 'Azure Cosmos DB: programowanie za pomocą interfejsu Table API na platformie .NET'
description: Dowiedz się, jak tworzyć aplikacje za pomocą interfejsu Table API usługi Azure Cosmos DB przy użyciu platformy .NET
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 02c4ead0f41463a70cc7123427193f835d9cca94
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877739"
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: programowanie za pomocą interfejsu Table API na platformie .NET

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Azure Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

Ten samouczek obejmuje następujące zadania: 

> [!div class="checklist"] 
> * Tworzenie konta usługi Azure Cosmos DB 
> * Włączenie funkcjonalności w pliku app.config 
> * Utworzenie tabeli przy użyciu [interfejsu Table API](table-introduction.md)
> * Dodawanie jednostki do tabeli 
> * Zbiorcze wstawianie jednostek 
> * Pobieranie pojedynczej jednostki 
> * Odpytywanie jednostek przy użyciu automatycznych indeksów pomocniczych 
> * Zastępowanie jednostki 
> * Usuwanie jednostki 
> * Usuwanie tabeli
 
## <a name="tables-in-azure-cosmos-db"></a>Tabele w usłudze Azure Cosmos DB 

Usługa Azure Cosmos DB udostępnia [interfejs Table API](table-introduction.md) dla aplikacji wymagających magazynu typu klucz-wartość niekorzystającego ze schematu. Interfejs Table API usługi Azure Cosmos DB i usługa [Azure Table Storage](../storage/common/storage-introduction.md) teraz obsługują te same zestawy SDK i interfejsy API REST. Usługa Azure Cosmos DB umożliwia tworzenie tabel o wysokich wymaganiach dotyczących przepływności.

Ten samouczek jest przeznaczony dla deweloperów, którzy są zaznajomieni z zestawem Azure Table Storage SDK i chcą korzystać z funkcji warstwy Premium dostępnych w usłudze Azure Cosmos DB. Jest on oparty na temacie [Get Started with Azure Table storage using .NET (Wprowadzenie do usługi Azure Table Storage za pomocą platformy .NET)](table-storage-how-to-use-dotnet.md) i przedstawia sposób korzystania z dodatkowych możliwości, takich jak indeksy pomocnicze, aprowizowana przepływność i obsługa wielu regionów. W tym samouczku opisano sposób użycia witryny Azure Portal do utworzenia konta usługi Azure Cosmos DB, a następnie skompilowania i wdrożenia aplikacji interfejsu Table API. Przedstawiamy także szczegółowo przykłady dla platformy .NET umożliwiające utworzenie i usunięcie tabeli oraz wstawianie, aktualizowanie, usuwanie i odpytywanie danych tabeli. 

Jeśli obecnie używasz usługi Azure Table Storage, korzystając z interfejsu Table API usługi Azure Cosmos DB, uzyskasz następujące korzyści:

- Natychmiastowa możliwość przeprowadzenia [globalnej dystrybucji](distribute-data-globally.md) z obsługą wielu regionów oraz [automatycznego i ręcznego trybu failover](high-availability.md)
- Obsługa automatycznego indeksowania wszystkich właściwości niezależna od schematu („indeksy pomocnicze”) i szybkie zapytania 
- Obsługa [niezależnego skalowania magazynu i przepływności](partition-data.md) na dowolną liczbą regionów
- Obsługa [dedykowanej przepływności dla tabeli](request-units.md), którą można skalować w zakresie od setek do milionów żądań na sekundę
- Obsługa [pięciu dostosowywalnych poziomów spójności](consistency-levels.md) umożliwiających zrównoważenie dostępności, opóźnienia i spójności w zależności od potrzeb aplikacji
- Dostępność na poziomie 99,99% dla pojedynczego regionu z możliwością dodania kolejnych regionów w celu uzyskania wyższej dostępności oraz [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) w ramach ogólnej dostępności
- Praca z istniejącym zestawem Azure Storage .NET SDK bez wprowadzania zmian w kodzie aplikacji

Ten samouczek obejmuje korzystanie z interfejsu Table API usługi Azure Cosmos DB przy użyciu zestawu .NET SDK. Zestaw [Azure Cosmos DB Table API .NET SDK](https://aka.ms/tableapinuget) możesz pobrać za pomocą narzędzia NuGet.

Aby dowiedzieć się więcej o złożonych zadaniach usługi Azure Table Storage, zobacz:

* [Wprowadzenie do interfejsu Table API usługi Azure Cosmos DB](table-introduction.md)
* Dokumentacja referencyjna usługi Table zawiera szczegółowe informacje o interfejsach API dostępnych w zestawie [Azure Cosmos DB Table API .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>Informacje o tym samouczku
Ten samouczek jest przeznaczony dla deweloperów, którzy są zaznajomieni z zestawem Azure Table Storage SDK i chcą korzystać z funkcji w warstwie Premium dostępnych w usłudze Azure Cosmos DB. Jest on oparty na temacie [Get Started with Azure Table storage using .NET (Wprowadzenie do usługi Azure Table Storage za pomocą platformy .NET)](table-storage-how-to-use-dotnet.md) i przedstawia sposób korzystania z dodatkowych możliwości, takich jak indeksy pomocnicze, aprowizowana przepływność i obsługa wielu regionów. Opisujemy sposób użycia witryny Azure Portal do utworzenia konta usługi Azure Cosmos DB, a następnie skompilowania i wdrożenia aplikacji interfejsu Table. Przedstawiamy także szczegółowo przykłady dla platformy .NET umożliwiające utworzenie i usunięcie tabeli oraz wstawianie, aktualizowanie, usuwanie i odpytywanie danych tabeli. 

Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Zacznijmy od utworzenia konta usługi Azure Cosmos DB w witrynie Azure Portal.  
 
> [!IMPORTANT]  
> Musisz utworzyć nowe konto interfejsu API tabeli, aby pracować z ogólnie dostępnymi zestawami SDK interfejsu API tabeli. Konta interfejsu API tabeli utworzone w okresie obowiązywania wersji zapoznawczej nie są obsługiwane przez ogólnie dostępne zestawy SDK. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Tabela z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz okno terminala usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do folderu instalacji aplikacji przykładowej. 

    ```bash
    cd "C:\git-samples"
    ```

2. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Następnie otwórz plik rozwiązania w programie Visual Studio. 

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych. 

1. W witrynie [Azure Portal](http://portal.azure.com/) kliknij pozycję **Parametry połączenia**. 

    Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować PODSTAWOWE PARAMETRY POŁĄCZENIA.

    ![Wyświetlanie i kopiowanie PARAMETRÓW POŁĄCZENIA w panelu parametrów połączenia](./media/create-table-dotnet/connection-string.png)

2. W programie Visual Studio otwórz plik app.config. 

3. Usuń znaczniki komentarza dla ciągu StorageConnectionString w wierszu 8 i wstaw znaczniki komentarza dla ciągu StorageConnectionString w wierszu 7, ponieważ ten samouczek nie korzysta z emulatora pamięci. Wiersze 7 i 8 powinny teraz wyglądać następująco:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Wklej PODSTAWOWE PARAMETRY POŁĄCZENIA z portalu do wartości StorageConnectionString w wierszu 8. Wklej parametry wewnątrz cudzysłowów.
   
    > [!IMPORTANT]
    > Jeśli dany punkt końcowy korzysta z adresu documents.azure.com, oznacza to, że masz konto w wersji zapoznawczej i musisz utworzyć [nowe konto interfejsu API tabeli](#create-a-database-account), aby korzystać z dostępnego ogólnie zestawu SDK API tabeli. 
    >

    Wiersz 8 powinien teraz wyglądać podobnie do:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Zapisz plik app.config.

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

## <a name="azure-cosmos-db-capabilities"></a>Możliwości usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje wiele funkcji, które nie są dostępne za pośrednictwem interfejsu API usługi Azure Table Storage. 

Niektóre funkcjonalności są dostępne przy pomocy nowych przeciążeń metody CreateCloudTableClient umożliwiających określenie zasad połączenia i poziomu spójności.

| Ustawienia połączenia interfejsu Table | Opis |
| --- | --- |
| Tryb połączenia  | Usługa Azure Cosmos DB obsługuje dwa tryby połączenia. W trybie `Gateway` żądania są zawsze kierowane do bramy usługi Azure Cosmos DB, która przekazuje je do odpowiednich partycji danych. W trybie `Direct` klient pobiera mapowanie tabel na partycje, a żądania są wykonywane bezpośrednio względem partycji danych. Zalecamy użycie domyślnego trybu `Direct`.  |
| Protokół połączenia | Usługa Azure Cosmos DB obsługuje dwa protokoły połączenia — `Https` i `Tcp`. Wartość `Tcp` jest domyślna i zalecana, ponieważ ten protokół powoduje mniejsze obciążenie. |
| Preferowane lokalizacje | Lista rozdzielana przecinkami zawierająca preferowane lokalizacje (z obsługą wielu regionów) dla odczytów. Każde konto usługi Azure Cosmos DB można skojarzyć z różną liczbą regionów — od 1 do ponad 30. Każde wystąpienie klienta może określić podzbiór tych regionów w kolejności preferencji w celu zminimalizowania opóźnienia odczytu. Regiony muszą być nazwane za pomocą ich [nazw wyświetlanych](https://msdn.microsoft.com/library/azure/gg441293.aspx), na przykład `West US`. Zobacz też temat [Multi-homing APIs (Interfejsy API z obsługi wielu regionów)](tutorial-global-distribution-table.md). |
| Poziom spójności | Istnieje możliwość zrównoważenia opóźnienia, spójności i dostępności przez wybranie jednego z pięciu dobrze zdefiniowanych poziomów spójności: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` i `Eventual`. Wartość domyślna to `Session`. Wybór poziomu spójności daje znaczącą różnicę wydajności w przypadku konfiguracji obejmujących wiele regionów. Szczegóły zawiera temat [Consistency levels (Poziomy spójności)](consistency-levels.md). |

Inne funkcje można włączyć za pomocą następujących wartości konfiguracji `appSettings`.

| Klucz | Opis |
| --- | --- |
| TableQueryMaxItemCount | Umożliwia skonfigurowanie maksymalnej liczby elementów zwracanych przez zapytanie tabeli w ramach jednej rundy. Wartość domyślna `-1` umożliwia usłudze Azure Cosmos DB dynamiczne określanie wartości w czasie wykonywania. |
| TableQueryEnableScan | Jeśli zapytanie nie może użyć indeksu dla żadnego filtru, uruchom je mimo to za pomocą funkcji skanowania. Wartość domyślna to `false`.|
| TableQueryMaxDegreeOfParallelism | Stopień równoległości dla wykonania zapytania obejmującego wiele partycji. Wartość `0` oznacza przetwarzanie szeregowe bez pobierania z wyprzedzeniem, wartość `1` oznacza przetwarzanie szeregowe z pobieraniem z wyprzedzeniem, a wyższe wartości zwiększają stopień równoległości. Wartość domyślna `-1` umożliwia usłudze Azure Cosmos DB dynamiczne określanie wartości w czasie wykonywania. |

Aby zmienić wartość domyślną, otwórz plik `app.config` w Eksploratorze rozwiązań programu Visual Studio. Dodaj zawartość elementu `<appSettings>` widocznego poniżej. Zastąp ciąg `account-name` nazwą konta magazynu, a ciąg `account-key` kluczem dostępu do konta. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Dokonajmy szybkiego przeglądu działań wykonywanych w aplikacji. Otwórz plik `Program.cs`, aby zobaczyć te wiersze kodu tworzące zasoby interfejsu Table. 

## <a name="create-the-table-client"></a>Tworzenie klienta interfejsu Table
Należy zainicjować obiekt `CloudTableClient`, aby nawiązać połączenie z kontem interfejsu Table.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Ten klient zostanie zainicjowany przy użyciu wartości konfiguracji `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` i `TablePreferredLocations`, jeśli są one określone w ustawieniach aplikacji.

## <a name="create-a-table"></a>Tworzenie tabeli
Następnie należy utworzyć tabelę za pomocą obiektu `CloudTable`. Tabele w usłudze Azure Cosmos DB można skalować niezależnie pod względem magazynu i przepływności, a partycjonowanie jest obsługiwane automatycznie przez usługę. Usługa Azure Cosmos DB obsługuje tabele o stałym rozmiarze i bez limitu rozmiaru. Szczegółowe informacje zawiera temat [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partition-data.md). 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Istnieje ważna różnica w sposobie tworzenia tabel. Usługa Azure Cosmos DB rezerwuje przepływność — w przeciwieństwie do modelu usługi Azure Storage opartego na zużyciu i przeznaczonego dla transakcji. Przepływność jest dedykowana/rezerwowana, więc nigdy nie jest ograniczana, jeśli tempo wysyłania żądań jest na poziomie aprowizowanej przepływności lub mniejsze.

Możesz skonfigurować przepływność domyślną, określając ją jako parametr metody CreateIfNotExists.

Odczyt jednostki o rozmiarze 1 KB jest normalizowany jako 1 jednostka RU, a inne operacje są normalizowane do stałej liczby jednostek RU na podstawie zużycia procesora CPU, pamięci i wartości IOPS. Dowiedz się więcej o [jednostkach żądania (RU) w usłudze Azure Cosmos DB](request-units.md), w szczególności w odniesieniu do [magazynów typu klucz-wartość](key-value-store-cost.md).

Następnie przejdziemy przez proste operacje odczytu i zapisu (CRUD) przy użyciu zestawu Azure Table Storage SDK. W tym samouczku przedstawiono przewidywalne małe opóźnienia (na poziomie pojedynczych milisekund) i szybkie zapytania udostępniane przez usługę Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Jednostki w usłudze Azure Table Storage rozszerzają klasę `TableEntity` i muszą mieć właściwości `PartitionKey` i `RowKey`. Oto przykładowa definicja jednostki klienta.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Poniższy fragment kodu przedstawia sposób wstawiania jednostki za pomocą zestawu Azure Storage SDK. Usługę Azure Cosmos DB zaprojektowano w taki sposób, aby opóźnienia były małe przy dowolnej skali i na całym świecie.

Operacje zapisu są kończone w czasie poniżej 15 ms na poziomie p99 i w około 6 ms na poziomie p50 w przypadku aplikacji uruchomionych w tym samym regionie co konto usługi Azure Cosmos DB. Ten czas obejmuje potwierdzenie zapisów wysłane do klienta po ich synchronicznym zreplikowaniu, trwałym zatwierdzeniu i zindeksowaniu całej zawartości.


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek
Usługa Azure Table Storage obsługuje interfejs API operacji wsadowych, który pozwala na łączenie operacji aktualizacji, usunięcia i wstawienia w jednej operacji wsadowej.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
Operacje pobrania (GET) w usłudze Azure Cosmos DB są kończone w czasie poniżej 10 ms na poziomie p99 i w około 1 ms na poziomie p50 w tym samym regionie Azure. Możesz dodać wiele regionów do konta w celu uzyskania odczytów z małymi opóźnieniami i wdrożyć aplikacje pod kątem odczytu z ich lokalnych regionów (obsługujących wiele sieci), ustawiając wartość `TablePreferredLocations`. 

Możesz pobrać pojedynczą jednostkę przy użyciu następującego fragmentu kodu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Dowiedz się więcej o interfejsach API do obsługi wielu regionów na stronie [Developing with multiple regions (Programowania dla wielu regionów)](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Odpytywanie jednostek przy użyciu automatycznych indeksów pomocniczych
Tabele można odpytywać za pomocą klasy `TableQuery`. Usługa Azure Cosmos DB zawiera aparat bazy danych zoptymalizowany pod kątem zapisów, który automatycznie indeksuje wszystkie kolumny w tabeli. Indeksowanie w usłudze Cosmos Azure DB jest niezależne od schematu. Dlatego nawet jeśli wiersze mają różne schematy lub schemat zmienia się z czasem, jest on automatycznie indeksowany. Ponieważ usługa Azure Cosmos DB używa automatycznych indeksów pomocniczych, zapytania względem dowolnej właściwości mogą użyć indeksu, a ich obsługa jest wydajna.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Usługa Azure Cosmos DB obsługuje tę samą funkcjonalność zapytań co usługa Azure Table Storage w przypadku interfejsu Table API. Usługa Azure Cosmos DB obsługuje także sortowanie, agregaty, zapytania geoprzestrzenne, hierarchię i szeroki zakres funkcji wbudowanych. Przegląd tych możliwości jest dostępny na stronie [Azure Cosmos DB query (Zapytanie usługi Azure Cosmos DB)](how-to-sql-query.md). 

## <a name="replace-an-entity"></a>Zastępowanie jednostki
Aby zaktualizować jednostkę, pobierz ją z usługi tabel, zmodyfikuj obiekt jednostki, a następnie zapisz zmiany w usłudze tabel. Poniższy kod zmienia istniejący numer telefonu klienta. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Podobnie możesz wykonać operację `InsertOrMerge` lub `Merge`.  

## <a name="delete-an-entity"></a>Usuwanie jednostki
Można z łatwością usunąć jednostkę po jej pobraniu, korzystając z tego samego wzorca co w przypadku aktualizowania jednostki. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Usuwanie tabeli
Poniższy przykład kodu usuwa tabelę z konta magazynu. Możesz usunąć i natychmiast utworzyć ponownie tabelę za pomocą usługi Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku omówiliśmy rozpoczęcie pracy przy użyciu interfejsu Table API usługi Azure Cosmos DB. W jego ramach wykonano następujące zadania: 

> [!div class="checklist"] 
> * Utworzenie konta usługi Azure Cosmos DB 
> * Włączenie funkcji w pliku app.config 
> * Utworzenie tabeli 
> * Dodanie jednostki do tabeli 
> * Wstawienie partii jednostek 
> * Pobranie pojedynczej jednostki 
> * Odpytanie jednostek przy użyciu automatycznych indeksów pomocniczych 
> * Zastąpienie jednostki 
> * Usunięcie jednostki 
> * Usunięcie tabeli  

Teraz możesz przejść do następnego samouczka i dowiedzieć się więcej o odpytywaniu danych w tabeli. 

> [!div class="nextstepaction"]
> [Query with the Table API (Odpytywanie za pomocą interfejsu Table API)](tutorial-query-table.md)
