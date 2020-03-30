---
title: Tworzenie udziału plików platformy Azure w wersji Premium
description: W tym artykule dowiesz się, jak utworzyć udział plików platformy Azure w wersji premium.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529111"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Jak utworzyć udział plików platformy Azure w wersji Premium
Udziały plików premium są oferowane na nośnikach pamięci masowej dysków PÓŁPRZEWODNIKOWYCH (SSD) i są przydatne w przypadku obciążeń intensywnie korzystających z we/wy, w tym hostingu baz danych i obliczeń o wysokiej wydajności (HPC). Udziały plików premium są hostowane w rodzaju konta magazynu specjalnego przeznaczenia, zwanego kontem FileStorage. Udziały plików premium są przeznaczone do zastosowań o wysokiej wydajności i skali przedsiębiorstwa, zapewniając spójne małe opóźnienia, wysokie we/wy i udziały o wysokiej przepływności.

W tym artykule pokazano, jak utworzyć ten nowy typ konta przy użyciu [witryny Azure Portal](https://portal.azure.com/), Azure PowerShell i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do zasobów platformy Azure, w tym udziałów plików platformy Azure w wersji premium, musisz mieć subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Tworzenie udziału plików premium przy użyciu portalu Azure

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Tworzenie konta magazynu plików

Teraz możesz utworzyć konto magazynu.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

1. W witrynie Azure portal wybierz pozycję **Konta magazynu** w menu po lewej stronie.

    ![Konto magazynu wybrane na stronie głównej portalu Azure](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów, jak pokazano na poniższej ilustracji.

1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
1. Aby **uzyskać opcję Wydajność** wybierz **Premium**.
1. Wybierz **opcję Rodzaj konta** i wybierz pozycję **FileStorage**.
1. Pozostaw **replikację** ustawioną na domyślną wartość **magazynu lokalnie nadmiarowego (LRS)**.

    ![Jak utworzyć konto magazynu dla udziału plików premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz **pozycję Utwórz**.

Po utworzeniu zasobu konta magazynu przejdź do niego.

### <a name="create-a-premium-file-share"></a>Tworzenie udziału plików w warstwie Premium

1. W menu po lewej stronie konta magazynu przewiń do sekcji **Usługa plików,** a następnie wybierz pozycję **Pliki**.
1. Wybierz **udział plików,** aby utworzyć udział plików premium.
1. Wprowadź nazwę i żądany przydział udziału plików, a następnie wybierz pozycję **Utwórz**.

> [!NOTE]
> Rozmiary aprowizowanych udziałów są określone przez przydział udziałów, udziały plików są rozliczane od aprowizowanego rozmiaru, zapoznaj się ze [stroną cen,](https://azure.microsoft.com/pricing/details/storage/files/) aby uzyskać więcej informacji.

   ![Tworzenie udziału plików w warstwie Premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule, możesz po prostu usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu, a także innych zasobów skojarzonych z grupą zasobów.

## <a name="create-a-premium-file-share-using-powershell"></a>Tworzenie udziału plików premium przy użyciu programu PowerShell

### <a name="create-an-account-using-powershell"></a>Tworzenie konta przy użyciu programu PowerShell

Najpierw zainstaluj najnowszą wersję modułu [PowerShellGet](/powershell/scripting/gallery/installing-psget).

Następnie uaktualnij moduł programu PowerShell, zaloguj się do subskrypcji platformy Azure, utwórz grupę zasobów, a następnie utwórz konto magazynu.

### <a name="upgrade-your-powershell-module"></a>Uaktualnianie modułu programu PowerShell

Aby wchodzić w interakcje z udziałem plików premium z programu PowerShell, należy zainstalować moduł Az.Storage w wersji 1.4.0 lub najnowszy moduł Az.Storage.

Rozpocznij od otwarcia sesji programu PowerShell z podwyższonym poziomem uprawnień.

Zainstaluj moduł Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

Użyj polecenia `Connect-AzAccount`, a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą programu PowerShell, użyj polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Tworzenie konta magazynu FileStorage

Aby utworzyć konto magazynu FileStorage z programu PowerShell, użyj polecenia [New-AzStorageAccount:](/powershell/module/az.storage/New-azStorageAccount)

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Tworzenie udziału plików w warstwie Premium

Teraz, gdy masz konto FileStorage, możesz utworzyć udział plików premium. Użyj polecenia cmdlet [New-AzStorageShare,](/powershell/module/az.storage/New-AzStorageShare) aby go utworzyć.

> [!NOTE]
> Rozmiary aprowizowanych udziałów są określone przez przydział udziałów, udziały plików są rozliczane od aprowizowanego rozmiaru, zapoznaj się ze [stroną cen,](https://azure.microsoft.com/pricing/details/storage/files/) aby uzyskać więcej informacji.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Tworzenie udziału plików premium przy użyciu interfejsu wiersza polecenia platformy Azure

Aby uruchomić usługę Azure Cloud Shell, zaloguj się do [witryny Azure Portal](https://portal.azure.com).

Jeśli chcesz zalogować się do lokalnej instalacji interfejsu wiersza polecenia, najpierw upewnij się, że masz najnowszą wersję, a następnie uruchom polecenie logowania:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Tworzenie konta magazynu FileStorage

Aby utworzyć konto magazynu FileStorage z interfejsu wiersza polecenia platformy Azure, użyj polecenia [tworzenia konta magazynu az.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu

Klucze konta magazynu kontrolują dostęp do zasobów na koncie magazynu, w tym artykule używamy klucza w celu utworzenia udziału plików premium. Klucze są tworzone automatycznie podczas tworzenia konta magazynu. Klucze konta magazynu możesz pobrać przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Tworzenie udziału plików w warstwie Premium

Teraz, gdy masz konto filetorage, możesz utworzyć udział plików premium. Użyj polecenia [tworzenia udziału magazynu az,](/cli/azure/storage/share) aby je utworzyć.

> [!NOTE]
> Rozmiary aprowizowanych udziałów są określone przez przydział udziałów, udziały plików są rozliczane od aprowizowanego rozmiaru, zapoznaj się ze [stroną cen,](https://azure.microsoft.com/pricing/details/storage/files/) aby uzyskać więcej informacji.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono udział plików premium. Aby dowiedzieć się więcej o skuteczności, które oferuje to konto, przejdź do sekcji warstwy wydajności przewodnika planowania.

> [!div class="nextstepaction"]
> [Warstwy udziału plików](storage-files-planning.md#storage-tiers)