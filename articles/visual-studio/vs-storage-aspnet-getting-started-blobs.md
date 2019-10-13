---
title: Rozpoczynanie pracy z usługą Azure Blob Storage za pomocą programu Visual Studio (ASP.NET)
description: Jak rozpocząć korzystanie z usługi Azure Blob Storage w projekcie ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298873"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage to usługa, która przechowuje dane niestrukturalne w chmurze jako obiekty blob. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Aby dowiedzieć się więcej o usłudze BLOB Storage, zobacz [wprowadzenie do usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy korzystających z usługi BLOB Storage. Scenariusze obejmują tworzenie kontenera obiektów blob, a także przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Kontrolery**.

2. Z menu kontekstowego wybierz pozycję **Dodaj** **kontroler** > .

    ![Zrzut ekranu przedstawiający Eksplorator rozwiązań, z wyróżnionym dodatkiem Add i Controller](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC 5 — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. W oknie dialogowym **Dodaj kontroler** Nadaj nazwę kontrolerowi *BlobsController*i wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie kontrolera](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Dodaj następujące dyrektywy `using` do pliku `BlobsController.cs`:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Połącz się z kontem magazynu i uzyskaj odwołanie do kontenera

Kontener obiektów BLOB jest zagnieżdżoną hierarchią obiektów blob i folderów. Pozostałe kroki opisane w tym dokumencie wymagają odwołania do kontenera obiektów blob, dzięki czemu kod powinien być umieszczony w osobnym metodzie do ponownego wykorzystania.

Poniższe kroki umożliwiają utworzenie metody łączenia się z kontem magazynu przy użyciu parametrów połączenia w **pliku Web. config**. Kroki te umożliwiają również utworzenie odwołania do kontenera.  Ustawienie parametrów połączenia w **pliku Web. config** ma nazwę o formacie `<storageaccountname>_AzureStorageConnectionString`. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie **GetCloudBlobContainer** , która zwraca **CloudBlobContainer**.  Pamiętaj, aby zamienić `<storageaccountname>_AzureStorageConnectionString` na rzeczywistą nazwę klucza w **pliku Web. config**.
    
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
> Chociaż *test-BLOB-Container* nie istnieje jeszcze, ten kod tworzy odwołanie do niego. Jest to tak dlatego, że kontener można utworzyć za pomocą metody `CreateIfNotExists` pokazanej w następnym kroku.

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Poniższe kroki ilustrują sposób tworzenia kontenera obiektów blob:

1. Dodaj metodę o nazwie `CreateBlobContainer`, która zwraca `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Pobierz obiekt `CloudBlobContainer`, który reprezentuje odwołanie do żądanej nazwy kontenera obiektów BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Wywołaj metodę `CloudBlobContainer.CreateIfNotExists`, aby utworzyć kontener, jeśli jeszcze nie istnieje. Metoda `CloudBlobContainer.CreateIfNotExists` zwraca **wartość true** , jeśli kontener nie istnieje i został pomyślnie utworzony. W przeciwnym razie metoda zwraca **wartość false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Aktualizacja `ViewBag` z nazwą kontenera obiektów BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Poniżej przedstawiono zakończono metodę `CreateBlobContainer`:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **widoki** .

1. Jeśli nie ma folderu **BLOB** , utwórz go. Z menu kontekstowego wybierz pozycję **dodaj** > **Nowy folder**. Nadaj nazwę nowemu folderowi *obiekty blob*. 
 
1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , a następnie kliknij prawym przyciskiem myszy pozycję **obiekty blob**.

1. Z menu kontekstowego wybierz pozycję **Dodaj** **Widok** > .

1. W oknie dialogowym **Dodawanie widoku** w polu Nazwa widoku wpisz **CreateBlobContainer** , a następnie wybierz pozycję **Dodaj**.

1. Otwórz `CreateBlobContainer.cshtml` i zmodyfikuj go tak, aby wyglądał następująco:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > **udostępniony** folder i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Utwórz kontener obiektów BLOB** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Zrzut ekranu przedstawiający tworzenie kontenera obiektów BLOB](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Jak wspomniano wcześniej, Metoda `CloudBlobContainer.CreateIfNotExists` zwraca **wartość true** tylko wtedy, gdy kontener nie istnieje i jest tworzony. W związku z tym, jeśli aplikacja jest uruchamiana, gdy kontener istnieje, metoda zwraca **wartość false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Przekazywanie obiektu BLOB do kontenera obiektów BLOB

Po [utworzeniu kontenera obiektów BLOB](#create-a-blob-container)Przekaż pliki do tego kontenera. W tej sekcji omówiono przekazywanie plików lokalnych do kontenera obiektów BLOB. W tych krokach przyjęto, że istnieje kontener obiektów BLOB o nazwie *test-BLOB-Container*. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `UploadBlob`, która zwraca ciąg.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W metodzie `UploadBlob` Pobierz obiekt `CloudBlobContainer`, który reprezentuje odwołanie do żądanej nazwy kontenera obiektów BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Usługa Azure Storage obsługuje różne typy obiektów BLOB. W tym samouczku są stosowane blokowe obiekty blob. Aby pobrać odwołanie do blokowego obiektu BLOB, wywołaj metodę `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Nazwa obiektu BLOB jest częścią adresu URL służącego do pobierania obiektu BLOB i może być dowolnym ciągiem, łącznie z nazwą pliku.

1. Po utworzeniu odwołania do obiektu BLOB możesz przekazać do niego strumień danych, wywołując metodę `UploadFromStream` obiektu odwołania obiektów BLOB. Metoda `UploadFromStream` tworzy obiekt BLOB, jeśli nie istnieje, lub zastępuje go, jeśli istnieje. (Zmień wartość *&lt;File-to-upload >* na w pełni kwalifikowaną ścieżkę do pliku, który ma zostać przekazany.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Poniżej przedstawiono zakończono metodę `UploadBlob` (z w pełni kwalifikowaną ścieżką pliku do przekazania):

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

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > **udostępniony** folder i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację i wybierz pozycję **Przekaż obiekt BLOB**.  Słowo *sukcesu zostało zakończone pomyślnie!* powinna zostać wyświetlona.
    
    ![Zrzut ekranu przedstawiający weryfikację sukcesów](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Wyświetlanie listy obiektów BLOB w kontenerze obiektów BLOB

W tej sekcji pokazano, jak wyświetlić listę obiektów BLOB w kontenerze obiektów BLOB. Przykładowy kod odwołuje się do *kontenera Test-BLOB-Container* utworzonego w sekcji, [Tworzenie kontenera obiektów BLOB](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `ListBlobs`, która zwraca `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. W metodzie `ListBlobs` Pobierz obiekt `CloudBlobContainer`, który reprezentuje odwołanie do kontenera obiektów BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Aby wyświetlić listę obiektów BLOB w kontenerze obiektów blob, użyj metody `CloudBlobContainer.ListBlobs`. Metoda `CloudBlobContainer.ListBlobs` zwraca obiekt `IListBlobItem`, który może być rzutowany na obiekt `CloudBlockBlob`, `CloudPageBlob` lub `CloudBlobDirectory`. Poniższy fragment kodu wylicza wszystkie obiekty blob w kontenerze obiektów BLOB. Każdy obiekt BLOB jest rzutowany na odpowiedni obiekt na podstawie jego typu. Jego nazwa (lub identyfikator URI w przypadku **CloudBlobDirectory**) jest dodawana do listy.

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

    Kontenery obiektów BLOB oprócz obiektów BLOB mogą zawierać katalogi. Załóżmy, że istnieje kontener obiektów BLOB o nazwie *test-BLOB-Container*, z następującą hierarchią:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Korzystając z powyższego przykładu kodu, lista ciągów **obiektów BLOB** zawiera wartości podobne do następujących:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Jak pokazano, lista zawiera tylko jednostki najwyższego poziomu, a nie zagnieżdżone (*bar. png* i *baz. png*). Aby wyświetlić listę wszystkich jednostek w kontenerze obiektów blob, Zmień kod tak, aby Metoda **CloudBlobContainer. ListBlobs** została przeniesiona do **wartości true** dla parametru **useFlatBlobListing** .    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Ustawienie parametru **useFlatBlobListing** na **wartość true** zwraca płaską listę wszystkich jednostek w kontenerze obiektów BLOB. Daje to następujące wyniki:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Poniżej przedstawiono zakończono metodę **ListBlobs** :

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

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , a następnie kliknij prawym przyciskiem myszy pozycję **obiekty blob**.

2. Z menu kontekstowego wybierz pozycję **Dodaj** **Widok** > .

1. W oknie dialogowym **Dodawanie widoku** w polu Nazwa widoku wprowadź `ListBlobs`, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `ListBlobs.cshtml` i Zastąp zawartość następującym kodem:

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

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > **udostępniony** folder i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Wyświetl obiekty blob** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Zrzut ekranu przedstawiający listę obiektów BLOB](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

W tej sekcji pokazano, jak pobrać obiekt BLOB. Można je zachować do magazynu lokalnego lub odczytać zawartość w postaci ciągu. Przykładowy kod odwołuje się do *kontenera Test-BLOB-Container* utworzonego w sekcji, [Tworzenie kontenera obiektów BLOB](#create-a-blob-container).

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `DownloadBlob`, która zwraca ciąg.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. W metodzie `DownloadBlob` Pobierz obiekt `CloudBlobContainer`, który reprezentuje odwołanie do kontenera obiektów BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobierz obiekt referencyjny obiektu BLOB, wywołując metodę `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby pobrać obiekt BLOB, użyj metody `CloudBlockBlob.DownloadToStream`. Poniższy kod przenosi zawartość obiektu BLOB do strumienia. Ten obiekt jest następnie utrwalany w pliku lokalnym. (Zmień *&lt;local-File-name >* na w pełni kwalifikowaną nazwę pliku reprezentującą lokalizację obiektu BLOB do pobrania). 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Poniżej przedstawiono zakończono metodę `DownloadBlob` (z w pełni kwalifikowaną ścieżką dla tworzonego pliku lokalnego):
    
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

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > **udostępniony** folder i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Pobierz obiekt BLOB** , aby pobrać obiekt BLOB. Obiekt BLOB określony w wywołaniu metody `CloudBlobContainer.GetBlockBlobReference` pobiera do lokalizacji określonej w wywołaniu metody `File.OpenWrite`.  Tekst zakończył się *pomyślnie* . powinien pojawić się w przeglądarce. 

## <a name="delete-blobs"></a>Usuwanie obiektów blob

Poniższe kroki ilustrują sposób usuwania obiektu BLOB:

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie `DeleteBlob`, która zwraca ciąg.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. W metodzie `DeleteBlob` Pobierz obiekt `CloudBlobContainer`, który reprezentuje odwołanie do kontenera obiektów BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Pobierz obiekt referencyjny obiektu BLOB, wywołując metodę `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Aby usunąć obiekt BLOB, użyj metody `Delete`.

    ```csharp
    blob.Delete();
    ```
    
    Ukończona Metoda `DeleteBlob` powinna wyglądać następująco:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > **udostępniony** folder i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Usuń obiekt BLOB** , aby usunąć obiekt BLOB określony w wywołaniu metody `CloudBlobContainer.GetBlockBlobReference`. Tekst zakończył się *pomyślnie* . powinien pojawić się w przeglądarce. Wybierz przycisk **Wstecz** przeglądarki, a następnie wybierz pozycję **Wyświetl obiekty blob** , aby sprawdzić, czy obiekt BLOB nie znajduje się już w kontenerze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób przechowywania, wyświetlania i pobierania obiektów BLOB w usłudze Azure Storage przy użyciu programu ASP.NET. Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure Table Storage i usługami połączonymi programu Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Rozpoczynanie pracy z usługą Azure queue storage i usługami połączonymi programu Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
