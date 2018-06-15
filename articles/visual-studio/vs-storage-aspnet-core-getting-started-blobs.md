---
title: Wprowadzenie do obiektów Blob platformy Azure magazynu i Visual Studio połączone usługi (platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu magazynu obiektów Blob platformy Azure w projekcie platformy ASP.NET Core w programie Visual Studio po połączeniu z kontem magazynu za pomocą programu Visual Studio połączone usługi
services: storage
documentationcenter: ''
author: camsoper
manager: wpickett
editor: ''
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2018
ms.locfileid: "27621227"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Wprowadzenie do obiektów Blob platformy Azure magazynu i Visual Studio połączone usługi (platformy ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Magazyn obiektów Blob Azure jest usługą służącą do przechowywania danych niestrukturalnych w chmurze jako obiektów lub obiekty BLOB. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów.

Ten samouczek pokazuje, jak napisać kod platformy ASP.NET Core dla niektórych typowych scenariuszy korzystających z magazynu obiektów Blob. Scenariusze obejmują tworzenie kontenera obiektów blob i przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów blob.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska deweloperskiego

Ta sekcja przeprowadzi Cię przez Konfigurowanie środowiska deweloperskiego. W tym tworzenie aplikacji ASP.NET Model-widok-kontroler (MVC), dodawanie połączenia usług połączonych dodawania kontrolera i określanie dyrektywy wymaganej przestrzeni nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Utwórz projekt aplikacji platformy ASP.NET MVC

1. Otwórz program Visual Studio.

1. Wybierz z menu głównego **pliku** > **nowy** > **projektu**.

1. W **nowy projekt** okno dialogowe, wybierz opcję **Web** > **aplikacji sieci Web platformy ASP.NET Core** > **AspNetCoreStorage**. Następnie wybierz **OK**.

    ![Zrzut ekranu programu Visual Studio okno dialogowe nowego projektu](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. W **nową aplikację sieci Web Core ASP.NET** okno dialogowe, wybierz opcję **.NET Core** > **ASP.NET Core 2.0** > **(aplikacja sieci Web Model-View-Controller)**. Następnie wybierz **OK**.

    ![Zrzut ekranu z nowej platformy ASP.NET Core aplikacji sieci Web, okno dialogowe](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Użyj podłączonych usług do łączenia się z kontem magazynu platformy Azure

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt.

2. Wybierz z menu kontekstowego **Dodaj** > **podłączonej usługi**.

1. W **usług połączonych** okno dialogowe, wybierz opcję **magazynu w chmurze z usługą Azure Storage**, a następnie wybierz **Konfiguruj**.

    ![Zrzut ekranu połączona usług — okno dialogowe](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. W **usługi Azure Storage** okno dialogowe, wybierz konto magazynu Azure do użycia na potrzeby tego samouczka. Aby utworzyć nowe konto magazynu platformy Azure, wybierz **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejącego konta magazynu lub tworzenia nowej, wybierz **Dodaj**. Visual Studio instaluje pakiet NuGet dla usługi Azure Storage i parametry połączenia magazynu **appsettings.json**.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu z [portalu Azure](https://portal.azure.com), zobacz [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Można również utworzyć konto magazynu przy użyciu [programu Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [interfejsu wiersza polecenia Azure](../storage/common/storage-azure-cli.md), lub [powłoki chmury Azure](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**.

2. Wybierz z menu kontekstowego **Dodaj** > **kontrolera**.

    ![Zrzut ekranu przedstawiający Eksploratora rozwiązań](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. W **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC — pusty**i wybierz **Dodaj**.

    ![Zrzut ekranu Dodawanie szkieletu — okno dialogowe](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. W **Dodaj pusty kontroler MVC** okno dialogowe, nazwy kontrolera *BlobsController*i wybierz **Dodaj**.

    ![Okno dialogowe zrzut ekranu z Dodaj pusty kontroler MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Dodaj następujące `using` dyrektywy `BlobsController.cs` pliku:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Łączenie z kontem magazynu i Pobierz odwołanie do kontenera

Kontener obiektów blob jest hierarchią zagnieżdżone obiekty BLOB i folderów. Pozostałe kroki w tym dokumencie wymagają odwołanie do kontenera obiektów blob, dzięki czemu kod powinna zostać umieszczona w jego własnej metody możliwość ponownego wykorzystania.

Poniższe kroki Tworzenie metody, aby nawiązać połączenie z kontem magazynu przy użyciu parametrów połączenia w **appsettings.json**. Kroki również utworzyć odwołanie do kontenera. Ustawienie parametrów połączenia w **appsettings.json** nosi nazwę w formacie `<storageaccountname>_AzureStorageConnectionString`. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie **GetCloudBlobContainer** zwracającą **CloudBlobContainer**. Pamiętaj zastąpić `<storageaccountname>_AzureStorageConnectionString` rzeczywistą nazwą klucza w **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Mimo że *blobcontainer testu* nie istnieje jeszcze, ten kod tworzy odwołanie do niej. Jest to tak kontenera mogą być tworzone za pomocą `CreateIfNotExists` metod przedstawionych w następnym kroku.

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Poniższe kroki przedstawiają sposób tworzenia kontenera obiektów blob:

1. Dodaj metodę o nazwie `CreateBlobContainer` zwracającą `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Pobierz `CloudBlobContainer` obiekt, który reprezentuje odwołanie do nazwy kontenera żądanego obiektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Wywołanie `CloudBlobContainer.CreateIfNotExists` metody, aby utworzyć kontener, jeśli jeszcze nie istnieje. `CloudBlobContainer.CreateIfNotExists` Metoda zwraca **true** Jeśli kontener nie istnieje i został utworzony pomyślnie. W przeciwnym razie metoda zwraca **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aktualizacja `ViewBag` o nazwie kontenera obiektów blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Poniżej pokazano ukończonej `CreateBlobContainer` metody:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **widoków** folderu.

2. Wybierz z menu kontekstowego **Dodaj** > **nowy Folder**. Nazwa nowego folderu *obiekty BLOB*. 

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folder, a następnie kliknij prawym przyciskiem myszy **obiekty BLOB**.

4. Wybierz z menu kontekstowego **Dodaj** > **widoku**.

1. W **Dodaj widok** okna dialogowego wprowadź **CreateBlobContainer** dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `CreateBlobContainer.cshtml`i zmodyfikuj go, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Wyszukaj nieuporządkowaną listę, która wygląda następująco: `<ul class="nav navbar-nav">`.  Po wykonaniu ostatniego `<li>` element na liście, Dodaj poniższy kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Uruchom aplikację i wybierz **Tworzenie kontenera obiektów Blob** aby zobaczyć wyniki podobne do następującego zrzutu ekranu:
  
    ![Zrzut ekranu Tworzenie kontenera obiektów blob](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Jak wspomniano wcześniej, `CloudBlobContainer.CreateIfNotExists` metoda zwraca **true** tylko, gdy kontener nie istnieje i zostanie utworzony. W związku z tym, jeśli aplikacja jest uruchamiana, gdy istnieje w kontenerze, metoda zwraca **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Przekazywanie obiektu blob do kontenera obiektów blob

Gdy [kontener obiektów blob jest tworzony](#create-a-blob-container), przekazać pliki do tego kontenera. Ta sekcja przeprowadzi Cię przez przekazanie pliku lokalnego do kontenera obiektów blob. W krokach założono Brak kontenera obiektów blob o nazwie *blobcontainer testu*. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `UploadBlob` która zwraca wartość typu ciąg.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W ramach `UploadBlob` metody get `CloudBlobContainer` obiekt, który reprezentuje odwołanie do nazwy kontenera żądanego obiektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Usługa Azure storage obsługuje typy inny obiektu blob. W tym samouczku korzysta z blokowych obiektów blob. Aby pobrać odwołanie do blokowego obiektu blob, należy wywołać `CloudBlobContainer.GetBlockBlobReference` metody.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Nazwa obiektu blob jest częścią adres URL używany do pobierania obiektu blob i może być dowolnym ciągiem, łącznie z nazwą pliku.

1. Po odwołanie do obiektu blob możesz przekazać dowolny strumień danych do niej przez wywołanie obiektu blob odwołania `UploadFromStream` metody. `UploadFromStream` Metoda tworzy obiekt blob, jeśli nie istnieje lub zastąpiony, jeśli istnieje. (Zmień  *&lt;przekazywania pliku >* do w pełni kwalifikowana ścieżka do pliku do przekazania.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Poniżej pokazano ukończonej `UploadBlob` — metoda (z w pełni kwalifikowana ścieżka do pliku do przekazania):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego `<li>` element na liście, Dodaj poniższy kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Uruchom aplikację i wybierz **przekazywanie obiektu blob**. Wyraz *sukcesu!* powinna zostać wyświetlona.
    
    ![Zrzut ekranu przedstawiający Powodzenie weryfikacji](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Wyświetlanie obiektów blob w kontenerze obiektów blob

W tej części przedstawiono sposób wyświetlania obiektów blob w kontenerze obiektów blob. Odwołania do kodu przykładowej *testu blobcontainer* utworzony w sekcji [Tworzenie kontenera obiektów blob](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `ListBlobs` zwracającą `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. W ramach `ListBlobs` metody get `CloudBlobContainer` obiekt, który reprezentuje odwołanie do kontenera obiektów blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Aby wyświetlić listę obiektów blob w kontenerze obiektów blob, użyj `CloudBlobContainer.ListBlobsSegmentedAsync` metody. `CloudBlobContainer.ListBlobsSegmentedAsync` Metoda zwraca `BlobResultSegment`. Zawiera `IListBlobItem` obiektów, które mogą być rzutowane na `CloudBlockBlob`, `CloudPageBlob`, lub `CloudBlobDirectory` obiektów. Poniższy fragment kodu wylicza wszystkie obiekty BLOB w kontenerze obiektów blob. Każdy obiekt blob jest rzutowane na odpowiedni obiekt na podstawie jego typu. Jego nazwę (lub identyfikator URI w odniesieniu `CloudBlobDirectory`) zostanie dodany do listy.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    Poniżej pokazano ukończonej `ListBlobs` metody:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folder, a następnie kliknij prawym przyciskiem myszy **obiekty BLOB**.

2. Wybierz z menu kontekstowego **Dodaj** > **widoku**.

1. W **Dodaj widok** okna dialogowego wprowadź `ListBlobs` dla nazwy widoku i wybierz **Dodaj**.

1. Otwórz `ListBlobs.cshtml`i Zastąp zawartość następującym kodem:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego `<li>` element na liście, Dodaj poniższy kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Uruchom aplikację i wybierz **wyświetlanie obiektów blob** aby zobaczyć wyniki podobne do następującego zrzutu ekranu:
  
    ![Zrzut ekranu listy obiektów blob](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

W tej części przedstawiono sposób pobierania obiektu blob. Możesz zachować go do lokalnego magazynu lub odczytu zawartości na ciąg. Odwołania do kodu przykładowej *testu blobcontainer* utworzony w sekcji [Tworzenie kontenera obiektów blob](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `DownloadBlob` która zwraca wartość typu ciąg.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W ramach `DownloadBlob` metody get `CloudBlobContainer` obiekt, który reprezentuje odwołanie do kontenera obiektów blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobierz obiektu blob odwołania przez wywołanie metody `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby pobrać obiektu blob, użyj `CloudBlockBlob.DownloadToStream` metody. Poniższy kod przesyła zawartość obiektu blob do obiektu strumienia. Ten obiekt jest następnie trwały do pliku lokalnego. (Zmień  *&lt;nazwy pliku lokalnego >* reprezentującą pliku w pełni kwalifikowaną nazwę, gdzie ma zostać pobrane obiektu blob.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Poniżej pokazano ukończonej `ListBlobs` — metoda (z pełną ścieżkę do pliku lokalnego tworzona):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego `<li>` element na liście, Dodaj poniższy kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Uruchom aplikację i wybierz **pobieranie obiektu blob** można pobrać obiektu blob. Określony w obiekcie blob `CloudBlobContainer.GetBlockBlobReference` wywołanie metody pobiera do lokalizacji określonej w `File.OpenWrite` wywołania metody. Tekst *sukcesu!* powinna zostać wyświetlona w przeglądarce. 

## <a name="delete-blobs"></a>Usuwanie obiektów blob

Następujące kroki ilustrują sposób usuwania obiektu blob:

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `DeleteBlob` która zwraca wartość typu ciąg.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. W ramach `DeleteBlob` metody get `CloudBlobContainer` obiekt, który reprezentuje odwołanie do kontenera obiektów blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobierz obiektu blob odwołania przez wywołanie metody `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby usunąć obiekt blob, użyj `Delete` metody.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Wypełniony `DeleteBlob` metoda powinna wyglądać następująco:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego `<li>` element na liście, Dodaj poniższy kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Uruchom aplikację i wybierz **usuwania obiektów blob** można usunąć obiektu blob, określona w `CloudBlobContainer.GetBlockBlobReference` wywołania metody. Tekst *sukcesu!* powinna zostać wyświetlona w przeglądarce. Wybierz przeglądarki **ponownie** przycisk, a następnie wybierz **wyświetlanie obiektów blob** można zweryfikować, że obiekt blob jest już w kontenerze.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób przechowywania listy i pobrać obiekty BLOB w usłudze Azure Storage za pomocą platformy ASP.NET Core. Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z tabel Azure magazynu i Visual Studio połączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Rozpoczynanie pracy z kolejek Azure magazynu i Visual Studio połączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
