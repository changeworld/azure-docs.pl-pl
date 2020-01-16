---
title: Rozpoczynanie pracy z usługą Azure Blob Storage za pomocą programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć korzystanie z usługi Azure Blob Storage w projekcie ASP.NET Core w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff221a32ff6c995d019b13f20ca2c3f9e2027f63
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980733"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage to usługa, która przechowuje dane niestrukturalne w chmurze jako obiekty blob. Magazyn obiektów blob umożliwia przechowywanie dowolnego typu danych tekstowych lub binarnych, takich jak dokumenty, pliki multimedialne lub instalatory aplikacji. Magazyn obiektów blob jest również nazywany magazynem obiektów. Aby dowiedzieć się więcej o usłudze BLOB Storage, zobacz [wprowadzenie do usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

W tym samouczku pokazano, jak napisać kod ASP.NET Core dla niektórych typowych scenariuszy korzystających z usługi BLOB Storage. Scenariusze obejmują tworzenie kontenera obiektów blob, a także przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

Ta sekcja zawiera instrukcje dotyczące konfigurowania środowiska deweloperskiego. Obejmuje to tworzenie aplikacji ASP.NET Model-View-Controller (MVC), Dodawanie połączenia usługi połączonej, dodawanie kontrolera i określanie wymaganych dyrektyw przestrzeni nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Tworzenie projektu aplikacji ASP.NET MVC

1. Otwórz program Visual Studio.

1. Z menu głównego wybierz pozycję **plik** > **Nowy** > **projekt**.

1. W oknie dialogowym **Nowy projekt** wybierz pozycję > **sieci Web** **ASP.NET Core aplikacja internetowa** > **AspNetCoreStorage**. Następnie wybierz przycisk **OK**.

    ![Zrzut ekranu okna dialogowego Nowy projekt programu Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET Core** wybierz pozycję **.net Core** > **ASP.NET Core 2,0** > **aplikacji sieci Web (Model-View-Controller)** . Następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa aplikacja sieci Web ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Łączenie się z kontem usługi Azure Storage przy użyciu usług połączonych

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt.

2. Z menu kontekstowego wybierz pozycję **dodaj** > **podłączona usługa**.

1. W oknie dialogowym **połączone usługi** wybierz pozycję **Magazyn w chmurze z usługą Azure Storage**, a następnie wybierz pozycję **Konfiguruj**.

    ![Zrzut ekranu przedstawiający okno dialogowe usługi połączone](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. W oknie dialogowym **Azure Storage** wybierz konto usługi Azure Storage, które ma być używane w tym samouczku. Aby utworzyć nowe konto usługi Azure Storage, wybierz pozycję **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejącego konta magazynu lub utworzeniu nowego, wybierz pozycję **Dodaj**. Program Visual Studio instaluje pakiet NuGet dla usługi Azure Storage oraz parametry połączenia magazynu z pliku **appSettings. JSON**.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu przy użyciu [Azure Portal](https://portal.azure.com), zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
>
> Konto magazynu można również utworzyć przy użyciu [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [interfejsu wiersza polecenia platformy Azure](../storage/common/storage-azure-cli.md)lub [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Kontrolery**.

2. Z menu kontekstowego wybierz pozycję **Dodaj** **kontroler** > .

    ![Zrzut ekranu przedstawiający Eksplorator rozwiązań](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie szkieletu](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. W oknie dialogowym **Dodaj pusty kontroler MVC** Nadaj nazwę kontrolerowi *BlobsController*i wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie pustego kontrolera MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Dodaj następujące dyrektywy `using` do pliku `BlobsController.cs`:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Połącz się z kontem magazynu i uzyskaj odwołanie do kontenera

Kontener obiektów BLOB jest zagnieżdżoną hierarchią obiektów blob i folderów. Pozostałe kroki opisane w tym dokumencie wymagają odwołania do kontenera obiektów blob, dzięki czemu kod powinien być umieszczony w osobnym metodzie do ponownego wykorzystania.

Poniższe kroki umożliwiają utworzenie metody łączenia się z kontem magazynu przy użyciu parametrów połączenia w pliku **appSettings. JSON**. Kroki te umożliwiają również utworzenie odwołania do kontenera. Ustawienie parametrów połączenia w pliku **appSettings. JSON** ma nazwę o formacie `<storageaccountname>_AzureStorageConnectionString`. 

1. Otwórz plik `BlobsController.cs`.

1. Dodaj metodę o nazwie **GetCloudBlobContainer** , która zwraca **CloudBlobContainer**. Pamiętaj, aby zastąpić `<storageaccountname>_AzureStorageConnectionString` nazwą klucza w **pliku Web. config**.
    
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
> Chociaż *test-BLOB-Container* nie istnieje jeszcze, ten kod tworzy odwołanie do niego. Jest tak dlatego, że kontener można utworzyć za pomocą metody `CreateIfNotExists` pokazanej w następnym kroku.

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
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Zaktualizuj `ViewBag` nazwą kontenera obiektów BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Poniżej przedstawiono zakończono metodę `CreateBlobContainer`:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder **widoki** .

2. Z menu kontekstowego wybierz pozycję **dodaj** > **Nowy folder**. Nadaj nazwę nowemu folderowi *obiekty blob*. 

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , a następnie kliknij prawym przyciskiem myszy pozycję **obiekty blob**.

4. Z menu kontekstowego wybierz opcję **Dodaj** **Widok** > .

1. W oknie dialogowym **Dodawanie widoku** w polu Nazwa widoku wpisz **CreateBlobContainer** , a następnie wybierz pozycję **Dodaj**.

1. Otwórz `CreateBlobContainer.cshtml`i zmodyfikuj go tak, aby wyglądał następująco:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > folderze **udostępnionym** , a następnie otwórz `_Layout.cshtml`.

1. Wyszukaj listę nieuporządkowaną, która wygląda następująco: `<ul class="nav navbar-nav">`.  Po ostatnim elemencie `<li>` na liście Dodaj następujący kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Utwórz kontener obiektów BLOB** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Zrzut ekranu przedstawiający tworzenie kontenera obiektów BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

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

1. Po utworzeniu odwołania do obiektu BLOB możesz przekazać do niego strumień danych, wywołując metodę `UploadFromStream` obiektu odwołania obiektów BLOB. Metoda `UploadFromStream` tworzy obiekt BLOB, jeśli nie istnieje, lub zastępuje go, jeśli istnieje. (Zmień *&lt;plik do przekazania >* do w pełni kwalifikowanej ścieżki do pliku, który ma zostać przekazany.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Poniżej przedstawiono metodę ukończenia `UploadBlob` (z w pełni kwalifikowaną ścieżką pliku do przekazania):

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

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > folderze **udostępnionym** , a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim elemencie `<li>` na liście Dodaj następujący kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Uruchom aplikację i wybierz pozycję **Przekaż obiekt BLOB**. Słowo *sukcesu zostało zakończone pomyślnie!* powinna zostać wyświetlona.
    
    ![Zrzut ekranu przedstawiający weryfikację sukcesów](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. Aby wyświetlić listę obiektów BLOB w kontenerze obiektów blob, użyj metody `CloudBlobContainer.ListBlobsSegmentedAsync`. Metoda `CloudBlobContainer.ListBlobsSegmentedAsync` zwraca `BlobResultSegment`. Zawiera `IListBlobItem` obiektów, które mogą być rzutowane na `CloudBlockBlob`, `CloudPageBlob`lub `CloudBlobDirectory` obiektów. Poniższy fragment kodu wylicza wszystkie obiekty blob w kontenerze obiektów BLOB. Każdy obiekt BLOB jest rzutowany na odpowiedni obiekt na podstawie jego typu. Jego nazwa (lub identyfikator URI w przypadku `CloudBlobDirectory`) jest dodawana do listy.

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
    Poniżej przedstawiono zakończono metodę `ListBlobs`:

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

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , a następnie kliknij prawym przyciskiem myszy pozycję **obiekty blob**.

2. Z menu kontekstowego wybierz opcję **Dodaj** **Widok** > .

1. W oknie dialogowym **Dodawanie widoku** wprowadź `ListBlobs` w polu Nazwa widoku, a następnie wybierz pozycję **Dodaj**.

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

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > folderze **udostępnionym** , a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim elemencie `<li>` na liście Dodaj następujący kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Wyświetl obiekty blob** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Zrzut ekranu przedstawiający listę obiektów BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

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

1. Aby pobrać obiekt BLOB, użyj metody `CloudBlockBlob.DownloadToStream`. Poniższy kod przenosi zawartość obiektu BLOB do strumienia. Ten obiekt jest następnie utrwalany w pliku lokalnym. (Zmień *&lt;nazwy pliku lokalnego >* na w pełni kwalifikowaną nazwę pliku reprezentującą lokalizację obiektu BLOB do pobrania). 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Poniżej przedstawiono metodę ukończenia `ListBlobs` (z w pełni kwalifikowaną ścieżką dla tworzonego pliku lokalnego):
    
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

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > folderze **udostępnionym** , a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim elemencie `<li>` na liście Dodaj następujący kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Pobierz obiekt BLOB** , aby pobrać obiekt BLOB. Obiekt BLOB określony w metodzie `CloudBlobContainer.GetBlockBlobReference` pobiera pliki do lokalizacji określonej w wywołaniu metody `File.OpenWrite`. Tekst zakończył się *pomyślnie* . powinien pojawić się w przeglądarce. 

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
    blob.DeleteAsync().Wait();
    ```
    
    Zapełniona Metoda `DeleteBlob` powinna wyglądać następująco:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki** > folderze **udostępnionym** , a następnie otwórz `_Layout.cshtml`.

1. Po ostatnim elemencie `<li>` na liście Dodaj następujący kod HTML, aby dodać inny element menu nawigacji:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Usuń obiekt BLOB** , aby usunąć obiekt BLOB określony w wywołaniu metody `CloudBlobContainer.GetBlockBlobReference`. Tekst zakończył się *pomyślnie* . powinien pojawić się w przeglądarce. Wybierz przycisk **Wstecz** przeglądarki, a następnie wybierz pozycję **Wyświetl obiekty blob** , aby sprawdzić, czy obiekt BLOB nie znajduje się już w kontenerze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób przechowywania, wyświetlania i pobierania obiektów BLOB w usłudze Azure Storage przy użyciu ASP.NET Core. Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure Table Storage i usługami połączonymi programu Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Rozpoczynanie pracy z usługą Azure queue storage i usługami połączonymi programu Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
