---
title: Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB z platformą .NET (wersja zapoznawcza) — Azure Storage
description: Informacje na temat tworzenia sygnatury dostępu współdzielonego (wersja zapoznawcza) delegowania użytkowników przy użyciu poświadczeń Azure Active Directory w usłudze Azure Storage przy użyciu biblioteki klienta .NET
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 32bcb12f39d65d63af1c6595c0d57c695ce0533f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792219"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu platformy .NET (wersja zapoznawcza)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu współdzielonego delegowania użytkowników (wersja zapoznawcza) dla kontenera lub obiektu BLOB za pomocą biblioteki klienta usługi Azure Storage dla platformy .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library"></a>Uwierzytelnianie przy użyciu biblioteki tożsamości platformy Azure

Biblioteka klienta tożsamości platformy Azure dla platformy .NET uwierzytelnia podmiot zabezpieczeń. Gdy kod jest uruchomiony na platformie Azure, podmiot zabezpieczeń jest zarządzaną tożsamością dla zasobów platformy Azure.

Gdy kod jest uruchomiony w środowisku programistycznym, uwierzytelnianie może być obsługiwane automatycznie lub może wymagać logowania w przeglądarce, w zależności od tego, które narzędzia są używane. Microsoft Visual Studio obsługuje logowanie jednokrotne (SSO), dzięki czemu aktywne konto użytkownika usługi Azure AD jest automatycznie używane do uwierzytelniania. Aby uzyskać więcej informacji na temat rejestracji jednokrotnej, zobacz Logowanie jednokrotne [do aplikacji](../../active-directory/manage-apps/what-is-single-sign-on.md).

Inne narzędzia programistyczne mogą monitować o zalogowanie się za pośrednictwem przeglądarki sieci Web. Można również użyć jednostki usługi do uwierzytelniania ze środowiska deweloperskiego. Aby uzyskać więcej informacji, zobacz [Tworzenie tożsamości dla aplikacji platformy Azure w portalu](../../active-directory/develop/howto-create-service-principal-portal.md).

Po uwierzytelnieniu Biblioteka klienta tożsamości platformy Azure Pobiera poświadczenia tokenu. To poświadczenie tokenu jest następnie hermetyzowane w obiekcie klienta usługi tworzonym w celu wykonywania operacji w usłudze Azure Storage. Biblioteka obsługuje to bezproblemowo, pobierając odpowiednie poświadczenia tokenu.

Aby uzyskać więcej informacji na temat biblioteki klienta tożsamości platformy Azure, zobacz [Biblioteka klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Przypisywanie ról RBAC na potrzeby dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektów blob, musi mieć uprawnienia do tego zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy konto użytkownika usługi Azure AD z uruchomionym kodem w środowisku deweloperskim, podmiot zabezpieczeń musi mieć przypisaną rolę RBAC, która przyznaje dostęp do danych obiektów BLOB w usłudze Azure Storage. Informacje o przypisywaniu uprawnień za pośrednictwem RBAC zawiera sekcja zatytułowana **Przypisywanie ról RBAC dla praw dostępu** w artykule [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-packages"></a>Zainstaluj pakiety

W przykładach w tym artykule użyto najnowszej wersji [biblioteki klienta usługi Azure Storage dla magazynu obiektów BLOB](https://www.nuget.org/packages/Azure.Storage.Blobs). Aby zainstalować pakiet, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

W przykładach w tym artykule użyto również najnowszej wersji [biblioteki klienta tożsamości platformy Azure dla platformy .NET](https://www.nuget.org/packages/Azure.Identity/) do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Aby zainstalować pakiet, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Identity
```

## <a name="add-using-directives"></a>Dodawanie dyrektyw using

Dodaj do kodu następujące dyrektywy `using`, aby użyć tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

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

## <a name="get-an-authenticated-token-credential"></a>Pobieranie poświadczeń uwierzytelnionego tokenu

Aby uzyskać poświadczenia tokenu, których kod może użyć do autoryzacji żądań do usługi Azure Storage, Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

Poniższy fragment kodu przedstawia sposób pobierania poświadczeń tokenu uwierzytelnionego i używania go do tworzenia klienta usługi dla magazynu obiektów blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
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

## <a name="create-the-sas-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonego

Poniższy fragment kodu przedstawia tworzenie nowego [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) i podanie parametrów dla delegowania dla delegatów użytkowników. Fragment kodu wywoła następnie [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) , aby uzyskać ciąg tokenu sygnatury dostępu współdzielonego. Na koniec kod kompiluje pełny identyfikator URI, w tym adres zasobu i token sygnatury dostępu współdzielonego.

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

## <a name="example-get-a-user-delegation-sas"></a>Przykład: pobieranie sygnatury dostępu współdzielonego użytkownika

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Przykład: odczytywanie obiektu BLOB z delegowaniem przez użytkownika

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

## <a name="see-also"></a>Zobacz także

- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)
