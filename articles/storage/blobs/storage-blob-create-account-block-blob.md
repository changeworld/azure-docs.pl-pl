---
title: Tworzenie konta magazynu obiektów blob bloku — usługa Azure Storage | Dokumenty firmy Microsoft
description: Pokazuje, jak utworzyć konto Azure BlockBlobStorage z charakterystyką wydajności w wersji premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536908"
---
# <a name="create-a-blockblobstorage-account"></a>Tworzenie konta BlockBlobStorage

Rodzaj konta BlockBlobStorage umożliwia tworzenie obiektów blob blokowych o właściwościach wydajności premium. Ten typ konta magazynu jest zoptymalizowany pod kątem obciążeń o wysokich stawkach transakcji lub wymagających bardzo szybkiego czasu dostępu. W tym artykule pokazano, jak utworzyć konto BlockBlobStorage przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Aby uzyskać więcej informacji na temat kont BlockBlobStorage, zobacz [Omówienie konta magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ten artykuł in how wymaga modułu Azure PowerShell Az w wersji 1.2.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz zalogować się na platformie Azure i uruchomić polecenia interfejsu wiersza polecenia platformy Azure na jeden z dwóch sposobów:

- Polecenia interfejsu wiersza polecenia można uruchamiać z poziomu witryny Azure portal w usłudze Azure Cloud Shell.
- Można zainstalować interfejsu wiersza polecenia i uruchomić polecenia interfejsu wiersza polecenia lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Narzędzie wiersza polecenia platformy Azure jest wstępnie zainstalowane i skonfigurowane do używania z kontem. Kliknij przycisk **Cloud Shell** w menu w prawej górnej części witryny Azure portal:

[![Powłoka chmury](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Przycisk uruchamia powłokę interaktywną, której można użyć do uruchomienia kroków opisanych w tym artykule inspekcyjnym:

[![Zrzut ekranu przedstawiający okno Powłoki chmury w portalu](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać lokalnie. Ten artykuł in how-to wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zaloguj się do [Portalu Azure](https://portal.azure.com).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Zaloguj się do subskrypcji `Connect-AzAccount` platformy Azure za pomocą polecenia i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie, aby uwierzytelnić.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić usługę Azure Cloud Shell, zaloguj się do [witryny Azure portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [logowania az:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Tworzenie konta BlockBlobStorage

## <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto BlockBlobStorage w witrynie Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz **pozycję Wszystkie usługi** > kategorii **Magazyn** > **kontach magazynu**.

1. W obszarze **Konta magazynu**wybierz pozycję **Dodaj**.

1. W polu **Subskrypcja** wybierz subskrypcję, w której ma być utworzone konto magazynu.

1. W polu **Grupa zasobów** wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowe**i wprowadź nazwę nowej grupy zasobów.

1. W polu **Nazwa konta magazynu** wprowadź nazwę konta. Należy zwrócić uwagę na następujące wskazówki:

   - Nazwa musi być unikatowa na platformie Azure.
   - Nazwa musi zawierać od trzech do 24 znaków.
   - Nazwa może zawierać tylko cyfry i małe litery.

1. W polu **Lokalizacja** wybierz lokalizację dla konta magazynu lub użyj lokalizacji domyślnej.

1. W przypadku pozostałych ustawień skonfiguruj następujące elementy:

   |Pole     |Wartość  |
   |---------|---------|
   |**Wydajność**    |  Wybierz **Premium**.   |
   |**Rodzaj konta**    | Wybierz **opcję BlockBlobStorage**.      |
   |**Replikacji**    |  Pozostaw domyślne ustawienie **magazynu lokalnie nadmiarowego (LRS)**.      |

   ![Pokazuje interfejs użytkownika portalu w celu utworzenia konta magazynu obiektów blob bloku](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć ustawienia konta magazynu.

1. Wybierz **pozycję Utwórz**.

## <a name="azure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień (uruchom jako administrator).

1. Uruchom następujące polecenie, aby upewnić `Az` się, że jest zainstalowana najnowsza wersja modułu programu PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otwórz nową konsolę programu PowerShell i zaloguj się za pomocą konta platformy Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. W razie potrzeby utwórz nową grupę zasobów. Zastąp wartości w cudzysłów i uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Utwórz konto BlockBlobStorage. Zastąp wartości w cudzysłów i uruchom następujące polecenie.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto obiektu blob bloku przy użyciu interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować interfejsu wiersza polecenia platformy Azure v. 2.0.46 lub nowszej wersji. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zaloguj się do subskrypcji platformy Azure.

   ```azurecli
   az login
   ```

1. W razie potrzeby utwórz nową grupę zasobów. Zastąp wartości w nawiasach (łącznie z nawiasami) i uruchom następujące polecenie.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Utwórz konto BlockBlobStorage. Zastąp wartości w nawiasach (łącznie z nawiasami) i uruchom następujące polecenie.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
