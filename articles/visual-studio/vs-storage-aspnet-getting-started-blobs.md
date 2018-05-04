---
title: Wprowadzenie do obiektów Blob platformy Azure magazynu i Visual Studio połączone usługi (ASP.NET) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu magazynu obiektów Blob platformy Azure w projekcie platformy ASP.NET w programie Visual Studio po połączeniu z kontem magazynu za pomocą programu Visual Studio połączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 55b083ac5384e749098338d2f3b7b24bfe16fe8f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Wprowadzenie do obiektów Blob platformy Azure magazynu i Visual Studio połączone usługi (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Magazyn obiektów Blob Azure jest usługą służącą do przechowywania danych niestrukturalnych w chmurze jako obiektów lub obiekty BLOB. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów.

Ten samouczek pokazuje, jak napisać kod ASP.NET dla niektórych typowych scenariuszy korzystających z magazynu obiektów Blob. Scenariusze obejmują tworzenie kontenera obiektów blob i przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów blob.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**.

2. Wybierz z menu kontekstowego **Dodaj** > **kontrolera**.

    ![Zrzut ekranu z Eksploratorze rozwiązań i Dodaj wyróżnione kontrolera](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. W **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC 5 — pusty**i wybierz **Dodaj**.

    ![Zrzut ekranu Dodawanie szkieletu — okno dialogowe](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. W **Dodaj kontroler** okno dialogowe, nazwy kontrolera *BlobsController*i wybierz **Dodaj**.

    ![Zrzut ekranu Dodaj kontroler, okno dialogowe](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Dodaj następujące `using` dyrektywy `BlobsController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Łączenie z kontem magazynu i Pobierz odwołanie do kontenera

Kontener obiektów blob jest hierarchią zagnieżdżone obiekty BLOB i folderów. Pozostałe kroki w tym dokumencie wymagają odwołanie do kontenera obiektów blob, dzięki czemu kod powinna zostać umieszczona w jego własnej metody możliwość ponownego wykorzystania.

Poniższe kroki Tworzenie metody, aby nawiązać połączenie z kontem magazynu przy użyciu parametrów połączenia w **Web.config**. Kroki również utworzyć odwołanie do kontenera.  Ustawienie parametrów połączenia w **Web.config** nosi nazwę w formacie `<storageaccountname>_AzureStorageConnectionString`. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie **GetCloudBlobContainer** zwracającą **CloudBlobContainer**.  Pamiętaj zastąpić `<storageaccountname>_AzureStorageConnectionString` rzeczywistą nazwą klucza w **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
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
    ViewBag.Success = container.CreateIfNotExists();
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
        ViewBag.Success = container.CreateIfNotExists();
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

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **Tworzenie kontenera obiektów Blob** aby zobaczyć wyniki podobne do następującego zrzutu ekranu:
  
    ![Zrzut ekranu Tworzenie kontenera obiektów blob](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

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
        blob.UploadFromStream(fileStream);
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
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **przekazywanie obiektu blob**.  Wyraz *sukcesu!* powinna zostać wyświetlona.
    
    ![Zrzut ekranu przedstawiający Powodzenie weryfikacji](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
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
   
1. Aby wyświetlić listę obiektów blob w kontenerze obiektów blob, użyj `CloudBlobContainer.ListBlobs` metody. `CloudBlobContainer.ListBlobs` Metoda zwraca `IListBlobItem` obiektów, które mogą być rzutowane na `CloudBlockBlob`, `CloudPageBlob`, lub `CloudBlobDirectory` obiektu. Poniższy fragment kodu wylicza wszystkie obiekty BLOB w kontenerze obiektów blob. Każdy obiekt blob jest rzutowane na odpowiedni obiekt na podstawie jego typu. Jego nazwę (lub identyfikator URI w odniesieniu **CloudBlobDirectory**) zostanie dodany do listy.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
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

    Oprócz obiektów blob kontenerów obiektów blob może zawierać katalogów. Załóżmy, że istnieje kontener obiektów blob o nazwie *blobcontainer testu*, przy użyciu następujących hierarchii:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Użyty w poprzednim przykładzie kodu **obiekty BLOB** ciąg lista zawiera wartości podobnie do następującego:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Co zostało pokazane, lista zawiera tylko jednostki najwyższego poziomu, a nie te zagnieżdżonych (*bar.png* i *baz.png*). Aby wyświetlić listę wszystkich jednostek w kontenerze obiektów blob, Zmień kod, aby **CloudBlobContainer.ListBlobs** metody jest przekazywany **true** dla **useFlatBlobListing** parametr.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Ustawienie **useFlatBlobListing** parametr **true** zwraca płaska lista wszystkich jednostek w kontenerze obiektów blob. Daje to następujące wyniki:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Poniżej pokazano ukończonej **ListBlobs** metody:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
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

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **wyświetlanie obiektów blob** aby zobaczyć wyniki podobne do następującego zrzutu ekranu:
  
    ![Zrzut ekranu listy obiektów blob](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

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
        blob.DownloadToStream(fileStream);
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
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **pobieranie obiektu blob** można pobrać obiektu blob. Określony w obiekcie blob `CloudBlobContainer.GetBlockBlobReference` wywołanie metody pobiera do lokalizacji określonej w `File.OpenWrite` wywołania metody.  Tekst *sukcesu!* powinna zostać wyświetlona w przeglądarce. 

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
    blob.Delete();
    ```
    
    Wypełniony `DeleteBlob` metoda powinna wyglądać następująco:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po wykonaniu ostatniego **Html.ActionLink**, Dodaj następujący **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **usuwania obiektów blob** można usunąć obiektu blob, określona w `CloudBlobContainer.GetBlockBlobReference` wywołania metody. Tekst *sukcesu!* powinna zostać wyświetlona w przeglądarce. Wybierz przeglądarki **ponownie** przycisk, a następnie wybierz **wyświetlanie obiektów blob** można zweryfikować, że obiekt blob jest już w kontenerze.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób przechowywania listy i pobrać obiekty BLOB w usłudze Azure Storage za pomocą programu ASP.NET. Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z tabel Azure magazynu i Visual Studio połączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Rozpoczynanie pracy z kolejek Azure magazynu i Visual Studio połączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
