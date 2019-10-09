---
title: Samouczek — używanie pliku parametrów w celu ułatwienia wdrażania szablonu Azure Resource Manager
description: Użyj plików parametrów, które zawierają wartości używane do wdrażania szablonu Azure Resource Manager.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa29ea3d2f6edbbb016ce5c0c74415a5e765e85a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177553"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Samouczek: używanie plików parametrów do wdrożenia szablonu Menedżer zasobów

W ramach tego samouczka nauczysz się używać [plików parametrów](resource-manager-parameter-files.md) do przechowywania wartości przekazywanych podczas wdrażania. W poprzednich samouczkach użyto parametrów wbudowanych z poleceniem wdrożenia. Takie podejście działało do testowania szablonu, ale w przypadku automatyzowania wdrożeń może być łatwiejsze przekazanie zestawu wartości dla danego środowiska. Pliki parametrów ułatwiają pakowanie wartości parametrów dla określonego środowiska. W tym samouczku utworzysz pliki parametrów dla środowisk deweloperskich i produkcyjnych. Ukończenie może potrwać około **12 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego tagów](template-tutorial-add-tags.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Przejrzyj szablon

Szablon zawiera wiele parametrów, które można podać podczas wdrażania. Na końcu poprzedniego samouczka szablon wyglądał następująco:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Ten szablon działa dobrze, ale teraz chcesz łatwo zarządzać parametrami przekazywanymi do szablonu.

## <a name="add-parameter-files"></a>Dodaj pliki parametrów

Pliki parametrów to pliki JSON ze strukturą podobną do szablonu. W pliku podaj wartości parametrów, które mają być przekazywane podczas wdrażania.

W VS Code Utwórz nowy plik z następującą zawartością. Zapisz plik o nazwie **azuredeploy. Parameters. dev. JSON**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Ten plik jest plikiem parametrów środowiska deweloperskiego. Zwróć uwagę, że używa Standard_LRS dla konta magazynu, nazw zasobów z prefiksem **dev** i ustawia tag **środowiska** na **dev**.

Utwórz nowy plik o następującej zawartości. Zapisz plik o nazwie **azuredeploy. Parameters. prod. JSON**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Ten plik jest plikiem parametrów dla środowiska produkcyjnego. Zwróć uwagę, że używa Standard_GRS dla konta magazynu, nazw zasobów z prefiksem **contoso** i ustawia tag **środowiska** na **produkcyjny**. W rzeczywistym środowisku produkcyjnym warto również używać usługi App Service z jednostką SKU inną niż bezpłatna, ale będziemy nadal korzystać z tej jednostki SKU dla tego samouczka.

## <a name="deploy-the-template"></a>Wdróż szablon

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon.

Jako ostatni test szablonu Utwórzmy dwie nowe grupy zasobów. Jeden dla środowiska deweloperskiego i jeden dla środowiska produkcyjnego.

Po pierwsze wdrożenie zostanie wdrożone w środowisku deweloperskim.

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Teraz wdrażamy je w środowisku produkcyjnym.

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Możesz zweryfikować wdrożenie, przeeksplorowanie grup zasobów z Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Zobaczysz dwie nowe grupy zasobów wdrożone w tym samouczku.
1. Wybierz pozycję Grupa zasobów i Wyświetl wdrożone zasoby. Zwróć uwagę, że są one zgodne z wartościami określonymi w pliku parametrów dla danego środowiska.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** . Jeśli ta seria została ukończona, istnieją trzy grupy zasobów do usunięcia — zasobu, myResourceGroupDev i myResourceGroupProd.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Gratulacje, wprowadzenie do wdrożenia szablonów na platformie Azure. Daj nam znać, jeśli masz jakieś komentarze i sugestie w sekcji Opinie. Za!

Możesz teraz przejść do bardziej zaawansowanych koncepcji dotyczących szablonów. Następny samouczek zawiera więcej szczegółów dotyczących korzystania z dokumentacji szablonów referencyjnych, aby ułatwić Definiowanie zasobów do wdrożenia.

> [!div class="nextstepaction"]
> [Użyj odwołania do szablonu](resource-manager-tutorial-create-encrypted-storage-accounts.md)
