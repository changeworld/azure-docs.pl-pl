---
title: Samouczek - dodawanie wyjść do szablonu
description: Dodaj dane wyjściowe do szablonu usługi Azure Resource Manager, aby uprościć składnię.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2ee1a2c7037bde68b7858b57a03c78bd2016ff1c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743552"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Samouczek: Dodawanie wyjść do szablonu ARM

W tym samouczku dowiesz się, jak zwrócić wartość z szablonu usługi Azure Resource Manager (ARM). Dane wyjściowe są używane, gdy potrzebna jest wartość z wdrożonego zasobu. Ten samouczek trwa **7 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy [ukończenie samouczka o zmiennych](template-tutorial-add-variables.md), ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Na końcu poprzedniego samouczka szablon miał następujący JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Wdraża konto magazynu, ale nie zwraca żadnych informacji o koncie magazynu. Może być konieczne przechwycenie właściwości z nowego zasobu, aby były one dostępne później w celach informacyjnych.

## <a name="add-outputs"></a>Dodawanie wyjść

Można użyć wyjść, aby zwrócić wartości z szablonu. Na przykład może być przydatne, aby uzyskać punkty końcowe dla nowego konta magazynu.

W poniższym przykładzie wyróżniono zmianę szablonu, aby dodać wartość wyjściową. Skopiuj cały plik i zastąp szablon jego zawartością.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Istnieje kilka ważnych elementów, aby pamiętać o wartości wyjściowej, którą dodałeś.

Typ zwracanej wartości jest ustawiany na **obiekt**, co oznacza, że zwraca obiekt JSON.

Używa funkcji [odwołania,](template-functions-resource.md#reference) aby uzyskać stan środowiska wykonawczego konta magazynu. Aby uzyskać stan środowiska wykonawczego zasobu, należy przekazać w nazwie lub identyfikator zasobu. W takim przypadku należy użyć tej samej zmiennej, która została użyta do utworzenia nazwy konta magazynu.

Na koniec zwraca **właściwość primaryEndpoints** z konta magazynu

## <a name="deploy-template"></a>Wdrażanie szablonu

Gotowe do wdrożenia szablonu i przyjrzeć się zwróconej wartości.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

W danych wyjściowych dla polecenia wdrożenia zobaczysz obiekt podobny do poniższego przykładu tylko wtedy, gdy dane wyjściowe są w formacie JSON:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj przełącznika **debugowania** z poleceniem wdrażania, aby wyświetlić dzienniki debugowania.  Można również użyć pełnego **przełącznika,** aby wyświetlić pełne dzienniki debugowania.

## <a name="review-your-work"></a>Przejrzyj swoją pracę

Zrobiłeś wiele w ciągu ostatnich sześciu tutoriali. Poświęćmy chwilę, aby przejrzeć to, co zrobiłeś. Utworzono szablon z parametrami, które są łatwe do zapewnienia. Szablon jest wielokrotnego użytku w różnych środowiskach, ponieważ umożliwia dostosowanie i dynamicznie tworzy potrzebne wartości. Zwraca również informacje o koncie magazynu, które można użyć w skrypcie.

Teraz przyjrzyjmy się grupie zasobów i historii wdrażania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. W zależności od czynności, które wykonasz, powinien mieć co najmniej jedno i być może kilka kont magazynu w grupie zasobów.
1. W historii powinno być również kilka udanych wdrożeń. Wybierz to łącze.

   ![Wybierz wdrożenia](./media/template-tutorial-add-outputs/select-deployments.png)

1. Wszystkie wdrożenia są widoczne w historii. Wybierz wdrożenie o nazwie **addoutputs**.

   ![Pokaż historię wdrażania](./media/template-tutorial-add-outputs/show-history.png)

1. Można przejrzeć dane wejściowe.

   ![Pokaż dane wejściowe](./media/template-tutorial-add-outputs/show-inputs.png)

1. Można przejrzeć dane wyjściowe.

   ![Pokaż dane wyjściowe](./media/template-tutorial-add-outputs/show-outputs.png)

1. Możesz przejrzeć szablon.

   ![Pokaż szablon](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano wartość zwracaną do szablonu. W następnym samouczku dowiesz się, jak wyeksportować szablon i użyć części tego wyeksportowanego szablonu w szablonie.

> [!div class="nextstepaction"]
> [Użyj wyeksportowanego szablonu](template-tutorial-export-template.md)
