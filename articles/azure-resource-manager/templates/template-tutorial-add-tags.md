---
title: Samouczek - dodawanie tagów do zasobów w szablonie
description: Dodaj tagi do zasobów, które można wdrożyć w szablonie usługi Azure Resource Manager. Tagi umożliwiają logiczne organizowanie zasobów.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 748a32d0ea8bfb0f23a99ce99d0aaf051118bc19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369876"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Samouczek: Dodawanie tagów w szablonie ARM

W tym samouczku dowiesz się, jak dodawać tagi do zasobów w szablonie usługi Azure Resource Manager (ARM). [Tagi](../management/tag-resources.md) ułatwiają logiczne organizowanie zasobów. Wartości tagów są wyświetlane w raportach kosztów. Ten samouczek trwa **8 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka na temat szablonów szybkiego startu,](template-tutorial-quickstart-template.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Poprzedni szablon wdrożył konto magazynu, plan usługi app service i aplikację sieci web.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Po wdrożeniu tych zasobów może być konieczne śledzenie kosztów i znajdowanie zasobów należących do kategorii. Możesz dodać tagi, aby pomóc rozwiązać te problemy.

## <a name="add-tags"></a>Dodawanie tagów

Oznaczasz zasoby, aby dodać wartości, które ułatwiają identyfikację ich użycia. Na przykład można dodać tagi, które wyświetlają listę środowiska i projektu. Można dodać tagi identyfikujące centrum kosztów lub zespół, który jest właścicielem zasobu. Dodaj wszystkie wartości, które mają sens dla twojej organizacji.

W poniższym przykładzie wyróżniono zmiany w szablonie. Skopiuj cały plik i zastąp szablon jego zawartością.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Nadszedł czas, aby wdrożyć szablon i spojrzeć na wyniki.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Wybierz jeden z zasobów, takich jak zasób konta magazynu. Widać, że ma teraz tagi.

   ![Pokaż znaczniki](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano tagi do zasobów. W następnym samouczku dowiesz się, jak używać plików parametrów, aby uprościć przekazywanie wartości do szablonu.

> [!div class="nextstepaction"]
> [Użyj pliku parametrów](template-tutorial-use-parameter-file.md)
