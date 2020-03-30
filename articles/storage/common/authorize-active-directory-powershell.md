---
title: Uruchamianie poleceń programu PowerShell z poświadczeniami usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub kolejki
titleSuffix: Azure Storage
description: Program PowerShell obsługuje logowanie się przy użyciu poświadczeń usługi Azure AD w celu uruchamiania poleceń w obiekcie blob usługi Azure Storage i kolejek danych. Token dostępu jest dostarczany dla sesji i służy do autoryzowania operacji wywołujących. Uprawnienia zależą od roli RBAC przypisanej do podmiotu zabezpieczeń usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553451"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Uruchamianie poleceń programu PowerShell z poświadczeniami usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub kolejki

Usługa Azure Storage udostępnia rozszerzenia programu PowerShell, które umożliwiają logowanie się i uruchamianie poleceń skryptów przy użyciu poświadczeń usługi Azure Active Directory (Azure AD). Po zalogowaniu się do programu PowerShell przy użyciu poświadczeń usługi Azure AD zwracany jest token dostępu OAuth 2.0. Ten token jest automatycznie używany przez program PowerShell do autoryzowania kolejnych operacji danych względem magazynu obiektów Blob lub Queue. W przypadku obsługiwanych operacji nie trzeba już przekazywać klucza konta lub tokenu Sygnatury dostępu Współdzielonego za pomocą polecenia.

Można przypisać uprawnienia do danych obiektów blob i kolejek do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia usługi Azure Storage są obsługiwane dla operacji na danych obiektów blob i kolejki. Operacje, które można wywołać, zależą od uprawnień przyznanych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do programu PowerShell. Uprawnienia do kontenerów lub kolejek usługi Azure Storage są przypisywane za pośrednictwem funkcji RBAC. Na przykład jeśli przypisano rolę **programu Blob Data Reader,** można uruchomić polecenia skryptów, które odczytują dane z kontenera lub kolejki. Jeśli przypisano rolę **współautora danych obiektów blob,** można uruchomić polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub kolejkę lub dane, które zawierają.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejce, zobacz [Wywoływanie operacji magazynu za pomocą tokenów OAuth.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Wywoływanie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby użyć programu Azure PowerShell do logowania się i uruchamiania kolejnych operacji w usłudze Azure `-UseConnectedAccount` Storage przy użyciu poświadczeń usługi Azure AD, należy utworzyć kontekst magazynu w celu odwołania się do konta magazynu i dołączyć parametr.

W poniższym przykładzie pokazano, jak utworzyć kontener na nowym koncie magazynu z programu Azure PowerShell przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

1. Zaloguj się do konta platformy Azure za pomocą polecenia [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Aby uzyskać więcej informacji na temat logowania się do platformy Azure za pomocą programu PowerShell, zobacz [Logowanie się za pomocą programu Azure PowerShell.](/powershell/azure/authenticate-azureps)

1. Utwórz grupę zasobów platformy Azure, wywołując [new-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Utwórz konto magazynu, wywołując [new-azStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Pobierz kontekst konta magazynu, który określa nowe konto magazynu, wywołując [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Działając na koncie magazynu, można odwoływać się do kontekstu zamiast wielokrotnie przekazywania poświadczeń. Dołącz `-UseConnectedAccount` parametr do wywoływania kolejnych operacji danych przy użyciu poświadczeń usługi Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Przed utworzeniem kontenera należy przypisać do siebie rolę [współautora danych obiektów blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) magazynu. Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Udzielanie dostępu do obiektów blob platformy Azure i danych kolejki za pomocą funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przydziałów ról RBAC może potrwać kilka minut.

1. Utwórz kontener, wywołując [new-azStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Ponieważ to wywołanie używa kontekstu utworzonego w poprzednich krokach, kontener jest tworzony przy użyciu poświadczeń usługi Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli RBAC w celu uzyskania dostępu do danych obiektów blob i kolejek za pomocą programu PowerShell](storage-auth-aad-rbac-powershell.md)
- [Autoryzowanie dostępu do danych obiektów blob i kolejek z zarządzanymi tożsamościami zasobów platformy Azure](storage-auth-aad-msi.md)
