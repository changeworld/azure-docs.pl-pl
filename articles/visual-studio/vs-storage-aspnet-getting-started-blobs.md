---
title: Wprowadzenie do usługi Azure Blob Storage przy użyciu programu Visual Studio (ASP.NET)
description: Jak rozpocząć korzystanie z magazynu obiektów Blob platformy Azure w projekcie ASP.NET w programie Visual Studio, po nawiązaniu połączenia z kontem magazynu przy użyciu połączonych usług programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6591cdb8f19484c87bb05f9007521adc34778f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298873"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Wprowadzenie do usługi Azure Blob Storage i usług połączonych z programem Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Usługa Azure Blob storage to usługa, która przechowuje nieustrukturyzowane dane w chmurze jako obiekty lub obiekty blob. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Aby dowiedzieć się więcej o magazynie obiektów Blob, zobacz [Wprowadzenie do magazynu obiektów Blob platformy Azure.](../storage/blobs/storage-blobs-introduction.md)

W tym samouczku pokazano, jak napisać ASP.NET kodu dla niektórych typowych scenariuszy, które używają magazynu obiektów Blob. Scenariusze obejmują tworzenie kontenera obiektów blob i przekazywanie, wystawianie, pobieranie i usuwanie obiektów blob.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Kontrolery**.

2. Z menu kontekstowego wybierz polecenie **Dodaj** > **kontroler**.

    ![Zrzut ekranu przedstawiający Eksploratora rozwiązań z wyróżnioną pozycją Dodaj i kontroler](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC 5 — opróżnij**i wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. W oknie dialogowym **Dodawanie kontrolera** nazwij kontroler *BlobsController*i wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie kontrolera](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Dodaj do `using` `BlobsController.cs` pliku następujące dyrektywy:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Łączenie się z kontem magazynu i uzyskanie odwołania do kontenera

Kontener obiektów blob jest zagnieżdżoną hierarchią obiektów blob i folderów. Pozostałe kroki w tym dokumencie wymagają odwołania do kontenera obiektów blob, tak aby kod powinien być umieszczony w jego własnej metody ponownego użycia.

Poniższe kroki tworzą metodę łączenia się z kontem magazynu przy użyciu ciągu połączenia w **witrynie Web.config**. Kroki również utworzyć odwołanie do kontenera.  Ustawienie ciągu połączenia w **witrynie Web.config** nosi nazwę formatu `<storageaccountname>_AzureStorageConnectionString`. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie **GetCloudBlobContainer,** która zwraca **CloudBlobContainer**.  Pamiętaj, aby `<storageaccountname>_AzureStorageConnectionString` zastąpić rzeczywistą nazwą klucza w **Web.config**.
    
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
> Mimo że *test-blob-container* jeszcze nie istnieje, ten kod tworzy odwołanie do niego. Jest to tak, że kontener `CreateIfNotExists` można utworzyć za pomocą metody pokazanej w następnym kroku.

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Poniższe kroki ilustrują sposób tworzenia kontenera obiektów blob:

1. Dodaj metodę `CreateBlobContainer` o nazwie, która zwraca . `ActionResult`

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Pobierz `CloudBlobContainer` obiekt, który reprezentuje odwołanie do żądanej nazwy kontenera obiektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Wywołanie `CloudBlobContainer.CreateIfNotExists` metody, aby utworzyć kontener, jeśli jeszcze nie istnieje. Metoda `CloudBlobContainer.CreateIfNotExists` zwraca **wartość true,** jeśli kontener nie istnieje i jest pomyślnie utworzony. W przeciwnym razie metoda zwraca **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Zaktualizuj `ViewBag` o nazwę kontenera obiektów blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Poniżej przedstawiono `CreateBlobContainer` ukończoną metodę:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder **Widoki.**

1. Jeśli nie ma folderu **obiektów Blobs,** utwórz go. Z menu kontekstowego wybierz polecenie **Dodaj** > **nowy folder**. Nazwij nowy folder *Obiektów Blobs*. 
 
1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki** i kliknij prawym przyciskiem myszy pozycję **Obiekty Blob**.

1. Z menu kontekstowego wybierz polecenie **Dodaj** > **widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź pozycję **CreateBlobContainer** dla nazwy widoku i wybierz pozycję **Dodaj**.

1. Otwórz `CreateBlobContainer.cshtml`i zmodyfikuj go tak, aby wyglądał jak następujący fragment kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksploratorze rozwiązań**rozwiń `_Layout.cshtml`folder**Udostępnione** **widoki** > i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz pozycję **Utwórz kontener obiektów blob,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Zrzut ekranu przedstawiający kontener tworzenia obiektów blob](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Jak wspomniano wcześniej, `CloudBlobContainer.CreateIfNotExists` metoda zwraca **true** tylko wtedy, gdy kontener nie istnieje i jest tworzony. W związku z tym jeśli aplikacja jest uruchamiana, gdy kontener istnieje, metoda zwraca **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Przekazywanie obiektu blob do kontenera obiektu blob

Po [utworzeniu kontenera obiektów blob](#create-a-blob-container)przekaż pliki do tego kontenera. W tej sekcji przechodzi przez przekazywanie pliku lokalnego do kontenera obiektu blob. Kroki zakładają, że istnieje kontener obiektów blob o nazwie *test-blob-container*. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę `UploadBlob` o nazwie, która zwraca ciąg.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W `UploadBlob` ramach metody `CloudBlobContainer` uzyskać obiekt, który reprezentuje odwołanie do żądanej nazwy kontenera obiektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Usługa Azure storage obsługuje różne typy obiektów blob. W tym samouczku użyto bloków obiektów blob. Aby pobrać odwołanie do bloku obiektu blob, wywołać `CloudBlobContainer.GetBlockBlobReference` metodę.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Nazwa obiektu blob jest częścią adresu URL używanego do pobierania obiektu blob i może być dowolnym ciągiem znaków, w tym nazwą pliku.

1. Po istnieje odwołanie do obiektu blob, można przekazać dowolny strumień danych do `UploadFromStream` niego, wywołując metodę obiektu odwołania obiektu blob. Metoda `UploadFromStream` tworzy obiekt blob, jeśli nie istnieje, lub zastępuje go, jeśli istnieje. (Zmień * &lt;>pliku do przekazywania* do w pełni kwalifikowanej ścieżki do pliku, który ma zostać przekazany).

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Poniżej przedstawiono `UploadBlob` ukończoną metodę (z w pełni kwalifikowaną ścieżką dla pliku, który ma zostać przekazany):

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

1. W **Eksploratorze rozwiązań**rozwiń `_Layout.cshtml`folder**Udostępnione** **widoki** > i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz pozycję **Przekaż obiekt blob**.  Słowo *sukces!* powinny się pojawić.
    
    ![Zrzut ekranu przedstawiający weryfikację sukcesu](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Wyświetlanie listy obiektów blob w kontenerze obiektu blob

W tej sekcji pokazano, jak wyświetlić listę obiektów blob w kontenerze obiektów blob. Przykładowy kod odwołuje się do *kontenera test-blob* utworzonego w sekcji [Utwórz kontener obiektów blob](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę `ListBlobs` o nazwie, która zwraca . `ActionResult`

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. W `ListBlobs` ramach metody `CloudBlobContainer` uzyskać obiekt, który reprezentuje odwołanie do kontenera obiektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Aby wyświetlić listę obiektów blob w `CloudBlobContainer.ListBlobs` kontenerze obiektów blob, należy użyć metody. Metoda `CloudBlobContainer.ListBlobs` zwraca `IListBlobItem` obiekt, który może `CloudBlockBlob`być `CloudPageBlob`rzutowana na , lub `CloudBlobDirectory` obiektu. Poniższy fragment kodu wylicza wszystkie obiekty blob w kontenerze obiektów blob. Każdy obiekt blob jest rzutowy do odpowiedniego obiektu, na podstawie jego typu. Jego nazwa (lub identyfikator URI w przypadku **CloudBlobDirectory)** jest dodawany do listy.

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

    Oprócz obiektów blob kontenery obiektów blob mogą zawierać katalogi. Załóżmy, że istnieje kontener obiektów blob o nazwie *test-blob-container,* z następującą hierarchią:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Przy użyciu poprzedniego przykładu kodu lista **ciągów obiektów blob** zawiera wartości podobne do następujących:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Jak pokazano, lista zawiera tylko jednostki najwyższego poziomu, a nie zagnieżdżone (*bar.png* i *baz.png*). Aby wyświetlić listę wszystkich jednostek w kontenerze obiektów blob, należy zmienić kod tak, aby **CloudBlobContainer.ListBlobs** metoda jest przekazywana **true** dla **useFlatBlobListing parametru.**    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Ustawienie **parametru useFlatBlobListing** na **true** zwraca płaską listę wszystkich jednostek w kontenerze obiektów blob. Daje to następujące wyniki:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Poniżej przedstawiono ukończoną metodę **ListBlobs:**

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

1. W **Eksploratorze rozwiązań**rozwiń folder **Widoki** i kliknij prawym przyciskiem myszy pozycję **Obiekty Blob**.

2. Z menu kontekstowego wybierz polecenie **Dodaj** > **widok**.

1. W oknie dialogowym Dodawanie `ListBlobs` **widoku** wprowadź nazwę widoku i wybierz pozycję **Dodaj**.

1. Otwórz `ListBlobs.cshtml`i zastąp zawartość następującym kodem:

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

1. W **Eksploratorze rozwiązań**rozwiń `_Layout.cshtml`folder**Udostępnione** **widoki** > i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Lista obiektów blob,** aby wyświetlić wyniki podobne do następującego zrzutu ekranu:
  
    ![Zrzut ekranu przedstawiający obiekty blob listy](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

W tej sekcji pokazano, jak pobrać obiekt blob. Można go utrwalić do magazynu lokalnego lub odczytać zawartość w ciągu. Przykładowy kod odwołuje się do *kontenera test-blob* utworzonego w sekcji [Utwórz kontener obiektów blob](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę `DownloadBlob` o nazwie, która zwraca ciąg.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W `DownloadBlob` ramach metody `CloudBlobContainer` uzyskać obiekt, który reprezentuje odwołanie do kontenera obiektu blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobierz obiekt odniesienia obiektu blob, wywołując `CloudBlobContainer.GetBlockBlobReference` metodę. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby pobrać obiekt blob, użyj `CloudBlockBlob.DownloadToStream` tej metody. Poniższy kod przenosi zawartość obiektu blob do obiektu strumienia. Ten obiekt jest następnie utrwalony do pliku lokalnego. (Zmień * &lt;nazwę pliku lokalnego>* na w pełni kwalifikowaną nazwę pliku reprezentującą miejsce pobierania obiektu blob). 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Poniżej przedstawiono `DownloadBlob` ukończoną metodę (z w pełni kwalifikowaną ścieżką dla tworzonego pliku lokalnego):
    
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

1. W **Eksploratorze rozwiązań**rozwiń `_Layout.cshtml`folder**Udostępnione** **widoki** > i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **pozycję Pobierz obiekt blob,** aby pobrać obiekt blob. Obiekt blob określony `CloudBlobContainer.GetBlockBlobReference` w wywołaniu metody pobiera `File.OpenWrite` do lokalizacji określonej w wywołaniu metody.  Sukces *tekstu!* powinny pojawić się w przeglądarce. 

## <a name="delete-blobs"></a>Usuwanie obiektów blob

W poniższych krokach pokazano, jak usunąć obiekt blob:

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę `DeleteBlob` o nazwie, która zwraca ciąg.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. W `DeleteBlob` ramach metody `CloudBlobContainer` uzyskać obiekt, który reprezentuje odwołanie do kontenera obiektu blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobierz obiekt odniesienia obiektu blob, wywołując `CloudBlobContainer.GetBlockBlobReference` metodę. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby usunąć obiekt blob, użyj `Delete` metody.

    ```csharp
    blob.Delete();
    ```
    
    Ukończona `DeleteBlob` metoda powinna wyglądać następująco:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. W **Eksploratorze rozwiązań**rozwiń `_Layout.cshtml`folder**Udostępnione** **widoki** > i otwórz .

1. Po ostatnim **html.ActionLink**dodaj następujący **html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz **usuń obiekt blob,** aby `CloudBlobContainer.GetBlockBlobReference` usunąć obiekt blob określony w wywołaniu metody. Sukces *tekstu!* powinny pojawić się w przeglądarce. Wybierz przycisk **Wstecz** przeglądarki, a następnie wybierz **pozycję Lista obiektów blob,** aby sprawdzić, czy obiekt blob nie znajduje się już w kontenerze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak przechowywać, listy i pobierania obiektów blob w usłudze Azure Storage przy użyciu ASP.NET. Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Wprowadzenie do usługi Azure Table Storage i usług połączonych w programie Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Wprowadzenie do usługi Azure Queue storage i usług połączonych z programem Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
