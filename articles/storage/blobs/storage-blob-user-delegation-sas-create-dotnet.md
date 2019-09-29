---
title: Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB z platformą .NET (wersja zapoznawcza) — Azure Storage
description: Informacje na temat tworzenia sygnatury dostępu współdzielonego użytkownika przy użyciu poświadczeń Azure Active Directory w usłudze Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671325"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu platformy .NET (wersja zapoznawcza)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Zainstaluj pakiety wersji zapoznawczej

W przykładach w tym artykule użyto najnowszej wersji zapoznawczej biblioteki klienta usługi Azure Storage dla magazynu obiektów BLOB. Aby zainstalować pakiet wersji zapoznawczej, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

W przykładach w tym artykule użyto również najnowszej wersji zapoznawczej [biblioteki klienta usługi Azure Identity dla platformy .NET](https://www.nuget.org/packages/Azure.Identity/) do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Biblioteka klienta tożsamości platformy Azure uwierzytelnia podmiot zabezpieczeń. Uwierzytelniony podmiot zabezpieczeń może następnie utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika. Aby uzyskać więcej informacji na temat biblioteki klienta tożsamości platformy Azure, zobacz [Biblioteka klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby uwierzytelnić się przy użyciu poświadczeń usługi Azure AD za pomocą biblioteki klienta tożsamości platformy Azure, użyj nazwy głównej usługi lub tożsamości zarządzanej jako podmiot zabezpieczeń, w zależności od tego, gdzie działa kod. Jeśli Twój kod jest uruchomiony w środowisku programistycznym, użyj jednostki usługi do celów testowych. Jeśli Twój kod jest uruchomiony na platformie Azure, Użyj tożsamości zarządzanej. W tym artykule przyjęto założenie, że uruchomiono kod ze środowiska programistycznego i pokazano, jak używać nazwy głównej usługi do tworzenia sygnatury dostępu współdzielonego delegowania użytkownika.

Aby utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure i przypisać rolę RBAC, wywołaj polecenie [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Podaj rolę dostępu do danych usługi Azure Storage, która ma zostać przypisana do nowej nazwy głównej usługi. Rola musi zawierać akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Aby uzyskać więcej informacji na temat ról wbudowanych dla usługi Azure Storage, zobacz [wbudowane role dla zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

Ponadto Podaj zakres przypisania roli. Jednostka usługi utworzy klucz delegowania użytkownika, który jest operacją wykonywaną na poziomie konta magazynu, więc przypisanie roli powinno być ograniczone do zakresu konta magazynu, grupy zasobów lub subskrypcji. Aby uzyskać więcej informacji na temat uprawnień RBAC do tworzenia sygnatury dostępu współdzielonego delegowania użytkowników, zobacz sekcję **przypisywanie uprawnień z RBAC** w temacie [Tworzenie funkcji SAS delegowania użytkowników (REST)](/rest/api/storageservices/create-user-delegation-sas).

Jeśli nie masz wystarczających uprawnień do przypisania roli do jednostki usługi, może być konieczne poproszenie właściciela konta lub administratora o wykonanie przypisania roli.

Poniższy przykład używa interfejsu wiersza polecenia platformy Azure, aby utworzyć nową nazwę główną usługi i przypisać do niej rolę **czytnika danych obiektów blob magazynu** z zakresem konta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Polecenie `az ad sp create-for-rbac` zwraca listę właściwości nazwy głównej usługi w formacie JSON. Skopiuj te wartości, aby można było użyć ich do utworzenia niezbędnych zmiennych środowiskowych w następnym kroku.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Propagowanie przypisań ról RBAC może potrwać kilka minut.

## <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Biblioteka klienta tożsamości platformy Azure odczytuje wartości z trzech zmiennych środowiskowych w czasie wykonywania w celu uwierzytelnienia nazwy głównej usługi. W poniższej tabeli opisano wartość ustawioną dla każdej zmiennej środowiskowej.

|Zmienna środowiskowa|Value
|-|-
|`AZURE_CLIENT_ID`|Identyfikator aplikacji dla jednostki usługi
|`AZURE_TENANT_ID`|Identyfikator dzierżawy usługi Azure AD w jednostce nazwy
|`AZURE_CLIENT_SECRET`|Hasło wygenerowane dla jednostki usługi

> [!IMPORTANT]
> Po ustawieniu zmiennych środowiskowych Zamknij i ponownie otwórz okno konsoli. Jeśli używasz programu Visual Studio lub innego środowiska programistycznego, może być konieczne ponowne uruchomienie środowiska programistycznego w celu zarejestrowania nowych zmiennych środowiskowych.

## <a name="add-using-directives"></a>Dodawanie dyrektyw using

Dodaj następujące dyrektywy `using` do kodu, aby korzystać z wersji zapoznawczej tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>Uwierzytelnianie jednostki usługi

Aby uwierzytelnić jednostkę usługi, Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Konstruktor `DefaultAzureCredential` odczytuje wcześniej utworzone zmienne środowiskowe.

Poniższy fragment kodu przedstawia sposób pobierania uwierzytelnionego poświadczenia i używania go do tworzenia klienta usługi dla magazynu obiektów BLOB

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Pobierz klucz delegowania użytkownika

Każde sygnatura dostępu współdzielonego jest podpisana przy użyciu klucza. Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkowników, musisz najpierw zażądać klucza delegowania użytkownika, który jest następnie używany do podpisywania sygnatury dostępu współdzielonego. Klucz delegowania użytkownika jest analogiczny do klucza konta używanego do podpisywania sygnatury dostępu współdzielonego usługi lub konta SAS, z tą różnicą, że używa poświadczeń usługi Azure AD. Gdy klient zażąda klucza delegowania użytkownika przy użyciu tokenu OAuth 2,0, usługa Azure Storage zwraca klucz delegowania użytkownika w imieniu użytkownika.

Po uzyskaniu klucza delegowania użytkownika można użyć tego klucza do utworzenia dowolnej liczby sygnatur dostępu współdzielonego delegowania użytkowników w okresie istnienia klucza. Klucz delegowania użytkownika jest niezależny od tokenu OAuth 2,0 użytego do jego uzyskania, więc token nie musi być odnawiany, dopóki klucz jest nadal ważny. Możesz określić, że klucz jest ważny przez okres do 7 dni.

Użyj jednej z następujących metod, aby zażądać klucza delegowania użytkownika:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Poniższy fragment kodu Pobiera klucz delegowania użytkownika i zapisuje jego właściwości:

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonego

Poniższy fragment kodu przedstawia tworzenie nowego [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) i podanie parametrów dla delegowania dla delegatów użytkowników. Fragment kodu wywoła następnie [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) , aby uzyskać ciąg tokenu sygnatury dostępu współdzielonego. Na koniec kod kompiluje pełny identyfikator URI, w tym adres zasobu i token sygnatury dostępu współdzielonego.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Przykład: Uzyskiwanie sygnatury dostępu współdzielonego użytkownika

Poniższa przykładowa Metoda przedstawia pełen kod uwierzytelniania podmiotu zabezpieczeń i tworzenia sygnatury dostępu współdzielonego delegowania użytkownika:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Przykład: Odczytywanie obiektu BLOB przy użyciu delegowania SKOJARZEŃ użytkowników

Poniższy przykład sprawdza, czy w poprzednim przykładzie utworzono sygnaturę dostępu współdzielonego delegowania użytkownika z symulowanej aplikacji klienckiej. Jeśli sygnatura dostępu współdzielonego jest prawidłowa, aplikacja kliencka może odczytać zawartość obiektu BLOB. Jeśli sygnatura dostępu współdzielonego jest nieprawidłowa, na przykład jeśli wygasła, usługa Azure Storage zwraca kod błędu 403 (dostęp zabroniony).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz także

- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)
