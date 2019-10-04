---
title: Samouczek — Dodawanie tagów do zasobów w szablonie Azure Resource Manager
description: Dodawanie tagów do zasobów wdrażanych w szablonie Azure Resource Manager. Tagi pozwalają logicznie organizować zasoby.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8c882d819771473db2cf1c91fb39912883d144d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963744"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Samouczek: Dodawanie tagów do szablonu Menedżer zasobów

W tym samouczku dowiesz się, jak dodać tagi do zasobów w szablonie. [Tagi](resource-group-using-tags.md) ułatwiają logiczne organizowanie zasobów. Wartości tagów są wyświetlane w raportach kosztów. Ten samouczek trwa **8 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego szablonów szybkiego startu](template-tutorial-quickstart-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Przejrzyj szablon

Poprzedni szablon został wdrożony, konto magazynu, plan App Service i aplikacja internetowa.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

Po wdrożeniu tych zasobów może być konieczne śledzenie kosztów i znajdowanie zasobów należących do kategorii. Możesz dodać tagi, aby pomóc w rozwiązaniu tych problemów.

## <a name="add-tags"></a>Dodaj Tagi

Oznacz zasoby, aby dodać wartości, które ułatwiają identyfikację ich użycia. Na przykład można dodać tagi, które oznaczają środowisko i projekt. Można dodać tagi, które identyfikują centrum kosztów lub zespół, który jest właścicielem zasobu. Dodaj wszystkie wartości, które mają sens dla organizacji.

Poniższy przykład wyróżnia zmiany w szablonie. Skopiuj cały plik i Zastąp jego zawartość.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-template"></a>Wdróż szablon

Czas na wdrożenie szablonu i przeszukanie zwracanej wartości.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Wybierz jeden z zasobów, na przykład zasób konta magazynu. Zobaczysz, że ma teraz Tagi.

   ![Pokaż Tagi](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** .
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano znaczniki do zasobów. W następnym samouczku dowiesz się, jak używać plików parametrów, aby uprościć przekazywanie wartości do szablonu.

> [!div class="nextstepaction"]
> [Użyj pliku parametrów](template-tutorial-use-parameter-file.md)