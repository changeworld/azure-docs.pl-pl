---
title: Uwierzytelniania w usłudze Azure AD z maszyny Wirtualnej platformy Azure zarządzanych tożsamości usługi (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Uwierzytelniania w usłudze Azure AD z maszyny Wirtualnej platformy Azure zarządzanych tożsamości usługi (wersja zapoznawcza).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 83d3a2d973604e3b8a709b24cabcb3abba1e304c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660868"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Uwierzytelniania za pomocą usługi Azure AD z tożsamością zarządzanych usług Azure (wersja zapoznawcza)

Usługa Azure Storage obsługuje uwierzytelniania usługi Azure Active Directory (Azure AD) za pomocą [zarządzane tożsamość usługi](../../active-directory/managed-service-identity/overview.md). Tożsamość usługi zarządzanej (MSI) zawiera automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). MSI służy do uwierzytelniania z aplikacji uruchomionych w maszynach wirtualnych platformy Azure, funkcja aplikacji zestawy skalowania maszyny wirtualnej i innych użytkowników usługi Azure Storage. Dzięki użyciu MSI i używania możliwości uwierzytelniania usługi Azure AD, można uniknąć przechowywania poświadczeń w aplikacjach, które działają w chmurze.  

Aby udzielić uprawnień do tożsamości usługi zarządzanej kontenery magazynu lub kolejek, można przypisać rolę RBAC obejmujące uprawnienia magazynu MSI. Aby uzyskać więcej informacji o rolach RBAC w magazynie, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Ta wersja zapoznawcza jest przeznaczony tylko do użytku z systemem innym niż produkcji. Produkcyjnej umowy poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jako ogólnie dostępna. Jeśli dla danego scenariusza integracji z usługą Azure AD nie jest jeszcze obsługiwana, nadal używać klucza wspólnego autoryzacji lub tokeny sygnatury dostępu Współdzielonego w aplikacji. Aby uzyskać dodatkowe informacje o wersji zapoznawczej, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
>
> W wersji zapoznawczej przypisań ról RBAC może potrwać maksymalnie pięć minut propagacji.

W tym artykule przedstawiono sposób uwierzytelniania usługi Azure Storage msi w maszynie Wirtualnej platformy Azure.  

## <a name="enable-msi-on-the-vm"></a>Włącz MSI w maszynie Wirtualnej

Przed użyciem MSI do uwierzytelniania usługi Azure Storage z maszyny Wirtualnej, należy najpierw włączyć MSI w maszynie Wirtualnej. Aby dowiedzieć się, jak włączyć MSI, zobacz następujące artykuły:

- [Azure Portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Szablonu usługi Azure Resource Manager](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Zestawy Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Uzyskaj token dostępu MSI

Do uwierzytelniania za pomocą Instalatora MSI, aplikacji lub skryptu należy uzyskać token dostępu MSI. Aby dowiedzieć się więcej o sposobie pozyskania tokenu dostępu, zobacz [sposobu użycia Azure VM zarządzane usługi tożsamości (MSI) do przejęcia tokenu](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu .NET: tworzenie blokowych obiektów blob

Przykład kodu zakłada, że token dostępu MSI. Token dostępu jest używany do autoryzacji tożsamość usługi zarządzanej utworzyć blokowych obiektów blob.

### <a name="add-references-and-using-statements"></a>Dodaj odwołania i instrukcje using  

W programie Visual Studio Zainstaluj wersję zapoznawczą biblioteki klienta magazynu Azure. Z **narzędzia** menu, wybierz opcję **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**. W konsoli, wpisz następujące polecenie:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Dodaj następujące instrukcje using do kodu:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Utwórz poświadczenia z tokenu dostępu do pliku MSI

Aby utworzyć blokowych obiektów blob, użyj **TokenCredentials** klasy pakietu w wersji zapoznawczej. Utworzyć nowe wystąpienie klasy **TokenCredentials**, przekazując MSI tokenu dostępu, który został uzyskany wcześniej:

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

- Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby uzyskać informacje o autoryzacji dostępu do kontenerów i kolejki ze w aplikacjach magazynu, zobacz [użycia usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
- Aby dowiedzieć się, jak zalogować się do wiersza polecenia platformy Azure i programu PowerShell przy użyciu tożsamości usługi Azure AD, zobacz [korzystania z tożsamości usługi Azure AD dostęp do magazynu Azure z interfejsu wiersza polecenia lub środowiska PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob Azure, zobacz w usłudze Azure Storage team na blogu, [Announcing uwierzytelniania Podgląd Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
