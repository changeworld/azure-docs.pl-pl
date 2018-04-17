---
title: Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu programu .NET | Microsoft Docs
description: Przechowywanie danych strukturalnych w chmurze za pomocą Magazynu tabel Azure, magazyn danych NoSQL.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: ff26ab122e920d6ca8dbf837a2229f8728a471ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage to usługa, która przechowuje dane NoSQL ze strukturą w chmurze, udostępniając magazyn par klucz-atrybut z projektem bez schematu. Ponieważ Magazyn tabel nie ma schematu, łatwo zaadaptować dane do rozwijających się potrzeb aplikacji. Dla większości aplikacji dostęp do danych w usłudze Table Storage jest szybki i ekonomiczny, jest też zazwyczaj tańszy od tradycyjnego rozwiązania SQL dla podobnych ilości danych.

Usługa Table Storage umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkowników dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach i inne typy metadanych, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek, a konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

### <a name="about-this-tutorial"></a>Informacje o tym samouczku
Ten samouczek przedstawia sposób użycia [Biblioteka tabeli CosmosDB Microsoft Azure dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) w typowych scenariuszy magazynu tabel Azure. Nazwa pakietu wskazuje go do użytku z bazy danych rozwiązania Cosmos platformy Azure, a pakiet działa zarówno z bazy danych Azure rozwiązania Cosmos i magazynu tabel Azure, każda usługa ma tylko unikatowe punktu końcowego. Te scenariusze są przedstawione przy użyciu języka C# przykłady ilustrujące jak:
* Tworzenie i usuwanie tabel
* Wstawianie, aktualizowanie i usuwanie wierszy
* Tabele kwerendy

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia samouczka niezbędne są:

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Wspólna biblioteka magazynu Azure dla platformy .NET (wersja zapoznawcza)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). To jest podgląd wymaganego pakietu, który jest obsługiwany w środowisku produkcyjnym. 
* [Biblioteka CosmosDB tabeli platformy Microsoft Azure dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [Menedżer konfiguracji Azure dla programu .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Konto usługi Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Więcej przykładów
Dodatkowe przykłady użycia usługi Table Storage znajdziesz w temacie [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Rozpoczynanie pracy z usługą Azure Table Storage w programie .NET). Możesz pobrać przykładową aplikację i uruchomić ją lub przejrzeć kod w witrynie GitHub.

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
Najprościej jest utworzyć pierwsze konto usługi Azure Storage przy użyciu witryny [Azure Portal](https://portal.azure.com). Więcej informacji można znaleźć w temacie [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Można również utworzyć konto usługi Azure Storage przy użyciu programu [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [interfejsu wiersza polecenia Azure](../storage/common/storage-azure-cli.md) lub [biblioteki klienta dostawcy zasobów magazynowych dla platformy .NET](/dotnet/api/microsoft.azure.management.storage).

Jeśli nie chcesz teraz tworzyć konta magazynu, możesz także użyć emulatora usługi Azure Storage do uruchomienia i testowania kodu w środowisku lokalnym. Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](../storage/common/storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta usługi interfejsu API Azure rozwiązania Cosmos DB tabeli
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego
Następnie skonfiguruj środowisko projektowe w programie Visual Studio, aby przygotować się do wypróbowania przykładów kodu zawartych w tym przewodniku.

### <a name="create-a-windows-console-application-project"></a>Utwórz projekt aplikacji konsoli dla systemu Windows
W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. W poniższej procedurze pokazano, jak utworzyć aplikację konsolową w programie Visual Studio 2017. Procedura jest podobna w innych wersjach programu Visual Studio.

1. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.
2. Wybierz **zainstalowane** > **Visual C#** > **klasycznego pulpitu systemu Windows**.
3. Wybierz pozycję **Aplikacja konsoli (.NET Framework)**.
4. Wprowadź nazwę aplikacji w polu **Nazwa**.
5. Kliknij przycisk **OK**.

Wszystkie przykłady kodu w tym samouczku można dodać do metody `Main()` w pliku `Program.cs` aplikacji konsolowej.

Można użyć biblioteki Azure CosmosDB tabeli w dowolnego typu aplikacji .NET, w tym chmury Azure usługi lub aplikacji sieci web oraz aplikacje komputerów stacjonarnych i przenośnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

### <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów
Istnieją trzy zalecane pakiety, który należy odwoływać się do projektu do ukończenia tego samouczka:

* [Biblioteki typowych Azure Storage dla platformy .NET (wersja zapoznawcza)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). 
* [Microsoft Azure rozwiązania Cosmos DB tabeli biblioteki dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Ten pakiet zapewnia dostęp programistyczny do zasobów danych na Twoje konto magazynu tabel Azure lub interfejsu API Azure rozwiązania Cosmos DB tabeli konta.
* [Biblioteka programu Microsoft Azure Configuration Manager dla środowiska .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): ten pakiet zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji, niezależnie od tego, gdzie została uruchomiona aplikacja.

Pakiet NuGet służy do pobrania obu pakietów. Wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.
2. Wyszukaj w trybie online "Microsoft.Azure.Storage.Common", a następnie wybierz **zainstalować** zainstalować wspólnej biblioteki usługi Azure Storage dla platformy .NET (wersja zapoznawcza) i jego zależności. Upewnij się, **Uwzględnij wersję wstępną** pole wyboru zostało zaznaczone, ponieważ jest to pakiet w wersji zapoznawczej.
3. Wyszukaj w trybie online "Microsoft.Azure.CosmosDB.Table", a następnie wybierz **zainstalować** zainstalować Biblioteka tabeli CosmosDB usługi Microsoft Azure.
4. Wyszukaj w trybie online "WindowsAzure.ConfigurationManager", a następnie wybierz **zainstalować** zainstalować Biblioteka programu Microsoft Azure Configuration Manager.

> [!NOTE]
> Zależności ODataLib w bibliotece wspólnej Storage dla platformy .NET są rozwiązywane przez pakiety ODataLib dostępne na NuGet, a nie z usługi danych WCF. Biblioteki ODataLib można pobrać bezpośrednio lub użyć odwołań w projekcie kodu za pośrednictwem pakietu NuGet. Określone pakiety ODataLib używane przez Bibliotekę klienta usługi Storage to [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) i [Spatial](http://nuget.org/packages/System.Spatial/). Te biblioteki są używane przez klasy magazynu tabel Azure, są one wymaganymi zależnościami do programowania za pomocą wspólnej biblioteki magazynu.
> 
> 

> [!TIP]
> Deweloperzy już zapoznać się z magazynem tabel Azure może użyto [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) pakietu w przeszłości. Zaleca się, że wszystkie nowe aplikacje tabeli używać [wspólna biblioteka magazynu Azure](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) i [biblioteki tabeli DB rozwiązania Cosmos Azure](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table), ale pakiet WindowsAzure.Storage nadal jest obsługiwany. Jeśli używasz biblioteki WindowsAzure.Storage obejmują Microsoft.WindowsAzure.Storage.Table w za pomocą instrukcji.
>
>

### <a name="determine-your-target-environment"></a>Określanie środowiska docelowego
W przypadku uruchamiania przykładów w tym przewodniku istnieją dwie opcje środowiska:

* Można uruchomić kod dla konta usługi Azure Storage w chmurze. 
* Można uruchomić kod dla konta bazy danych Azure rozwiązania Cosmos w chmurze.
* Można uruchomić kod dla emulatora usługi Azure Storage. Emulator magazynu jest lokalnym środowiskiem, które emuluje konto usługi Azure Storage w chmurze. Emulator jest bezpłatną opcją do testowania i debugowania kodu, gdy aplikacja jest w fazie projektowania. Emulator używa dobrze znanego konta i klucza. Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../storage/common/storage-use-emulator.md).

Jeśli obiektem docelowym jest konto magazynu w chmurze, skopiuj podstawowy klucz dostępu dla konta magazynu z witryny Azure Portal. Aby uzyskać więcej informacji, zobacz temat [View and copy storage access keys](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys) (Wyświetlanie i kopiowanie kluczy dostępu kopiowania).

> [!NOTE]
> Na obiekt docelowy można wybrać emulator magazynu, aby uniknąć ponoszenia kosztów związanych z usługą Azure Storage. Jednak jeśli wybranym obiektem docelowym będzie konto usługi Azure Storage w chmurze, koszty związane z wykonaniem instrukcji w tym samouczku będą niewielkie.
> 
> 

Jeśli ma być przeznaczona dla konta bazy danych Azure rozwiązania Cosmos, skopiuj podstawowy klucz dostępu dla konta tabeli interfejsu API w portalu Azure. Aby uzyskać więcej informacji, zobacz [zaktualizować parametry połączenia](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
Biblioteka wspólnej magazynu Azure dla środowiska .NET obsługuje konfigurowania punktów końcowych i poświadczeń do uzyskiwania dostępu do usług magazynu przy użyciu parametrów połączenia magazynu. Najlepiej przechowywać parametry połączenia magazynu w pliku konfiguracji. 

Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Konfigurowanie parametrów połączenia z usługą Azure Storage](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Klucz konta jest podobny do hasła głównego konta magazynu. Zawsze chroń klucz konta magazynu. Nie udostępniaj go innym użytkownikom, nie koduj go trwale ani nie zapisuj w zwykłym pliku tekstowym, do którego mają dostęp inne osoby. Wygeneruj ponownie klucz za pośrednictwem witryny Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.
> 
> 

Aby skonfigurować parametry połączenia, otwórz plik `app.config` z Eksploratora rozwiązań w programie Visual Studio. Dodaj zawartość elementu `<appSettings>` widocznego poniżej. Zastąp `account-name` o nazwie konta, a `account-key` kluczem dostępu do konta:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Na przykład jeśli używasz konta usługi Azure Storage, ustawień konfiguracji wyglądać podobnie do:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Jeśli używasz konta bazy danych Azure rozwiązania Cosmos ustawień konfiguracji wyglądać podobnie do:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Jeśli obiektem docelowym ma być emulator magazynu, możesz użyć skrótu klawiaturowego, który mapuje dobrze znaną nazwę konta i klucz. W takim przypadku ustawienie parametrów połączenia wygląda następująco:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Dodawanie dyrektyw using
Dodaj następujące dyrektywy **using** na początku pliku `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Tworzenie klienta usługi tabel
Klasa [CloudTableClient][dotnet_CloudTableClient] umożliwia pobieranie tabel i jednostek przechowywanych w usłudze Table Storage. Oto jeden ze sposobów tworzenia klienta usługi Table service:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Teraz możesz przystąpić do pisania kodu, który będzie odczytywać dane z Magazynu tabel i zapisywać je w nim.

## <a name="create-a-table"></a>Tworzenie tabeli
W tym przykładzie pokazano, jak utworzyć tabelę, jeśli jeszcze nie istnieje:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Jednostki są mapowane na obiekty języka C# za pomocą niestandardowej klasy pochodzącej od klasy [TableEntity][dotnet_TableEntity]. Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, niemniej użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Jednostki, które mają być przechowywane w tabelach, muszą być obsługiwanego typu, na przykład muszą pochodzić od klasy [TableEntity][dotnet_TableEntity]. Właściwości jednostki, które zamierzasz przechowywać w tabeli, muszą być publicznymi właściwościami typu i obsługiwać zarówno pobieranie, jak i ustawianie wartości. Ponadto typ jednostki *musi* ujawniać konstruktor bez parametrów.

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

Operacje tabeli obejmujące jednostki są wykonywane za pośrednictwem obiektu [CloudTable][dotnet_CloudTable] utworzonego wcześniej w sekcji „Tworzenie tabeli”. Operacja do wykonania jest reprezentowana przez obiekt [TableOperation][dotnet_TableOperation]. W poniższym przykładzie kodu pokazano tworzenie obiektu [CloudTable][dotnet_CloudTable], a następnie obiektu **CustomerEntity**. Aby przygotować operację, obiekt [TableOperation][dotnet_TableOperation] jest tworzony w celu umieszczenia jednostki klienta w tabeli. Na koniec operacja jest wykonywana przez wywołanie obiektu [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
Możesz wstawić partię jednostek do tabeli w jednej operacji zapisu. Inne wybrane uwagi dotyczące operacji zbiorczych:

* W tej samej, pojedynczej operacji zbiorczej możesz przeprowadzić aktualizację, usuwanie i wstawianie.
* Jedna operacja zbiorcza może zawierać do 100 jednostek.
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.
* W operacji zbiorczej można wykonywać zapytanie, ale musi to być jedyna operacja w operacji zbiorczej.

Poniższy przykład kodu tworzy dwa obiekty jednostki i dodaje je do obiektu [TableBatchOperation][dotnet_TableBatchOperation] za pomocą metody [Insert][dotnet_TableBatchOperation_Insert]. Następnie obiekt [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] jest wywoływany, aby wykonać operację.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Aby wysłać zapytanie do tabeli dla wszystkich obiektów w partycji, użyj obiektu [TableQuery][dotnet_TableQuery]. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji
Jeśli nie chcesz wykonywać zapytania dla wszystkich jednostek w partycji, możesz określić zakres, łącząc filtr klucza partycji z filtrem klucza wiersza. Poniższy przykład kodu wykorzystuje dwa filtry do pobrania wszystkich jednostek w partycji „Smith”, w których klucz wiersza (imię) rozpoczyna się od litery alfabetu wcześniejszej niż „E”, a następnie drukuje wyniki zapytania.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod używa obiektu [TableOperation][dotnet_TableOperation] do określenia klienta „Ben Smith”. Metoda ta zwraca tylko jedną jednostkę zamiast ich zbioru, a zwrócona wartość w pozycji [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] jest obiektem **CustomerEntity**. Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Zastępowanie jednostki
Aby zaktualizować jednostkę, pobierz ją z usługi tabel, zmodyfikuj obiekt jednostki, a następnie zapisz zmiany w usłudze tabel. Poniższy kod zmienia istniejący numer telefonu klienta. Zamiast wywoływać metodę [Insert][dotnet_TableOperation_Insert], ten kod używa metody [Replace][dotnet_TableOperation_Replace]. Metoda [Replace][dotnet_TableOperation_Replace] powoduje, że jednostka zostanie całkowicie zastąpiona na serwerze, chyba że jednostka na serwerze zmieniła się od czasu jej pobrania. W takim przypadku operacja nie powiedzie się. Ten błąd uniemożliwia aplikacji nieodwracalne nadpisanie zmiany dokonanej pomiędzy pobraniem i zaktualizowaniem przez inny składnik aplikacji. W przypadku tego błędu prawidłowa procedura obejmuje ponowne pobranie jednostki, wprowadzenie zmian (jeśli nadal mają zastosowanie), a następnie ponowne przeprowadzenie operacji [Replace][dotnet_TableOperation_Replace]. W następnej sekcji opisano sposób zastąpienia tego zachowania.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Wstawianie lub zastępowanie jednostki
Operacje [Replace][dotnet_TableOperation_Replace] zakończą się niepowodzeniem, jeśli jednostka została zmieniona od czasu jej pobrania z serwera. Ponadto aby operacja [Replace][dotnet_TableOperation_Replace] zakończyła się powodzeniem, jednostka musi zostać wcześniej pobrana z serwera. Czasami jednak nie wiadomo, czy jednostka istnieje na serwerze oraz czy obecne wartości przechowywane w jednostce są istotne. Aktualizacja powinna nadpisać je wszystkie. Aby to zrobić, użyj operacji [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Ta operacja wstawi jednostkę, jeśli jednostka nie istnieje, lub zastąpi ją, jeśli już istnieje — niezależnie od czasu ostatniej aktualizacji.

W poniższym przykładzie kodu jednostka klienta dla „Fred Jones” jest tworzona i wstawiana do tabeli „people”. Następnie używany operacji [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] do zapisania na serwerze jednostki z tym samym kluczem partycji (Jones) i kluczem wiersza (Fred), ale tym razem z inną wartością właściwości PhoneNumber. Ponieważ używamy operacji [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], wszystkie wartości właściwości tej jednostki są zastępowane. Jeśli jednak jednostka „Fred Jones” nie istniałaby w tabeli, zostałaby wstawiona.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki
Zapytanie tabeli może pobrać kilka właściwości jednostki zamiast wszystkich właściwości jednostki. Ta technika, zwana projekcją, redukuje przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Zapytanie w poniższym kodzie zwraca wyłącznie adresy e-mail jednostek w tabeli. Operację tę przeprowadza się za pomocą zapytania [DynamicTableEntity][dotnet_DynamicTableEntity] lub [EntityResolver][dotnet_EntityResolver]. Użytkownik może dowiedzieć się więcej o projekcji z [wpisu na blogu Introducing Upsert and Query Projection][blog_post_upsert] (Wprowadzenie operacji upsert i projekcji zapytań). Funkcja projekcji nie jest obsługiwana w emulatorze magazynu, dlatego ten kod zadziała tylko w przypadku użycia konta w usłudze Table service.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Można z łatwością usunąć jednostkę po jej pobraniu, korzystając z tego samego wzorca co w przypadku aktualizowania jednostki. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Usuwanie tabeli
Poniższy przykład kodu usuwa tabelę z konta magazynu. Tabeli, która została usunięta, nie będzie można ponownie utworzyć przez dany okres czasu po jej usunięciu.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Pobieranie asynchroniczne jednostek na stronach
Jeśli odczytujesz dużą liczbę jednostek i chcesz przetworzyć/wyświetlić jednostki po ich pobraniu, zamiast czekać na zwrócenie ich wszystkich, możesz pobierać jednostki za pomocą zapytania segmentowanego. W tym przykładzie przedstawiono sposób zwracania wyników na stronach za pomocą wzorca Async-Await, dzięki czemu wykonanie nie jest blokowane podczas oczekiwania na zwrócenie dużych zestawów wyników. Aby uzyskać szczegółowe informacje o wykorzystaniu wzorca Async-Await w programie .NET, zobacz [Asynchronous Programming with Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Programowanie asynchroniczne z Async i Await [C# i Visual Basic]).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi Table Storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu:

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* Więcej przykładów użycia usługi Table Storage znajdziesz w temacie [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Rozpoczynanie pracy z usługą Azure Table Storage w programie .NET)
* Przejrzyj dokumentację referencyjną usługi Table service, aby uzyskać szczegółowe informacje o dostępnych interfejsach API:
* [Dokumentacja biblioteki klienta usługi Storage dla programu .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Dokumentacja interfejsu API REST](http://msdn.microsoft.com/library/azure/dd179355)
* Dowiedz się, jak uprościć zapisywany kod, aby pracować z usługą Azure Storage za pomocą zestawu [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.
* Zapoznaj się z tematem [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md), aby przechowywać dane bez struktury.
* [Połącz się z usługą SQL Database przy użyciu platformy .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md), aby zapisać dane relacyjne.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
