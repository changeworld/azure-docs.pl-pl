---
title: Tworzenie konta usługi block blob storage — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Pokazuje, jak utworzyć konto usługi Azure block blob storage za pomocą charakterystyki wydajności premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141013"
---
# <a name="create-a-block-blob-storage-account"></a>Tworzenie konta magazynu blokowych obiektów blob

Typ konta magazynu blokowych obiektów blob umożliwia tworzenie blokowych obiektów blob za pomocą charakterystyki wydajności premium. Ten typ konta magazynu jest zoptymalizowany dla obciążeń przy użyciu stawki za transakcje o wysokiej lub które wymagają bardzo szybkie godziny dostępu. W tym artykule przedstawiono sposób tworzenia konta usługi block blob storage za pomocą witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

Aby uzyskać więcej informacji na temat kont usługi block blob storage, zobacz [Przegląd konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Utwórz konto w witrynie Azure portal

Aby utworzyć konto usługi block blob storage w witrynie Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz **wszystkich usług** > **magazynu** kategorii > **kont magazynu**.

1. W obszarze **kont magazynu**, wybierz opcję **Dodaj**.

1. W **subskrypcji** Wybierz subskrypcję, w której ma zostać utworzone konto magazynu.

1. W **grupy zasobów** pola, wybierz istniejącą grupę zasobów lub **Utwórz nową**, a następnie wprowadź nazwę dla nowej grupy zasobów.

1. W **nazwa konta magazynu** wprowadź nazwę konta. Należy zwrócić uwagę następujących wytycznych:

   - Nazwa musi być unikatowa w obrębie platformy Azure.
   - Nazwa musi wynosić od 3 do 24 znaków.
   - Nazwa może zawierać tylko cyfry i małe litery.

1. W **lokalizacji** pola, wybierz lokalizację dla konta magazynu lub użyj domyślnej lokalizacji.

1. Dla pozostałych ustawień skonfiguruj następujące ustawienia:

   |Pole     |Wartość  |
   |---------|---------|
   |**Wydajność**    |  Wybierz **Premium**.   |
   |**Rodzaj konta**    | Wybierz **BlockBlobStorage**.      |
   |**Replikacja**    |  Pozostaw domyślne ustawienie **magazyn lokalnie nadmiarowy (LRS)** .      |

   ![Pokazuje interfejsu użytkownika do utworzenia konta usługi blob storage bloku portalu](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Wybierz **Przejrzyj + Utwórz** Aby przejrzeć ustawienia konta magazynu.

1. Wybierz pozycję **Utwórz**.

## <a name="create-account-using-azure-powershell"></a>Tworzenie konta przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator).

1. Uruchom następujące polecenie, aby upewnić się, że najnowsza wersja `Az` zainstalowany moduł PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otwórz nowy konsolę programu PowerShell i zaloguj się przy użyciu konta platformy Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Jeśli to konieczne, Utwórz nową grupę zasobów. Zastąp wartości w ofert, a następnie uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Utwórz konto magazynu blokowych obiektów blob. Zastąp wartości w ofert, a następnie uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Tworzenie konta przy użyciu wiersza polecenia platformy Azure

Aby utworzyć konto usługi block blob przy użyciu wiersza polecenia platformy Azure, należy najpierw zainstalować interfejs wiersza polecenia platformy Azure. 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zaloguj się do subskrypcji platformy Azure.

   ```azurecli
   az login
   ```

1. Jeśli to konieczne, Utwórz nową grupę zasobów. Zastąp wartości w nawiasach kwadratowych (razem z nawiasami), a następnie uruchom następujące polecenie.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Utwórz konto magazynu blokowych obiektów blob. Zastąp wartości w nawiasach kwadratowych (razem z nawiasami), a następnie uruchom następujące polecenie.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview](https://docs.microsoft.com/azure/storage/common/storage-account-overview) (Omówienie konta usługi Azure Storage).

- Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
