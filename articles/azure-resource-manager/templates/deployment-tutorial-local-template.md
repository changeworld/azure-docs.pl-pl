---
title: Samouczek — wdrażanie szablonu lokalnego usługi Azure Resource Manager
description: Dowiedz się, jak wdrożyć szablon usługi Azure Resource Manager na komputerze lokalnym
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8e3eb62fa52caeaa63808b6b9ea199bdff5c4da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082258"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Samouczek: Wdrażanie szablonu lokalnego usługi Azure Resource Manager

Dowiedz się, jak wdrożyć szablon usługi Azure Resource Manager na komputerze lokalnym. Trwa około **8 minut.**

Ten samouczek jest pierwszym z serii. W miarę postępów w serii, modułowość szablonu przez utworzenie szablonu połączonego, przechowywania połączonego szablonu na koncie magazynu i zabezpieczania połączonego szablonu przy użyciu tokenu Sygnatury dostępu Współdzielonego oraz dowiedzenia się, jak utworzyć potok DevOp w celu wdrożenia szablonów. Ta seria koncentruje się na wdrażaniu szablonów.  Jeśli chcesz nauczyć się tworzenia szablonów, zobacz [samouczki dla początkujących](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Uzyskaj narzędzia

Zacznijmy od upewnienia się, że masz narzędzia potrzebne do wdrażania szablonów.

### <a name="command-line-deployment"></a>Wdrażanie wiersza polecenia

Aby wdrożyć szablon, musisz wdrożyć szablon w programie Azure PowerShell lub azure cli. Instrukcje instalacji można znaleźć na:

- [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)

Po zainstalowaniu interfejsu wiersza polecenia programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure upewnij się, że logujesz się po raz pierwszy. Aby uzyskać pomoc, zobacz [Logowanie — program PowerShell](/powershell/azure/install-az-ps#sign-in) lub [Logowanie — Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Edytor (opcjonalnie)

Szablony są plikami JSON. Aby przeglądać / edytować szablony, potrzebujesz dobrego edytora JSON. Zalecamy program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów. Jeśli chcesz zainstalować te narzędzia, zobacz [Tworzenie szablonów usługi Azure Resource Manager za pomocą programu Visual Studio](use-vs-code-to-create-template.md).

## <a name="review-template"></a>Szablon recenzji

Szablon użyty w tym samouczku jest podobny do szablonu użytego w [samouczku na temat szablonów Szybki start](template-tutorial-quickstart-template.md). Jeśli jesteś zainteresowany tworzeniem szablonu, możesz przejść przez ten samouczek. Jednak nie jest to wymagane do ukończenia tego samouczka.

Szablon wdraża konto magazynu, plan usługi aplikacji i aplikację sieci web.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i mniejszych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu jest nazwą projektu z dołączenym "magazynem", a nazwa projektu musi zawierać od 3 do 11 znaków. Dlatego nazwa projektu musi spełniać wymagania dotyczące nazwy konta magazynu i ma mniej niż 11 znaków.

Zapisz kopię szablonu na komputerze lokalnym z rozszerzeniem .json, na przykład azuredeploy.json. Ten szablon można wdrożyć w dalszej części samouczka.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby rozpocząć pracę z platformą Azure PowerShell/Azure CLI w celu wdrożenia szablonu, zaloguj się przy użyciu poświadczeń platformy Azure.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az login
```

---

Jeśli masz wiele subskrypcji platformy Azure, wybierz subskrypcję, której chcesz użyć:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Podczas wdrażania szablonu należy określić grupę zasobów, która będzie zawierać zasoby. Przed uruchomieniem polecenia wdrażania utwórz grupę zasobów za pomocą interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Wybierz karty w poniższej sekcji kodu, aby wybrać między platformą Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Przykłady interfejsu wiersza polecenia w tym artykule są zapisywane dla powłoki Bash.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj jednej lub obu opcji wdrażania, aby wdrożyć szablon.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Aby dowiedzieć się więcej na temat wdrażania szablonu przy użyciu programu Azure PowerShell, zobacz [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Aby dowiedzieć się więcej na temat wdrażania szablonu przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure.](./deploy-cli.md)

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Dowiedzialiście się, jak wdrożyć szablon lokalny. W następnym samouczku należy oddzielić szablon na szablon główny i szablon połączony, a także dowiedzieć się, jak przechowywać i zabezpieczać połączony szablon.

> [!div class="nextstepaction"]
> [Wdrażanie szablonu połączonego](./deployment-tutorial-linked-template.md)
