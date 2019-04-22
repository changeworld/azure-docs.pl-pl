---
title: Rozpoczynanie pracy z usługą Azure table storage i Visual Studio połączonych usług (ASP.NET) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę, przy użyciu usługi Azure table storage w projektach programu ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: ea50506df53bfd586656d0030be4536d9d3b907d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59010145"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z usługą Azure table storage i Visual Studio połączonych usług (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie

Usługa Azure Table storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz i na zewnątrz chmury platformy Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostki usługi Azure table storage. Te scenariusze obejmują tworzenie tabeli i dodawanie, wyszukiwanie i usuwania jednostek tablic. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto usługi Azure Storage](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**i wybierz z menu kontekstowego **Dodaj -> kontrolera**.

    ![Dodawanie kontrolera do aplikacji ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Na **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC 5 — pusty**i wybierz **Dodaj**.

    ![Określ typ kontrolera MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Na **Dodaj kontroler** okno dialogowe, nazwy kontrolera *TablesController*i wybierz **Dodaj**.

    ![Nazwa kontrolera MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Dodaj następujący kod *przy użyciu* dyrektywy `TablesController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Utwórz klasę modelu

Większość przykładów w tym artykule korzystają **TableEntity**-pochodne klasy o nazwie **CustomerEntity**. Poniższe kroki prowadzą przez zadeklarowanie tę klasę jako klasę modelu:

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **modeli**i wybierz z menu kontekstowego **Dodaj -> klasa**.

1. Na **Dodaj nowy element** okno dialogowe, nazwa klasy, **CustomerEntity**.

1. Otwórz `CustomerEntity.cs` pliku i Dodaj następujący kod **przy użyciu** dyrektywy:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Zmodyfikuj klasę, tak, aby po zakończeniu tej klasy jest zadeklarowana tak jak w poniższym kodzie. Klasa deklaruje klasę jednostki o nazwie **CustomerEntity** , używa imienia klienta jako klucz wiersza i nazwiska jako klucza partycji.

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

Poniższe kroki ilustrują Tworzenie tabeli:

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki opisane w [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **CreateTable** zwracającego **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **CreateTable** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do nazwy żądanej tabeli. **CloudTableClient.GetTableReference** metoda nie powoduje żądanie względem usługi table storage. Czy tabela istnieje, zwracany jest odwołanie. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Wywołaj **CloudTable.CreateIfNotExists** metodę, aby utworzyć tabelę, jeśli jeszcze nie istnieje. **CloudTable.CreateIfNotExists** metoda zwraca **true** Jeśli tabela nie istnieje i został pomyślnie utworzony. W przeciwnym razie **false** jest zwracana.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualizacja **obiekt ViewBag** nazwą tabeli.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **CreateTable** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `CreateTable.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Tworzenie tabeli** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Utwórz tabelę](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak wspomniano wcześniej, **CloudTable.CreateIfNotExists** metoda zwraca **true** tylko, gdy w tabeli nie istnieje i zostanie utworzony. W związku z tym, po uruchomieniu aplikacji, gdy tabela istnieje, metoda zwraca **false**. Aby uruchomić aplikację wiele razy, możesz usunąć tabeli przed ponownym uruchomieniem aplikacji. Usunięcie tabeli może odbywać się za pośrednictwem **CloudTable.Delete** metody. Możesz także usunąć tabeli przy użyciu [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

*Jednostki* mapy do C\# obiektów przy użyciu niestandardowej klasy pochodne **TableEntity**. Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. W tej sekcji pokazano, jak zdefiniować klasę jednostki, która używa imienia klienta jako klucz wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, niemniej użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Dla dowolnej właściwości, które mają być przechowywane w usłudze table service właściwość musi być właściwością publiczną obsługiwanego typu, która ujawnia zarówno ustawiania i pobierania wartości.
Klasa jednostki *musi* zadeklarować publicznego konstruktora bez parametrów.

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki opisane w [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj następujące dyrektywy, aby kod w `TablesController.cs` mogą uzyskiwać dostęp do pliku **CustomerEntity** klasy:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Dodaj metodę o nazwie **AddEntity** zwracającego **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **AddEntity** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, do której zamierzasz dodać nową jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utworzyć wystąpienia i zainicjalizować **CustomerEntity** klasy.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Tworzenie **TableOperation** obiektu, który wstawia jednostki klienta.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Wykonywanie operacji insert, wywołując **CloudTable.Execute** metody. Sprawdź wynik operacji, sprawdzając **TableResult.HttpStatusCode** właściwości. Kod stanu 2xx wskazuje, że akcja zażądane przez klienta zostało przetworzone pomyślnie. Na przykład pomyślne wstawienia nowych jednostek skutkuje kod stanu HTTP 204, co oznacza, że operacja zostało pomyślnie przetworzone i serwer nie zwrócił żadnej zawartości.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualizacja **obiekt ViewBag** za pomocą nazwy tabeli i wyniki operacji wstawiania.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **AddEntity** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `AddEntity.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj jednostkę** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Dodawanie jednostki](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Możesz sprawdzić, czy jednostka został dodany, wykonując czynności opisane w sekcji [pobrać pojedynczą jednostkę](#get-a-single-entity). Można również użyć [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) Aby wyświetlić wszystkie jednostki dla tabel.

## <a name="add-a-batch-of-entities-to-a-table"></a>Dodawanie partii jednostek do tabeli

Oprócz możliwości [Dodawanie jednostki do tabeli, jeden w danym momencie](#add-an-entity-to-a-table), możesz również dodać jednostki w usłudze batch. Dodawanie jednostek w usłudze batch zmniejsza liczbę rund między kodu i usługą Azure table. Poniższe kroki pokazują, jak dodać wiele jednostek do tabeli za pomocą jednej operacji wstawiania:

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki opisane w [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **AddEntities** zwracającego **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **AddEntities** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, do której chcesz dodać nowe jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Wystąpienia niektórych obiektów klienta na podstawie **CustomerEntity** klasa znajdujące się w sekcji modelu [Dodawanie jednostki do tabeli](#add-an-entity-to-a-table).

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

1. Dodawanie jednostki do obiektu operacji wstawiania wsadowego.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Wykonywanie operacji wstawiania wsadowego przez wywołanie metody **CloudTable.ExecuteBatch** metody.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** metoda zwraca listę **TableResult** obiektów gdzie każdy **TableResult** obiektu można zbadać do ustalenia powodzenia lub niepowodzenia każdego pojedynczego działania. W tym przykładzie należy przekazać listy do widoku i pozwól widoku wyświetlania wyników każdej operacji. 
 
    ```csharp
    return View(results);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **AddEntities** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `AddEntities.cshtml`i zmodyfikuj go tak, aby wygląda podobnie do poniższego.

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj jednostki** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Dodawanie jednostek](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Możesz sprawdzić, czy jednostka został dodany, wykonując czynności opisane w sekcji [pobrać pojedynczą jednostkę](#get-a-single-entity). Można również użyć [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) Aby wyświetlić wszystkie jednostki dla tabel.

## <a name="get-a-single-entity"></a>Pobieranie pojedynczej jednostki

W tej sekcji pokazano, jak można pobrać pojedynczą jednostkę z tabeli za pomocą klucz wiersza jednostki i klucz partycji. 

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki opisane w [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment)oraz korzysta z danych [Dodawanie partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetSingle** zwracającego **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **GetSingle** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, z którego są pobierane za jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operację pobierania, który przyjmuje obiekt jednostki pochodzące z **TableEntity**. Pierwszy parametr jest *partitionKey*, a drugi parametr jest *rowKey*. Za pomocą **CustomerEntity** klas i danych znajdujące się w sekcji [Dodawanie partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table), poniższy fragment kodu wysyła zapytanie tabeli **CustomerEntity**jednostki o *partitionKey* wartość "Kowalski" i *rowKey* wartość "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Wykonywanie operacji pobierania.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Przekazać wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **GetSingle** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `GetSingle.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **pobieranie pojedynczego** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Pobieranie pojedynczego](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Pobrania wszystkich jednostek w partycji

Jak wspomniano w sekcji [Dodawanie jednostki do tabeli](#add-an-entity-to-a-table), kombinacja partycji i klucz wiersza jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji. W tej sekcji pokazano, jak wysyłać zapytania do tabeli dla wszystkich obiektów z określonej partycji.  

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki opisane w [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment)oraz korzysta z danych [Dodawanie partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetPartition** zwracającego **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **GetPartition** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, z którego są pobierane jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz wystąpienie **TableQuery** obiekt określający zapytanie w **gdzie** klauzuli. Za pomocą **CustomerEntity** klas i danych znajdujące się w sekcji [Dodawanie partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table), poniższy fragment kodu wysyła zapytanie tabeli dla wszystkich obiektów gdzie **PartitionKey**  (nazwiska) ma wartość "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. W ramach pętli, należy wywołać **CloudTable.ExecuteQuerySegmented** metoda przekazanie obiektu zapytanie utworzone w poprzednim kroku.  **CloudTable.ExecuteQuerySegmented** metoda zwraca **TableContinuationToken** obiekt - podczas **null** — wskazuje, że więcej jednostek do pobrania. W pętli należy użyć innej pętli do wykonywania iteracji zwrócone jednostki. W poniższym przykładzie kodu każdy obiekt zwrócony zostanie dodany do listy. Po zakończeniu pętli listy jest przekazywany do widoku do wyświetlenia: 

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **GetPartition** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `GetPartition.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **uzyskać partycji** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Pobierz partycji](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Usuwanie jednostki

W tej sekcji przedstawiono sposób usuwania jednostki z tabeli.

> [!NOTE]
> 
> W tej sekcji założono, zostały wykonane kroki opisane w [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment)oraz korzysta z danych [Dodawanie partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **DeleteEntity** zwracającego **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **DeleteEntity** metody, Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu: (Zmień  *&lt;nazwa konta magazynu >* nazwę konta usługi Azure storage uzyskiwany jest dostęp.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiekt, który reprezentuje odwołanie do tabeli, z którego są Usuwanie jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji usuwania, który przyjmuje obiekt jednostki pochodzące z **TableEntity**. W tym przypadku używamy **CustomerEntity** klas i danych znajdujące się w sekcji [Dodawanie partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). Jednostki **ETag** musi być ustawione na prawidłową wartość.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Wykonaj operację usuwania.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Przekazać wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folderu, kliknij prawym przyciskiem myszy **tabel**i z menu kontekstowego wybierz pozycję **Dodaj -> Widok**.

1. Na **Dodaj widok** okno dialogowe, wprowadź **DeleteEntity** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `DeleteEntity.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków -> udostępnione** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Usuń jednostkę** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Pobieranie pojedynczego](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure blob storage i Visual Studio połączonych usług (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Wprowadzenie do usługi Azure queue storage i Visual Studio połączonych usług (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
