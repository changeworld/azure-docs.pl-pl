---
title: Samouczek — korzystanie z szablonów szybkiego startu platformy Azure | Microsoft Docs
description: Dowiedz się, jak korzystać z szablonów szybkiego startu platformy Azure, aby ukończyć tworzenie szablonu.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1ef6dd913958f963cb62b4014c09a47903831aa
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963849"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Samouczek: korzystanie z szablonów szybkiego startu platformy Azure

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium szablonów utworzonych przez społeczność. Możesz użyć przykładowych szablonów w opracowywaniu szablonu. W tym samouczku znajdziesz definicję zasobu witryny sieci Web i dodasz ją do własnego szablonu. Ukończenie może potrwać około **12 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego wyeksportowanych szablonów](template-tutorial-export-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Ten szablon działa w przypadku wdrażania kont magazynu i planów usługi App Service, ale można dodać do niej witrynę sieci Web. Możesz użyć wstępnie skompilowanych szablonów, aby szybko wykryć kod JSON wymagany do wdrożenia zasobu.

## <a name="find-a-template"></a>Znajdź szablon

1. Otwórz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)
1. W obszarze **wyszukiwania**wprowadź polecenie **Wdróż aplikację sieci Web systemu Linux**.
1. Wybierz ten element z tytułem **Wdróż podstawową aplikację sieci Web**w systemie Linux. Jeśli masz problemy z znalezieniem go, Oto [link bezpośredni](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Wybierz pozycję **Przeglądaj w witrynie GitHub**.
1. Wybierz **azuredeploy. JSON**.
1. Zapoznaj się z szablonem. W szczególności poszukaj zasobów `Microsoft.Web/sites`.

    ![Witryna sieci Web szybkiego startu szablonu Menedżer zasobów](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-the-existing-template"></a>Popraw istniejący szablon

Scal szablon szybkiego startu z istniejącym szablonem:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

Nazwa webapp musi być unikatowa na platformie Azure. Aby zapobiec duplikowaniu nazw, zmienna **webAppPortalName** została zaktualizowana z **"webAppPortalName": "[concat (parametry (" webAppName "),"-webapp ")]"** do **"webAppPortalName": "[concat (Parameters (" webAppName "), uniqueString (podsourceing (). ID)] "** .

Dodaj przecinek na końcu definicji `Microsoft.Web/serverfarms`, aby oddzielić definicję zasobu od definicji `Microsoft.Web/sites`.

Istnieje kilka ważnych funkcji, które należy wziąć pod uwagę w tym nowym zasobie.

Zobaczysz, że ma on element o nazwie **dependsOn** , który jest ustawiony na plan usługi App Service. To ustawienie jest wymagane, ponieważ musi istnieć plan usługi App Service przed utworzeniem aplikacji sieci Web. Element **dependsOn** informuje o tym, Menedżer zasobów sposób porządkowania zasobów do wdrożenia.

Właściwość **serverFarmId** używa funkcji [ResourceID](resource-group-template-functions-resource.md#resourceid) . Ta funkcja pobiera unikatowy identyfikator zasobu. W takim przypadku pobiera unikatowy identyfikator planu usługi App Service. Aplikacja sieci Web jest skojarzona z jednym określonym planem usługi App Service.

## <a name="deploy-the-template"></a>Wdróż szablon

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** .
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać szablonu szybkiego startu do tworzenia szablonu. W następnym samouczku dodasz Tagi do zasobów.

> [!div class="nextstepaction"]
> [Dodaj Tagi](template-tutorial-add-tags.md)