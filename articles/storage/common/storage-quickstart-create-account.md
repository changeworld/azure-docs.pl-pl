---
title: 'Szybki start: Tworzenie konta magazynu — Azure Storage'
description: W tym samouczku Szybki start nauczysz się, jak utworzyć konto magazynu przy użyciu witryny Azure Portal, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Konto usługi Azure Storage zapewnia unikatową przestrzeń nazw na platformie Microsoft Azure do przechowywania i umożliwiania dostępu do obiektów danych utworzonych w usłudze Azure Storage.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/18/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ebe23c606d95baa6c79c668fc929177c8bc37e44
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862951"
---
# <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W tym samouczku Szybki start nauczysz się, jak utworzyć konto magazynu przy użyciu witryny [Azure Portal](https://portal.azure.com/), programu [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Do tego przewodnika Szybki start jest wymagany moduł Az programu Azure PowerShell w wersji 0.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz zalogować się do platformy Azure i uruchomić polecenia interfejsu wiersza polecenia platformy Azure w jeden z dwóch sposobów:

- Polecenia interfejsu wiersza polecenia platformy Azure możesz uruchamiać z poziomu witryny Azure Portal w usłudze Azure Cloud Shell 
- Możesz zainstalować interfejs wiersza polecenia i uruchamiać jego komendy lokalnie  

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu witryny Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ten przycisk służy do uruchamiania interaktywnej powłoki, której możesz używać do wykonywania kroków opisanych w tym przewodniku Szybki start:

[![Zrzut ekranu przedstawiający okno usługi Cloud Shell w portalu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać go lokalnie. Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Brak.

---

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount`, a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić usługę Azure Cloud Shell, zaloguj się do witryny [Azure Portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie logowania:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

ND

---

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Teraz możesz przystąpić do tworzenia konta magazynu.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym przewodniku Szybki start pokazano, jak utworzyć nową grupę zasobów. 

Konto magazynu **ogólnego przeznaczenia, wersja 2** zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. W tym samouczku przedstawiono tworzenie konta magazynu ogólnego przeznaczenia, wersja 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne.   

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw użyj polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) w programie PowerShell, aby utworzyć nową grupę zasobów: 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location 
```

Jeśli nie masz pewności, który region należy określić dla parametru `-Location`, za pomocą polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji:

```powershell
Get-AzLocation | select Location 
$location = "westus"
```

Następnie utwórz konto magazynu ogólnego przeznaczenia, wersja 2 z magazynem lokalnie nadmiarowym (LRS). Użyj polecenia [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount): 

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 przy użyciu magazynu strefowo nadmiarowego (ZRS) (wersja zapoznawcza), magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS), zastąp żądaną wartość w poniższej tabeli dla parametru **SkuName**. 

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
    --name storage-quickstart-resource-group \
    --location westus
```

Jeśli nie masz pewności, który region należy określić dla parametru `--location`, za pomocą polecenia [az account list-locations](/cli/azure/account#az_account_list) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Następnie utwórz konto magazynu ogólnego przeznaczenia, wersja 2 z magazynem lokalnie nadmiarowym. Użyj polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create):

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z magazynu strefowo nadmiarowego (wersja zapoznawcza magazynu ZRS), magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS), zastąp żądaną wartość w poniższej tabeli dla parametru **sku**. 

|Opcja replikacji  |parametr sku  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Aby wdrożyć szablon usługi Resource Manager, aby utworzyć konto magazynu, można użyć programu Azure Powershell lub wiersza polecenia platformy Azure. Szablon używany w tym przewodniku Szybki Start jest z [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Aby uruchomić skrypty, zaznacz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**.

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

Jeśli chcesz oczyścić zasoby utworzone w tym przewodniku Szybki start, możesz po prostu usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

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
az group delete --name storage-quickstart-resource-group
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

W tym przewodniku Szybki start zostało utworzone standardowe konto magazynu ogólnego przeznaczenia w wersji 2. Aby dowiedzieć się, jak przekazywać i pobierać obiekty blob z konta magazynu i do niego, przejdź do przewodnika Szybki start dotyczącego magazynu obiektów blob.

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
