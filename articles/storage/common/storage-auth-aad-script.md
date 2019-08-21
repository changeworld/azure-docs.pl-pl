---
title: Uruchamianie interfejsu wiersza polecenia platformy Azure lub poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki | Microsoft Docs
description: Interfejs wiersza polecenia platformy Azure i program PowerShell obsługują Logowanie przy użyciu poświadczeń usługi Azure AD w celu uruchamiania poleceń w usłudze Azure Storage BLOB i kolejkach danych. Token dostępu jest dostarczany dla sesji i używany do autoryzacji operacji wywoływania. Uprawnienia są zależne od roli RBAC przypisanej do podmiotu zabezpieczeń usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 32b3f113658a20790e0f149739a882004f12fe21
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640825"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Uruchamianie interfejsu wiersza polecenia platformy Azure lub poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki

Usługa Azure Storage udostępnia rozszerzenia interfejsu wiersza polecenia platformy Azure i programu PowerShell, które umożliwiają logowanie się i uruchamianie poleceń skryptów przy użyciu poświadczeń Azure Active Directory (Azure AD). Gdy logujesz się do interfejsu wiersza polecenia platformy Azure lub programu PowerShell przy użyciu poświadczeń usługi Azure AD, zwracany jest token dostępu OAuth 2,0. Ten token jest automatycznie używany przez interfejs wiersza polecenia lub program PowerShell do autoryzowania kolejnych operacji na danych w odniesieniu do magazynu obiektów blob lub Queue. W przypadku obsługiwanych operacji nie jest już konieczne przekazywanie klucza konta ani tokenu SAS przy użyciu polecenia.

Można przypisać uprawnienia do obiektów blob i kolejek danych do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia są obsługiwane dla operacji w kontenerach i kolejkach. Operacje, które można wywołać, zależą od uprawnień udzielonych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Uprawnienia do kontenerów lub kolejek usługi Azure Storage są przypisywane za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Na przykład, jeśli przypisano rolę **czytnika danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują dane z kontenera lub kolejki. Jeśli przypisano rolę **współautor danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub kolejkę lub zawarte w nich dane. 

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejce, zobacz [wywoływanie operacji magazynu przy użyciu tokenów OAuth](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Wywoływanie poleceń interfejsu wiersza polecenia przy użyciu poświadczeń usługi Azure AD

Interfejs wiersza polecenia platformy `--auth-mode` Azure obsługuje parametr operacji na danych obiektów blob i kolejek:

- `--auth-mode` Ustaw`login` parametr na, aby zalogować się przy użyciu podmiotu zabezpieczeń usługi Azure AD.
- Ustaw parametr na starszą `key` wartość, aby podjąć próbę zbadania klucza konta, jeśli nie podano parametrów uwierzytelniania dla konta. `--auth-mode` 

Poniższy przykład pokazuje, jak utworzyć kontener na nowym koncie magazynu z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami: 

1. Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom `az --version` , aby sprawdzić zainstalowaną wersję.

1. Uruchom `az login` i Uwierzytelnij w oknie przeglądarki: 

    ```azurecli
    az login
    ```

1. Określ żądaną subskrypcję. Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Utwórz konto magazynu w ramach tej grupy zasobów przy użyciu polecenia [AZ Storage account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [przyznawanie dostępu do obiektów blob platformy Azure i danych z kolejki RBAC w Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról RBAC może potrwać kilka minut.

1. Wywołaj polecenie [AZ Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) z `--auth-mode` parametrem ustawionym `login` na, aby utworzyć kontener przy użyciu poświadczeń usługi Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Zmienna środowiskowa skojarzona z `--auth-mode` parametrem `AZURE_STORAGE_AUTH_MODE`to. Możesz określić odpowiednią wartość w zmiennej środowiskowej, aby uniknąć uwzględniania jej przy każdym wywołaniu operacji na danych usługi Azure Storage.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Wywoływanie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby użyć Azure PowerShell do logowania się i uruchamiania kolejnych operacji w usłudze Azure Storage przy użyciu poświadczeń usługi Azure AD, Utwórz kontekst magazynu, aby odwołać się do konta `-UseConnectedAccount` magazynu, i Uwzględnij parametr.

Poniższy przykład pokazuje, jak utworzyć kontener na nowym koncie magazynu z Azure PowerShell przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

1. Zaloguj się do konta platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Aby uzyskać więcej informacji na temat logowania się do platformy Azure przy użyciu programu PowerShell, zobacz [Logowanie za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Utwórz grupę zasobów platformy Azure, wywołując metodę [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Utwórz konto magazynu, wywołując metodę [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Pobierz kontekst konta magazynu, który określa nowe konto magazynu, wywołując metodę [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Gdy działa na koncie magazynu, można odwołać się do kontekstu, zamiast wielokrotnie przekazywać poświadczenia. `-UseConnectedAccount` Dołącz parametr do wywołania wszelkich kolejnych operacji na danych przy użyciu poświadczeń usługi Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [przyznawanie dostępu do obiektów blob platformy Azure i danych z kolejki RBAC w Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról RBAC może potrwać kilka minut.

1. Utwórz kontener, wywołując metodę [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Ponieważ to wywołanie używa kontekstu utworzonego w poprzednich krokach, kontener jest tworzony przy użyciu poświadczeń usługi Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure Storage, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą RBAC](storage-auth-aad-rbac.md).
- Aby dowiedzieć się więcej o używaniu tożsamości zarządzanych dla zasobów platformy Azure w usłudze Azure Storage, zobacz temat [uwierzytelnianie dostępu do obiektów blob i kolejek przy użyciu Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](storage-auth-aad-msi.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejek z poziomu aplikacji magazynu, zobacz [Korzystanie z usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
