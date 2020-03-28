---
title: Samouczek - użyj pliku parametrów do wdrożenia szablonu
description: Użyj plików parametrów, które zawierają wartości używane do wdrażania szablonu usługi Azure Resource Manager.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0f7b966d4241716d71779e966de5d408711e4543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371792"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Samouczek: Wdrażanie szablonu ARM za pomocą plików parametrów

W tym samouczku dowiesz się, jak używać [plików parametrów](parameter-files.md) do przechowywania wartości, które należy przekazać podczas wdrażania. W poprzednich samouczkach użyto parametrów wbudowanych z poleceniem wdrożenia. To podejście pracował do testowania szablonu usługi Azure Resource Manager (ARM), ale podczas automatyzacji wdrożeń może być łatwiejsze do przekazania zestaw wartości dla środowiska. Pliki parametrów ułatwiają pakowanie wartości parametrów dla określonego środowiska. W tym samouczku utworzysz pliki parametrów dla środowisk deweloperskich i produkcyjnych. Trwa około **12 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka na temat tagów,](template-tutorial-add-tags.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Szablon ma wiele parametrów, które można podać podczas wdrażania. Na końcu poprzedniego samouczka szablon wyglądał następująco:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Ten szablon działa dobrze, ale teraz chcesz łatwo zarządzać parametrami, które przekazujesz dla szablonu.

## <a name="add-parameter-files"></a>Dodawanie plików parametrów

Pliki parametrów są plikami JSON o strukturze podobnej do szablonu. W pliku należy podać wartości parametrów, które mają być przełożeni podczas wdrażania.

W programie VS Code utwórz nowy plik z następującą zawartością. Zapisz plik o nazwie **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Ten plik jest plikiem parametrów dla środowiska programistycznego. Należy zauważyć, że używa Standard_LRS dla konta magazynu, nazwy zasobów z prefiksem **dev** i ustawia tag **Środowisko** **dev**.

Ponownie utwórz nowy plik z następującą zawartością. Zapisz plik o nazwie **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Ten plik jest plikiem parametrów dla środowiska produkcyjnego. Należy zauważyć, że używa Standard_GRS dla konta magazynu, nazwy zasobów z prefiksem **contoso** i ustawia tag **Środowisko** **do produkcji**. W rzeczywistym środowisku produkcyjnym należy również użyć usługi aplikacji z jednostką SKU inną niż bezpłatna, ale nadal będziemy używać tej jednostki SKU w tym samouczku.

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby wdrożyć szablon.

Jako ostateczny test szablonu utwórzmy dwie nowe grupy zasobów. Jeden dla środowiska deweloperskiego i jeden dla środowiska produkcyjnego.

Najpierw wdrożymy w środowisku deweloperskim.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Teraz wdrożymy w środowisku produkcyjnym.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupy zasobów z witryny Azure Portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Zobaczysz dwie nowe grupy zasobów wdrożone w tym samouczku.
1. Wybierz jedną z grup zasobów i wyświetl wdrożone zasoby. Należy zauważyć, że są one zgodne z wartościami określonymi w pliku parametrów dla tego środowiska.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**. Jeśli ta seria została ukończona, masz trzy grupy zasobów do usunięcia - myResourceGroup, myResourceGroupDev i myResourceGroupProd.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Gratulacje, to wprowadzenie zostało ukończone do wdrażania szablonów na platformie Azure. Daj nam znać, jeśli masz jakieś uwagi i sugestie w sekcji opinii. Dziękujemy.

Następna seria samouczków zawiera więcej szczegółów na temat wdrażania szablonów.

> [!div class="nextstepaction"]
> [Wdrażanie szablonu lokalnego](./deployment-tutorial-local-template.md)
