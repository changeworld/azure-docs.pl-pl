---
title: Wprowadzenie do magazynu tabel platformy Azure przy użyciu programu Visual Studio (ASP.NET)
description: Jak rozpocząć korzystanie z magazynu tabel platformy Azure w projekcie ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług Połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979629"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Wprowadzenie do magazynu tabel platformy Azure i połączonych usług programu Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie

Usługa Azure Table Storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostek magazynu tabel platformy Azure. Te scenariusze obejmują tworzenie tabeli i dodawanie, wykonywanie zapytań i usuwanie jednostek tabeli. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto magazynu platformy Azure](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Kontrolery,** a następnie z menu kontekstowego wybierz polecenie **Kontroler >add.**

    ![Dodawanie kontrolera do ASP.NET aplikacji MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC 5 — opróżnij**i wybierz pozycję **Dodaj**.

    ![Określanie typu kontrolera MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. W oknie dialogowym **Dodawanie kontrolera** nazwij kontroler *TabeleController*i wybierz pozycję **Dodaj**.

    ![Nadaj nazwę kontrolerowi MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Dodaj do *using* `TablesController.cs` pliku następujące elementy za pomocą dyrektyw:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Tworzenie klasy modelu

Wiele przykładów w tym artykule używa **TableEntity**-pochodna klasy o nazwie **CustomerEntity**. Poniższe kroki prowadzą użytkownika przez deklarowanie tej klasy jako klasy modelu:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Modele,** a następnie w menu kontekstowym wybierz polecenie **Add->Class**.

1. W oknie dialogowym **Dodawanie nowego elementu** nazwij klasę **CustomerEntity**.

1. Otwórz `CustomerEntity.cs` plik i dodaj następujące **elementy za pomocą** dyrektywy:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Zmodyfikuj klasę tak, aby po zakończeniu klasa została zadeklarowana tak, jak w poniższym kodzie. Klasa deklaruje klasę jednostki o nazwie **CustomerEntity,** która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji.

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

W poniższych krokach pokazano, jak utworzyć tabelę:

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki opisane w temacie [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **CreateTable,** która zwraca **actionresult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **CreateTable** metody, uzyskać **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabeli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiektu, który reprezentuje odwołanie do żądanej nazwy tabeli. **CloudTableClient.GetTableReference** metoda nie ma żądania względem magazynu tabel. Odwołanie jest zwracane, niezależnie od tego, czy tabela istnieje. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Wywołanie **CloudTable.CreateIfNotExists** metody, aby utworzyć tabelę, jeśli jeszcze nie istnieje. **CloudTable.CreateIfNotExists** Metoda zwraca **wartość true,** jeśli tabela nie istnieje i jest pomyślnie utworzony. W przeciwnym razie zwracana jest **wartość false.**    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Zaktualizuj **ViewBag** o nazwę tabeli.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Tabele,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **CreateTable** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `CreateTable.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz **pozycję Utwórz tabelę,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Tworzenie tabeli](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak wspomniano wcześniej, **CloudTable.CreateIfNotExists** metoda zwraca **true** tylko wtedy, gdy tabela nie istnieje i jest tworzony. W związku z tym po uruchomieniu aplikacji, gdy tabela istnieje, metoda zwraca **false**. Aby uruchomić aplikację wiele razy, należy usunąć tabelę przed ponownym uruchomieniem aplikacji. Usuwanie tabeli można wykonać za pomocą **CloudTable.Delete** metody. Tabelę można również usunąć za pomocą [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Eksploratora magazynu platformy Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

*Jednostki mapują* do obiektów C\# przy użyciu niestandardowej klasy pochodnej **tableentity**. Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. W tej sekcji zobaczysz, jak zdefiniować klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, niemniej użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Dla każdej właściwości, które powinny być przechowywane w usłudze tabel, właściwość musi być własnością publiczną obsługiwanego typu, który udostępnia zarówno ustawienie i pobieranie wartości.
Klasa jednostki *musi zadeklarować* konstruktora bez parametrów publicznych.

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki opisane w temacie [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj następującą dyrektywę, aby `TablesController.cs` kod w pliku mógł uzyskać dostęp do klasy **CustomerEntity:**

    ```csharp
    using StorageAspnet.Models;
    ```

1. Dodaj metodę o nazwie **AddEntity,** która zwraca **actionresult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **AddEntity** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabeli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiektu, który reprezentuje odwołanie do tabeli, do której masz zamiar dodać nową jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Tworzenie wystąpienia i inicjowanie **customerEntity** klasy.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Utwórz obiekt **TableOperation,** który wstawia encję klienta.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Wykonaj operację wstawania, wywołując **metodę CloudTable.Execute.** Wynik operacji można sprawdzić, sprawdzając **tableResult.HttpStatusCode** właściwości. Kod stanu 2xx wskazuje, że akcja żądana przez klienta została pomyślnie przetworzona. Na przykład pomyślne wstawienia nowych jednostek powoduje kod stanu HTTP 204, co oznacza, że operacja została pomyślnie przetworzona i serwer nie zwrócił żadnej zawartości.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Zaktualizuj **ViewBag** o nazwę tabeli i wyniki operacji wstawiania.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Tabele,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **AddEntity** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `AddEntity.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz **pozycję Dodaj encję,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Dodawanie jednostki](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Można sprawdzić, czy encja została dodana, wykonując kroki opisane w sekcji [Pobierz pojedynczą encję](#get-a-single-entity). Za pomocą [Eksploratora usługi Microsoft Azure Storage można](../vs-azure-tools-storage-manage-with-storage-explorer.md) również wyświetlić wszystkie jednostki dla tabel.

## <a name="add-a-batch-of-entities-to-a-table"></a>Dodawanie partii encji do tabeli

Oprócz możliwości [dodawania jednostki do tabeli po kolei](#add-an-entity-to-a-table)można również dodawać jednostki w partii. Dodawanie jednostek w partii zmniejsza liczbę rund między kodem a usługą tabel platformy Azure. Poniższe kroki ilustrują sposób dodawania wielu elementów do tabeli za pomocą pojedynczej operacji wstawiania:

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki opisane w temacie [Konfigurowanie środowiska programistycznego](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **AddEntities,** która zwraca **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **AddEntities** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabeli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiektu, który reprezentuje odwołanie do tabeli, do której masz zamiar dodać nowe jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Tworzenie wystąpienia niektórych obiektów klienta na podstawie klasy modelu **CustomerEntity** przedstawionej w sekcji [Dodaj jednostkę do tabeli](#add-an-entity-to-a-table).

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

1. Dodaj elementy do obiektu operacji wstawiania partii.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Wykonaj operację wstawania partii, wywołując **metodę CloudTable.ExecuteBatch.**   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** Metoda zwraca listę **TableResult** obiektów, gdzie każdy **TableResult** obiektu mogą być badane w celu określenia sukcesu lub niepowodzenia każdej operacji poszczególnych. W tym przykładzie przekaż listę do widoku i pozwól widokowi wyświetlić wyniki każdej operacji. 
 
    ```csharp
    return View(results);
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Tabele,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **AddEntities** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `AddEntities.cshtml`i zmodyfikuj go tak, aby wyglądał jak poniżej.

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

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Dodaj jednostki,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Dodawanie jednostek](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Można sprawdzić, czy encja została dodana, wykonując kroki opisane w sekcji [Pobierz pojedynczą encję](#get-a-single-entity). Za pomocą [Eksploratora usługi Microsoft Azure Storage można](../vs-azure-tools-storage-manage-with-storage-explorer.md) również wyświetlić wszystkie jednostki dla tabel.

## <a name="get-a-single-entity"></a>Uzyskaj jedną encję

W tej sekcji pokazano, jak uzyskać pojedynczą jednostkę z tabeli przy użyciu klucza wiersza jednostki i klucza partycji. 

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki w [temacie Konfigurowanie środowiska programistycznego](#set-up-the-development-environment)i użyto danych z [programu Dodaj partię encji do tabeli.](#add-a-batch-of-entities-to-a-table) 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetSingle,** która zwraca **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **GetSingle** metody, uzyskać **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabeli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiektu, który reprezentuje odwołanie do tabeli, z której są pobieranie jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji pobierania, który przyjmuje obiekt encji pochodzący z **tableentity**. Pierwszym parametrem jest *partitionKey*, a drugim parametrem jest *rowKey*. Za pomocą **CustomerEntity** klasy i danych przedstawionych w sekcji [Dodaj partię jednostek do tabeli,](#add-a-batch-of-entities-to-a-table)następujący fragment kodu kwerendy tabeli dla **customerEntity** jednostki z *partitionKey* wartość "Smith" i *rowKey* wartość "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Wykonaj operację pobierania.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Przekaż wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Tabele,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **polecenie GetSingle** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `GetSingle.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

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

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Pobierz single,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Uzyskaj singiel](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Pobierz wszystkie jednostki w partycji

Jak wspomniano w sekcji, [Dodaj jednostkę do tabeli](#add-an-entity-to-a-table), połączenie partycji i klucza wiersza jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji można zbadać szybciej niż jednostki z różnymi kluczami partycji. W tej sekcji pokazano, jak kwerendy tabeli dla wszystkich jednostek z określonej partycji.  

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki w [temacie Konfigurowanie środowiska programistycznego](#set-up-the-development-environment)i użyto danych z [programu Dodaj partię encji do tabeli.](#add-a-batch-of-entities-to-a-table) 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetPartition,** która zwraca **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **GetPartition** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabeli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiektu, który reprezentuje odwołanie do tabeli, z której są pobieranie jednostek. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Tworzenie wystąpienia **obiektu TableQuery** określające kwerendę w **klauzuli Where.** Za pomocą **CustomerEntity** klasy i danych przedstawionych w sekcji [Dodaj partię jednostek do tabeli,](#add-a-batch-of-entities-to-a-table)następujący fragment kodu kwerendy tabeli dla wszystkich jednostek, gdzie **PartitionKey** (nazwisko klienta) ma wartość "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. W ramach pętli wywołać **CloudTable.ExecuteQuerySegmented** metody przekazywania obiektu kwerendy, który wystąpienia w poprzednim kroku.  **CloudTable.ExecuteQuerySegmented** metoda zwraca **TableContinuationToken** obiektu, który - po **null** - wskazuje, że nie ma więcej jednostek do pobrania. W pętli użyj innej pętli, aby iterować za pomocą zwróconych jednostek. W poniższym przykładzie kodu każda zwrócona jednostka jest dodawana do listy. Po zakończeniu pętli lista jest przekazywana do widoku do wyświetlenia: 

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

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Tabele,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **GetPartition** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `GetPartition.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

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

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Pobierz partycję,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Pobierz partycję](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Usuwanie jednostki

W tej sekcji pokazano, jak usunąć encję z tabeli.

> [!NOTE]
> 
> W tej sekcji przyjęto założenie, że wykonaliśmy kroki w [temacie Konfigurowanie środowiska programistycznego](#set-up-the-development-environment)i użyto danych z [programu Dodaj partię encji do tabeli.](#add-a-batch-of-entities-to-a-table) 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **DeleteEntity,** która zwraca **actionresult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach **DeleteEntity** metody, pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje * &lt;* o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień nazwę konta magazynu>na nazwę konta magazynu platformy Azure, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz **CloudTableClient** obiekt reprezentuje klienta usługi tabeli.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz **CloudTable** obiektu, który reprezentuje odwołanie do tabeli, z której są usuwane jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji usuwania, który przyjmuje obiekt encji pochodzący z **tableentity**. W takim przypadku używamy **CustomerEntity** klasy i danych przedstawionych w sekcji [Dodaj partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table). **ETag** jednostki musi być ustawiona na prawidłową wartość.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Wykonaj operację usuwania.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Przekaż wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki,** kliknij prawym przyciskiem myszy **pozycję Tabele,** a następnie z menu kontekstowego wybierz polecenie **Dodaj >widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **Usuń** element dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `DeleteEntity.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

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

1. W **Eksploratorze rozwiązań**rozwiń folder Widoki >`_Layout.cshtml` **Udostępnione** i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Usuń encję,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Uzyskaj singiel](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Wprowadzenie do magazynu obiektów blob platformy Azure i usług połączonych programu Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Wprowadzenie do magazynu kolejek platformy Azure i połączonych usług programu Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
