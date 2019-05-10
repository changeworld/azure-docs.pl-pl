---
title: Tworzenie konta magazynu — Azure Storage
description: W tym artykule dowiesz się utworzyć konto magazynu przy użyciu witryny Azure portal, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Konto usługi Azure Storage zapewnia unikatową przestrzeń nazw na platformie Microsoft Azure do przechowywania i umożliwiania dostępu do obiektów danych utworzonych w usłudze Azure Storage.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234182"
---
# <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu platformy Azure zawiera wszystkie obiekty danych usługi Azure Storage: obiekty BLOB, pliki, kolejki, tabele i dyski. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, który jest dostępny z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na Twoim koncie usługi Azure storage jest trwały i o wysokiej dostępności, bezpieczny i wysoce skalowalny.

W tym artykule dowiesz się utworzyć konto magazynu przy użyciu [witryny Azure portal](https://portal.azure.com/), [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), lub [usługi Azure Resource Manager Szablon](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

W tym artykule wymaga modułu Azure PowerShell Az wersji 0,7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Można zalogować się do platformy Azure i uruchamiać polecenia wiersza polecenia platformy Azure w jeden z dwóch sposobów:

- Można uruchomić polecenia interfejsu wiersza polecenia z w witrynie Azure portal w usłudze Azure Cloud Shell.
- Można zainstalować interfejs wiersza polecenia i uruchamiać jego komendy lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do korzystania z Twoim kontem. Kliknij przycisk **Cloud Shell** przycisk menu w prawej górnej części witryny Azure portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ten przycisk uruchamia interaktywną powłokę, której można użyć, aby uruchomić kroki opisane w tym artykule:

[![Zrzut ekranu przedstawiający okno usługi Cloud Shell w portalu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać go lokalnie. W tym artykule wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

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

Aby uruchomić usługę Azure Cloud Shell, zaloguj się do [witryny Azure portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom [az login](/cli/azure/reference-index#az-login) polecenia:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

ND

---

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Teraz możesz przystąpić do tworzenia konta magazynu.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule przedstawiono sposób tworzenia nowej grupy zasobów.

Konto magazynu **ogólnego przeznaczenia, wersja 2** zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Kroki opisane w tym miejscu Utwórz konto magazynu ogólnego przeznaczenia w wersji 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne.

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

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2 z magazynu geograficznie nadmiarowego dostęp do odczytu (RA-GRS) przy użyciu [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) polecenia. Należy pamiętać, że nazwa konta magazynu musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach unikatową wartość:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2, za pomocą opcji replikacji innej, Zastąp żądaną wartość w poniższej tabeli opisano **SkuName** parametru.

|Opcja replikacji  |Parametr SkuName  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |

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

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2 z magazynu geograficznie nadmiarowego do odczytu za pomocą [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create) polecenia. Należy pamiętać, że nazwa konta magazynu musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach unikatową wartość:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2, za pomocą opcji replikacji innej, Zastąp żądaną wartość w poniższej tabeli opisano **jednostki sku** parametru.

|Opcja replikacji  |parametr sku  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Aby wdrożyć szablon usługi Resource Manager, aby utworzyć konto magazynu, można użyć programu Azure Powershell lub wiersza polecenia platformy Azure. Do szablonu używanego w tym artykule pochodzą [szablony szybkiego startu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Aby uruchomić skrypty, zaznacz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**.

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

Aby dowiedzieć się, jak utworzyć szablony, zobacz:

- [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/).
- [Odwołanie do szablonu kont magazynu](/azure/templates/microsoft.storage/allversions).
- [Przykłady szablonów konta magazynu dodatkowego](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Aby uzyskać więcej informacji na temat dostępnych opcji replikacji, zobacz [Storage replication options (Opcje replikacji danych usługi Storage)](storage-redundancy.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk **Więcej** (**...** ) po prawej stronie listy.
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

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, należy użyć programu Azure PowerShell lub wiersza polecenia platformy Azure.

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono konto magazynu w warstwie standardowa ogólnego przeznaczenia w wersji 2. Aby Dowiedz się, jak przekazywać i pobierać obiekty BLOB, do i z konta magazynu, przejdź do jednego z przewodników Szybki Start z magazynu obiektów Blob.

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
