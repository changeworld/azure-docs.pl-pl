---
title: Uwierzytelnianie za pomocą usługi Azure AD z maszyny Wirtualnej platformy Azure zarządzanych tożsamości usługi (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Uwierzytelnianie za pomocą usługi Azure AD z maszyny Wirtualnej platformy Azure zarządzanych tożsamości usługi (wersja zapoznawcza).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 080cb3ee536227e5ddce3fac856de79b2b061dcf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970772"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Uwierzytelnianie za pomocą usługi Azure AD z tożsamości usługi zarządzanej platformy Azure (wersja zapoznawcza)

Usługa Azure Storage obsługuje uwierzytelnianie usługi Azure Active Directory (Azure AD) przy użyciu [tożsamości usługi zarządzanej](../../active-directory/managed-service-identity/overview.md). Tożsamość usługi zarządzanej (MSI) zapewnia automatycznie zarządzanych tożsamości w usłudze Azure Active Directory (Azure AD). Przy użyciu pliku MSI do uwierzytelniania usługi Azure Storage z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych. Przy użyciu pliku MSI i możliwości usługi uwierzytelniania usługi Azure AD, możesz uniknąć przechowywania poświadczeń za pomocą aplikacji działających w chmurze.  

Aby udzielić uprawnień do tożsamości usługi zarządzanej dla kontenerów magazynów lub kolejek, Przypisz rolę RBAC obsługę magazynu uprawnienia do pliku MSI. Aby uzyskać więcej informacji na temat ról RBAC w magazynie, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Tej wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jest ogólnie dostępna. Jeśli integracji z usługą Azure AD nie jest jeszcze obsługiwana dla danego scenariusza, należy nadal używać klucza wspólnego autoryzacji lub tokenów SAS w swoich aplikacjach. Aby uzyskać dodatkowe informacje na temat korzystania z wersji zapoznawczej, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
>
> W trakcie okresu zapoznawczego przypisania ról RBAC może potrwać do pięciu minut na propagację.

W tym artykule przedstawiono sposób uwierzytelniania usługi Azure Storage przy użyciu pliku MSI w Maszynie wirtualnej platformy Azure.  

## <a name="enable-msi-on-the-vm"></a>Włączanie tożsamości usługi Zarządzanej maszyny wirtualnej

Zanim przy użyciu pliku MSI do uwierzytelniania usługi Azure Storage z maszyny Wirtualnej, należy najpierw włączyć tożsamości usługi Zarządzanej maszyny wirtualnej. Aby dowiedzieć się, jak włączyć tożsamości usługi Zarządzanej, zobacz jeden z następujących artykułów:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Zestawy SDK platformy Azure](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Uzyskiwanie tokenu dostępu tożsamości usługi Zarządzanej

Aby uwierzytelniać się przy użyciu pliku MSI, aplikacji lub skryptu należy uzyskać token dostępu tożsamości usługi Zarządzanej. Aby dowiedzieć się więcej o tym, jak można uzyskać tokenu dostępu, zobacz [jak używać usługi Azure VM tożsamość usługi zarządzanej (MSI) dla tokenu](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Przykładowy kod platformy .NET: Utwórz blokowy obiekt blob

W przykładzie kodu założono, że token dostępu tożsamości usługi Zarządzanej. Token dostępu jest używany do autoryzowania tożsamości usługi zarządzanej, aby utworzyć blokowego obiektu blob.

### <a name="add-references-and-using-statements"></a>Dodaj odwołania i przy użyciu instrukcji  

W programie Visual Studio należy zainstalować wersję zapoznawczą biblioteki klienta usługi Azure Storage. Z **narzędzia** menu, wybierz opcję **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**. W konsoli, należy wpisać następujące polecenie:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Dodaj następujące instrukcje using do kodu:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Utwórz poświadczenia z tokenu dostępu tożsamości usługi Zarządzanej

Aby utworzyć blokowego obiektu blob, użyj **TokenCredentials** klasy dostarczane przez pakiet (wersja zapoznawcza). Utworzyć nowe wystąpienie klasy **TokenCredentials**, przekazując plik MSI token dostępu uzyskany wcześniej:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Integracja usługi Azure AD z usługą Azure Storage wymaga używania protokołu HTTPS dla operacji usługi Azure Storage.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z magazynu aplikacji](storage-auth-aad-app.md).
- Aby dowiedzieć się, jak zalogować się do wiersza polecenia platformy Azure i programu PowerShell przy użyciu tożsamości usługi Azure AD, zobacz [tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob platformy Azure, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
