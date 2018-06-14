---
title: Tworzenie i używanie sygnatury dostępu współdzielonego (SAS) z magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia sposób tworzenia sygnatur dostępu współdzielonego do użytku z magazynu obiektów Blob i jak korzystać z nich w aplikacji klienta.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873212"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Udostępnione sygnatur dostępu, część 2: Tworzenie i sygnatury dostępu Współdzielonego za pomocą magazynu obiektów Blob

[Część 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tego samouczka przedstawione udostępnionym sygnatur dostępu (SAS) i wyjaśniono najlepsze rozwiązania dotyczące korzystania z nich. Część 2 przedstawiono sposób generowania, a następnie użyj sygnatur dostępu współdzielonego z magazynem obiektów Blob. Przykłady są napisane w języku C# i używać biblioteki klienta magazynu Azure dla platformy .NET. Przykłady w tym samouczku:

* Generowanie sygnatury dostępu współdzielonego w kontenerze
* Generowanie sygnatury dostępu współdzielonego na obiektu blob
* Tworzenie zasad dostępu przechowywane do zarządzania podpisów zasobów kontenera
* Testowanie sygnatur dostępu współdzielonego w aplikacji klienta

## <a name="about-this-tutorial"></a>Informacje o tym samouczku
W tym samouczku utworzymy dwóch aplikacji konsoli, które przedstawiają tworzenia i używania sygnatur dostępu współdzielonego dla kontenerów i obiektów blob:

**1 aplikacja**: aplikacji do zarządzania. Generuje sygnaturę dostępu współdzielonego dla kontenera i obiektu blob. Zawiera klucz dostępu do konta magazynu w kodzie źródłowym.

**Aplikacja 2**: aplikacji klienckiej. Uzyskuje dostęp do kontenera obiektów blob zasobów i używania sygnatur dostępu współdzielonego utworzone za pomocą pierwszej aplikacji. Używa tylko sygnatur dostępu współdzielonego dostępu do kontenera i zasobów obiektów blob — robi *nie* obejmują klucz dostępu do konta magazynu.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Część 1: Tworzenie aplikacji konsoli można wygenerować sygnatury dostępu współdzielonego
Najpierw upewnij się, że masz biblioteki klienta magazynu Azure dla programu .NET zainstalowane. Można zainstalować [pakietu NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "pakietu NuGet") zawierająca aktualną zestawy dla biblioteki klienta. Jest to zalecana metoda sprawdzeniu, czy masz najnowsze poprawki. Możesz również pobrać jako część najnowszej wersji biblioteki klienta [zestawu Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/).

W programie Visual Studio Utwórz nową aplikację konsoli systemu Windows i nadaj mu nazwę **GenerateSharedAccessSignatures**. Dodaj odwołania do [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) i [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) przy użyciu jednej z następujących metod:

* Użyj [Menedżera pakietów NuGet](https://docs.nuget.org/consume/installing-nuget) w programie Visual Studio. Wybierz **projektu** > **Zarządzaj pakietami NuGet**, Wyszukaj w trybie online dla każdego pakietu (Microsoft.WindowsAzure.ConfigurationManager i WindowsAzure.Storage) i zainstaluj je.
* Możesz też zlokalizować te zestawy w instalacji zestawu SDK platformy Azure i dodaj odwołania do nich:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Na początku pliku Program.cs Dodaj następujące **przy użyciu** dyrektywy:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Edytowanie pliku app.config, tak, aby zawierał ustawienia konfiguracji z użyciem parametrów połączenia, który wskazuje na koncie magazynu. Plik app.config powinien wyglądać podobnie do tego:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Generuj identyfikator URI sygnatury dostępu współdzielonego dla kontenera
Najpierw dodamy metodę, aby wygenerować sygnaturę dostępu współdzielonego na nowy kontener. W takim przypadku podpis nie jest skojarzone z zasadami dostępu przechowywane, więc prowadzi w identyfikatorze URI informacje wskazujące jego czas wygaśnięcia i uprawnienia, które domyślnie przyzna.

Najpierw dodaj kod, aby **Main()** metody w celu uwierzytelniania dostępu do konta magazynu i utworzyć nowy kontener:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Następnie dodaj metodę, która generuje sygnatury dostępu współdzielonego dla kontenera i zwraca podpis identyfikatora URI:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Dodaj następujące wiersze w dolnej części **Main()** przed wywołaniem metody **Console.ReadLine()**, aby wywołać **GetContainerSasUri()** i zapisywania podpisu identyfikator URI do okno konsoli:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Kompilowanie i uruchamianie do wyjściowego sygnatury dostępu współdzielonego identyfikatora URI dla nowego kontenera. Identyfikator URI będzie podobny do następującego:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Po uruchomieniu kodu sygnatury dostępu współdzielonego, utworzenia kontenera będzie obowiązywać przez następne 24 godziny. Podpis udziela uprawnienia klienta do listy obiektów blob, w kontenerze i zapisać nowe obiekty BLOB do kontenera.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generowanie sygnatury dostępu współdzielonego identyfikatora URI dla obiektu blob
Następnie należy napisać kod podobne do tworzenia nowych obiektów blob w kontenerze i Generowanie sygnatury dostępu współdzielonego. Ta sygnatura dostępu współdzielonego nie jest skojarzone z zasadami dostępu przechowywane, więc obejmuje czas rozpoczęcia, czas wygaśnięcia i informacji o uprawnieniach w identyfikatorze URI.

Dodawanie nowej metody, która tworzy nowy obiekt blob i zapisuje tekst, a następnie generuje sygnaturę dostępu współdzielonego i zwraca podpis identyfikatora URI:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

W dolnej części **Main()** metody, Dodaj następujące wiersze do wywołania **GetBlobSasUri()**, przed wywołaniem do **Console.ReadLine()** i zapisu sygnatury dostępu współdzielonego Identyfikator URI w oknie konsoli:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Kompilowanie i uruchamianie do wyjściowego sygnatury dostępu współdzielonego identyfikatora URI dla nowego obiektu blob. Identyfikator URI będzie podobny do następującego:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Tworzenie zasad dostępu przechowywanych w kontenerze
Teraz Utwórzmy zasad dostępu do przechowywanych w kontenerze, który będzie definiował ograniczenia żadnych sygnatur dostępu współdzielonego, które są skojarzone z nim.

W poprzednich przykładach możemy określić czas rozpoczęcia (niejawnie lub jawnie), czas wygaśnięcia i uprawnienia na sygnatury dostępu współdzielonego URI sam. W poniższych przykładach możemy określić zasady dostępu przechowywane, a nie na sygnatury dostępu współdzielonego. W ten sposób umożliwia firmie Microsoft w celu zmiany tych warunków ograniczających bez ponownego wystawienia sygnatury dostępu współdzielonego.

Istnieje możliwość występuje jeden lub więcej ograniczenia na sygnatury dostępu współdzielonego i pozostałej w zasadach dostępu przechowywane. Jednak tylko użytkownika czas rozpoczęcia, czas wygaśnięcia i uprawnień w jednym miejscu lub w drugim. Na przykład nie można określić uprawnienia na sygnatury dostępu współdzielonego i określ je w zasadach dostępu przechowywane.

Po dodaniu zasad przechowywanych dostępu do kontenera musi uzyskać uprawnienia istniejącego kontenera, Dodaj nowe zasady dostępu, a następnie ustaw uprawnienia do kontenera.

Dodaj nową metodę tworzy nowe zasady dostępu do przechowywanych w kontenerze, który zwraca nazwę zasady:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

W dolnej części **Main()** przed wywołaniem metody **Console.ReadLine()** Dodaj następujące wiersze do pierwszego wyczyść wszystkie istniejące zasady dostępu, a następnie wywołać  **CreateSharedAccessPolicy()** metody:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Po wyczyszczeniu zasady dostępu do kontenera, należy najpierw pobrać kontenera istniejące uprawnienia, a następnie wyczyść uprawnienia, a następnie ponownie ustawić uprawnienia.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generowanie sygnatury dostępu współdzielonego identyfikatora URI w kontenerze, który korzysta z zasad dostępu
Następnie należy utworzyć inny sygnatury dostępu współdzielonego dla kontenera, w którym utworzono wcześniej, ale tym razem powiązane z zasadami dostępu przechowywane utworzone w poprzednim przykładzie podpisu.

Dodawanie nowej metody, aby wygenerować inny sygnatury dostępu współdzielonego dla kontenera:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

W dolnej części **Main()** przed wywołaniem metody **Console.ReadLine()**, Dodaj następujące wiersze do wywołania **GetContainerSasUriWithPolicy** metody:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generowanie sygnatury dostępu współdzielonego identyfikatora URI dla obiektu Blob, który używa zasad dostępu
Na koniec należy dodać podobnej metody do tworzenia innego obiektu blob i Generowanie sygnatury dostępu współdzielonego, skojarzone z zasadami dostępu przechowywane.

Dodaj nową metodę do tworzenia obiektu blob i Generowanie sygnatury dostępu współdzielonego:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

W dolnej części **Main()** przed wywołaniem metody **Console.ReadLine()**, Dodaj następujące wiersze do wywołania **GetBlobSasUriWithPolicy** metody:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

**Main()** metoda powinna wyglądać tak jak to w całości. Uruchom go, aby zapisać sygnatury dostępu współdzielonego identyfikatorów URI w oknie konsoli, a następnie skopiuj i wklej je do pliku tekstowego do użycia w drugiej części tego samouczka.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Po uruchomieniu aplikacji konsoli GenerateSharedAccessSignatures, zostanie wyświetlone dane wyjściowe podobne do następującego. Są to używanych w część 2 samouczka sygnatury dostępu współdzielonego.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Część 2: Tworzenie aplikacji konsoli, aby przetestować sygnatur dostępu współdzielonego
Aby przetestować sygnatur dostępu współdzielonego utworzone w poprzednich przykładach, utworzymy drugi aplikacja konsolowa, która korzysta z sygnatur do wykonywania operacji na kontenera i obiektu blob.

> [!NOTE]
> Jeśli od ukończone pierwszej części samouczka minęło ponad 24 godzin, podpisy wygenerowanego nie będzie już prawidłowy. W takim przypadku należy uruchomić kod w pierwszym aplikację konsoli w celu wygenerowania sygnatur dostępu współdzielonego świeże do użycia w drugiej części samouczka.
>

W programie Visual Studio Utwórz nową aplikację konsoli systemu Windows i nadaj mu nazwę **ConsumeSharedAccessSignatures**. Dodaj odwołania do [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) i [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), tak jak poprzednio.

Na początku pliku Program.cs Dodaj następujące **przy użyciu** dyrektywy:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

W treści **Main()** metody, Dodaj następujące ograniczenia ciągu, zmiana wartości do sygnatur dostępu współdzielonego wygenerowanych w część 1 samouczka.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Dodaj metodę próby operacji kontenera za pomocą sygnatury dostępu współdzielonego
Następnie dodaj metodę, która sprawdza niektóre operacje kontenera przy użyciu sygnatury dostępu współdzielonego dla kontenera. Sygnatury dostępu współdzielonego służy do zwracania odwołanie do kontenera uwierzytelniania dostępu do kontenera w oparciu jedynie podpisu.

Dodaj następującą metodę do pliku Program.cs:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Aktualizacja **Main()** metodę do wywołania **UseContainerSAS()** zarówno utworzony w kontenerze sygnatur dostępu współdzielonego:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Dodaj metodę, aby spróbować operacje obiektów blob przy użyciu sygnatury dostępu współdzielonego
Na koniec należy dodać metody, która sprawdza operacje niektórych obiektów blob przy użyciu sygnatury dostępu współdzielonego w obiekcie blob. W takim przypadku stosujemy konstruktora **CloudBlockBlob(String)**, przekazując sygnatury dostępu współdzielonego, zwraca odwołanie do obiektu blob. Żadne inne uwierzytelnianie nie jest wymagane; jest on oparty na samych podpisu.

Dodaj następującą metodę do pliku Program.cs:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Aktualizacja **Main()** metodę do wywołania **UseBlobSAS()** zarówno sygnatur dostępu współdzielonego, które zostały utworzone w obiekcie blob:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Uruchom aplikację konsoli i sprawdź dane wyjściowe, aby zobaczyć, jakie operacje są dozwolone w przypadku których podpisów. Dane wyjściowe w oknie konsoli będzie wyglądać podobnie do poniższej:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Następne kroki

* [Część 1 sygnatur dostępu współdzielonego: Opis modelu sygnatur dostępu Współdzielonego](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md)
* [Delegowanie dostępu za pomocą podpisu dostępu współdzielonego (interfejsu API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Wprowadzenie do tabeli i kolejki SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
