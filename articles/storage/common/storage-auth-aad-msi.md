---
title: Autoryzowanie dostępu do danych przy obliczu tożsamości zarządzanej
titleSuffix: Azure Storage
description: Dowiedz się, jak używać tożsamości zarządzanych dla zasobów platformy Azure do autoryzowania dostępu do danych obiektów blob i kolejek z aplikacji uruchomionych na maszynach wirtualnych platformy Azure, aplikacjach funkcji, zestawach skalowania maszyn wirtualnych i innych.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255342"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autoryzowanie dostępu do danych obiektów blob i kolejek z zarządzanymi tożsamościami zasobów platformy Azure

Usługa Azure Blob and Queue storage obsługuje uwierzytelnianie usługi Azure Active Directory (Azure AD) z [zarządzanymi tożsamościami zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Tożsamości zarządzane dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych na maszynach wirtualnych platformy Azure (maszyny wirtualne), aplikacji funkcji, zestawów skalowania maszyny wirtualnej i innych usług. Korzystając z zarządzanych tożsamości dla zasobów platformy Azure wraz z uwierzytelnianiem usługi Azure AD, można uniknąć przechowywania poświadczeń w aplikacjach uruchamianych w chmurze.  

W tym artykule pokazano, jak autoryzować dostęp do danych obiektów blob lub kolejek z maszyny Wirtualnej platformy Azure przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Opisano również, jak przetestować kod w środowisku programistycznym.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie Wirtualnej

Aby można było używać tożsamości zarządzanych dla zasobów platformy Azure do autoryzowania dostępu do obiektów blob i kolejek z maszyny Wirtualnej, należy najpierw włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie Wirtualnej. Aby dowiedzieć się, jak włączyć tożsamości zarządzane dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Portal Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Biblioteki klientów usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Uwierzytelnij się za pomocą biblioteki tożsamości platformy Azure

Biblioteka klienta usługi Azure Identity zapewnia obsługę uwierzytelniania tokenów usługi Azure AD dla [sdk azure.](https://github.com/Azure/azure-sdk) Najnowsze wersje bibliotek klienta usługi Azure Storage dla platformy .NET, Java, Python i JavaScript integrują się z biblioteką tożsamości platformy Azure, aby zapewnić prosty i bezpieczny sposób uzyskiwania tokenu OAuth 2.0 do autoryzacji żądań usługi Azure Storage.

Zaletą biblioteki klienta usługi Azure Identity jest to, że umożliwia użycie tego samego kodu do uwierzytelniania, czy aplikacja jest uruchomiona w środowisku deweloperskim lub na platformie Azure. Biblioteka klienta usługi Azure Identity dla platformy .NET uwierzytelnia podmiot zabezpieczeń. Gdy kod jest uruchomiony na platformie Azure, podmiot zabezpieczeń jest tożsamością zarządzaną dla zasobów platformy Azure. W środowisku deweloperskim tożsamość zarządzana nie istnieje, więc biblioteka klienta uwierzytelnia użytkownika lub jednostkę usługi do celów testowych.

Po uwierzytelnieniu biblioteka klienta usługi Azure Identity pobiera poświadczenia tokenu. To poświadczenie tokenu jest następnie hermetyzowane w obiekcie klienta usługi, który można utworzyć w celu wykonania operacji względem usługi Azure Storage. Biblioteka obsługuje to bezproblemowo, uzyskując odpowiednie poświadczenia tokenu.

Aby uzyskać więcej informacji na temat biblioteki klienta usługi Azure Identity dla platformy .NET, zobacz [biblioteka klienta usługi Azure Identity dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Aby uzyskać dokumentację referencyjną dla biblioteki klienta usługi Azure Identity, zobacz [Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Przypisywanie ról kontroli dostępu opartej na rolach (RBAC) w celu uzyskania dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektów blob lub kolejki, ten podmiot zabezpieczeń musi mieć uprawnienia do zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy kontem użytkownika usługi Azure AD z kodem w środowisku deweloperskim, podmiot zabezpieczeń musi mieć przypisaną rolę RBAC, która udziela dostępu do danych obiektów blob lub kolejek w usłudze Azure Storage. Aby uzyskać informacje dotyczące przypisywania uprawnień za pośrednictwem funkcji RBAC, zobacz sekcję **"Przypisywanie ról RBAC dla praw dostępu** w [obszarze Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Uwierzytelnij użytkownika w środowisku programistycznym

Gdy kod jest uruchomiony w środowisku programistycznym, uwierzytelnianie może być obsługiwane automatycznie lub może wymagać logowania do przeglądarki, w zależności od używanych narzędzi. Na przykład program Microsoft Visual Studio obsługuje logowanie jednokrotne(SSO), dzięki czemu aktywne konto użytkownika usługi Azure AD jest automatycznie używane do uwierzytelniania. Aby uzyskać więcej informacji na temat logowania jednokrotnego, zobacz [Logowanie jednokrotne do aplikacji](../../active-directory/manage-apps/what-is-single-sign-on.md).

Inne narzędzia programistyczne mogą monitować o zalogowanie się za pośrednictwem przeglądarki internetowej.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Uwierzytelnij jednostkę usługi w środowisku deweloperskim

Jeśli środowisko programistyczne nie obsługuje logowania jednokrotnego lub logowania za pośrednictwem przeglądarki sieci web, można użyć jednostki usługi do uwierzytelniania ze środowiska programistycznego.

#### <a name="create-the-service-principal"></a>Tworzenie jednostki usługi

Aby utworzyć jednostkę usługi za pomocą interfejsu wiersza polecenia platformy Azure i przypisać rolę RBAC, należy wywołać polecenie [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Podaj rolę dostępu do danych usługi Azure Storage do przypisania do nowego podmiotu usługi. Ponadto podaj zakres przypisania roli. Aby uzyskać więcej informacji na temat wbudowanych ról przewidzianych dla usługi Azure Storage, zobacz [Wbudowane role zasobów platformy Azure.](../../role-based-access-control/built-in-roles.md)

Jeśli nie masz wystarczających uprawnień, aby przypisać rolę do jednostki usługi, może być konieczne zwrócenie się do właściciela konta lub administratora o wykonanie przypisania roli.

W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure do utworzenia nowego podmiotu usługi i przypisania do niej roli **czytnika danych obiektów blob magazynu** z zakresem konta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Polecenie `az ad sp create-for-rbac` zwraca listę właściwości głównej usługi w formacie JSON. Skopiuj te wartości, aby można było ich użyć do utworzenia niezbędnych zmiennych środowiskowych w następnym kroku.

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
> Propagowanie przydziałów ról RBAC może potrwać kilka minut.

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Biblioteka klienta usługi Azure Identity odczytuje wartości z trzech zmiennych środowiskowych w czasie wykonywania w celu uwierzytelnienia jednostki usługi. W poniższej tabeli opisano wartość ustawioną dla każdej zmiennej środowiskowej.

|Zmienna środowiskowa|Wartość
|-|-
|`AZURE_CLIENT_ID`|Identyfikator aplikacji dla jednostki usługi
|`AZURE_TENANT_ID`|Identyfikator dzierżawy usługi Azure AD jednostki usługi
|`AZURE_CLIENT_SECRET`|Hasło wygenerowane dla jednostki usługi

> [!IMPORTANT]
> Po ustawieniu zmiennych środowiskowych zamknij i ponownie otwórz okno konsoli. Jeśli używasz programu Visual Studio lub innego środowiska programistycznego, może być konieczne ponowne uruchomienie środowiska programistycznego w celu zarejestrowania nowych zmiennych środowiskowych.

Aby uzyskać więcej informacji, zobacz [Tworzenie tożsamości dla aplikacji platformy Azure w portalu](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu .NET: Tworzenie bloku blob

Dodaj następujące `using` dyrektywy do kodu, aby użyć bibliotek klienta usługi Azure Identity i Usługi Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Aby uzyskać poświadczenia tokenu, których kod może używać do autoryzowania żądań do usługi Azure Storage, utwórz wystąpienie klasy [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) Poniższy przykład kodu pokazuje, jak uzyskać uwierzytelnione poświadczenia tokenu i użyć go do utworzenia obiektu klienta usługi, a następnie użyć klienta usługi do przekazania nowego obiektu blob:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Aby autoryzować żądania względem danych obiektów blob lub kolejek za pomocą usługi Azure AD, należy użyć protokołu HTTPS dla tych żądań.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie prawami dostępu do danych magazynu za pomocą pliku RBAC](storage-auth-aad-rbac.md).
- [Użyj usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
- [Uruchamianie poleceń interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure lub programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub kolejek.](authorize-active-directory-powershell.md)
