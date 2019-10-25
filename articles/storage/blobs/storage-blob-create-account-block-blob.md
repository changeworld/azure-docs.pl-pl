---
title: Tworzenie konta usługi Block BLOB Storage — Azure Storage | Microsoft Docs
description: Pokazuje, jak utworzyć konto usługi Azure BlockBlobStorage z charakterystyką wydajności Premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881867"
---
# <a name="create-a-blockblobstorage-account"></a>Utwórz konto BlockBlobStorage

Rodzaj konta BlockBlobStorage umożliwia tworzenie blokowych obiektów blob z charakterystyką wydajności Premium. Ten typ konta magazynu jest zoptymalizowany pod kątem obciążeń z wysoką stawką transakcji lub wymagających bardzo szybkiego dostępu. W tym artykule przedstawiono sposób tworzenia konta usługi BlockBlobStorage przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

Aby uzyskać więcej informacji o kontach BlockBlobStorage, zobacz [Omówienie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto BlockBlobStorage w Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz pozycję **wszystkie usługi** > kategoria **magazynu** > **konta magazynu**.

1. W obszarze **konta magazynu**wybierz pozycję **Dodaj**.

1. W polu **subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.

1. W polu **Grupa zasobów** wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.

1. W polu **nazwa konta magazynu** wprowadź nazwę konta. Należy zwrócić uwagę na następujące wytyczne:

   - Nazwa musi być unikatowa na platformie Azure.
   - Nazwa musi mieć długość od 3 do 24 znaków.
   - Nazwa może zawierać tylko cyfry i małe litery.

1. W polu **Lokalizacja** wybierz lokalizację konta magazynu lub Użyj domyślnej lokalizacji.

1. W pozostałych ustawieniach skonfiguruj następujące opcje:

   |Pole     |Wartość  |
   |---------|---------|
   |**Wydajność**    |  Wybierz pozycję **Premium**.   |
   |**Rodzaj konta**    | Wybierz pozycję **BlockBlobStorage**.      |
   |**Replikacja**    |  Pozostaw domyślne ustawienie **magazynu lokalnie nadmiarowego (LRS)** .      |

   ![Przedstawia interfejs użytkownika portalu do tworzenia konta blokowego magazynu obiektów BLOB](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia konta magazynu.

1. Wybierz pozycję **Utwórz**.

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator).

1. Uruchom następujące polecenie, aby upewnić się, że zainstalowano najnowszą wersję modułu `Az` PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otwórz nową konsolę programu PowerShell i zaloguj się przy użyciu konta platformy Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. W razie konieczności Utwórz nową grupę zasobów. Zastąp wartości w cudzysłowach i uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Utwórz konto BlockBlobStorage. Zastąp wartości w cudzysłowach i uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto blokowego obiektu BLOB przy użyciu interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0. 2.0.46 lub nowszą wersję. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zaloguj się do subskrypcji platformy Azure.

   ```azurecli
   az login
   ```

1. W razie konieczności Utwórz nową grupę zasobów. Zastąp wartości w nawiasach (w tym nawiasy klamrowe) i uruchom następujące polecenie.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Utwórz konto BlockBlobStorage. Zastąp wartości w nawiasach (w tym nawiasy klamrowe) i uruchom następujące polecenie.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview (Omówienie konta usługi Azure Storage)](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
