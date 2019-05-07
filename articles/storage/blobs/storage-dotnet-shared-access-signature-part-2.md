---
title: Tworzenie i używanie sygnatury dostępu współdzielonego (SAS) za pomocą usługi Azure Blob storage | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć sygnatury dostępu współdzielonego do użycia z usługą Blob storage i jak ich używać w aplikacjach klienckich.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.devlang: dotnet
ms.date: 05/15/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: de035517383f066ed847f06b7bc4ff650ec496f3
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148382"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Udostępnione sygnatur dostępu, część 2: Tworzenie i używanie sygnatury dostępu Współdzielonego z usługą Blob storage

[Część 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) po ukończeniu tego samouczka zbadano udostępnione access signatures (SAS) i opisano najlepsze rozwiązania dotyczące korzystania z nich. Części 2 dowiesz się, jak wygenerować, a następnie użyj sygnatury dostępu współdzielonego z usługą Blob storage. Przykłady są napisane w języku C# i używać biblioteki klienta usługi Azure Storage dla platformy .NET. Przykłady w tym samouczku:

* Generowanie sygnatury dostępu współdzielonego do kontenera
* Generowanie sygnatury dostępu współdzielonego dla obiektu blob
* Utwórz przechowywanych zasad dostępu do zarządzania podpisów zasoby kontenera
* Testowanie sygnatur dostępu współdzielonego w aplikacji klienckiej

## <a name="about-this-tutorial"></a>Informacje o tym samouczku
W tym samouczku utworzymy dwie aplikacje konsolowe, które pokazują, tworzenia i używania sygnatur dostępu współdzielonego dla kontenerów i obiektów blob:

**Aplikacja 1**: Aplikacji do zarządzania. Generuje sygnaturę dostępu współdzielonego dla kontenera i obiektu blob. Zawiera klucz dostępu konta magazynu w kodzie źródłowym.

**Aplikacja 2**: Aplikacja kliencka. Uzyskuje dostęp do kontenerów i obiektów blob zasobów przy użyciu sygnatury dostępu współdzielonego utworzony przy użyciu pierwszej aplikacji. Używa tylko sygnatur dostępu współdzielonego kontenera dostępu i zasobów obiektów blob — robi *nie* obejmują klucz dostępu konta magazynu.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Część 1: Tworzenie aplikacji konsoli aby wygenerować sygnatury dostępu współdzielonego
Najpierw upewnij się, że biblioteki klienta usługi Azure Storage dla programu .NET zainstalowane. Możesz zainstalować [pakietu NuGet](https://nuget.org/packages/WindowsAzure.Storage/ "pakietu NuGet") zawierającego najnowsze zestawy dla biblioteki klienta. Jest to zalecana metoda za zapewnienie, że masz najnowsze poprawki. Możesz również pobrać jako część najnowszej wersji biblioteki klienckiej [zestawu Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/).

W programie Visual Studio Utwórz nową aplikację konsoli Windows i nadaj mu nazwę **GenerateSharedAccessSignatures**. Dodaj odwołania do [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) i [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) przy użyciu jednej z następujących metod:

* Użyj [Menedżera pakietów NuGet](https://docs.nuget.org/consume/installing-nuget) w programie Visual Studio. Wybierz **projektu** > **Zarządzaj pakietami NuGet**, Wyszukaj w trybie online dla każdego pakietu (Microsoft.WindowsAzure.ConfigurationManager i biblioteki WindowsAzure.Storage) i zainstaluj je.
* Alternatywnie te zestawy należy umieszczać w instalacji zestawu SDK platformy Azure i dodać odwołania do nich:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

W górnej części pliku Program.cs Dodaj następujące **przy użyciu** dyrektywy:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Przeprowadź edycję pliku app.config, tak, aby zawierał ustawienia konfiguracji przy użyciu parametrów połączenia, który wskazuje na koncie magazynu. Pliku app.config powinno wyglądać podobnie do następującego:

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
Najpierw dodamy metodę, aby wygenerować sygnaturę dostępu współdzielonego na nowy kontener. W tym przypadku podpis nie jest skojarzony z przechowywanych zasad dostępu, więc jego niesie ze sobą w identyfikatorze URI informacje wskazujące jej czas wygaśnięcia i uprawnienia, które on przyznanie.

Najpierw dodaj kod, aby **Main()** metodą autoryzacji dostępu do konta magazynu i utworzyć nowy kontener:

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

Następnie dodaj metodę, która generuje sygnaturę dostępu współdzielonego dla kontenera i zwraca wartość identyfikatora URI sygnatury:

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

Dodaj następujące wiersze na końcu **Main()** metoda przed wywołaniem do **Console.ReadLine()**, aby wywołać **GetContainerSasUri()** i zapisywanie identyfikatora URI sygnatury do okno konsoli:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Skompiluj i uruchom, aby dane wyjściowe identyfikatora URI sygnatury dostępu współdzielonego dla nowego kontenera. Identyfikator URI będzie podobny do następującego:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Po uruchomieniu kodu sygnatura dostępu współdzielonego utworzony dla kontenera będzie obowiązywać przez następne 24 godziny. Podpis przyznaje uprawnienia klienta, aby wyświetlanie listy obiektów blob w kontenerze i zapisać nowe obiekty BLOB do kontenera.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generuj identyfikator URI sygnatury dostępu współdzielonego dla obiektu blob
Następnie napiszemy podobny kod, aby utworzyć nowy obiekt blob w kontenerze i wygenerować sygnaturę dostępu współdzielonego. Ta sygnatura dostępu współdzielonego nie jest skojarzony z przechowywanych zasad dostępu, więc zawiera godzinę rozpoczęcia, czas wygaśnięcia i informacji o uprawnieniach w identyfikatorze URI.

Dodaj nową metodę, która tworzy nowy obiekt blob i zapisuje tekst, a następnie generuje sygnaturę dostępu współdzielonego i zwraca wartość identyfikatora URI sygnatury:

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

Skompiluj i uruchom, aby dane wyjściowe identyfikatora URI sygnatury dostępu współdzielonego dla nowego obiektu blob. Identyfikator URI będzie podobny do następującego:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Utwórz zasady dostępu przechowywane w kontenerze
Teraz Utwórzmy przechowywanych zasad dostępu w kontenerze, który określa ograniczenia dla żadnych sygnatur dostępu współdzielonego, które są skojarzone z nim.

W poprzednich przykładach określonej czas rozpoczęcia (jawnie lub niejawnie), czas wygaśnięcia i uprawnienia na sygnatury dostępu współdzielonego identyfikatora URI, sam. W poniższych przykładach możemy określić zasady dostępu przechowywane, a nie na sygnatury dostępu współdzielonego. Ten krok umożliwia nam się zmiany tych warunków ograniczających bez ponownego wystawienia sygnatury dostępu współdzielonego.

Użytkownik może mieć jedną lub więcej ograniczenia na sygnatury dostępu współdzielonego i resztę na przechowywanych zasad dostępu. Jednak należy tylko określić godzinę rozpoczęcia, czas wygaśnięcia i uprawnienia w jednym miejscu lub w drugim. Na przykład nie można określić uprawnienia na sygnatury dostępu współdzielonego i również określić je na przechowywanych zasad dostępu.

Po dodaniu przechowywanych zasad dostępu do kontenera musi uzyskać uprawnienia istniejącego kontenera, dodawanie nowych zasad dostępu, a następnie ustaw uprawnienia do kontenera.

Dodaj nową metodę, która tworzy nowe zasady dostępu przechowywane w kontenerze i zwraca nazwę zasady:

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

W dolnej części **Main()** metoda przed wywołaniem do **Console.ReadLine()**, Dodaj następujące wierszy do pierwszego wyczyść wszystkich istniejących zasad dostępu, a następnie wywołaj  **CreateSharedAccessPolicy()** metody:

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

Po usunięciu zaznaczenia z zasadami dostępu do kontenera, należy najpierw pobrać istniejących uprawnień kontenera, a następnie wyczyść uprawnienia, a następnie ponownie ustawić uprawnienia.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generowanie sygnatury dostępu współdzielonego identyfikator URI kontenera, która używa zasad dostępu
Następnie należy utworzyć inny sygnatury dostępu współdzielonego dla kontenera, utworzony wcześniej, ale tym razem powiązane z przechowywanych zasad dostępu utworzony w poprzednim przykładzie podpis.

Dodaj nową metodę, aby wygenerować inny podpis dostępu współdzielonego dla kontenera:

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

W dolnej części **Main()** metoda przed wywołaniem do **Console.ReadLine()**, Dodaj następujące wiersze do wywołania **GetContainerSasUriWithPolicy** metody:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generowanie sygnatury dostępu współdzielonego identyfikatora URI obiektu blob, która używa zasad dostępu
Na koniec Dodaj podobnej metody, aby utworzyć inny obiekt blob i wygeneruj sygnaturę dostępu współdzielonego, która jest skojarzona z przechowywanych zasad dostępu.

Dodaj nową metodę, aby utworzyć obiekt blob i wygenerować sygnaturę dostępu współdzielonego:

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

W dolnej części **Main()** metoda przed wywołaniem do **Console.ReadLine()**, Dodaj następujące wiersze do wywołania **GetBlobSasUriWithPolicy** metody:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

**Main()** metoda powinna teraz wyglądać następująco w całości. Uruchom go, aby zapisać sygnatury dostępu współdzielonego identyfikatory URI w oknie konsoli, a następnie skopiuj i wklej je do pliku tekstowego do użycia w drugiej części tego samouczka.

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

Po uruchomieniu aplikacji konsoli GenerateSharedAccessSignatures, zobaczysz dane wyjściowe podobne do następujących. Są to sygnatur dostępu współdzielonego, którego używasz w części 2 samouczka.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Część 2: Tworzenie aplikacji konsoli w celu przetestowania sygnatur dostępu współdzielonego
Aby przetestować sygnatur dostępu współdzielonego utworzony w poprzednich przykładach, możemy utworzyć drugi aplikację konsolową, która korzysta z sygnatur do wykonywania operacji na kontenerze, a na obiekt blob.

> [!NOTE]
> Jeśli od należy wykonać czynności opisane w pierwszej części samouczka minęło ponad 24-godzinny, sygnatur, który został wygenerowany nie będzie już prawidłowy. W takim przypadku należy uruchomić kod w pierwszą aplikację konsolową do generowania podpisów świeże dostępu współdzielonego do użytku w drugiej części tego samouczka.
>

W programie Visual Studio Utwórz nową aplikację konsoli Windows i nadaj mu nazwę **ConsumeSharedAccessSignatures**. Dodaj odwołania do [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) i [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), tak jak wcześniej.

W górnej części pliku Program.cs Dodaj następujące **przy użyciu** dyrektywy:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

W treści **Main()** metody, Dodaj następujące stałe ciągu, zmieniając ich wartości do sygnatur dostępu współdzielonego, generowane w części 1 tego samouczka.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Dodaj metodę, aby wypróbować operacji kontenera przy użyciu sygnatury dostępu współdzielonego
Następnie dodaj metodę, która sprawdza niektóre operacje kontenera przy użyciu sygnatury dostępu współdzielonego dla kontenera. Sygnatura dostępu współdzielonego jest używany do zwraca odwołanie do kontenera, uwierzytelnianie dostępu do kontenera, w zależności od samego podpisu.

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

Aktualizacja **Main()** metodę do wywołania **UseContainerSAS()** zarówno sygnatur dostępu współdzielonego, który został utworzony na kontenerze:

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

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Dodaj metodę, aby spróbować obiekty blob — operacje przy użyciu sygnatury dostępu współdzielonego
Na koniec Dodaj metodę, która sprawdza niektóre obiekty blob — operacje przy użyciu sygnatury dostępu współdzielonego w obiekcie blob. W tym przypadku używamy konstruktora **CloudBlockBlob(String)**, przekazując sygnaturę dostępu współdzielonego, zwraca odwołanie do obiektu blob. Uwierzytelnianie nie jest wymagana. jest ona oparta na sygnaturze samodzielnie.

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

Aktualizacja **Main()** metodę do wywołania **UseBlobSAS()** zarówno sygnatur dostępu współdzielonego, które utworzono w obiekcie blob:

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

Uruchom aplikację konsolową i sprawdzanie danych wyjściowych, aby zobaczyć, jakie operacje są dozwolone w przypadku których podpisów. Dane wyjściowe w oknie konsoli będzie wyglądać podobnie do następującego:

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

* [Udostępnione sygnatur dostępu, część 1: Opis modelu sygnatur dostępu Współdzielonego](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Zarządzanie dostępem anonimowym odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md)
* [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego (interfejs API REST)](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Wprowadzenie do tabel i token SAS kolejki](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
