---
title: Rozpoczynanie pracy z usługą Azure Blob storage i Visual Studio podłączone usługi (ASP.NET) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu usługi Azure Blob storage w projektach programu ASP.NET w programie Visual Studio, po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 557b47aab5cdadef79427c4da9c18477daac0689
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123046"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z usługą Azure Blob storage i Visual Studio podłączone usługi (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Usługa Azure Blob storage to usługa, która przechowuje dane niestrukturalne w chmurze jako obiekty lub obiekty BLOB. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Aby dowiedzieć się więcej o usłudze Blob storage, zobacz [wprowadzenie do usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy korzystających z usługi Blob storage. Scenariusze obejmują tworzenie kontenera obiektów blob, przekazywanie, wyświetlanie listy, pobieranie i usuwanie obiektów blob.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **kontrolerów**.

2. Z menu kontekstowego wybierz **Dodaj** > **kontrolera**.

    ![Zrzut ekranu z Eksploratora rozwiązań Dodaj i wyróżnione kontrolera](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. W **Dodawanie szkieletu** okno dialogowe, wybierz opcję **kontroler MVC 5 — pusty**i wybierz **Dodaj**.

    ![Zrzut ekranu Dodawanie szkieletu, okno dialogowe](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. W **Dodaj kontroler** okno dialogowe, nazwy kontrolera *BlobsController*i wybierz **Dodaj**.

    ![Zrzut ekranu Dodaj kontroler, okno dialogowe](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Dodaj następujący kod `using` dyrektywy `BlobsController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Łączenie z kontem magazynu i Uzyskaj odwołanie do kontenera

Kontener obiektów blob jest hierarchię zagnieżdżoną obiektów blob oraz folderów. Pozostałe kroki w tym dokumencie wymagają odwołanie do kontenera obiektów blob, tak, aby kod powinny być umieszczone w jego własnej metody możliwość ponownego wykorzystania.

Poniższe kroki umożliwiają utworzenie metoda nawiązywania połączenia z kontem magazynu przy użyciu parametrów połączenia w **Web.config**. Kroki także Utwórz odwołanie do kontenera.  Ustawienie parametrów połączenia w **Web.config** nosi nazwę w formacie `<storageaccountname>_AzureStorageConnectionString`. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie **GetCloudBlobContainer** zwracającego **CloudBlobContainer**.  Koniecznie Zastąp `<storageaccountname>_AzureStorageConnectionString` z rzeczywistą nazwę klucza w **Web.config**.
    
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
> Mimo że *blobcontainer testu* nie istnieje jeszcze, ten kod tworzy odwołanie do niej. Umożliwia to kontener mogą być tworzone za pomocą `CreateIfNotExists` metod przedstawionych w następnym kroku.

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Poniższe kroki ilustrują Tworzenie kontenera obiektów blob:

1. Dodaj metodę o nazwie `CreateBlobContainer` zwracającego `ActionResult`.

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

1. Wywołaj `CloudBlobContainer.CreateIfNotExists` metodę, aby utworzyć kontener, jeśli jeszcze nie istnieje. `CloudBlobContainer.CreateIfNotExists` Metoda zwraca **true** Jeśli kontener nie istnieje i został pomyślnie utworzony. W przeciwnym razie metoda zwraca **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Aktualizacja `ViewBag` o nazwie kontener obiektów blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Poniżej przedstawiono ukończoną `CreateBlobContainer` metody:

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

1. Jeśli nie ma **obiektów blob** folderu, utwórz je. Z menu kontekstowego wybierz **Dodaj** > **nowy Folder**. Nadaj nazwę nowego folderu *obiektów blob*. 
 
1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folder, a następnie kliknij prawym przyciskiem myszy **obiektów blob**.

1. Z menu kontekstowego wybierz **Dodaj** > **widoku**.

1. W **Dodaj widok** okna dialogowego wprowadź **CreateBlobContainer** dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `CreateBlobContainer.cshtml`i zmodyfikuj go tak, aby wygląda jak poniższy fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** > **Shared** folder, a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Utwórz kontener obiektów Blob** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Zrzut ekranu opcji Utwórz kontener obiektów blob](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Jak wspomniano wcześniej, `CloudBlobContainer.CreateIfNotExists` metoda zwraca **true** tylko, kiedy kontener nie istnieje i zostanie utworzony. W związku z tym, jeśli aplikacja jest uruchamiana, gdy kontener istnieje, metoda zwraca **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Przekazywanie obiektu blob w kontenerze obiektów blob

Gdy [kontener obiektów blob jest tworzony](#create-a-blob-container), przekazywanie plików do tego kontenera. W tej sekcji przedstawiono przekazanie pliku lokalnego do kontenera obiektów blob. Założono w nim jest kontener obiektów blob o nazwie *blobcontainer testu*. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `UploadBlob` , zwraca wartość typu ciąg.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W ramach `UploadBlob` metody, Pobierz `CloudBlobContainer` obiekt, który reprezentuje odwołanie do nazwy kontenera żądanego obiektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Usługa Azure storage obsługuje typy różnych obiektów blob. W tym samouczku korzysta z blokowych obiektów blob. Aby pobrać odwołanie do blokowego obiektu blob, należy wywołać `CloudBlobContainer.GetBlockBlobReference` metody.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Nazwa obiektu blob jest częścią adresu URL używany do pobierania obiektu blob może być dowolny ciąg, łącznie z nazwą pliku.

1. Po odwołanie do obiektu blob możesz przekazać dowolny strumień danych do niego przez wywołanie obiektu odwołanie do obiektu blob `UploadFromStream` metody. `UploadFromStream` Metoda tworzy obiekt blob, jeśli nie istnieje lub zastępuje go, jeśli istnieje. (Zmień  *&lt;przekazywanie pliku >* do pełni kwalifikowaną ścieżkę do pliku do przekazania.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Poniżej przedstawiono ukończoną `UploadBlob` metody (za pomocą w pełni kwalifikowana ścieżka dla pliku do przekazania):

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

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **przekazywanie obiektu blob**.  Wyraz *sukces!* powinna zostać wyświetlona.
    
    ![Zrzut ekranu przedstawiający Powodzenie weryfikacji](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Wyświetlanie listy obiektów blob w kontenerze obiektów blob

W tej sekcji przedstawiono sposób wyświetlenia listy obiektów blob w kontenerze obiektów blob. Odwołania do kodu przykładowej *testu blobcontainer* utworzony w sekcji [Utwórz kontener obiektów blob](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `ListBlobs` zwracającego `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. W ramach `ListBlobs` metody, Pobierz `CloudBlobContainer` obiekt, który reprezentuje odwołanie do kontenera obiektów blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Aby wyświetlić listę obiektów blob w kontenerze obiektów blob, użyj `CloudBlobContainer.ListBlobs` metody. `CloudBlobContainer.ListBlobs` Metoda zwraca `IListBlobItem` obiekt, który może być rzutowany `CloudBlockBlob`, `CloudPageBlob`, lub `CloudBlobDirectory` obiektu. Poniższy fragment kodu wylicza wszystkie obiekty BLOB w kontenerze obiektów blob. Każdy obiekt blob jest rzutowanie do odpowiedniego obiektu, na podstawie jego typu. Jego nazwę (lub identyfikator URI w przypadku właściwości **CloudBlobDirectory**) zostanie dodany do listy.

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

    Oprócz obiektów blob kontenerów obiektów blob może zawierać katalogów. Załóżmy, że istnieje kontener obiektów blob o nazwie *blobcontainer testu*, przy użyciu następującej hierarchii:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Użyty w poprzednim przykładzie kodu **obiektów blob** lista parametrów zawiera wartości podobny do następującego:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Jak widać, lista zawiera tylko jednostki najwyższego poziomu, a nie te zagnieżdżonych (*bar.png* i *baz.png*). Aby wyświetlić listę wszystkich jednostek w ramach kontenera obiektów blob, Zmień kod, aby **CloudBlobContainer.ListBlobs** metody jest przekazywana **true** dla **useFlatBlobListing** parametr.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Ustawienie **useFlatBlobListing** parametr **true** podaje płaską listę wszystkich jednostek w kontenerze obiektów blob. To daje następujące wyniki:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Poniżej przedstawiono ukończoną **ListBlobs** metody:

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

1. W **Eksploratora rozwiązań**, rozwiń węzeł **widoków** folder, a następnie kliknij prawym przyciskiem myszy **obiektów blob**.

2. Z menu kontekstowego wybierz **Dodaj** > **widoku**.

1. W **Dodaj widok** okna dialogowego wprowadź `ListBlobs` dla nazwy widoku, a następnie wybierz **Dodaj**.

1. Otwórz `ListBlobs.cshtml`i zastąp jego zawartość następującym kodem:

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

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **wyświetlanie listy obiektów blob** aby zobaczyć wyniki, podobnie jak poniższy zrzut ekranu:
  
    ![Zrzut ekranu listy obiektów blob](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

W tej sekcji przedstawiono sposób pobierania obiektu blob. Można utrwalić ją w magazynie lokalnym lub odczytać zawartość ciągu. Odwołania do kodu przykładowej *testu blobcontainer* utworzony w sekcji [Utwórz kontener obiektów blob](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `DownloadBlob` , zwraca wartość typu ciąg.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W ramach `DownloadBlob` metody, Pobierz `CloudBlobContainer` obiekt, który reprezentuje odwołanie do kontenera obiektów blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobieranie obiektu blob odwołania, wywołując `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby pobrać obiekt blob, użyj `CloudBlockBlob.DownloadToStream` metody. Poniższy kod przesyła zawartość obiektu blob do obiektu strumienia. Ten obiekt zostanie następnie utrwalone w pliku lokalnym. (Zmień  *&lt;lokalnej nazwy pliku >* reprezentującą pliku w pełni kwalifikowana nazwa, gdy obiekt blob jest pobranie.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Poniżej przedstawiono ukończoną `DownloadBlob` — metoda (z pełną ścieżkę do pliku lokalnego, tworzona):
    
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

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **pobierania obiektów blob** można pobrać obiektu blob. Określona w obiekcie blob `CloudBlobContainer.GetBlockBlobReference` wywołania metody pliki do pobrania w lokalizacji określonej w `File.OpenWrite` wywołania metody.  Tekst *sukces!* powinna zostać wyświetlona w przeglądarce. 

## <a name="delete-blobs"></a>Usuwanie obiektów blob

Poniższe kroki ilustrują usuwanie obiektu blob:

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `DeleteBlob` , zwraca wartość typu ciąg.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. W ramach `DeleteBlob` metody, Pobierz `CloudBlobContainer` obiekt, który reprezentuje odwołanie do kontenera obiektów blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobieranie obiektu blob odwołania, wywołując `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby usunąć obiekt blob, użyj `Delete` metody.

    ```csharp
    blob.Delete();
    ```
    
    Gotowy `DeleteBlob` metoda powinna wyglądać następująco:
    
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

1. Po ostatnim **Html.ActionLink**, Dodaj następujący kod **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **usunięcia obiektu blob** można usunąć obiektu blob, określone w `CloudBlobContainer.GetBlockBlobReference` wywołania metody. Tekst *sukces!* powinna zostać wyświetlona w przeglądarce. Wybierz przeglądarkę **ponownie** przycisk, a następnie wybierz **wyświetlanie listy obiektów blob** Aby sprawdzić, czy obiekt blob nie jest już w kontenerze.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób przechowywania i pobierania obiektów blob w usłudze Azure Storage za pomocą programu ASP.NET oraz listy. Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure Table storage i Visual Studio podłączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Rozpoczynanie pracy z usługą Azure Queue storage i Visual Studio podłączone usługi (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
