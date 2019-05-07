---
title: Uwierzytelnianie dostępu do obiektów blob i kolejki z zarządzanych tożsamości dla zasobów platformy Azure — usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Magazynu obiektów Blob i kolejek platformy Azure obsługują uwierzytelnianie usługi Azure Active Directory za pomocą tożsamości zarządzanych zasobów platformy Azure. Zarządzanych tożsamości dla zasobów platformy Azure służy do uwierzytelniania dostępu do obiektów blob i kolejki z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: daa3bd49fcb9970a4a4a026f764cc195423e83d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154363"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Uwierzytelnianie dostępu do obiektów blob i kolejek usługi Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure

Usługa Azure storage Blob i kolejki obsługuje uwierzytelnianie usługi Azure Active Directory (Azure AD) przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure można autoryzować dostępu do obiektów blob i kolejki danych za pomocą poświadczeń usługi Azure AD z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure (maszyny wirtualne), aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych usług. Korzystając z zarządzanych tożsamości dla zasobów platformy Azure wraz z uwierzytelniania usługi Azure AD, można uniknąć przechowywania poświadczeń za pomocą aplikacji działających w chmurze.  

W tym artykule przedstawiono sposób autoryzacji dostępu do danych obiektów blob lub kolejki za pomocą tożsamości zarządzanej maszyny wirtualnej platformy Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Włącz zarządzanych tożsamości na maszynie Wirtualnej

Korzystać z zarządzanych tożsamości dla zasobów platformy Azure, do autoryzacji dostępu do obiektów blob i kolejki z maszyny Wirtualnej, należy najpierw włączyć zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej. Aby dowiedzieć się, jak włączyć zarządzanych tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Zestawy SDK platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Udzielanie uprawnień do tożsamości usługi Azure AD zarządzane

Aby autoryzować żądania do usługi blob Storage lub kolejki z zarządzanych tożsamości w aplikacji usługi Azure Storage, należy najpierw skonfigurować ustawienia kontroli (RBAC) dostępu opartej na rolach dla tej tożsamości zarządzanej. Usługa Azure Storage określa role RBAC, które obejmują uprawnienia do danych obiektów blob i kolejek. Gdy rola RBAC jest przypisane do tożsamości zarządzanej, tożsamość zarządzaną otrzymuje te uprawnienia do danych obiektów blob lub kolejki w odpowiednim rozwiązaniem. 

Aby uzyskać więcej informacji na temat przypisywania ról RBAC zobacz jeden z następujących artykułów:

- [Udzielanie dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu RBAC przy użyciu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [Udzielanie dostępu do danych platformy Azure obiektów blob i kolejek przy użyciu kontroli RBAC przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Autoryzuj przy użyciu tokenu dostępu tożsamości zarządzanej

Aby autoryzować żądania kierowane do magazynu obiektów Blob i kolejek przy użyciu tożsamości zarządzanej, aplikacji lub skryptu należy uzyskać OAuth token. [Uwierzytelniania platformy Microsoft Azure App](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) Biblioteka kliencka dla platformy .NET (wersja zapoznawcza) upraszcza proces pobierania i odnawiania tokenu w kodzie.

Biblioteka klienta uwierzytelniania aplikacji, które automatycznie zarządza uwierzytelnianiem. Biblioteki do uwierzytelniania podczas tworzenia lokalnego przy użyciu poświadczeń dla deweloperów. Podczas tworzenia lokalnego przy użyciu poświadczeń dewelopera jest bezpieczniejsza, ponieważ nie trzeba utworzyć poświadczenia usługi Azure AD lub udostępnić poświadczeń między deweloperów. Gdy rozwiązanie jest później wdrożone na platformie Azure, biblioteka przełącza się do przy użyciu poświadczeń aplikacji.

Za pomocą biblioteki uwierzytelniania aplikacji w aplikacji usługi Azure Storage, należy zainstalować najnowszy pakiet (wersja zapoznawcza) z [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), a także najnowszą wersję [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Dodaj następujący kod **przy użyciu** instrukcji w kodzie:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

Biblioteka uwierzytelniania aplikacji zapewnia **AzureServiceTokenProvider** klasy. Wystąpienie tej klasy może być przekazywany do wywołania zwrotnego, który pobiera token, a następnie odnawia token przed jego wygaśnięciem.

Poniższy przykład pobiera token i używa jej do utworzenia nowego obiektu blob, a następnie używa tego samego tokenu do odczytania obiektu blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function 
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token, 
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider, 
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), 
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Metoda wywołania zwrotnego sprawdza, czy czas wygaśnięcia tokenu i jest odnawiana go zgodnie z potrzebami:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

Aby uzyskać więcej informacji na temat biblioteki uwierzytelniania aplikacji, zobacz [Service-to-service authentication usługi Azure Key Vault przy użyciu platformy .NET](../../key-vault/service-to-service-authentication.md). 

Aby dowiedzieć się więcej o tym, jak można uzyskać tokenu dostępu, zobacz [jak uzyskiwanie tokenu dostępu, za pomocą tożsamości zarządzanych zasobów platformy Azure na Maszynie wirtualnej platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Aby autoryzować żądań na podstawie danych obiektów blob lub kolejki przy użyciu usługi Azure AD, musi używać protokołu HTTPS dla tych żądań.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC](storage-auth-aad-rbac.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z magazynu aplikacji](storage-auth-aad-app.md).
- Aby dowiedzieć się, jak uruchamiać polecenia wiersza polecenia platformy Azure i programu PowerShell przy użyciu poświadczeń usługi Azure AD, zobacz [polecenia uruchomienia wiersza polecenia platformy Azure lub programu PowerShell przy użyciu poświadczeń usługi Azure AD, aby uzyskiwać dostęp do danych obiektów blob i kolejki](storage-auth-aad-script.md).