---
title: Tworzenie konta magazynu plików Azure w warstwie premium
description: W tym artykule dowiesz się, jak utworzyć konto magazynu plików Azure w warstwie premium i premium udziału plików.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288429"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Jak utworzyć udział plików platformy Azure — wersja premium

Typ konta magazynu FileStorage (wersja zapoznawcza) stanowi nową warstwę dla usługi Azure Files, co pozwala na tworzenie udziałów plików za pomocą charakterystyki wydajności premium. Te udziały plików są przeznaczone do wysokiej wydajności i aplikacji w skali przedsiębiorstwa, zapewniając niskie opóźnienia, wysoka operacje We/Wy i wysoką przepływność akcji.

W tym artykule dowiesz się, jak utworzyć tego nowego konta typu przy użyciu [witryny Azure portal](https://portal.azure.com/), programu Azure PowerShell i wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Tworzenie udziału plików — wersja premium za pomocą witryny Azure portal

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Utwórz konto magazynu FileStorage (wersja zapoznawcza)

Teraz możesz przystąpić do tworzenia konta magazynu.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule przedstawiono sposób tworzenia nowej grupy zasobów.

1. W witrynie Azure portal wybierz **kont magazynu** w menu po lewej stronie.

    ![Główne stronę witryny Azure portal wybierz konto magazynu](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów, jak pokazano na poniższej ilustracji.

1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
1. Aby uzyskać **wydajności** wybierz **Premium**.
1. Wybierz **rodzaj konta** i wybierz polecenie **FileStorage (wersja zapoznawcza)**.
1. Pozostaw **replikacji** ustawiona na wartość domyślną **magazyn lokalnie nadmiarowy (LRS)**.

    ![Jak utworzyć konto magazynu premium storage plików](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz pozycję **Utwórz**.

Po utworzeniu zasobu konta magazynu, przejdź do niego.

### <a name="create-a-premium-file-share"></a>Tworzenie udziału plików — wersja premium

1. W menu po lewej stronie konta magazynu, przewiń do **usługi plików** sekcji, a następnie wybierz **pliki (wersja zapoznawcza)**.
1. Wybierz **+ udział plików** można utworzyć udziału plików — wersja premium.
1. Wprowadź nazwę i żądany limit przydziału udziału plików, a następnie wybierz **Utwórz**.

> [!NOTE]
> Rozmiary elastycznie udziału jest określona przez przydziału udziału udziały plików są rozliczane w systemie zarezerwowany rozmiar, zapoznaj się [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/) Aby uzyskać więcej informacji.

   ![Tworzenie udziału plików — wersja premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule, możesz po prostu usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu, a także wszystkich innych zasobów skojarzonych z grupą zasobów.

## <a name="create-a-premium-file-share-using-powershell"></a>Tworzenie udziału plików — wersja premium za pomocą programu PowerShell

### <a name="create-an-account-using-powershell"></a>Tworzenie konta przy użyciu programu PowerShell

Najpierw zainstaluj najnowszą wersję modułu [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).

Następnie uaktualniania modułu programu powershell, zaloguj się do subskrypcji platformy Azure, Utwórz grupę zasobów i następnie utworzyć konto magazynu.

### <a name="upgrade-your-powershell-module"></a>Uaktualnianie modułu PowerShell

Do interakcji z files w warstwie premium przy użyciu programu PowerShell, musisz zainstalować najnowszy moduł Az.Storage.

Rozpocznij od otwarcia sesji programu PowerShell z podwyższonym poziomem uprawnień.

Zainstaluj moduł Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

Użyj polecenia `Login-AzAccount`, a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Login-AzAccount
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

### <a name="create-a-filestorage-preview-storage-account"></a>Utwórz konto magazynu filestorage (wersja zapoznawcza)

Aby utworzyć konto magazynu FileStorage (wersja zapoznawcza) za pomocą programu PowerShell, użyj [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) polecenia:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Tworzenie udziału plików — wersja premium

Teraz, gdy masz konto FileStorage, można utworzyć udziału plików — wersja premium. Użyj [New AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) polecenia cmdlet, aby go utworzyć.

> [!NOTE]
> Rozmiary elastycznie udziału jest określona przez przydziału udziału udziały plików są rozliczane w systemie zarezerwowany rozmiar, zapoznaj się [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/) Aby uzyskać więcej informacji.

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>Tworzenie udziału plików — wersja premium, przy użyciu wiersza polecenia platformy Azure

Aby uruchomić usługę Azure Cloud Shell, zaloguj się do [witryny Azure portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie logowania:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Dodaj rozszerzenie interfejsu wiersza polecenia dla plików Azure w warstwie premium

Do interakcji z files w warstwie premium przy użyciu interfejsu wiersza polecenia, musisz dodać rozszerzenia powłoki.

Aby to zrobić, wprowadź następujące polecenie, używając usłudze Cloud Shell lub lokalnej powłoki: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Utwórz konto magazynu FileStorage (wersja zapoznawcza)

Aby utworzyć konto magazynu FileStorage (wersja zapoznawcza) z wiersza polecenia platformy Azure, użyj [Tworzenie konta magazynu az](/cli/azure/storage/account) polecenia.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu

Klucze konta magazynu kontrolują dostęp do zasobów na koncie magazynu, w tym artykule, możemy użyć klucza w celu utworzenia udziału plików — wersja premium. Klucze są tworzone automatycznie podczas tworzenia konta magazynu. Klucze konta magazynu możesz pobrać przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Tworzenie udziału plików — wersja premium

Teraz, gdy masz konto FileStorage, można utworzyć udziału plików — wersja premium. Użyj [Utwórz udział magazynu az](/cli/azure/storage/share) polecenie, aby go utworzyć.

> [!NOTE]
> Rozmiary elastycznie udziału jest określona przez przydziału udziału udziały plików są rozliczane w systemie zarezerwowany rozmiar, zapoznaj się [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/) Aby uzyskać więcej informacji.

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono konto magazynu premium storage plików. Informacje na temat wydajności oferowanych przez to konto, przejdź do sekcji warstwy wydajności przewodnika planowania.

> [!div class="nextstepaction"]
> [Warstwy wydajności udziału plików](storage-files-planning.md#file-share-performance-tiers)