---
title: Samouczek - dodaj zmienną do szablonu
description: Dodaj zmienne do szablonu usługi Azure Resource Manager, aby uprościć składnię.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b1df86e5b593edec784de21e21a4399274d820bb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411689"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Samouczek: Dodawanie zmiennych do szablonu ARM

W tym samouczku dowiesz się, jak dodać zmienną do szablonu usługi Azure Resource Manager (ARM). Zmienne upraszczają szablony, umożliwiając jednocześnie pisanie wyrażenia i ponowne użycie go w całym szablonie. Ten samouczek trwa **7 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka na temat funkcji,](template-tutorial-add-functions.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Na końcu poprzedniego samouczka szablon miał następujący JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

Parametr nazwy konta magazynu jest trudny do użycia, ponieważ trzeba podać unikatową nazwę. Jeśli ukończyłeś wcześniejsze samouczki z tej serii, prawdopodobnie masz dość zgadywania unikatowej nazwy. Rozwiązać ten problem, dodając zmienną, która tworzy unikatową nazwę dla konta magazynu.

## <a name="use-variable"></a>Użyj zmiennej

W poniższym przykładzie przedstawiono zmiany, aby dodać zmienną do szablonu, który tworzy unikatową nazwę konta magazynu. Skopiuj cały plik i zastąp szablon jego zawartością.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Należy zauważyć, że zawiera zmienną o nazwie **uniqueStorageName**. Ta zmienna używa czterech funkcji do konstruowania wartości ciągu.

Znasz już funkcję [parametrów,](template-functions-deployment.md#parameters) więc nie będziemy jej badać.

Znasz również funkcję [resourceGroup.](template-functions-resource.md#resourcegroup) W takim przypadku otrzymasz właściwość **id** zamiast właściwości **lokalizacji,** jak pokazano w poprzednim samouczku. Właściwość **identyfikator** zwraca pełny identyfikator grupy zasobów, w tym identyfikator subskrypcji i nazwę grupy zasobów.

Funkcja [uniqueString](template-functions-string.md#uniquestring) tworzy wartość mieszania 13 znaków. Zwracana wartość jest określana przez parametry, które przekazujesz. W tym samouczku używasz identyfikatora grupy zasobów jako danych wejściowych dla wartości skrótu. Oznacza to, że można wdrożyć ten szablon do różnych grup zasobów i uzyskać inną unikatową wartość ciągu. Jednak można uzyskać taką samą wartość, jeśli można wdrożyć do tej samej grupy zasobów.

Funkcja [concat](template-functions-string.md#concat) przyjmuje wartości i łączy je. Dla tej zmiennej pobiera ciąg z parametru i ciąg z uniqueString funkcji i łączy je w jeden ciąg.

**Parametr storagePrefix** umożliwia przekazywanie w prefiksie, który ułatwia identyfikowanie kont magazynu. Można utworzyć własną konwencję nazewnictwa, która ułatwia identyfikowanie kont magazynu po wdrożeniu z długiej listy zasobów.

Na koniec należy zauważyć, że nazwa magazynu jest teraz ustawiona na zmienną zamiast parametru.

## <a name="deploy-template"></a>Wdrażanie szablonu

Zastosujmy szablon. Wdrażanie tego szablonu jest łatwiejsze niż poprzednie szablony, ponieważ podajesz tylko prefiks nazwy magazynu.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj przełącznika **debugowania** z poleceniem wdrażania, aby wyświetlić dzienniki debugowania.  Można również użyć pełnego **przełącznika,** aby wyświetlić pełne dzienniki debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Widać, że zasób konta magazynu został wdrożony. Nazwa konta magazynu jest **przechowywana** plus ciąg losowych znaków.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano zmienną, która tworzy unikatową nazwę konta magazynu. W następnym samouczku zwracasz wartość z wdrożonego konta magazynu.

> [!div class="nextstepaction"]
> [Dodawanie wyjść](template-tutorial-add-outputs.md)
