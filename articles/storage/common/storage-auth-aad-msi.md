---
title: Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure — Azure Storage
description: Usługi Azure BLOB i queue storage obsługują autoryzowanie dostępu do zasobów za pomocą tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure. Za pomocą zarządzanych tożsamości dla zasobów platformy Azure można autoryzować dostęp do obiektów blob i kolejek z aplikacji uruchamianych na maszynach wirtualnych platformy Azure, aplikacjach funkcji, zestawach skalowania maszyn wirtualnych i innych.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0f1c66126a1aa9a6ebf6f78ac6fb1ba37ba41829
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985425"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure

Usługi Azure BLOB i queue storage obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawów skalowania maszyn wirtualnych i innych usług. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze.  

W tym artykule przedstawiono sposób autoryzacji dostępu do danych obiektu BLOB lub kolejki przy użyciu tożsamości zarządzanej z maszyny wirtualnej platformy Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej

Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania dostępu do obiektów blob i kolejek z poziomu maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Przyznawanie uprawnień do tożsamości zarządzanej usługi Azure AD

Aby autoryzować żądanie do obiektu BLOB lub usługa kolejki z tożsamości zarządzanej w aplikacji usługi Azure Storage, najpierw Skonfiguruj ustawienia kontroli dostępu opartej na rolach (RBAC) dla tej tożsamości zarządzanej. Usługa Azure Storage definiuje role RBAC, które obejmują uprawnienia do danych obiektów blob i kolejek. Gdy rola RBAC zostanie przypisana do tożsamości zarządzanej, zarządzana tożsamość otrzymuje te uprawnienia do danych obiektu BLOB lub kolejki w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz jeden z następujących artykułów:

- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach w witrynie Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach przy użyciu interfejsu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [ przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Identyfikator zasobu usługi Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu platformy .NET: Tworzenie blokowego obiektu BLOB

W przykładzie kodu pokazano, jak uzyskać token OAuth 2,0 z usługi Azure AD i użyć go do autoryzowania żądania utworzenia blokowego obiektu BLOB. Aby skorzystać z tego przykładu, należy najpierw wykonać czynności opisane w poprzednich sekcjach.

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>Dodaj metodę wywołania zwrotnego

Metoda wywołania zwrotnego sprawdza czas wygaśnięcia tokenu i odnawia go w razie konieczności:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
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

### <a name="get-a-token-and-create-a-block-blob"></a>Pobieranie tokenu i tworzenie blokowego obiektu BLOB

Biblioteka uwierzytelniania aplikacji udostępnia klasę **AzureServiceTokenProvider** . Wystąpienie tej klasy można przesłać do wywołania zwrotnego, które pobiera token, a następnie odnawia token przed jego wygaśnięciem.

Poniższy przykład pobiera token i używa go do utworzenia nowego obiektu BLOB, a następnie używa tego samego tokenu do odczytu obiektu BLOB.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

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
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Aby uzyskać więcej informacji na temat biblioteki uwierzytelniania aplikacji, zobacz [uwierzytelnianie między usługą w celu Azure Key Vault przy użyciu platformy .NET](../../key-vault/service-to-service-authentication.md).

Aby dowiedzieć się więcej na temat uzyskiwania tokenu dostępu, zobacz [jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Aby autoryzować żądania względem obiektów blob lub danych z kolejki za pomocą usługi Azure AD, musisz użyć protokołu HTTPS dla tych żądań.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure Storage, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą RBAC](storage-auth-aad-rbac.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejek z poziomu aplikacji magazynu, zobacz [Korzystanie z usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
- Aby dowiedzieć się, jak uruchomić interfejs wiersza polecenia platformy Azure i poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD, zobacz [Uruchamianie interfejsu wiersza polecenia platformy Azure lub poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów](storage-auth-aad-script.md)
