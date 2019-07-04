---
title: 'Szybki start: Biblioteka klienta magazynu obiektów Blob platformy Azure dla platformy .NET'
description: W tym przewodniku Szybki Start dowiesz się, jak używać biblioteki klienta magazynu obiektów Blob platformy Azure dla platformy .NET do tworzenia kontenera i obiektów blob w magazynie obiektów Blob (obiekt). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: c5e9981c6854ff778775631f1d671189830e564b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435765"
---
# <a name="quickstart-azure-blob-storage-client-library-for-net"></a>Szybki start: Biblioteka klienta magazynu obiektów Blob platformy Azure dla platformy .NET

Rozpocznij pracę z biblioteki klienta usługi Azure Blob Storage dla platformy .NET. Usługa Azure Blob Storage to rozwiązanie do magazynowania obiektów firmy Microsoft w chmurze. Wykonaj kroki, aby zainstalować pakiet i wypróbuj przykładowy kod dla podstawowych zadań. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury.

Za pomocą biblioteki klienta usługi Azure Blob Storage dla platformy .NET do:

* Tworzenie kontenera
* Ustaw uprawnienia do kontenera
* Tworzenie obiektu blob w usłudze Azure Storage
* Pobieranie obiektu blob na komputerze lokalnym
* Wyświetlić listę wszystkich obiektów blob w kontenerze
* Usuwanie kontenera

[Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [kod źródłowy biblioteki](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [pakietów (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [przykłady](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — [utworzyć jedno za darmo](https://azure.microsoft.com/free/)
* Konto usługi Azure Storage — [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Zestaw NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core), lub nowszy, system operacyjny

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja przeprowadzi Cię przez przygotowanie projektu do pracy za pomocą biblioteki klienta usługi Azure Blob Storage dla platformy .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Najpierw należy utworzyć aplikację platformy .NET Core, o nazwie **obiektów blob — Szybki Start**.

1. W oknie konsoli (np. cmd, PowerShell lub Bash), należy użyć `dotnet new` polecenie, aby utworzyć nową aplikację konsoli o nazwie **obiektów blob — Szybki Start**. To polecenie umożliwia utworzenie prostego "Hello World" C# projektu z jednym pliku źródłowym: **Program.cs**.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Przełącz się do nowo utworzonego **obiektów blob — Szybki Start** folder i kompilacja aplikacji, aby sprawdzić, czy wszystkie poprawnie.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Oczekiwane dane wyjściowe z kompilacji powinien wyglądać mniej więcej tak:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mając nadal w katalogu aplikacji, należy zainstalować biblioteki klienta magazynu obiektów Blob platformy Azure dla pakietu .NET przy użyciu `dotnet add package` polecenia.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz plik Program.cs w edytorze
2. Usuń **elementu Console.WriteLine** — instrukcja
3. Dodaj **przy użyciu** dyrektywy
4. Tworzenie **ProcessAsync** lokalizację głównego kodu dla przykładu, metoda
5. Asynchroniczne wywołanie **ProcessAsync** metody z **Main**

Oto kod:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń z witryny Azure Portal

Aplikacja przykładowa musi uwierzytelnić dostęp do konta magazynu. W celu uwierzytelnienia dodaj do aplikacji poświadczenia konta magazynu w postaci parametrów połączenia. Wyświetl poświadczenia konta magazynu, wykonując następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. W tym miejscu możesz przeglądać klucze dostępu do konta oraz pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Parametry połączenia** w obszarze **key1** i wybierz przycisk **Kopiuj**, aby skopiować parametry połączenia. W następnym kroku dodasz wartość parametrów połączenia do zmiennej środowiskowej.

    ![Zrzut ekranu pokazujący sposób kopiowania parametrów połączenia z witryny Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Po skopiowaniu parametrów połączenia zapisz je w nowej zmiennej środowiskowej na komputerze, na którym uruchomiona jest aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zastąp `<yourconnectionstring>` z rzeczywistymi parametrami połączenia.

Po dodaniu zmiennej środowiskowej, może być konieczne ponowne uruchomienie uruchomione programy, które muszą odczytywać zmienną środowiskową. Na przykład jeśli używasz programu Visual Studio jako edytora, uruchom ponownie program Visual Studio przed uruchomieniem przykładu.

#### <a name="windows"></a>Windows

```cmd
setx STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

#### <a name="linux"></a>Linux

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob storage jest zoptymalizowany pod kątem przechowywania olbrzymich ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu.
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-quickstart-blobs-dotnet/blob1.png)

Użyj następujących klas platformy .NET, aby wchodzić w interakcje z tymi zasobami:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): **CloudStorageAccount** klasa reprezentuje konto usługi Azure storage. Klasa używana do autoryzowania dostępu do magazynu obiektów Blob za pomocą kluczy dostępu do Twojego konta.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): **CloudBlobClient** klasy zapewnia punkt dostępu do usługi obiektów Blob w kodzie.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): **CloudBlobContainer** klasa reprezentuje kontener obiektów blob w kodzie.
* [CloudBlockBlob](//dotnet/api/microsoft.azure.storage.blob.cloudblockblob): **CloudBlockBlob** obiekt reprezentuje blokowych obiektów blob w kodzie. Blokowe obiekty blob składają się z bloków danych, którymi można zarządzać indywidualnie.

## <a name="code-examples"></a>Przykłady kodu

Następujące przykładowe fragmenty kodu pokazują, jak wykonaj następujące czynności za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure dla platformy .NET:

   * [Uwierzytelnianie klienta](#authenticate-the-client)
   * [Tworzenie kontenera](#create-a-container)
   * [Ustaw uprawnienia do kontenera](#set-permissions-on-a-container)
   * [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
   * [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
   * [Pobieranie obiektów blob](#download-blobs)
   * [Usuwanie kontenera](#delete-a-container)

### <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Poniższy kod sprawdza, czy zmienna środowiskowa zawiera parametry połączenia, które można przeanalizować w celu utworzenia [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) wskazujące na koncie magazynu. Aby sprawdzić, czy parametry połączenia są prawidłowe, należy użyć metody [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet). Jeśli użycie metody **TryParse** zakończy się powodzeniem, zostanie zainicjowana zmienna *storageAccount* i zwrócony wynik **true**.

Dodaj następujący kod wewnątrz **ProcessAsync** metody:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Wykonaj pozostałe czynności w tym artykule, Zastąp **/ / Dodaj inne operacje, w tym miejscu** w kodzie powyżej z fragmentów kodu w poniższych sekcjach.

### <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, najpierw utwórz wystąpienie obiektu [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), które wskazuje na magazynu obiektów blob na koncie magazynu. Następnie utwórz wystąpienie obiektu [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) i utwórz kontener.

W takim wypadku kod wywołuje [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metodę, aby utworzyć kontener. Wartość identyfikatora GUID jest dołączana do nazwy kontenera w celu zapewnienia, że jest ona unikatowa. W środowisku produkcyjnym, jest często lepiej jest używać [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metodę, aby utworzyć kontener, tylko wtedy, gdy już nie istnieje.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Ustaw uprawnienia do kontenera

Ustaw uprawnienia w kontenerze, tak aby wszystkie obiekty BLOB w kontenerze były publiczne. Jeśli obiekt blob jest publiczny, dowolny klient może uzyskiwać do niego anonimowy dostęp.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Poniższy fragment kodu pobiera odwołanie do **CloudBlockBlob** obiektu przez wywołanie metody [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metody w kontenerze utworzonym w poprzedniej sekcji. Następnie przekazuje wybrany plik lokalny do obiektu blob przez wywołanie metody [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) metody. Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
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

Listy obiektów blob w kontenerze za pomocą [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metody. W takim przypadku tylko jeden obiekt blob dodano do kontenera, więc operacja tworzenia listy zwraca tylko ten jeden obiekt blob.

Jeśli jest zbyt wiele obiektów blob do zwrócenia w jednym wywołaniu (domyślnie więcej niż 5000), metoda **ListBlobsSegmentedAsync** zwraca segment łącznej liczby wyników i token kontynuacji. Aby pobrać następny segment obiektów blob, udostępnij token kontynuacji zwrócony przez poprzednie wywołanie i tak dalej, aż token kontynuacji będzie mieć wartość null. Token kontynuacji o wartości null wskazuje, że wszystkie obiekty blob zostały pobrane. Kod przedstawia sposób używania kontynuacji tokenu w celu użycia najlepszych rozwiązań.

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
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobieranie obiektu blob został wcześniej utworzony do lokalnego systemu plików przy użyciu [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metody. Przykładowy kod dodaje sufiks "_DOWNLOADED" do nazwy obiektu blob, tak, aby oba pliki można wyświetlić w lokalnym systemie plików.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby aplikację utworzoną przez usunięcie całego kontenera przy użyciu [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Jeśli chcesz, możesz także usunąć pliki lokalne.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
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

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w lokalnym **Moje dokumenty** folder i przekazuje go do magazynu obiektów Blob. Przykład następnie wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą, tak aby można było porównać stary i nowy plik.

Jeśli używasz programu Visual Studio jako edytora, możesz go uruchomić, naciskając klawisz **F5**.

W przeciwnym razie przejdź do katalogu aplikacji, a następnie tworzenie i uruchamianie aplikacji.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe z przykładowej aplikacji jest podobny do poniższego przykładu:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Po naciśnięciu klawisza **Enter** aplikacja usunie kontener magazynu i pliki. Przed ich usunięciem sprawdź, czy folder **Moje dokumenty** zawiera dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne. Skopiuj adres URL obiektu blob z okna konsoli i wklej go do przeglądarki, aby wyświetlić zawartość obiektu blob.

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start opisano następujące czynności: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET.

Aby dowiedzieć się, jak utworzyć aplikację sieci web, która przekazuje obraz do usługi Blob storage, przejdź do:

> [!div class="nextstepaction"]
> [Przekazywanie i przetwarzanie obrazu](storage-upload-process-images.md)

* Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
* Aby zapoznać się z przykładową aplikacją, którą można wdrożyć z programu Visual Studio dla systemu Windows, zobacz [.NET Photo Gallery Web Application Sample with Azure Blob Storage (Przykład internetowej aplikacji platformy .NET z galerią zdjęć i z usługą Azure Blob Storage)](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
