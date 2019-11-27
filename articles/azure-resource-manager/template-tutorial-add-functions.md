---
title: Samouczek — Dodawanie funkcji szablonu
description: Dodaj funkcje szablonu do szablonu Azure Resource Manager, aby skonstruować wartości.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 03a38178ec66c1c1a10934975d20778369d80dbe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405958"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Samouczek: Dodawanie funkcji szablonu do szablonu Menedżer zasobów

W tym samouczku dowiesz się, jak dodać [funkcje szablonu](resource-group-template-functions.md) do szablonu. Funkcje służą do dynamicznego konstruowania wartości. Oprócz tych funkcji szablonu udostępnianych przez system można także tworzyć [funkcje zdefiniowane przez użytkownika](./template-user-defined-functions.md). Ukończenie tego samouczka trwa **7 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego parametrów](template-tutorial-add-parameters.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

Lokalizacja konta magazynu jest trwale kodowana dla **regionu Wschodnie stany USA**. Jednak może być konieczne wdrożenie konta magazynu w innych regionach. Nastąpi ponowne wystawienie problemu związanego z brakiem możliwości elastyczności szablonu. Można dodać parametr do lokalizacji, ale byłoby to doskonałe, jeśli jego wartość domyślna była większa niż tylko zakodowana wartość.

## <a name="use-function"></a>Use — funkcja

Po ukończeniu poprzedniego samouczka w tej serii funkcja została już użyta. Po dodaniu **"[Parameters (" storagename ")]"** użyto funkcji [Parameters](resource-group-template-functions-deployment.md#parameters) . Nawiasy wskazują, że składnia wewnątrz nawiasów jest [wyrażeniem szablonu](template-expressions.md). Menedżer zasobów rozpoznaje składnię zamiast traktować ją jako wartość literału.

Funkcje zwiększają elastyczność szablonu przez dynamiczne pobieranie wartości podczas wdrażania. W tym samouczku użyjesz funkcji, aby pobrać lokalizację grupy zasobów używanej do wdrożenia.

Poniższy przykład wyróżnia zmiany w celu dodania parametru o nazwie **Location**.  Wartość domyślna parametru wywołuje funkcję [ResourceManager](resource-group-template-functions-resource.md#resourcegroup) . Ta funkcja zwraca obiekt z informacjami o grupie zasobów używanej do wdrożenia. Jedną z właściwości obiektu jest właściwość Location. W przypadku użycia wartości domyślnej Lokalizacja konta magazynu ma tę samą lokalizację co grupa zasobów. Zasoby w grupie zasobów nie muszą udostępniać tej samej lokalizacji. W razie konieczności można także podać inną lokalizację.

Skopiuj cały plik i Zastąp jego zawartość.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Wdrażanie szablonu

W poprzednich samouczkach utworzono konto magazynu w regionie Wschodnie stany USA, ale Grupa zasobów została utworzona w środkowych stanach USA. W tym samouczku konto magazynu zostanie utworzone w tym samym regionie, w którym znajduje się grupa zasobów. Użyj wartości domyślnej dla lokalizacji, więc nie musisz podawać tej wartości parametru. Musisz podać nową nazwę konta magazynu, ponieważ tworzysz konto magazynu w innej lokalizacji. Na przykład użyj **Store2** jako prefiksu zamiast **store1**.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Zobaczysz, że zasób konta magazynu został wdrożony i ma tę samą lokalizację co grupa zasobów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto funkcji podczas definiowania wartości domyślnej dla parametru. W tej serii samouczków będziesz używać funkcji. Na końcu serii dodasz funkcje do każdej sekcji szablonu.

> [!div class="nextstepaction"]
> [Dodaj zmienne](template-tutorial-add-variables.md)
