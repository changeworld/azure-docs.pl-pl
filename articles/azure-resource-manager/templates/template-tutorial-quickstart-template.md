---
title: Samouczek - Korzystanie z szablonów szybkiego startu
description: Dowiedz się, jak korzystać z szablonów szybki start platformy Azure w celu ukończenia tworzenia szablonów.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4b82e02ecc009e587b89d1fd151fd13f75a4bcf8
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408507"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Samouczek: Korzystanie z szablonów szybki startu platformy Azure

[Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium szablonów współtworzonych przez społeczność. Przykładowych szablonów można użyć w rozwoju szablonu. W tym samouczku znajdziesz definicję zasobów witryny sieci Web i dodasz ją do własnego szablonu. Trwa około **12 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka na temat eksportowanych szablonów,](template-tutorial-export-template.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Na końcu poprzedniego samouczka szablon miał następujący JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Ten szablon działa do wdrażania kont magazynu i planów usługi aplikacji, ale można dodać do niego witrynę sieci Web. Można użyć wstępnie utworzonych szablonów, aby szybko odkryć JSON wymagane do wdrażania zasobu.

## <a name="find-template"></a>Znajdź szablon

1. Otwieranie [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/)
1. W **wyszukiwarce**wprowadź **wdrożyć aplikację internetową linux**.
1. Wybierz ten o tytule **Wdrażanie podstawowej aplikacji internetowej Linux**. Jeśli masz problemy ze znalezieniem go, oto [bezpośredni link](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Wybierz **pozycję Przeglądaj w usłudze GitHub**.
1. Wybierz **azuredeploy.json**.
1. Przejrzyj szablon. W szczególności poszukaj zasobu. `Microsoft.Web/sites`

    ![Witryna szybkiego startu szablonu Menedżera zasobów](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Poprawianie istniejącego szablonu

Scal szablon szybkiego startu z istniejącym szablonem:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Nazwa aplikacji sieci web musi być unikatowa na platformie Azure. Aby zapobiec posiadaniu zduplikowanych nazw, **zmienna webAppPortalName** została zaktualizowana z **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** do **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]]"**.

Dodaj przecinek na końcu `Microsoft.Web/serverfarms` definicji, aby oddzielić `Microsoft.Web/sites` definicję zasobu od definicji.

Istnieje kilka ważnych funkcji, które należy zwrócić uwagę w tym nowym zasobie.

Zauważysz, że ma element o nazwie **dependsOn,** który jest ustawiony na plan usługi aplikacji. To ustawienie jest wymagane, ponieważ plan usługi aplikacji musi istnieć przed utworzeniem aplikacji sieci web. Element **dependsOn** informuje Menedżera zasobów, jak zamówić zasoby do wdrożenia.

Właściwość **serverFarmId** używa funkcji [resourceId.](template-functions-resource.md#resourceid) Ta funkcja pobiera unikatowy identyfikator zasobu. W takim przypadku pobiera unikatowy identyfikator planu usługi aplikacji. Aplikacja internetowa jest skojarzona z jednym określonym planem usługi aplikacji.

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby wdrożyć szablon.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj przełącznika **debugowania** z poleceniem wdrażania, aby wyświetlić dzienniki debugowania.  Można również użyć pełnego **przełącznika,** aby wyświetlić pełne dzienniki debugowania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Dowiedziasz się, jak używać szablonu szybkiego startu dla tworzenia szablonów. W następnym samouczku dodajesz tagi do zasobów.

> [!div class="nextstepaction"]
> [Dodawanie tagów](template-tutorial-add-tags.md)
