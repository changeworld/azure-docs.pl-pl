---
title: Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET | Microsoft Docs
description: Dowiedz się, jak opracować aplikacje platformy .NET i usługi korzystające z usługi Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/22/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35f29e425fc471e4df4a037ef312af0fd041dcd7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699783"
---
# <a name="develop-for-azure-files-with-net"></a>Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

W tym samouczku przedstawiono podstawy korzystania z platformy .NET do tworzenia aplikacji, które korzystają z usługi [Azure Files](storage-files-introduction.md) do przechowywania danych plików. Ten samouczek umożliwia utworzenie prostej aplikacji konsolowej w celu wykonywania podstawowych działań za pomocą platformy .NET i usługi Azure Files:

* Pobieranie zawartości pliku
* Ustawienie limitu przydziału (maksymalnego rozmiaru) udziału plików.
* Utworzenie sygnatury dostępu współdzielonego (klucza SAS) dla pliku, która używa zasad dostępu współdzielonego zdefiniowanych w udziale.
* Skopiowanie pliku do innego pliku w tym samym koncie magazynu.
* Skopiowanie pliku do obiektu blob w tym samym koncie magazynu.
* Rozwiązywanie problemów przy użyciu metryk usługi Azure Storage.

Aby dowiedzieć się więcej na temat usługi Azure Files, zobacz [Wprowadzenie do usługi Azure Files](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Opis interfejsów API platformy .NET

Usługa Azure Files oferuje dwa obszerne podejścia do aplikacji klienckich: blok komunikatów serwera (SMB) i interfejs REST. W ramach platformy .NET te metody zostały wyabstrahowane przez interfejsy API `System.IO` i `WindowsAzure.Storage`.

interfejs API | Kiedy stosować | Uwagi
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Twoja aplikacja: <ul><li>Musi odczytywać/zapisywać pliki za pośrednictwem protokołu SMB</li><li>Działa na urządzeniu, które ma dostęp do konta usługi Azure Files za pośrednictwem portu 445</li><li>Nie musi zarządzać żadnymi ustawieniami administracyjnymi udziału plików</li></ul> | Kodowanie operacji We/Wy pliku usługi Azure Files za pośrednictwem protokołu SMB jest ogólnie takie samo, jak kodowanie operacji We/Wy za pomocą dowolnego sieciowego udziału plików lub lokalnego urządzenia magazynującego. Zobacz [ten samouczek](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter), aby zapoznać się z wprowadzeniem do wielu funkcji platformy .NET, w tym do operacji We/Wy pliku.
[Microsoft.Azure.Storage.File](https://docs.microsoft.com/dotnet/api/overview/azure/storage#client-library) | Twoja aplikacja: <ul><li>Nie może uzyskiwać dostępu do usługi Azure Files za pośrednictwem protokołu SMB na porcie 445 z powodu ograniczeń zapory lub usługodawcy internetowego</li><li>Wymaga funkcji administracyjnych, takich jak możliwość ustawiania przydziału udziału plików lub tworzenia sygnatury dostępu współdzielonego</li></ul> | W tym artykule przedstawiono użycie biblioteki `Microsoft.Azure.Storage.File` dla operacji We/Wy pliku przy użyciu interfejsu REST (zamiast protokołu SMB) oraz zarządzania udziałem plików.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Tworzenie aplikacji konsolowej i uzyskiwanie zestawu
W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. Poniższe kroki pokazują, jak utworzyć aplikację konsoli w programie Visual Studio 2017, jednak kroki w innych wersjach programu Visual Studio są podobne.

1. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**
2. Wybierz kolejno pozycje **Zainstalowane** > **Szablony** > **Visual C#**  > **Klasyczny pulpit systemu Windows**
3. Wybierz pozycję **Aplikacja konsoli (.NET Framework)**
4. Wprowadź nazwę aplikacji w polu **Nazwa:**
5. Kliknij przycisk **OK**

Wszystkie przykłady kodu w tym samouczku można dodać do metody `Main()` w pliku `Program.cs` aplikacji konsolowej.

Biblioteka klienta usługi Azure Storage może być używana w dowolnym typie aplikacji platformy .NET, w tym w usłudze w chmurze platformy Azure lub aplikacji sieci Web oraz aplikacji klasycznych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

## <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów
Istnieją dwa pakiety, które trzeba przywołać w projekcie, aby ukończyć ten samouczek:

* [Microsoft Azure Storage wspólna biblioteka dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Ten pakiet zapewnia programistyczny dostęp do wspólnych zasobów na koncie magazynu.
* [Biblioteka obiektów Blob Microsoft Azure Storage dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): Ten pakiet zapewnia programistyczny dostęp do zasobów obiektów BLOB na koncie magazynu.
* [Biblioteka programu Microsoft Azure Configuration Manager dla środowiska .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): ten pakiet zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji, niezależnie od tego, gdzie została uruchomiona aplikacja.

Pakiet NuGet służy do pobrania obu pakietów. Wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.
2. Wyszukaj w trybie online ciąg „WindowsAzure.Storage”, a następnie kliknij przycisk **Zainstaluj**, aby zainstalować Bibliotekę klienta usługi Storage oraz jej zależności.
3. Wyszukaj w trybie online ciąg „WindowsAzure.ConfigurationManager” i kliknij przycisk **Zainstaluj**, aby zainstalować program Azure Configuration Manager.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Zapisywanie poświadczeń konta magazynu w pliku app.config
Teraz zapiszemy poświadczenia w pliku app.config projektu. Zmodyfikuj plik app.config, jak pokazano w poniższym przykładzie, zastępując ciąg `myaccount` nazwą konta magazynu, a ciąg `mykey` kluczem konta magazynu.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Najnowsza wersja emulatora magazynu Azure nie obsługuje usługi Azure Files. Aby można było pracować z usługą Azure Files, parametry połączenia muszą wskazywać konto usługi Azure Storage w chmurze.

## <a name="add-using-directives"></a>Dodawanie dyrektyw using
Otwórz plik `Program.cs` w Eksploratorze rozwiązań i dodaj następujące dyrektywy using na początku pliku.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Programowy dostęp do udziału plików
Teraz dodamy poniższy kod do metody `Main()` (po kodzie pokazanym powyżej) w celu pobrania parametrów połączenia. Ten kod pobiera odwołanie do pliku utworzonego wcześniej i wyświetla jego zawartość w oknie konsoli.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Aby zobaczyć dane wyjściowe, uruchom aplikację konsolową.

## <a name="set-the-maximum-size-for-a-file-share"></a>Ustawianie maksymalnego rozmiaru udziału plików
Od wersji 5.x biblioteki klienta usługi Azure Storage można ustawić limit przydziału (lub maksymalny rozmiar) udziału plików w gigabajtach. Można również sprawdzić, ile danych jest obecnie przechowywanych w udziale.

Ustawiając limit przydziału dla udziału, można ograniczyć całkowity rozmiar plików przechowywanych w udziale. Jeśli całkowity rozmiar plików w udziale przekroczy ustawiony limit przydziału, klienci nie będą mogli zwiększyć rozmiaru istniejących plików ani tworzyć nowych plików (chyba że pliki będą puste).

W poniższym przykładzie pokazano, jak sprawdzić bieżące użycie udziału oraz jak ustawić limit przydziału w udziale.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generowanie sygnatury dostępu współdzielonego dla pliku lub udziału plików
Począwszy od wersji 5.x biblioteki klienta usługi Azure Storage, można wygenerować sygnaturę dostępu współdzielonego dla udziału plików lub dla pojedynczego pliku. Można też utworzyć zasady dostępu współdzielonego w udziale plików na potrzeby zarządzania sygnaturami dostępu współdzielonego. Utworzenie zasad dostępu współdzielonego jest zalecane, ponieważ umożliwia cofnięcie sygnatur w przypadku zagrożenia bezpieczeństwa.

W poniższym przykładzie tworzone są zasady dostępu współdzielonego w udziale. Następnie za pomocą tych zasad nakładane są ograniczenia na sygnatury dostępu współdzielonego w pliku w udziale.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Aby uzyskać więcej informacji na temat tworzenia i używania sygnatur dostępu współdzielonego, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="copy-files"></a>Kopiowanie plików
Począwszy od wersji 5.x biblioteki klienta usługi Azure Storage, można kopiować pliki do innych plików, pliki do obiektów blob oraz obiekty blob do plików. W kolejnych sekcjach pokażemy, jak programowo wykonywać te operacje kopiowania.

Do kopiowania plików do innych plików oraz obiektów blob do plików i odwrotnie można także użyć narzędzia AzCopy. Zobacz: [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> W przypadku kopiowania obiektu blob do pliku lub pliku do obiektu blob konieczne jest autoryzowanie dostępu do obiektu źródłowego za pomocą sygnatury dostępu współdzielonego, nawet jeśli kopiowanie odbywa się w ramach tego samego konta magazynu.
> 
> 

**Kopiowanie pliku do innego pliku** Poniższy przykładowy kod powoduje skopiowanie pliku do innego pliku w tym samym udziale. Ponieważ ta operacja kopiowania jest wykonywana w ramach tego samego konta magazynu, można użyć uwierzytelniania przy użyciu klucza wspólnego.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Kopiowanie pliku do obiektu blob** Poniższy przykładowy kod powoduje utworzenie pliku i skopiowanie go do obiektu blob w ramach tego samego konta magazynu. Dla pliku źródłowego tworzona jest sygnatura dostępu współdzielonego, za pomocą której usługa autoryzuje dostęp do tego pliku podczas operacji kopiowania.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

W ten sam sposób można skopiować obiekt blob do pliku. Jeśli obiekt źródłowy jest obiektem blob, utwórz sygnaturę dostępu współdzielonego w celu autoryzacji dostępu do tego obiektu blob podczas operacji kopiowania.

## <a name="share-snapshots"></a>Migawki udziałów
Od wersji 8.5 biblioteki klienta usługi Azure Storage można tworzyć migawki udziałów. Można również wyświetlać listę migawek udziałów oraz je przeglądać i usuwać. Migawki udziałów są przeznaczone tylko do odczytu, nie można w ich obrębie wykonywać żadnych operacji zapisu.

**Tworzenie migawek udziałów**

W poniższym przykładzie przedstawiono sposób tworzenia migawki udziału plików.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Wyświetlanie listy migawek udziałów**

W poniższym przykładzie przedstawiono sposób wyświetlania listy migawek udziałów w udziale.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Przeglądanie plików i katalogów w obrębie migawek udziałów**

W poniższym przykładzie przedstawiono sposób przeglądania plików i katalogów w ramach migawek udziałów.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Wyświetlanie listy udziałów i udziałów migawek oraz przywracanie udziałów plików lub plików z migawek udziałów** 

Po utworzeniu migawki udziału plików można w przyszłości odzyskiwać poszczególne pliki lub cały udział plików. 

Aby przywrócić plik z migawki udziału plików, można utworzyć zapytanie o migawki udziałów w udziale plików. Następnie można pobrać plik, który należy do danej migawki udziału i użyć tej wersji do bezpośredniego odczytywania i porównywania albo do przywracania.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();

       var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

           
CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();

       var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
       SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
       sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
       sasConstraints.Permissions = SharedAccessFilePermissions.Read;
       //Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));

```


**Usuwanie migawek udziałów**

W poniższym przykładzie przedstawiono sposób usuwania migawki udziału plików.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Rozwiązywanie problemów z usługą Azure Files przy użyciu metryk
Funkcja analizy usługi Azure Storage obsługuje teraz metryki na potrzeby usługi Azure Files. Dane metryk umożliwiają śledzenie żądań i diagnozowanie problemów.

Można włączyć metryki dla Azure Files z [Azure Portal](https://portal.azure.com). Można też włączyć je programowo przez wywołanie operacji ustawiania właściwości usługi plików za pomocą interfejsu API REST lub przy użyciu jednej z analogicznych operacji w bibliotece klienta usługi Storage.

Poniższy przykładowy kod pokazuje, jak włączyć metryki dla usługi Azure Files za pomocą biblioteki klienta usługi Storage programu .NET.

Najpierw dodaj następujące dyrektywy `using` do pliku `Program.cs` (obok tych dodanych powyżej):

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Zwróć uwagę, że usługi Azure Blobs, Azure Table i Azure Queues używają wspólnie typu `ServiceProperties` w przestrzeni nazw `Microsoft.Azure.Storage.Shared.Protocol`, natomiast usługa Azure Files ma swój własny typ `FileServiceProperties` w przestrzeni nazw `Microsoft.Azure.Storage.File.Protocol`. Jednak aby można było skompilować poniższy kod, musi on zawierać odwołania do obu tych przestrzeni nazw.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Aby uzyskać kompleksowe wskazówki dotyczące rozwiązywania problemów, można zajrzeć do [artykułu na temat rozwiązywania problemów z usługą Azure Files](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Kolejne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files.

### <a name="conceptual-articles-and-videos"></a>Artykuły koncepcyjne i filmy
* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Azure Files: płynnie działający system plików SMB w chmurze dla systemów Windows i Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure Files with Linux (Jak używać usługi Azure Files z systemem Linux)](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Narzędzia dostępne dla usługi Magazyn plików
* [How to use AzCopy with Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Jak używać narzędzia AzCopy z usługą Microsoft Azure Storage)
* [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Troubleshooting Azure Files problems](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems) (Rozwiązywanie problemów z usługą Azure Files)

### <a name="reference"></a>Tematy pomocy
* [Dokumentacja biblioteki klienta usługi Storage dla programu .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Dokumentacja interfejsu API REST usługi plików](https://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Wpisy na blogach
* [Azure Files is now generally available (Usługa Azure Files jest teraz ogólnie dostępna)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure Files (Za kulisami usługi Azure Files)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Wprowadzenie do usługi plików platformy Microsoft Azure)
* [Persisting connections to Microsoft Azure Files](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx) (Utrwalanie połączeń z plikami platformy Microsoft Azure)
