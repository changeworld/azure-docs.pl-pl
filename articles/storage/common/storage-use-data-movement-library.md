---
title: Transferowanie danych za pomocą Biblioteka przenoszenia danych magazynu platformy Microsoft Azure | Dokumentacja firmy Microsoft
description: Biblioteka przenoszenia danych umożliwia przenoszenie lub kopiowanie danych do lub z obiektów blob i zawartości pliku. Kopiowanie danych do usługi Azure Storage z lokalnych plików lub kopiowania danych w ramach lub między kontami magazynu. Łatwo Migruj dane do usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 8e09e2c33359c94275d9819b335544d15d4c7d78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790093"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Transferowanie danych za pomocą Biblioteka przenoszenia danych magazynu platformy Microsoft Azure

## <a name="overview"></a>Omówienie
Biblioteka przenoszenia danych do programu Microsoft Azure Storage jest biblioteką "open source" dla wielu platform, które zaprojektowano z myślą o wysokiej wydajności, przekazywanie, pobieranie i kopiowanie plików i obiektów blob usługi Azure Storage. Ta biblioteka jest podstawowym środowisku przenoszenia danych, która napędza [AzCopy](../storage-use-azcopy.md). Biblioteka przenoszenia danych udostępnia wygodne metody, które nie są dostępne w przypadku naszej tradycyjnych [biblioteki klienta usługi Azure Storage .NET](../blobs/storage-dotnet-how-to-use-blobs.md). Obejmuje to możliwość Ustaw liczbę operacji równoległych, śledzić postępu przenoszenia, łatwe wznawianie transferu anulowane i wiele innych.

Ta biblioteka korzysta z platformy .NET Core, co oznacza, że możesz użyć go podczas tworzenia aplikacji .NET for Windows, Linux i macOS. Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [dokumentacji platformy .NET Core](https://dotnet.github.io/). Ta biblioteka działa także dla tradycyjnych aplikacji .NET Framework dla Windows.

Ten dokument przedstawia sposób tworzenia aplikacji konsolowej .NET Core, który działa na Windows, Linux i macOS i wykonuje następujące scenariusze:

- Przekazywanie plików i katalogów do magazynu obiektów Blob.
- Podczas transferu danych, należy zdefiniować liczbę operacji równoległych.
- Śledzenie postępu transferu danych.
- Transfer danych Wznów zostało anulowane.
- Skopiuj plik z adresu URL do magazynu obiektów Blob.
- Skopiuj z magazynu obiektów Blob do usługi Blob Storage.

**Co jest potrzebne:**

* [Visual Studio Code](https://code.visualstudio.com/)
* [konto usługi Azure Storage](storage-quickstart-create-account.md)

> [!NOTE]
> W tym przewodniku założono, że czytelnik zna już [usługi Azure Storage](https://azure.microsoft.com/services/storage/). Jeśli nie, odczytywanie [wprowadzenie do usługi Azure Storage](storage-introduction.md) dokumentacja jest pomocne. Co najważniejsze, musisz [Tworzenie konta magazynu](storage-quickstart-create-account.md) aby rozpocząć korzystanie z Biblioteka przenoszenia danych.
>
>

## <a name="setup"></a>Konfiguracja

1. Odwiedź stronę [Przewodnik instalacji platformy .NET Core](https://www.microsoft.com/net/core) do zainstalowania platformy .NET Core. Podczas wybierania środowiska, wybierz opcję wiersza polecenia.
2. W wierszu polecenia Utwórz katalog dla projektu. Przejdź do tego katalogu, wpisz `dotnet new console -o <sample-project-name>` Aby utworzyć projekt konsoli języka C#.
3. Otwórz ten katalog, w programie Visual Studio Code. Ten krok można szybko wykonać z poziomu wiersza polecenia, wpisując `code .` w Windows.
4. Zainstaluj [rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) w Visual Studio Code Marketplace. Uruchom ponownie program Visual Studio Code.
5. W tym momencie powinno być widoczne dwa monity. Jeden jest przeznaczony dla dodawania "wymagane zasoby do tworzenia i debugowania." Kliknij przycisk "tak". Innego wiersza jest przywracania nierozwiązane zależności. Kliknij przycisk "Przywróć".
6. Modyfikowanie `launch.json` w obszarze `.vscode` służące terminalu zewnętrznym jako konsoli. To ustawienie, powinny być odczytywane jako `"console": "externalTerminal"`
7. Visual Studio Code umożliwia debugowanie aplikacji platformy .NET Core. Trafienia `F5` do uruchamiania aplikacji i sprawdź, czy działa z konfiguracją. Powinien zostać wyświetlony "Hello World!" drukowane w konsoli.

## <a name="add-data-movement-library-to-your-project"></a>Dodaj Biblioteka przenoszenia danych do projektu

1. Najnowszą wersję Biblioteka przenoszenia danych, aby dodać `dependencies` części Twojej `<project-name>.csproj` pliku. W czasie pisania byłoby tej wersji `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Można przywrócić projektu powinien być wyświetlany monit. Kliknij przycisk "Przywróć". Projekt można przywrócić z wiersza polecenia, wpisując polecenie `dotnet restore` w głównym katalogu projektu.

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
Pierwszą rzeczą, jaką robimy to ustawienie kodu "szkielet" naszej aplikacji. Ten kod nam wyświetla monit o podanie konta nazwy i klucza konta magazynu i używa tych poświadczeń w celu utworzenia `CloudStorageAccount` obiektu. Ten obiekt służy do interakcji z naszego konta magazynu, we wszystkich scenariuszach transferu. Kod wyświetli nam wybierz typ operacji transferu, które firma Microsoft chce wykonać.

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

## <a name="transfer-local-file-to-azure-blob"></a>Przesyłanie pliku lokalnego do obiektu Blob platformy Azure
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

Ten kod nam monituje o podanie ścieżki do pliku lokalnego, nazwę nowego lub istniejącego kontenera i nazwa nowy obiekt blob. `TransferManager.UploadAsync` Metoda wykonuje przekazywania, korzystając z tych informacji.

Trafienia `F5` do uruchamiania aplikacji. Możesz sprawdzić, przekazywania, które wystąpiły, wyświetlając konta magazynu przy użyciu [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Ustaw liczbę operacji równoległych
Doskonałe funkcji oferowanych przez Biblioteka przenoszenia danych jest możliwość Ustaw liczbę operacji równoległych, aby zwiększyć przepływność transferu danych. Domyślnie biblioteka przenoszenia danych Ustawia liczbę operacji równoległych 8 * liczba rdzeni na swojej maszynie.

Należy pamiętać, że wiele operacji równoległych w środowisku o niskiej przepustowości może przeciąży połączenie sieciowe i faktycznie zapobiec operacje pełni ukończenie. Należy wypróbować to ustawienie, aby określić, jakie rozwiązanie sprawdzi się najlepiej w oparciu o na dostępną przepustowość sieci.

Dodajmy trochę kodu, który pozwala ustawić liczbę operacji równoległych. Możemy również dodać kod, który razy, ile Trwa przesyłanie do ukończenia.

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

Modyfikowanie `TransferLocalFileToAzureBlob` metodę, aby używał czasomierza:

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

## <a name="track-transfer-progress"></a>Śledzenie postępu transferu
Wiedząc, jak długo dane do transferu nadaje się doskonale. Jednak możliwość wyświetlany jest postęp naszych transferu *podczas* operacji transferu byłoby jeszcze lepiej. Aby osiągnąć ten scenariusz, należy utworzyć `TransferContext` obiektu. `TransferContext` Obiektu jest oferowana w dwóch formach: `SingleTransferContext` i `DirectoryTransferContext`. Jest transferu pojedynczy plik (jest to, co robimy teraz) i jest on transferu katalog plików, (które dodajemy w dalszej części).

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
Kolejną funkcją wygodne oferowane przez bibliotekę przepływu danych jest możliwość wznowić anulowanej transferu. Dodajmy trochę kodu, która umożliwia czasowo Anuluj przeniesienie, wpisując `c`, a następnie Wznów transferu później 3 sekundy.

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

Pory nasz `checkpoint` zawsze ustawiono wartość `null`. Teraz Jeśli trwa anulowanie transferu, możemy pobrać ostatniego punktu kontrolnego naszych transferu, a następnie użyć tego nowego punktu kontrolnego w kontekście naszych transferu.

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Przenieś lokalny katalog do katalogu usługi Azure Blob
Byłoby zadowalające, jeśli biblioteka przenoszenia danych w danym momencie może przekazać tylko jeden plik. Na szczęście to nie jest wymagane. Biblioteka przenoszenia danych zapewnia możliwość przesyłania katalogu plików i wszystkich podkatalogach. Dodajmy trochę kodu, który pozwala nam to zrobić.

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

Istnieje kilka różnic między tą metodą a metodą przekazywania pojedynczy plik. Używamy teraz `TransferManager.UploadDirectoryAsync` i `getDirectoryTransferContext` metoda utworzony wcześniej. Ponadto zapewniamy obecnie `options` wartość operacji przekazywania, co pozwala nam w celu wskazania, że chcemy, które mają zostać objęte podkatalogów przesyłania.

## <a name="copy-file-from-url-to-azure-blob"></a>Skopiuj plik z adresu URL obiektu Blob platformy Azure
Teraz możemy dodać kod, który pozwala kopiować pliki z adresu URL do obiektu Blob platformy Azure.

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

Jeden przypadek użycia istotne dla tej funkcji jest, jeśli musisz przenieść dane z innej usługi w chmurze (np. usług AWS) na platformie Azure. Tak długo, jak masz adres URL, który umożliwia dostęp do zasobu, można łatwo przenosić tego zasobu do obiektów blob platformy Azure przy użyciu `TransferManager.CopyAsync` metody. Ta metoda wprowadza również nowy parametr logiczny. Ustawienie tego parametru na `true` wskazuje, że chcemy asynchronicznego kopiowanie po stronie serwera. Ustawienie tego parametru na `false` wskazuje kopię synchroniczne — czyli zasób jest najpierw pobierany na nasz komputer lokalny, a następnie przekazywane do obiektu Blob platformy Azure. Jednak kopia synchroniczna jest obecnie dostępna tylko do kopiowania z jednym zasobem usługi Azure Storage do innego.

## <a name="transfer-azure-blob-to-azure-blob"></a>Przesyłanie obiektów Blob platformy Azure do obiektów Blob platformy Azure
Kolejną funkcją jednoznacznie dostarczone przez Biblioteka przenoszenia danych jest możliwość kopiowania z jednym zasobem usługi Azure Storage do innego.

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

W tym przykładzie ustawimy parametr logiczny `TransferManager.CopyAsync` do `false` aby wskazać, że firma Microsoft ma synchroniczne kopiowanie. Oznacza to, czy zasób jest najpierw pobierany na nasz komputer lokalny, a następnie przekazywane do obiektu Blob platformy Azure. Opcja kopia synchroniczna jest doskonały sposób, aby upewnić się, że operacja kopiowania spójne szybkość. Z kolei szybkości asynchronicznych kopii po stronie serwera jest zależny od dostępnej przepustowości sieci na serwerze, co może powodować wahania. Jednak kopia synchroniczna może generować koszty dodatkowe ruchu wychodzącego w porównaniu z kopiowania asynchronicznego. Zalecanym podejściem jest używać kopia synchroniczna w maszynie Wirtualnej platformy Azure, który znajduje się w tym samym regionie, co źródłowego konta magazynu w celu uniknięcia koszty ruchu wychodzącego.

## <a name="conclusion"></a>Podsumowanie
Nasza aplikacja przenoszenie danych zostało zakończone. [Przykład pełny kod jest dostępny w witrynie GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Kolejne kroki
W tym wprowadzenie, utworzyliśmy aplikację, która wchodzi w interakcję z usługą Azure Storage i działa w Windows, Linux i macOS. To wprowadzenie koncentruje się na magazynu obiektów Blob. Jednak ta wiedza tego samego mogą dotyczyć usługi File Storage. Aby dowiedzieć się więcej, zapoznaj się z [dokumentację referencyjną Biblioteka przenoszenia danych usługi Azure Storage](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
