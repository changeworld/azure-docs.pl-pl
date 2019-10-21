---
title: Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure — Azure Storage
description: Usługi Azure BLOB i queue storage obsługują autoryzowanie dostępu do zasobów za pomocą tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure. Za pomocą zarządzanych tożsamości dla zasobów platformy Azure można autoryzować dostęp do obiektów blob i kolejek z aplikacji uruchamianych na maszynach wirtualnych platformy Azure, aplikacjach funkcji, zestawach skalowania maszyn wirtualnych i innych.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595179"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure

Usługi Azure BLOB i queue storage obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawów skalowania maszyn wirtualnych i innych usług. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze.  

W tym artykule przedstawiono sposób autoryzacji dostępu do danych obiektu BLOB lub kolejki z maszyny wirtualnej platformy Azure przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Opisano w nim również sposób testowania kodu w środowisku deweloperskim.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej

Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania dostępu do obiektów blob i kolejek z poziomu maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Uwierzytelnianie za pomocą biblioteki tożsamości platformy Azure (wersja zapoznawcza)

Biblioteka klienta tożsamości platformy Azure dla programu .NET (wersja zapoznawcza) uwierzytelnia podmiot zabezpieczeń. Gdy kod jest uruchomiony na platformie Azure, podmiot zabezpieczeń jest zarządzaną tożsamością dla zasobów platformy Azure.

Gdy kod jest uruchomiony w środowisku programistycznym, uwierzytelnianie może być obsługiwane automatycznie lub może wymagać logowania w przeglądarce, w zależności od tego, które narzędzia są używane. Microsoft Visual Studio obsługuje logowanie jednokrotne (SSO), dzięki czemu aktywne konto użytkownika usługi Azure AD jest automatycznie używane do uwierzytelniania. Aby uzyskać więcej informacji na temat rejestracji jednokrotnej, zobacz Logowanie jednokrotne [do aplikacji](../../active-directory/manage-apps/what-is-single-sign-on.md).

Inne narzędzia programistyczne mogą monitować o zalogowanie się za pośrednictwem przeglądarki sieci Web. Można również użyć jednostki usługi do uwierzytelniania ze środowiska deweloperskiego. Aby uzyskać więcej informacji, zobacz [Tworzenie tożsamości dla aplikacji platformy Azure w portalu](../../active-directory/develop/howto-create-service-principal-portal.md).

Po uwierzytelnieniu Biblioteka klienta tożsamości platformy Azure Pobiera poświadczenia tokenu. To poświadczenie tokenu jest następnie hermetyzowane w obiekcie klienta usługi tworzonym w celu wykonywania operacji w usłudze Azure Storage. Biblioteka obsługuje to bezproblemowo, pobierając odpowiednie poświadczenia tokenu.

Aby uzyskać więcej informacji na temat biblioteki klienta tożsamości platformy Azure, zobacz [Biblioteka klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Przypisywanie ról RBAC na potrzeby dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektu BLOB lub kolejki, musi mieć uprawnienia do tego zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy konto użytkownika usługi Azure AD z uruchomionym kodem w środowisku deweloperskim, podmiot zabezpieczeń musi mieć przypisaną rolę RBAC, która przyznaje dostęp do danych obiektów blob lub kolejki w usłudze Azure Storage. Informacje o przypisywaniu uprawnień za pośrednictwem RBAC zawiera sekcja zatytułowana **Przypisywanie ról RBAC dla praw dostępu** w artykule [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Zainstaluj pakiety wersji zapoznawczej

W przykładach w tym artykule użyto najnowszej wersji zapoznawczej biblioteki klienta usługi Azure Storage dla magazynu obiektów BLOB. Aby zainstalować pakiet wersji zapoznawczej, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

W przykładach w tym artykule użyto również najnowszej wersji zapoznawczej [biblioteki klienta usługi Azure Identity dla platformy .NET](https://www.nuget.org/packages/Azure.Identity/) do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Aby zainstalować pakiet wersji zapoznawczej, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu platformy .NET: Tworzenie blokowego obiektu BLOB

Dodaj następujące dyrektywy `using` do kodu, aby użyć wersji zapoznawczej tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

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

Aby uzyskać poświadczenia tokenu, których kod może użyć do autoryzacji żądań do usługi Azure Storage, Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Poniższy przykład kodu pokazuje, jak uzyskać uwierzytelnione poświadczenia tokenu i użyć go do utworzenia obiektu klienta usługi, a następnie użyć klienta usługi do przekazania nowego obiektu BLOB:

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
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Aby autoryzować żądania względem obiektów blob lub danych z kolejki za pomocą usługi Azure AD, musisz użyć protokołu HTTPS dla tych żądań.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure Storage, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą RBAC](storage-auth-aad-rbac.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejek z poziomu aplikacji magazynu, zobacz [Korzystanie z usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
- Aby dowiedzieć się, jak uruchomić interfejs wiersza polecenia platformy Azure i poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD, zobacz [Uruchamianie interfejsu wiersza polecenia platformy Azure lub poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów](storage-auth-aad-script.md)
