---
title: Samouczek - dodawanie parametrów do szablonu
description: Dodaj parametry do szablonu usługi Azure Resource Manager, aby był wielokrotnego użycia.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de7ec961672db2f3120e00f1a42b33f71e7ab092
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437829"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Samouczek: Dodawanie parametrów do szablonu ARM

W [poprzednim samouczku](template-tutorial-add-resource.md)opisano, jak dodać konto magazynu do szablonu i wdrożyć je. W tym samouczku dowiesz się, jak ulepszyć szablon usługi Azure Resource Manager (ARM), dodając parametry. Ten samouczek trwa około **14 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka na temat zasobów,](template-tutorial-add-resource.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Na końcu poprzedniego samouczka szablon miał następujący JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Być może zauważyłeś, że wystąpił problem z tym szablonem. Nazwa konta magazynu jest zakodowany na czas. Tego szablonu można używać tylko do wdrażania tego samego konta magazynu za każdym razem. Aby wdrożyć konto magazynu o innej nazwie, należy utworzyć nowy szablon, który oczywiście nie jest praktycznym sposobem automatyzacji wdrożeń.

## <a name="make-template-reusable"></a>Nadaj szablonowi wielokrotnego pożycie

Aby szablon był wielokrotnego użytku, dodajmy parametr, którego można użyć do przekazania nazwy konta magazynu. Wyróżniony JSON w poniższym przykładzie pokazuje, co zmieniło się w szablonie. Parametr **storageName** jest identyfikowany jako ciąg. Maksymalna długość jest ustawiona na 24 znaki, aby zapobiec wszelkie nazwy, które są zbyt długie.

Skopiuj cały plik i zastąp szablon jego zawartością.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Zastosujmy szablon. W poniższym przykładzie wdraża szablon z interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Należy zauważyć, że podasz nazwę konta magazynu jako jedną z wartości w poleceniu wdrażania. Dla nazwy konta magazynu podaj taką samą nazwę, która została użyta w poprzednim samouczku.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Opis aktualizacji zasobów

W poprzedniej sekcji wdrożono konto magazynu o tej samej nazwie, które zostało utworzone wcześniej. Możesz się zastanawiać, jak zasób ma wpływ na ponowne wdrożenie.

Jeśli zasób już istnieje i nie zostanie wykryta żadna zmiana we właściwościach, nie zostanie podjęta żadna akcja. Jeśli zasób już istnieje i właściwość została zmieniona, zasób jest aktualizowany. Jeśli zasób nie istnieje, jest tworzony.

Ten sposób obsługi aktualizacji oznacza, że szablon może zawierać wszystkie zasoby potrzebne do rozwiązania platformy Azure. Można bezpiecznie ponownie wdrożyć szablon i wiedzieć, że zasoby są zmieniane lub tworzone tylko w razie potrzeby. Na przykład po dodaniu plików do konta magazynu można ponownie wdrożyć konto magazynu bez utraty tych plików.

## <a name="customize-by-environment"></a>Dostosowywanie według środowiska

Parametry umożliwiają dostosowanie wdrożenia poprzez podanie wartości dopasowanych do danego środowiska. Na przykład można przekazać różne wartości na podstawie tego, czy wdrażasz w środowisku w celu programowania, testowania i produkcji.

Poprzedni szablon zawsze wdrażał konto magazynu Standard_LRS. Można elastyczność do wdrażania różnych jednostek SKU w zależności od środowiska. W poniższym przykładzie przedstawiono zmiany, aby dodać parametr dla jednostki SKU. Skopiuj cały plik i wklej szablon.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

Parametr **storageSKU** ma wartość domyślną. Ta wartość jest używana, gdy wartość nie jest określona podczas wdrażania. Posiada również listę dozwolonych wartości. Te wartości są zgodne z wartościami, które są potrzebne do utworzenia konta magazynu. Nie chcesz, aby użytkownicy szablonu przechodzili w jednostkach SKU, które nie działają.

## <a name="redeploy-template"></a>Ponowne wdrażanie szablonu

Możesz przystąpić do ponownego wdrożenia. Ponieważ domyślna jednostka SKU jest ustawiona na **Standard_LRS,** nie trzeba podawać wartości dla tego parametru.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj przełącznika **debugowania** z poleceniem wdrażania, aby wyświetlić dzienniki debugowania.  Można również użyć pełnego **przełącznika,** aby wyświetlić pełne dzienniki debugowania.

Aby zobaczyć elastyczność szablonu, zastosuj ponownie. Tym razem parametr SKU ma **Standard_GRS**. Możesz przekazać nową nazwę, aby utworzyć inne konto magazynu, lub użyć tej samej nazwy, aby zaktualizować istniejące konto magazynu. Obie opcje działają.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Na koniec uruchommy jeszcze jeden test i zobaczmy, co się stanie po przejściu w jednostce SKU, która nie jest jedną z dozwolonych wartości. W takim przypadku testujemy scenariusz, w którym użytkownik szablonu uważa, że **podstawowe** jest jednym z jednostek SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Polecenie kończy się niepowodzeniem natychmiast z komunikatem o błędzie, który określa, które wartości są dozwolone. Menedżer zasobów identyfikuje błąd przed rozpoczęciem wdrażania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Ulepszono szablon utworzony w [pierwszym samouczku,](template-tutorial-create-first-template.md) dodając parametry. W następnym samouczku dowiesz się o funkcjach szablonu.

> [!div class="nextstepaction"]
> [Dodawanie funkcji szablonu](template-tutorial-add-functions.md)