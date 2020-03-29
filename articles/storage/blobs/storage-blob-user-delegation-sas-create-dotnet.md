---
title: Użyj platformy .NET do utworzenia sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników przy użyciu poświadczeń usługi Azure Active Directory przy użyciu biblioteki klienta platformy .NET dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371840"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą platformy .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob z biblioteką klienta usługi Azure Storage dla platformy .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Przypisywanie ról RBAC w celu uzyskania dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektów blob, ten podmiot zabezpieczeń musi mieć uprawnienia do zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy kontem użytkownika usługi Azure AD z kodem w środowisku deweloperskim, podmiot zabezpieczeń musi mieć przypisaną rolę RBAC, która udziela dostępu do danych obiektów blob w usłudze Azure Storage. Aby uzyskać informacje dotyczące przypisywania uprawnień za pośrednictwem funkcji RBAC, zobacz sekcję **"Przypisywanie ról RBAC dla praw dostępu** w [obszarze Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Aby dowiedzieć się więcej o tym, jak uwierzytelniać się przy użyciu biblioteki klienta usługi Azure Identity z usługi Azure Storage, zobacz sekcję **"Uwierzytelnij za pomocą biblioteki tożsamości platformy Azure** w [obszarze Autoryzowanie dostępu do obiektów blob i kolejek za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="add-using-directives"></a>Dodawanie dyrektyw using

Dodaj następujące `using` dyrektywy do kodu, aby użyć bibliotek klienta usługi Azure Identity i Usługi Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Uzyskaj uwierzytelnione poświadczenia tokenu

Aby uzyskać poświadczenia tokenu, których kod może używać do autoryzowania żądań do usługi Azure Storage, utwórz wystąpienie klasy [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

Poniższy fragment kodu pokazuje, jak uzyskać uwierzytelnione poświadczenia tokenu i użyć go do utworzenia klienta usługi dla magazynu obiektów Blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Pobierz klucz delegowania użytkowników

Każdy sas jest podpisany za pomocą klucza. Aby utworzyć sygnaturę dostępu Współdzielonego delegowania użytkowników, należy najpierw zażądać klucza delegowania użytkowników, który jest następnie używany do podpisywania sygnatury dostępu Współdzielonego. Klucz delegowania użytkowników jest analogiczna do klucza konta używanego do podpisywania sygnatury dostępu Współdzielonego usługi lub sygnatury dostępu Współdzielonego konta, z tą różnicą, że opiera się na poświadczeniach usługi Azure AD. Gdy klient żąda klucza delegowania użytkownika przy użyciu tokenu OAuth 2.0, usługa Azure Storage zwraca klucz delegowania użytkownika w imieniu użytkownika.

Po uzyskaniu klucza delegowania użytkownika można użyć tego klucza do utworzenia dowolnej liczby podpisów dostępu współdzielonego delegowania użytkowników przez cały okres istnienia klucza. Klucz delegowania użytkownika jest niezależny od tokenu OAuth 2.0 używanego do jego uzyskania, więc token nie musi być odnawiany, o ile klucz jest nadal ważny. Można określić, że klucz jest ważny przez okres do 7 dni.

Użyj jednej z następujących metod, aby zażądać klucza delegowania użytkownika:

- [Klucz GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Poniższy fragment kodu pobiera klucz delegowania użytkownika i zapisuje jego właściwości:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Tworzenie tokenu sygnatury dostępu Współdzielonego

Poniższy fragment kodu pokazuje utworzyć nowy [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) i podać parametry dla sygnatury dostępu Współdzielonego delegowania użytkownika. Fragment kodu następnie wywołuje [ToSasQueryParameters,](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) aby uzyskać ciąg tokenu sygnatury dostępu Współdzielonego. Na koniec kod tworzy pełny identyfikator URI, w tym adres zasobu i token sygnatury dostępu Współdzielonego.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

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
```

## <a name="example-get-a-user-delegation-sas"></a>Przykład: Pobierz sygnaturę dostępu Współdzielonego delegowania użytkowników

Poniższa przykładowa metoda pokazuje pełny kod do uwierzytelniania podmiotu zabezpieczeń i tworzenia sygnatury dostępu Współdzielonego delegowania użytkowników:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

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
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Przykład: Odczyt obiektu blob z sygnaturą dostępu Współdzielonego delegowania użytkowników

Poniższy przykład testuje sygnatury dostępu Współdzielonego delegowania użytkownika utworzone w poprzednim przykładzie z symulowanej aplikacji klienckiej. Jeśli sygnatury dostępu Współdzielonego jest prawidłowy, aplikacja kliencka jest w stanie odczytać zawartość obiektu blob. Jeśli sygnatury dostępu Współdzielonego jest nieprawidłowy, na przykład jeśli wygasł, usługa Azure Storage zwraca kod błędu 403 (zabronione).

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
    catch (RequestFailedException e)
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

## <a name="see-also"></a>Zobacz też

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md)
- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników (INTERFEJS API REST)](/rest/api/storageservices/create-user-delegation-sas)
