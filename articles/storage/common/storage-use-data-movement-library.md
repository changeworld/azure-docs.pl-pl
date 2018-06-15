---
title: Transfer danych za pomocą Biblioteka przenoszenia danych magazynu Microsoft Azure | Dokumentacja firmy Microsoft
description: Biblioteka przenoszenia danych umożliwia przenoszenie lub kopiowanie danych do lub z obiektu blob i zawartości pliku. Kopiowanie danych do magazynu Azure z lokalnych plików lub kopiowania danych w ramach urządzeń magazynujących lub między kontami magazynu. Łatwo przeprowadzić migrację danych do usługi Azure Storage.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.openlocfilehash: eb96f3697d5369ba96a1b0c491e3eacf09e7aac4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2018
ms.locfileid: "27564743"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Transfer danych za pomocą Biblioteka przenoszenia danych magazynu Microsoft Azure

## <a name="overview"></a>Przegląd
Biblioteka przenoszenia danych w programie Microsoft Azure magazynu jest biblioteki typu open source i platform, która jest przeznaczona dla wysokiej wydajności, przekazywanie, pobieranie i kopiowania plików i obiektów blob magazynu Azure. Ta biblioteka jest podstawowym środowisku przenoszenia danych, które obsługuje [AzCopy](../storage-use-azcopy.md). Biblioteka przenoszenia danych udostępnia wygodne metody, które nie są dostępne w przypadku naszego tradycyjnych [biblioteki klienta magazynu Azure .NET](../blobs/storage-dotnet-how-to-use-blobs.md). Obejmuje to możliwość Ustaw liczbę operacji równoległych, śledzić postępu przenoszenia, łatwe wznawianie transferu anulowane i o wiele więcej.  

Ta biblioteka używa również .NET Core, co oznacza, że użytkownik może być używany podczas tworzenia aplikacji programu .NET dla systemu Windows, Linux i macOS. Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [dokumentacji platformy .NET Core](https://dotnet.github.io/). Ta biblioteka działa także dla tradycyjnej aplikacji .NET Framework dla systemu Windows. 

W tym dokumencie przedstawiono sposób tworzenia aplikacji konsoli .NET Core, która działa w systemach Windows, Linux i macOS i wykonuje następujące scenariusze:

- Przekazywanie plików i katalogów do magazynu obiektów Blob.
- Określ liczbę operacji równoległych podczas transferu danych.
- Śledź postęp transferu danych.
- Transfer danych Wznów anulowane. 
- Skopiuj plik z adresu URL do magazynu obiektów Blob. 
- Skopiuj z magazynu obiektów Blob do magazynu obiektów Blob.

**Co jest potrzebne:**

* [Visual Studio Code](https://code.visualstudio.com/)
* [konto usługi Azure Storage](storage-create-storage-account.md#create-a-storage-account)

> [!NOTE]
> W tym przewodniku założono, że czytelnik zna już [usługi Azure Storage](https://azure.microsoft.com/services/storage/). Jeśli nie, odczytywanie [wprowadzenie do usługi Azure Storage](storage-introduction.md) warto dokumentacji. Przede wszystkim należy [Utwórz konto magazynu](storage-create-storage-account.md#create-a-storage-account) aby rozpocząć korzystanie z Biblioteka przenoszenia danych.
> 
> 

## <a name="setup"></a>Konfiguracja  

1. Odwiedź stronę [Przewodnik instalacji programu .NET Core](https://www.microsoft.com/net/core) do zainstalowania platformy .NET Core. Podczas wybierania środowiska, należy wybrać opcję wiersza polecenia. 
2. W wierszu polecenia Utwórz katalog projektu. Przejdź do tego katalogu, wpisz `dotnet new console -o <sample-project-name>` do tworzenia projektu konsoli C#.
3. Otwórz ustawienia tego katalogu w Visual Studio Code. Ten krok można szybko zrobić przy użyciu wiersza polecenia, wpisując `code .` w systemie Windows.  
4. Zainstaluj [C# rozszerzenia](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) z Visual Studio Marketplace kodu. Uruchom ponownie program Visual Studio Code. 
5. W tym momencie powinny zostać wyświetlone dwa monity. W przypadku dodawania "zasoby wymagane do tworzenia i debugowania." Kliknij przycisk "tak". Innego wiersza jest przywracania nierozwiązane zależności. Kliknij pozycję "Przywróć".
6. Modyfikowanie `launch.json` w obszarze `.vscode` do użycia jako konsolę terminalu zewnętrznego. To ustawienie należy przeczytać jako` "console": "externalTerminal"`
7. Visual Studio Code pozwala na debugowanie aplikacji .NET Core. Trafienia `F5` do uruchomienia aplikacji, a następnie zweryfikować konfigurację. Powinny pojawić się "Witaj świecie!" podane w konsoli. 

## <a name="add-data-movement-library-to-your-project"></a>Dodaj Biblioteka przenoszenia danych do projektu

1. Dodaj Biblioteka przenoszenia danych do najnowszej wersji `dependencies` części Twojego `<project-name>.csproj` pliku. W czasie zapisywania będzie to wersja`"Microsoft.Azure.Storage.DataMovement": "0.6.2"` 
2. Aby przywrócić projekt powinien być wyświetlany monit. Kliknij przycisk "Przywracanie". Można też przywrócić projektu z poziomu wiersza polecenia, wpisując polecenie `dotnet restore` w głównym katalogu projektu.

Modyfikowanie `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Konfigurowanie szkielet aplikacji
Najpierw nie skonfigurowano kodu "szkielet" naszej aplikacji. Ten kod wyświetla monit o nas dla konta magazynu kluczy nazwy i konta i używa tych poświadczeń w celu utworzenia `CloudStorageAccount` obiektu. Ten obiekt służy do interakcji z nasze konto magazynu we wszystkich scenariuszach transferu. Kod monituje nam wybierz typ operacji transferu, którą chcemy do wykonania. 

Modyfikowanie `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

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

## <a name="transfer-local-file-to-azure-blob"></a>Przenieś plik lokalny do obiektów Blob platformy Azure
Dodaj metody `GetSourcePath` i `GetBlob` do `Program.cs`:

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

Modyfikowanie `TransferLocalFileToAzureBlob` metody:

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

Ten kod nam monituje o podanie ścieżki do pliku lokalnego, Nazwa nowego lub istniejącego kontenera i nazwę nowego obiektu blob. `TransferManager.UploadAsync` Metoda przeprowadza przekazywania, korzystając z tych informacji. 

Trafienia `F5` do uruchamiania aplikacji. Możesz sprawdzić, czy przekazywania wystąpił wyświetlając konta magazynu z [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Ustaw liczbę operacji równoległych
Wspaniałych funkcji oferowanych przez Biblioteka przenoszenia danych jest możliwość określenia liczbę operacji równoległych w celu zwiększenia przepływności transferu danych. Domyślnie biblioteka przenoszenia danych Ustawia liczbę operacji równoległych 8 * liczba rdzeni na tym komputerze. 

Należy pamiętać, że wiele operacji równoległych w środowisku niskiej przepustowości może przeciąży połączenie sieciowe i faktycznie zapobiec operacji pełni zakończenie działania. Należy wypróbować to ustawienie, aby określić, co działa najlepiej oparte na dostępną przepustowość sieci. 

Dodajmy trochę kodu, który pozwala ustawić liczbę operacji równoległych. Umożliwia również Dodaj kod, który czas to czas transferu zakończyć.

Dodaj `SetNumberOfParallelOperations` metody `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modyfikowanie `ExecuteChoice` metodę `SetNumberOfParallelOperations`:

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

Modyfikowanie `TransferLocalFileToAzureBlob` metodę czasomierza:

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
Znajomość czas trwania dla naszych danych do przesłania jest doskonałym rozwiązaniem. Jednak możliwość wyświetlany jest postęp naszego przenoszenia *podczas* operacji transferu będą jeszcze bardziej poprawić jakość. Aby zrealizować ten scenariusz, należy utworzyć `TransferContext` obiektu. `TransferContext` Obiektu jest dostarczany w dwóch formach: `SingleTransferContext` i `DirectoryTransferContext`. Jest transferowania jednym pliku (czyli co to jest robimy teraz) i jest on transferowania katalogu plików (które dodajemy później).

Dodaj metody `GetSingleTransferContext` i `GetDirectoryTransferContext` do `Program.cs`: 

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

Modyfikowanie `TransferLocalFileToAzureBlob` metodę `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Wznawianie transferu anulowane
Inna funkcja wygodny oferowane przez Biblioteka przenoszenia danych jest możliwość wznowić transfer anulowane. Dodajmy trochę kodu umożliwiający tymczasowo anulować transfer wpisując `c`, a następnie Wznów transferu 3 sekundy później.

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

Dotychczas naszych `checkpoint` zawsze ustawiono wartość `null`. Teraz wycofanie transferu możemy pobrać ostatniego punktu kontrolnego wynoszącego naszego przenoszenia, a następnie użyj tego nowego punktu kontrolnego w kontekście naszego przenoszenia. 

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Przesunięcia katalogu lokalnego do katalogu obiektów Blob platformy Azure
Byłoby zadowalające, jeśli biblioteka przenoszenia danych w czasie może przekazać tylko jeden plik. Na szczęście to nie jest wielkość liter. Biblioteka przenoszenia danych umożliwia transfer plików i wszystkich jego podkatalogach katalogu. Dodajmy trochę kodu, który pozwala spełniają właśnie tę funkcję.

Najpierw dodaj metody `GetBlobDirectory` do `Program.cs`:

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

Następnie należy zmodyfikować `TransferLocalDirectoryToAzureBlobDirectory`:

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

Istnieje kilka różnic między tą metodą a metoda przekazywania jednego pliku. Używamy teraz `TransferManager.UploadDirectoryAsync` i `getDirectoryTransferContext` metody utworzony wcześniej. Ponadto obecnie udostępniamy `options` wartość naszych operacji przekazywania, co pozwala wskazać, że firma Microsoft ma należeć podkatalogi przesyłania. 

## <a name="copy-file-from-url-to-azure-blob"></a>Skopiuj plik z adresu URL do obiektów Blob platformy Azure
Teraz możemy dodać kod, który pozwala kopiować pliki z adresu URL do obiektów Blob platformy Azure. 

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

Jeden przypadek użycia ważne dla tej funkcji jest, gdy trzeba przenieść danych z innej usługi w chmurze (np. AWS) na platformie Azure. Zawiera adres URL, który umożliwia dostęp do zasobu, można łatwo przenosić tego zasobu w obiektach blob Azure przy użyciu `TransferManager.CopyAsync` metody. Ta metoda wprowadza również nowe parametrów typu boolean. Ustawienie tego parametru `true` wskazuje chęć wykonaj asynchroniczne kopiowania po stronie serwera. Ustawienie tego parametru `false` wskazuje synchroniczne kopię - oznacza zasób jest najpierw pobierany do naszej komputera lokalnego, a następnie przekazać do obiektu Blob Azure. Jednak synchronicznego kopiowania jest obecnie dostępna tylko do skopiowania z jednego zasobu usługi Magazyn Azure. 

## <a name="transfer-azure-blob-to-azure-blob"></a>Przenoszenie obiektów Blob platformy Azure do obiektów Blob platformy Azure
Inna funkcja jednoznacznie zapewnianej przez Biblioteka przenoszenia danych jest możliwość kopiowania z jednego zasobu magazynu Azure do innego. 

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

W tym przykładzie firma Microsoft ustaw dla parametru logicznych `TransferManager.CopyAsync` do `false` wskazująca chęć wykonaj kopię synchronicznego. Oznacza to, czy zasób jest najpierw pobierany do naszej komputera lokalnego, a następnie przekazać do obiektu Blob Azure. Opcja synchronicznego kopiowania jest doskonały sposób, aby upewnić się, że operacji kopiowania ma spójne szybkości. Z kolei szybkości asynchroniczne kopiowania po stronie serwera jest zależny od dostępną przepustowość sieci na serwerze, które mogą się zmieniać. Jednak synchronicznych kopii może wygenerować dodatkowe wyjście koszt w porównaniu do kopiowania asynchronicznego. Zalecanym podejściem jest Użyj synchronicznych kopii w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie co konta magazynu źródłowego, aby uniknąć kosztów wyjście.

## <a name="conclusion"></a>Podsumowanie
Naszej aplikacji przepływu danych jest teraz ukończona. [Przykład pełny kod jest dostępny w witrynie GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app). 

## <a name="next-steps"></a>Kolejne kroki
W tym wprowadzenie, utworzyliśmy aplikację, która współdziała z usługą Azure Storage i działa w systemach Windows, Linux i macOS. Rozpoczęto pobieranie ukierunkowanych na magazyn obiektów Blob. Jednak ten sam wiedzy może odnosić się do przechowywania plików. Aby dowiedzieć się więcej, zapoznaj się [Biblioteka przenoszenia danych magazynu Azure dokumentacji](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]




