---
title: Samouczek - dodawanie funkcji szablonu
description: Dodaj funkcje szablonu do szablonu usługi Azure Resource Manager, aby utworzyć wartości.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e4984b286bf031b66272919a487d09a90f972ce0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410974"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Samouczek: Dodawanie funkcji szablonu do szablonu ARM

W tym samouczku dowiesz się, jak dodać [funkcje szablonu](template-functions.md) do szablonu usługi Azure Resource Manager (ARM). Funkcje są używane do dynamicznego konstruowania wartości. Oprócz tych funkcji szablonów dostarczanych przez system można również tworzyć [funkcje zdefiniowane przez użytkownika](./template-user-defined-functions.md). Ten samouczek trwa **7 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka na temat parametrów,](template-tutorial-add-parameters.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Na końcu poprzedniego samouczka szablon miał następujący JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Lokalizacja konta magazynu jest zakodowany na **wschodniej części STANÓW Zjednoczonych**. Jednak może być konieczne wdrożenie konta magazynu w innych regionach. Ponownie naprzestajesz przed problemem szablonu, który nie ma elastyczności. Można dodać parametr dla lokalizacji, ale byłoby świetnie, jeśli jego wartość domyślna ma większy sens niż tylko wartość zakodowane na czas.

## <a name="use-function"></a>Użyj funkcji

Jeśli ukończono poprzedni samouczek z tej serii, już użyto funkcji. Po dodaniu **"[parameters('storageName')]"** użyto funkcji [parametrów.](template-functions-deployment.md#parameters) Nawiasy wskazują, że składnia wewnątrz nawiasów jest [wyrażeniem szablonu](template-expressions.md). Menedżer zasobów rozpoznaje składnię, a nie traktuje ją jako wartość literału.

Funkcje zwiększają elastyczność szablonu, dynamicznie uzyskując wartości podczas wdrażania. W tym samouczku można użyć funkcji, aby uzyskać lokalizację grupy zasobów, których używasz do wdrożenia.

W poniższym przykładzie wyróżniono zmiany w celu dodania parametru o nazwie **location**.  Wartość domyślna parametru wywołuje funkcję [resourceGroup.](template-functions-resource.md#resourcegroup) Ta funkcja zwraca obiekt z informacjami o grupie zasobów używanej do wdrożenia. Jedną z właściwości obiektu jest właściwość lokalizacji. W przypadku użycia wartości domyślnej lokalizacja konta magazynu ma taką samą lokalizację jak grupa zasobów. Zasoby wewnątrz grupy zasobów nie muszą współużytkować tej samej lokalizacji. W razie potrzeby można również podać inną lokalizację.

Skopiuj cały plik i zastąp szablon jego zawartością.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Wdrażanie szablonu

W poprzednich samouczkach utworzono konto magazynu we wschodnich stanach USA, ale grupa zasobów została utworzona w centralnych stanach USA. W tym samouczku konto magazynu jest tworzone w tym samym regionie co grupa zasobów. Użyj wartości domyślnej dla lokalizacji, więc nie trzeba podawać tej wartości parametru. Musisz podać nową nazwę dla konta magazynu, ponieważ tworzysz konto magazynu w innej lokalizacji. Na przykład użyj **store2** jako prefiksu zamiast **store1**.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj przełącznika **debugowania** z poleceniem wdrażania, aby wyświetlić dzienniki debugowania.  Można również użyć pełnego **przełącznika,** aby wyświetlić pełne dzienniki debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Widać, że zasób konta magazynu został wdrożony i ma taką samą lokalizację jak grupa zasobów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto funkcji podczas definiowania wartości domyślnej dla parametru. W tej serii samouczków będziesz nadal korzystać z funkcji. Pod koniec serii dodasz funkcje do każdej sekcji szablonu.

> [!div class="nextstepaction"]
> [Dodawanie zmiennych](template-tutorial-add-variables.md)
