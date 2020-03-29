---
title: Przesyłanie danych za pomocą biblioteki Przenoszenie danych dla platformy .NET
titleSuffix: Azure Storage
description: Biblioteka Przenoszenie danych służy do przenoszenia lub kopiowania danych do lub z zawartości obiektów blob i plików. Kopiowanie danych do usługi Azure Storage z plików lokalnych lub kopiowanie danych na kontach magazynu lub między nimi. Łatwo migruj dane do usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942904"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Przenoszenie danych za pomocą biblioteki przenoszenia danych

Biblioteka przenoszenia danych usługi Azure Storage to wieloplatformowa biblioteka open source, która jest przeznaczona do przekazywania, pobierania i kopiowania obiektów blob i plików o wysokiej wydajności. Biblioteka przenoszenia danych zawiera wygodne metody, które nie są dostępne w bibliotece klienta usługi Azure Storage dla platformy .NET. Metody te umożliwiają ustawienie liczby operacji równoległych, śledzenie postępu transferu, łatwe wznowienie anulowanego transferu i wiele więcej.

Ta biblioteka korzysta również z platformy .NET Core, co oznacza, że można jej używać podczas tworzenia aplikacji platformy .NET dla systemów Windows, Linux i macOS. Aby dowiedzieć się więcej o .NET Core, zapoznaj się z [dokumentacją .NET Core](https://dotnet.github.io/). Ta biblioteka działa również dla tradycyjnych aplikacji platformy .NET Framework dla systemu Windows.

W tym dokumencie pokazano, jak utworzyć aplikację konsoli .NET Core, która działa w systemach Windows, Linux i macOS i wykonuje następujące scenariusze:

- Przekazywanie plików i katalogów do magazynu obiektów Blob.
- Zdefiniuj liczbę operacji równoległych podczas przesyłania danych.
- Śledzenie postępu transferu danych.
- Wznawianie anulowania transferu danych.
- Kopiowanie pliku z adresu URL do magazynu obiektów blob.
- Kopiowanie z magazynu obiektów blob do magazynu obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne

- [Kod programu Visual Studio](https://code.visualstudio.com/)
- [Konto magazynu platformy Azure](storage-account-create.md)

## <a name="setup"></a>Konfiguracja

1. Odwiedź [przewodnik instalacji .NET Core,](https://www.microsoft.com/net/core) aby zainstalować program .NET Core. Wybierając środowisko, wybierz opcję wiersza polecenia.
2. W wierszu polecenia utwórz katalog dla projektu. Przejdź do tego katalogu, `dotnet new console -o <sample-project-name>` a następnie wpisz, aby utworzyć projekt konsoli języka C#.
3. Otwórz ten katalog w programie Visual Studio Code. Ten krok można szybko wykonać za pomocą `code .` wiersza polecenia, wpisując go w systemie Windows.
4. Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) z portalu Visual Studio Code Marketplace. Uruchom ponownie kod programu Visual Studio.
5. W tym momencie powinny zostać wyświetlene dwa monity. Jednym z nich jest dodanie "wymaganych zasobów do tworzenia i debugowania." Kliknij "tak". Innym monitem jest przywrócenie nierozwiązanych zależności. Kliknij "przywróć".
6. Modyfikuj `launch.json` w `.vscode` celu użycia zewnętrznego terminala jako konsoli. To ustawienie powinno być odczytywane jako`"console": "externalTerminal"`
7. Visual Studio Code umożliwia debugowanie aplikacji .NET Core. Naciśnij, `F5` aby uruchomić aplikację i sprawdzić, czy konfiguracja działa. Powinieneś zobaczyć "Hello World!" wydrukowane na konsoli.

## <a name="add-the-data-movement-library-to-your-project"></a>Dodawanie biblioteki przenoszenia danych do projektu

1. Dodaj najnowszą wersję biblioteki przenoszenia danych do `dependencies` sekcji `<project-name>.csproj` pliku. W momencie pisania tej wersji`"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Aby przywrócić projekt, powinien zostać wyświetlony monit. Kliknij przycisk "Przywróć". Projekt można również przywrócić z wiersza polecenia, `dotnet restore` wpisując polecenie w katalogu głównym projektu.

Modyfikowanie `<project-name>.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Konfigurowanie szkieletu aplikacji

Pierwszą rzeczą, którą robimy, jest skonfigurowanie kodu "szkieletu" naszej aplikacji. Ten kod monituje nas o nazwę konta magazynu i klucz konta `CloudStorageAccount` i używa tych poświadczeń do utworzenia obiektu. Ten obiekt jest używany do interakcji z naszym kontem magazynu we wszystkich scenariuszach transferu. Kod monituje nas również o wybranie typu operacji transferu, którą chcielibyśmy wykonać.

Modyfikowanie `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Przekazywanie pliku lokalnego do obiektu blob

Dodaj metody `GetSourcePath` `GetBlob` i `Program.cs`do:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Ten kod monituje nas o ścieżkę do pliku lokalnego, nazwę nowego lub istniejącego kontenera i nazwę nowego obiektu blob. Metoda `TransferManager.UploadAsync` wykonuje przekazywanie przy użyciu tych informacji.

Naciśnij, `F5` aby uruchomić aplikację. Można sprawdzić, czy przekazywanie nastąpiło, przeglądając konto Magazynu w [Eksploratorze magazynu microsoft azure](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Ustawianie liczby operacji równoległych

Jedną z funkcji oferowanych przez bibliotekę przenoszenia danych jest możliwość ustawienia liczby operacji równoległych w celu zwiększenia przepływności transferu danych. Domyślnie biblioteka przenoszenia danych ustawia liczbę operacji równoległych na 8 * liczbę rdzeni na komputerze.

Należy pamiętać, że wiele operacji równoległych w środowisku o niskiej przepustowości może przeciążeć połączenie sieciowe i faktycznie uniemożliwić pełne ukończenie operacji. Musisz poeksperymentować z tym ustawieniem, aby określić, co działa najlepiej na podstawie dostępnej przepustowości sieci.

Dodajmy kod, który pozwala nam ustawić liczbę operacji równoległych. Dodajmy również kod, który razy, jak długo trwa do transferu, aby zakończyć.

Dodaj `SetNumberOfParallelOperations` metodę `Program.cs`do :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Zmodyfikuj `ExecuteChoice` metodę, która ma być używana: `SetNumberOfParallelOperations`

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę, aby użyć czasomierza:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Śledź postęp transferu

Wiedza o tym, jak długo trwało przesyłanie danych, jest pomocna. Jednak możliwość zobaczenia postępów w transferze *podczas* transferu byłaby jeszcze lepsza. Aby osiągnąć ten scenariusz, musimy `TransferContext` utworzyć obiekt. Obiekt `TransferContext` występuje w dwóch `SingleTransferContext` `DirectoryTransferContext`formach: i . Pierwszy z nich służy do przesyłania pojedynczego pliku, a drugi do przesyłania katalogu plików.

Dodaj metody `GetSingleTransferContext` `GetDirectoryTransferContext` i `Program.cs`do:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Zmodyfikuj `TransferLocalFileToAzureBlob` metodę, która ma być używana: `GetSingleTransferContext`

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Wznowienie anulowanego transferu

Inną wygodną funkcją oferowaną przez bibliotekę przenoszenia danych jest możliwość wznowienia anulowanego transferu. Dodajmy kod, który pozwala nam tymczasowo anulować transfer, wpisując `c`, a następnie wznowić transfer 3 sekundy później.

Modyfikowanie `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Do tej pory `checkpoint` nasza wartość zawsze `null`była ustawiona na . Teraz, jeśli anulujemy transfer, pobieramy ostatni punkt kontrolny naszego transferu, a następnie używamy tego nowego punktu kontrolnego w naszym kontekście transferu.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Przenoszenie katalogu lokalnego do magazynu obiektów Blob

Byłoby rozczarowujące, gdyby biblioteka przenoszenia danych mogła przesyłać tylko jeden plik naraz. Na szczęście tak nie jest. Biblioteka przenoszenia danych umożliwia przenoszenie katalogu plików i wszystkich jego podkatalogów. Dodajmy kod, który pozwala nam to zrobić.

Najpierw dodaj metodę `GetBlobDirectory` `Program.cs`do:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Następnie zmodyfikuj: `TransferLocalDirectoryToAzureBlobDirectory`

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Istnieje kilka różnic między tą metodą a metodą przekazywania pojedynczego pliku. Teraz używamy `TransferManager.UploadDirectoryAsync` metody, `getDirectoryTransferContext` którą utworzyliśmy wcześniej. Ponadto teraz zapewniamy `options` wartość naszej operacji przesyłania, co pozwala nam wskazać, że chcemy uwzględnić podkatalogi w naszym przesłaniu.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopiowanie pliku z adresu URL do obiektu blob

Teraz dodajmy kod, który pozwala nam skopiować plik z adresu URL do obiektu blob platformy Azure.

Modyfikowanie `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Jednym z ważnych przypadków użycia tej funkcji jest, gdy trzeba przenieść dane z innej usługi w chmurze (np. AWS) na platformę Azure. Tak długo, jak masz adres URL, który daje dostęp do zasobu, można `TransferManager.CopyAsync` łatwo przenieść tego zasobu do obiektów blob platformy Azure przy użyciu metody. Ta metoda wprowadza również nowy parametr logiczny. Ustawienie tego `true` parametru wskazuje, że chcemy wykonać asynchroniczne kopii po stronie serwera. Ustawienie tego `false` parametru wskazuje synchroniczny kopii — co oznacza, że zasób jest pobierany do naszego komputera lokalnego, a następnie przekazywane do obiektu Blob platformy Azure. Jednak kopia synchronicznie jest obecnie dostępna tylko do kopiowania z jednego zasobu usługi Azure Storage do innego.

## <a name="copy-a-blob"></a>Kopiowanie obiektu blob

Inną funkcją, która jest unikatowo dostarczana przez bibliotekę przenoszenia danych, jest możliwość kopiowania z jednego zasobu usługi Azure Storage do innego.

Modyfikowanie `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

W tym przykładzie ustawiamy parametr `TransferManager.CopyAsync` `false` logiczny w, aby wskazać, że chcemy wykonać kopię synchroniową. Oznacza to, że zasób jest pobierany najpierw na nasz komputer lokalny, a następnie przekazany do obiektu Blob platformy Azure. Opcja kopiowania synchronicznie jest doskonałym sposobem na zapewnienie stałej szybkości operacji kopiowania. Natomiast szybkość asynchroniiowej kopii po stronie serwera zależy od dostępnej przepustowości sieci na serwerze, która może się zmieniać. Jednak kopia synchroniczowa może generować dodatkowe koszty ruchu wychodzącego w porównaniu do kopii asynchronicznej. Zalecane podejście jest użycie kopii synchronicznego w maszynie Wirtualnej platformy Azure, która znajduje się w tym samym regionie co konto magazynu źródłowego, aby uniknąć kosztów ruchu wychodzącego.

Aplikacja przenoszenia danych została ukończona. [Pełny przykład kodu jest dostępny na GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Następne kroki

[Dokumentacja referencyjna biblioteki przenoszenia danych usługi Azure Storage](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
