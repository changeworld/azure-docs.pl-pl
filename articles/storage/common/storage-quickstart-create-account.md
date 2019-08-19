---
title: Tworzenie konta magazynu — Azure Storage
description: W tym artykule z tego artykułu dowiesz się, jak utworzyć konto magazynu przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Konto usługi Azure Storage zapewnia unikatową przestrzeń nazw na platformie Microsoft Azure do przechowywania i umożliwiania dostępu do obiektów danych utworzonych w usłudze Azure Storage.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 06/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e72f8c3ed0658765827c4a9b0669c08fefd9044f
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016331"
---
# <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, która jest dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na koncie usługi Azure Storage są trwałe i wysoce dostępne, bezpieczne i skalowalne.

W tym artykule z tego artykułu dowiesz się, jak utworzyć konto magazynu przy użyciu [Azure Portal](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)lub [szablonu Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Ten artykuł instruktażowy wymaga modułu Azure PowerShell AZ w wersji 0,7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz zalogować się do platformy Azure i uruchamiać polecenia interfejsu wiersza poleceń platformy Azure na jeden z dwóch sposobów:

- Poleceń interfejsu wiersza polecenia można uruchomić z poziomu Azure Portal w Azure Cloud Shell.
- Interfejs wiersza polecenia można zainstalować i uruchomić polecenie CLI lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Przycisk uruchamia interaktywną powłokę, której można użyć, aby wykonać kroki opisane w tym artykule:

[![Zrzut ekranu przedstawiający okno usługi Cloud Shell w portalu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać go lokalnie. Ten artykuł z artykułu wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Brak.

---

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami do uwierzytelniania.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [AZ login](/cli/azure/reference-index#az-login) :

```cli
az login
```

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

ND

---

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Teraz możesz przystąpić do tworzenia konta magazynu.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

Konto magazynu **ogólnego przeznaczenia, wersja 2** zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Kroki opisane tutaj tworzą konto magazynu ogólnego przeznaczenia w wersji 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw użyj polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) w programie PowerShell, aby utworzyć nową grupę zasobów:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Jeśli nie masz pewności, który region należy określić dla parametru `-Location`, za pomocą polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji:

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2 z magazynem Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS) za pomocą polecenia [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Pamiętaj, że nazwa konta magazynu musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z inną opcją replikacji, należy zastąpić żądaną wartość w poniższej tabeli dla parametru **SkuName** .

|Opcja replikacji  |Parametr SkuName  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |
|Magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza)    |Standard_GZRS         |
|Strefa geograficzna z dostępem do odczytu — magazyn nadmiarowy (RA-GZRS) (wersja zapoznawcza)    |Standard_RAGZRS         |

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw użyj polecenia [az group create](/cli/azure/group#az_group_create) interfejsu wiersza polecenia platformy Azure, aby utworzyć nową grupę zasobów.

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Jeśli nie masz pewności, który region należy określić dla parametru `--location`, za pomocą polecenia [az account list-locations](/cli/azure/account#az_account_list) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2 z magazynem geograficznie nadmiarowym z dostępem do odczytu przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az_storage_account_create) . Pamiętaj, że nazwa konta magazynu musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z inną opcją replikacji, należy zastąpić żądaną wartość w poniższej tabeli dla parametru **SKU** .

|Opcja replikacji  |parametr sku  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |
|Magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza)    |Standard_GZRS         |
|Strefa geograficzna z dostępem do odczytu — magazyn nadmiarowy (RA-GZRS) (wersja zapoznawcza)    |Standard_RAGZRS         |

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można wdrożyć szablon Menedżer zasobów, aby utworzyć konto magazynu. Szablon używany w tym artykule z przewodnikiem pochodzi z [Azure Resource Manager szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Aby uruchomić skrypty, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Aby dowiedzieć się, jak tworzyć szablony, zobacz:

- [Dokumentacja Azure Resource Manager](/azure/azure-resource-manager/).
- [Odwołanie do szablonu konta magazynu](/azure/templates/microsoft.storage/allversions).
- [Dodatkowe przykłady szablonów kont magazynu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Aby uzyskać więcej informacji na temat dostępnych opcji replikacji, zobacz [Storage replication options (Opcje replikacji danych usługi Storage)](storage-redundancy.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule z tego artykułu, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk **Więcej** ( **...** ) po prawej stronie listy.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Następne kroki

W tym artykule z tego artykułu opisano tworzenie standardowego konta magazynu ogólnego przeznaczenia w wersji 2. Aby dowiedzieć się, jak przekazywać i pobierać obiekty blob do i z konta magazynu, przejdź do jednego z przewodników szybki start dla usługi BLOB Storage.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Praca z obiektami blob za pomocą witryny Azure Portal](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Praca z obiektami blob za pomocą programu PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Praca z obiektami blob za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

> [!div class="nextstepaction"]
> [Praca z obiektami blob za pomocą witryny Azure Portal](../blobs/storage-quickstart-blobs-portal.md)

---
