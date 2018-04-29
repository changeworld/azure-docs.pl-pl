---
title: Rozpoczynanie pracy z magazynu tabel platformy Azure i programu Visual Studio połączone usługi (ASP.NET) | Dokumentacja firmy Microsoft
description: Jak rozpocząć korzystanie z magazynu tabel platformy Azure w projekcie platformy ASP.NET w programie Visual Studio po połączeniu z kontem magazynu przy użyciu programu Visual Studio usług połączonych
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z magazynu tabel platformy Azure i programu Visual Studio połączone usługi (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Przegląd

Magazyn tabel Azure umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz lub na zewnątrz w chmurze Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

Ten samouczek pokazuje, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostek magazynu tabel Azure. Te scenariusze obejmują tworzenie tabeli i dodanie, wyszukiwanie i usuwania jednostek tabeli. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto usługi Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**i z menu kontekstowego wybierz **kontrolera -> Dodaj**.

    ![Dodawanie kontrolera do aplikacji ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Na **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC 5 — pusty**i wybierz **Dodaj**.

    ![Określ typ kontrolera MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Na **Dodaj kontroler** okna dialogowego, nazwy kontrolera *TablesController*i wybierz **Dodaj**.

    ![Nazwa kontrolera MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Dodaj następujące *przy użyciu* dyrektywy `TablesController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Utwórz klasę modelu

Wiele przykładów w tym artykule użyj **TableEntity**-klasy o nazwie **CustomerEntity**. Poniższe kroki przedstawiono deklarowanie tę klasę jako klasę modelu:

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **modele**i z menu kontekstowego wybierz **klasy -> Dodaj**.

1. Na **Dodaj nowy element** okna dialogowego, nazwę klasy, **CustomerEntity**.

1. Otwórz `CustomerEntity.cs` pliku i dodaj następującą **przy użyciu** dyrektywy:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Zmodyfikuj klasy, tak, aby po zakończeniu zadeklarowana jest klasa, zgodnie z poniższym kodem. Klasa deklaruje klasy jednostki o nazwie **CustomerEntity** używającej imienia klienta jako klucza wiersza i nazwiska jako klucza partycji.

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
    }
    ```

## <a name="create-a-table"></a>Tworzenie tabeli

Poniższe kroki pokazano, jak utworzyć tabelę:

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki w [Konfigurowanie środowiska programowania](#set-up-the-development-environment). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **CreateTable** zwracającą **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **CreateTable** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabel.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do nazwy żądanej tabeli. **CloudTableClient.GetTableReference** — metoda nie powoduje żądanie magazyn tabel. Czy tabela istnieje, zwracany jest odwołanie. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Wywołanie **CloudTable.CreateIfNotExists** metodę w celu utworzenia tabeli, jeśli jeszcze nie istnieje. **CloudTable.CreateIfNotExists** metoda zwraca **true** Jeśli tabela nie istnieje i został utworzony pomyślnie. W przeciwnym razie **false** jest zwracany.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualizacja **obiekt ViewBag** z nazwą tabeli.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **CreateTable** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `CreateTable.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **Utwórz tabelę** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Utwórz tabelę](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak wspomniano wcześniej, **CloudTable.CreateIfNotExists** metoda zwraca **true** tylko po tabeli nie istnieje i jest tworzona. W związku z tym po uruchomieniu aplikacji, gdy tabela istnieje, metoda zwraca **false**. Aby uruchomić aplikację wiele razy, możesz usunąć tabeli przed ponownym uruchomieniem aplikacji. Usunięcie tabeli może odbywać się za pośrednictwem **CloudTable.Delete** metody. Możesz także usunąć za pomocą tabeli [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

*Jednostki* mapy w celu C\# obiektów przy użyciu niestandardowej klasy pochodzące z **TableEntity**. Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. W tej sekcji pojawi się, jak zdefiniować klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, niemniej użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Dla właściwości, które mają być przechowywane w usłudze tabel właściwość musi być właściwością publiczną obsługiwanego typu, która ujawnia zarówno ustawiania i pobierania wartości.
Klasa jednostki *musi* zadeklarować publicznego konstruktora bez parametrów.

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki w [Konfigurowanie środowiska programowania](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj następujące dyrektywy, aby kod w `TablesController.cs` mogą uzyskiwać dostęp do pliku **CustomerEntity** klasy:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Dodaj metodę o nazwie **AddEntity** zwracającą **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **AddEntity** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabel.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, do której zamierzasz dodać nową jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz wystąpienie i zainicjować **CustomerEntity** klasy.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Utwórz **TableOperation** obiekt, który wstawia jednostki klienta.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Wykonywanie operacji insert, wywołując **CloudTable.Execute** metody. Sprawdź wynik operacji, sprawdzając **TableResult.HttpStatusCode** właściwości. Kod stanu 2xx wskazuje, że akcja żądanego przez klienta zostało przetworzone pomyślnie. Na przykład pomyślnie wstawienia nowych jednostek skutkuje kod stanu HTTP 204, co oznacza, że operacja została pomyślnie przetworzył i serwer nie zwrócił żadnej zawartości.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualizacja **obiekt ViewBag** z nazwą tabeli i wyniki operacji insert.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **AddEntity** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `AddEntity.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **dodania jednostki** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Dodawanie jednostki](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Możesz sprawdzić, czy obiekt został dodany, wykonując kroki opisane w sekcji [pobrać pojedynczą jednostką](#get-a-single-entity). Można również użyć [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) do wyświetlania wszystkich jednostek tabel.

## <a name="add-a-batch-of-entities-to-a-table"></a>Dodawanie partię jednostek do tabeli

Oprócz możliwości [dodać jednostkę do jednej tabeli w czasie](#add-an-entity-to-a-table), możesz także dodać jednostek w partii. Dodawanie jednostek w partii zmniejsza liczbę przechodzenia między kodu i usługa tabel Azure. Następujące kroki przedstawiono sposób dodawania wielu jednostek do tabeli za pomocą jednej operacji wstawiania:

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki w [Konfigurowanie środowiska programowania](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **AddEntities** zwracającą **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **AddEntities** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabel.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, do której zamierzasz dodać nowe jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utworzenie wystąpienia niektórych obiektów klienta na podstawie **CustomerEntity** klasa przedstawione w sekcji modelu [dodać jednostkę do tabeli](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Pobierz **TableBatchOperation** obiektu.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Dodaj jednostki do obiektu operacji wstawiania wsadowego.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Wykonanie operacji wstawiania wsadowego przez wywołanie metody **CloudTable.ExecuteBatch** metody.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** metoda zwraca listę **TableResult** obiektów gdzie każdy **TableResult** obiektu można zbadać do ustalenia powodzenia lub niepowodzenia poszczególnych działań. Na przykład przekazania do widoku listy i pozwól widoku wyświetlane wyniki każdego działania. 
 
    ```csharp
    return View(results);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **AddEntities** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `AddEntities.cshtml`i zmodyfikuj go, aby wygląda podobnie do poniższego.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **Dodaj jednostki** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Dodawanie jednostek](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Możesz sprawdzić, czy obiekt został dodany, wykonując kroki opisane w sekcji [pobrać pojedynczą jednostką](#get-a-single-entity). Można również użyć [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) do wyświetlania wszystkich jednostek tabel.

## <a name="get-a-single-entity"></a>Pobierz pojedynczy element

W tej części przedstawiono sposób pobierania pojedyncza jednostka z tabeli za pomocą klucz wiersza jednostki i klucz partycji. 

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki w [Konfigurowanie środowiska programowania](#set-up-the-development-environment)i korzysta z danych [dodać partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetSingle** zwracającą **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **GetSingle** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabel.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, z którego są pobierane jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji pobierania, który przyjmuje do obiektu jednostki pochodne **TableEntity**. Pierwszym parametrem jest *partitionKey*, a drugi parametr jest *rowKey*. Przy użyciu **CustomerEntity** klasy i dane podane w sekcji [dodać partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table), poniższy fragment kodu wysyła zapytanie do tabeli dla **CustomerEntity**jednostki o *partitionKey* wartość "Smith" i *rowKey* wartość "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Wykonanie operacji pobierania.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Przekazać wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **GetSingle** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `GetSingle.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **Pobierz pojedynczy** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Pobierz pojedynczy](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Jak wspomniano w sekcji [dodać jednostkę do tabeli](#add-an-entity-to-a-table), partycji i klucz wiersza jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji. W tej części przedstawiono sposób tworzenia zapytań tabeli dla wszystkich obiektów z określonej partycji.  

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki w [Konfigurowanie środowiska programowania](#set-up-the-development-environment)i korzysta z danych [dodać partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetPartition** zwracającą **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **GetPartition** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabel.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, z którego są pobierane jednostek. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz wystąpienie **TableQuery** obiekt określający zapytanie w **gdzie** klauzuli. Przy użyciu **CustomerEntity** klasy i dane podane w sekcji [dodać partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table), poniższy fragment kodu wysyła zapytanie do tabeli dla wszystkich jednostek gdzie  **PartitionKey** (nazwiska) ma wartość "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. W pętli, wywołaj **CloudTable.ExecuteQuerySegmented** metody przekazanie obiektu zapytanie utworzone w poprzednim kroku.  **CloudTable.ExecuteQuerySegmented** metoda zwraca **TableContinuationToken** obiekt, który — podczas **null** — wskazuje, że nie ma żadnych kolejnych jednostek do pobrania. W pętli Użyj innego pętli w celu wykonania iteracji zwrócone jednostki. W poniższym przykładzie kodu każdy zwrócony obiekt zostanie dodany do listy. Po zakończenia pętli, listy jest przekazywany do widoku do wyświetlenia: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **GetPartition** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `GetPartition.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **pobrać partycji** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Pobierz partycji](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Usuwanie jednostki

W tej części przedstawiono sposób usuwania jednostek z tabeli.

> [!NOTE]
> 
> W tej sekcji założono zostały wykonane kroki w [Konfigurowanie środowiska programowania](#set-up-the-development-environment)i korzysta z danych [dodać partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **DeleteEntity** zwracającą **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **DeleteEntity** metody get **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure: (zmiany  *&lt;nazwy konta magazynu >* do nazwy konta magazynu Azure uzyskujesz dostęp do.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabel.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, z której po usunięciu jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji delete, który przyjmuje do obiektu jednostki pochodne **TableEntity**. W takim przypadku stosujemy **CustomerEntity** klasy i dane podane w sekcji [dodać partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). Jednostki **ETag** musi mieć ustawioną prawidłową wartość.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Wykonanie operacji usuwania.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Przekazać wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**, a z menu kontekstowego wybierz **Dodaj -> Widok**.

1. Na **Dodaj widok** okna dialogowego, wprowadź **DeleteEntity** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `DeleteEntity.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **Shared -> widoki** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **usuwanie jednostek** aby zobaczyć wyniki, podobnie jak na poniższym zrzucie ekranu:
  
    ![Pobierz pojedynczy](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Wprowadzenie do magazynu obiektów blob platformy Azure i programu Visual Studio połączone usługi (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Rozpoczynanie pracy z magazynem kolejek Azure i programu Visual Studio połączone usługi (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
