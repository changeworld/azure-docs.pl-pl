---
title: 'Azure Cosmos DB: programowanie za pomocą interfejsu SQL API na platformie .NET | Microsoft Docs'
description: Dowiedz się, jak tworzyć aplikacje za pomocą interfejsu SQL API usługi Azure Cosmos DB przy użyciu platformy .NET
services: cosmos-db
documentationcenter: ''
author: rafats
manager: jhubbard
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 7fca9f184097ed50ace665cde0c5ef8fb180feda
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmosdb-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: programowanie za pomocą interfejsu SQL API na platformie .NET

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów. 

Ten samouczek pokazuje, jak utworzyć konto usługi Azure Cosmos DB przy użyciu witryny Azure Portal oraz jak utworzyć kolekcję i bazę danych dokumentów z [kluczem partycji](sql-api-partition-data.md#partition-keys) przy użyciu [interfejsu SQL API na platformie .NET](sql-api-introduction.md). Dzięki zdefiniowaniu klucza partycji podczas tworzenia kolekcji aplikacja jest przygotowana do łatwego skalowania wraz ze wzrostem ilości danych. 

Ten samouczek obejmuje następujące zadania wykonywane za pomocą [interfejsu SQL API na platformie .NET](sql-api-sdk-dotnet.md):

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB
> * Tworzenie kolekcji i bazy danych przy użyciu klucza partycji
> * Tworzenie dokumentów JSON
> * Aktualizowanie dokumentu
> * Wykonywanie zapytań o kolekcje partycjonowane
> * Uruchamianie procedur składowanych
> * Usuwanie dokumentu
> * Usuwanie bazy danych

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Zacznijmy od utworzenia konta usługi Azure Cosmos DB w witrynie Azure Portal.

> [!TIP]
> * Masz już konto usługi Azure Cosmos DB? Jeśli tak, przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS)
> * Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do sekcji [Konfigurowanie rozwiązania programu Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Konfigurowanie rozwiązania programu Visual Studio
1. Otwórz program **Visual Studio** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Szablony** / **Visual C#** / **Aplikacja konsolowa (.NET Framework)**, podaj nazwę projektu, a następnie kliknij przycisk **OK**.
   ![Zrzut ekranu przedstawiający okno Nowy projekt](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy nową aplikację konsolową, która znajduje się w ramach rozwiązania Visual Studio, a następnie kliknij pozycję **Zarządzaj pakietami NuGet...**
    
    ![Zrzut ekranu przedstawiający menu projektu kliknięte prawym przyciskiem myszy](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. Na karcie **NuGet** kliknij pozycję **Przeglądaj** i wpisz ciąg  **documentdb** w polu wyszukiwania.
<!---stopped here--->
6. W wynikach znajdź pozycję **Microsoft.Azure.DocumentDB** i kliknij przycisk **Zainstaluj**.
   Identyfikator pakietu biblioteki klienta usługi Azure Cosmos DB to [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Zrzut ekranu przedstawiający menu NuGet służące do znajdowania zestawu SDK klienta usługi Azure Cosmos DB](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Jeśli zostanie wyświetlony komunikat dotyczący przejrzenia zmian wprowadzonych w rozwiązaniu, kliknij przycisk **OK**. Jeśli wyświetlany jest komunikat o akceptacji licencji, kliknij pozycję **Akceptuję**.

## <a id="Connect"></a>Dodawanie odwołań do projektu
W pozostałych krokach tego samouczka przedstawiono fragmenty kodu interfejsu SQL API wymagane do tworzenia i aktualizowania zasobów usługi Azure Cosmos DB w projekcie.

Najpierw dodaj te odwołania do aplikacji.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Łączenie aplikacji

Następnie dodaj te dwie stałe i zmienną *klienta* do aplikacji.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Wróć do witryny [Azure Portal](https://portal.azure.com), aby pobrać adres URL punktu końcowego i klucz podstawowy. Adres URL punktu końcowego i klucz podstawowy są niezbędne, aby aplikacja wiedziała, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji.

W witrynie Azure Portal przejdź do konta usługi Azure Cosmos DB, kliknij pozycję **Klucze**, a następnie kliknij pozycję **Klucze odczytu i zapisu**.

Skopiuj identyfikator URI z portalu i wklej go w miejsce elementu `<your endpoint URL>` w pliku program.cs. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce elementu `<your primary key>`. Pamiętaj o usunięciu elementu `<` i `>` z własnych wartości.

![Zrzut ekranu przedstawiający witrynę Azure Portal używaną przez samouczek NoSQL do tworzenia aplikacji konsolowej C#. Przedstawia konto usługi Azure Cosmos DB z pozycją KLUCZE wyróżnioną w bloku konta usługi Azure Cosmos DB oraz wartościami IDENTYFIKATOR URI i KLUCZ PODSTAWOWY wyróżnionymi w bloku Klucze](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Tworzenie wystąpienia klasy DocumentClient

Teraz utwórz nowe wystąpienie klasy **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Tworzenie bazy danych

Następnie utwórz [bazę danych](sql-api-resources.md#databases) usługi Azure Cosmos DB za pomocą metody [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) lub metody [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) klasy  **DocumentClient** z zestawu [SQL SDK na platformie .NET](sql-api-sdk-dotnet.md). Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Wybieranie klucza partycji 

Kolekcje to kontenery do przechowywania dokumentów. Są one zasobami logicznymi i mogą [obejmować co najmniej jedną partycję fizyczną](partition-data.md). [Klucz partycji](sql-api-partition-data.md) to właściwość (lub ścieżka) w dokumentach używana do dystrybuowania danych między serwerami lub partycjami. Wszystkie dokumenty z takim samym kluczem partycji są przechowywane w jednej partycji. 

Określenie klucza partycji przed utworzeniem kolekcji jest ważnym krokiem. Klucze partycji to właściwości (lub ścieżki) w dokumentach używane przez usługę Azure Cosmos DB do dystrybuowania danych między wieloma serwerami lub partycjami. Usługa Cosmos DB skraca wartość klucza partycji i używa skróconego wyniku w celu określenia partycji do przechowywania dokumentu. Wszystkie dokumenty z tym samym kluczem partycji są przechowywane w tej samej partycji. Kluczy partycji nie można zmienić po utworzeniu kolekcji. 

W tym samouczku ustawimy klucz partycji `/deviceId` tak, aby wszystkie dane na jednym urządzeniu były przechowywane w jednej partycji. Wskazany jest wybór klucza partycji z dużą liczbą wartości. Wartości te powinny być używane w przybliżeniu z taką samą częstotliwością, co sprawi, że usługa Cosmos DB będzie mogła równoważyć obciążenie wraz ze wzrostem ilości danych i osiągać pełną przepływność kolekcji. 

Aby uzyskać więcej informacji na temat partycjonowania, zobacz [How to partition and scale in Azure Cosmos DB? (Jak partycjonować i skalować w usłudze Azure Cosmos DB?)](partition-data.md) 

## <a id="CreateColl"></a>Tworzenie kolekcji 

Teraz, gdy znamy klucz partycji `/deviceId`, utwórzmy [kolekcję](sql-api-resources.md#collections) za pomocą metody [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) lub metody [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i dowolnej skojarzonej logiki aplikacji JavaScript. 

> [!WARNING]
> Tworzenie kolekcji ma wpływ na cenę, ponieważ rezerwujesz przepływność aplikacji na potrzeby komunikowania się z usługą Azure Cosmos DB. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Ta metoda przeprowadza wywołanie interfejsu API REST do usługi Azure Cosmos DB, a usługa aprowizuje liczbę partycji na podstawie żądanej przepływności. Można zmienić przepływność kolekcji, ponieważ wydajność musi się zmieniać, przy użyciu zestawu SDK lub witryny [Azure Portal](set-throughput.md).

## <a id="CreateDoc"></a>Tworzenie dokumentów JSON
Teraz można wstawić wybrane dokumenty JSON do usługi Azure Cosmos DB. [Dokument](sql-api-resources.md#documents) można utworzyć za pomocą metody [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) klasy **DocumentClient**. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Ta przykładowa klasa zawiera odczyt urządzenia i wywołanie metody CreateDocumentAsync umożliwiające wstawienie nowego odczytu urządzenia do kolekcji.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>Odczyt danych

Odczytajmy dokument za pomocą klucza partycji i identyfikatora przy użyciu metody ReadDocumentAsync. Należy pamiętać, że odczyty obejmują wartość PartitionKey (odpowiadającą nagłówkowi żądania `x-ms-documentdb-partitionkey` w interfejsie API REST).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Aktualizowanie danych

Teraz zaktualizujmy wybrane dane przy użyciu metody ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Usuwanie danych

Teraz usuńmy dokument według klucza partycji i identyfikatora przy użyciu metody DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Wykonywanie zapytań o kolekcje partycjonowane

Kiedy wykonujesz zapytanie o dane w kolekcjach partycjonowanych, usługa Azure Cosmos DB automatycznie przekierowuje zapytanie do partycji odpowiadających wartościom klucza partycji określonym w filtrze (jeśli istnieją). Na przykład to zapytanie jest kierowane tylko do partycji zawierającej klucz partycji „XMS 0001”.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Poniższe zapytanie nie ma filtra klucza partycji (DeviceId) i jest rozsyłane do wszystkich partycji, w których jest wykonywane względem indeksu partycji. Należy pamiętać, że trzeba określić element EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` w interfejsie API REST), aby zestaw SDK wykonał zapytanie w partycjach.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Równoległe wykonywanie zapytań
Zestawy SQL SDK usługi Azure Cosmos DB w wersji 1.9.0 i nowszej obsługują opcje równoległego wykonywania zapytań, które umożliwiają wykonywanie zapytań o małych opóźnieniach dotyczących kolekcji partycjonowanych nawet wtedy, gdy konieczne jest uwzględnienie dużej liczby partycji. Na przykład poniższe zapytanie zostało skonfigurowane do uruchomienia równoległego w ramach partycji.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Możesz zarządzać równoległym wykonywaniem zapytań przez dostrojenie następujących parametrów:

* Ustawienie elementu `MaxDegreeOfParallelism` pozwala kontrolować stopień równoległości, czyli maksymalną liczbę równoczesnych połączeń sieciowych z partycją w kolekcji. Jeśli ustawisz ten element na -1, stopień równoległości będzie zarządzany przez zestaw SDK. Jeśli element `MaxDegreeOfParallelism` nie zostanie określony lub ustawiony na 0 (wartość domyślna), zostanie nawiązane jedno połączenie sieciowe z partycjami kolekcji.
* Ustawiając element `MaxBufferedItemCount`, można osiągnąć kompromis między opóźnieniem zapytania i wykorzystaniem pamięci po stronie klienta. Pominięcie tego parametru lub ustawienie go na -1 spowoduje, że liczba elementów buforowanych podczas równoległego wykonywania zapytań będzie zarządzana przez zestaw SDK.

Jeśli kolekcja będzie mieć taki sam stan, zapytanie równoległe zwróci wyniki w tej samej kolejności jak wykonanie szeregowe. Podczas wykonywania zapytań między partycjami z uwzględnieniem sortowania (opcje ORDER BY i/lub TOP) zestaw SDK SQL uruchamia zapytania równolegle w partycjach i scala częściowo posortowane wyniki po stronie klienta, aby wygenerować wyniki uporządkowane globalnie.

## <a name="execute-stored-procedures"></a>Wykonywanie procedur składowanych
Dodatkowo można wykonywać transakcje niepodzielne względem dokumentów z takim samym identyfikatorem urządzenia, np. jeśli zarządzasz wartościami agregowanymi lub najnowszym stanem urządzenia w jednym dokumencie, dodając poniższy kod do projektu.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

I to wszystko. Są to główne składniki aplikacji usługi Azure Cosmos DB, która używa klucza partycji do efektywnego skalowania dystrybucji danych w partycjach.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij unikatową nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności: 

> [!div class="checklist"]
> * Utworzenie konta usługi Azure Cosmos DB
> * Utworzenie bazy danych i kolekcji przy użyciu klucza partycji
> * Utworzenie dokumentów JSON
> * Zaktualizowanie dokumentu
> * Wykonanie zapytań o kolekcje partycjonowane
> * Uruchomienie procedury składowanej
> * Usunięcie dokumentu
> * Usunięcie bazy danych

Teraz możesz przejść do następnego samouczka i zaimportować dodatkowe dane do konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
