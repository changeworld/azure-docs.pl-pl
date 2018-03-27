---
title: Azure Quickstart — przekazywanie, pobieranie i wyświetlanie listy obiektów blob w usłudze Azure Storage za pomocą platformy .NET | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz konto magazynu i kontener. Następnie przy użyciu biblioteki klienta platformy .NET przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: 716e61840f4bfb5a68a995683e67dae0b43d3854
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą platformy .NET

Dzięki temu przewodnikowi Szybki start dowiesz się, w jaki sposób za pomocą biblioteki klienckiej platformy .NET dla usługi Azure Storage przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, najpierw utwórz konto usługi Azure Storage w witrynie [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Aby uzyskać pomoc przy tworzeniu konta, zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

Następnie pobierz i zainstaluj program .NET Core 2.0 dla swojego systemu operacyjnego. Możesz również zainstalować edytor do korzystania w systemie operacyjnym.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Zainstaluj program [.NET Core dla systemu Windows](https://www.microsoft.com/net/download/windows/build) 
- Opcjonalnie zainstaluj program [Visual Studio dla systemu Windows](https://www.visualstudio.com/) 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Zainstaluj program [.NET Core dla systemu Linux](https://www.microsoft.com/net/download/linux/build)
- Opcjonalnie zainstaluj program [Visual Studio Code](https://www.visualstudio.com/) i [rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Zainstaluj program [.NET Core dla systemu macOS](https://www.microsoft.com/net/download/macos/build)
- Opcjonalnie zainstaluj program [Visual Studio dla komputerów Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja używana w tym przewodniku Szybki start to podstawowa aplikacja konsoli. Przykładową aplikację można eksplorować w serwisie [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć rozwiązanie programu Visual Studio, wyszukaj folder storage-dotnet-quickstart, otwórz go i kliknij dwukrotnie pozycję storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Aby uruchomić aplikację, należy wprowadzić parametry połączenia konta magazynu. Te parametry połączenia można przechowywać w zmiennej środowiskowej na maszynie lokalnej z uruchomioną aplikacją. Utwórz zmienną środowiskową przy użyciu jednego z poniższych poleceń, w zależności od systemu operacyjnego. Zastąp wartość `<yourconnectionstring>` rzeczywistymi parametrami połączenia.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Edytuj swój plik .bash_profile i dodaj zmienną środowiskową:

```
export STORAGE_CONNECTION_STRING=
```

Po dodaniu zmiennej środowiskowej wyloguj się i zaloguj ponownie, aby zmiany zostały uwzględnione. Możesz również wpisać ciąg „source .bash_profile” z terminala.

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Ten przykład tworzy plik testowy w lokalnym folderze **Moje dokumenty** i przekazuje go do magazynu obiektów blob. Następnie w przykładzie zostanie utworzona lista obiektów blob w kontenerze i zostanie pobrany plik z nową nazwą, aby można było porównać stare i nowe pliki. 

Przejdź do katalogu aplikacji, a następnie uruchom aplikację za pomocą polecenia `dotnet run`.

```
dotnet run
```

Wyświetlone dane wyjściowe są podobne do poniższego przykładu:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Po naciśnięciu klawisza **Enter** aplikacja usunie kontener magazynu i pliki. Przed ich usunięciem sprawdź, czy folder **Moje dokumenty** zawiera dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne. Skopiuj adres URL obiektu blob z okna konsoli i wklej go do przeglądarki, aby wyświetlić zawartość obiektu blob.

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe. Wiesz już, jak działa aplikacja przykładowa. Otwórz plik Program.cs i spójrz na kod. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Następnie omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="try-parsing-the-connection-string"></a>Próba analizowania parametrów połączenia

Przykładowy kod najpierw sprawdza, czy zmienna środowiskowa zawiera parametry połączenia, które można przeanalizować w celu utworzenia obiektu [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) wskazującego na konto magazynu. Aby sprawdzić, czy parametry połączenia są prawidłowe, należy użyć metody [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Jeśli użycie metody **TryParse** zakończy się powodzeniem, zostanie zainicjowana zmienna *storageAccount* i zwrócony wynik **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring", EnvironmentVariableTarget.User);

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Tworzenie kontenera i ustawianie uprawnień

Następnie przykład tworzy kontener i ustawia jego uprawnienia tak, aby wszystkie obiekty blob w kontenerze były publiczne. Jeśli obiekt blob jest publiczny, dowolny klient może uzyskiwać do niego anonimowy dostęp.

Aby utworzyć kontener, najpierw utwórz wystąpienie obiektu [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient), które wskazuje na magazynu obiektów blob na koncie magazynu. Następnie utwórz wystąpienie obiektu [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) i utwórz kontener. 

W takim przypadku przykład wywołuje metodę [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) w celu utworzenia kontenera. Wartość identyfikatora GUID jest dołączana do nazwy kontenera w celu zapewnienia, że jest ona unikatowa. W środowisku produkcyjnym często zaleca się użycie metody [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync), aby kontener był tworzony tylko wtedy, gdy jeszcze nie istnieje i można uniknąć konfliktu nazw.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Następnie przykład przekazuje plik lokalny do blokowego obiektu blob. Przykładowy kod pobiera odwołanie do obiektu **CloudBlockBlob** przez wywołanie metody [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) w kontenerze utworzonym w poprzedniej sekcji. Wybrany plik jest następnie przekazywany do obiektu blob przez wywołanie metody [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Przykład tworzy listę obiektów blob w kontenerze za pomocą metody [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). W tym przykładzie do kontenera dodano tylko jeden obiekt blob, więc operacja tworzenia listy zwraca tylko ten jeden obiekt blob.

Jeśli jest zbyt wiele obiektów blob do zwrócenia w jednym wywołaniu (domyślnie więcej niż 5000), metoda **ListBlobsSegmentedAsync** zwraca segment łącznej liczby wyników i token kontynuacji. Aby pobrać następny segment obiektów blob, udostępnij token kontynuacji zwrócony przez poprzednie wywołanie i tak dalej, aż token kontynuacji będzie mieć wartość null. Token kontynuacji o wartości null wskazuje, że wszystkie obiekty blob zostały pobrane. Przykładowy kod przedstawia sposób użycia tokenu kontynuacji w celu użycia najlepszych rozwiązań.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Następnie przykład pobiera utworzony wcześniej obiekt blob do lokalnego systemu plików przy użyciu metody [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). Przykładowy kod dodaje sufiks „_DOWNLOADED” do nazwy obiektu blob, aby oba pliki były widoczne w lokalnym systemie plików.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Przykład czyści utworzone zasoby przez usunięcie całego kontenera przy użyciu elementu [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Jeśli chcesz, możesz także usunąć pliki lokalne.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji .NET z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji .NET przy użyciu magazynu obiektów blob:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- Pobierz pakiet NuGet dla najnowszej wersji [biblioteki klienta .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) dla usługi Azure Storage. 
- Wyświetl [kod źródłowy biblioteki klienta .NET](https://github.com/Azure/azure-storage-net) w usłudze GitHub.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta .NET, zobacz [dokumentację interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) napisanymi przy użyciu biblioteki klienta .NET.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano następujące czynności: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET. 

Aby dowiedzieć się, jak utworzyć aplikację internetową, która przekazuje obraz do usługi Blob Storage, przejdź do tematu [Przekazywanie danych obrazu do chmury za pomocą usługi Azure Storage](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](storage-dotnet-how-to-use-blobs.md)

- Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
- Aby zapoznać się z przykładową aplikacją, którą można wdrożyć z programu Visual Studio dla systemu Windows, zobacz [.NET Photo Gallery Web Application Sample with Azure Blob Storage (Przykład internetowej aplikacji platformy .NET z galerią zdjęć i z usługą Azure Blob Storage)](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 