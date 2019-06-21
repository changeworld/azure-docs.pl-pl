---
title: Jak utworzyć szablon usługi Azure Resource Manager
description: W tym artykule opisano proces tworzenia szablonu usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: bbe891aa584423d64531ae4b23bb8a6ead38c3da
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205556"
---
# <a name="create-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

W tym artykule opisano proces oraz decyzjach podejmowanych podczas tworzenia szablonu usługi Azure Resource Manager. Zapewnia przegląd przykładów i funkcje, które mogą pomóc podczas tworzenia szablonu. Tego artykułu przyjęto założenie, że w przypadku wdrażania zasobów w grupie zasobów. Jeśli potrzebujesz do wdrażania zasobów w ramach subskrypcji platformy Azure, takich jak tworzenie zasad platformy Azure lub przypisania kontroli dostępu opartej na rolach, zobacz [tworzenia grupy zasobów i zasobów subskrypcji platformy Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Wybierz edytor JSON

Szablon usługi Resource Manager jest plik w formacie JSON. Konieczne jest dobrym narzędziem autorskim do pracy w pliku JSON. Istnieje wiele opcji, ale jeśli nie masz jeszcze Edytor który można jednak zainstalować [programu Visual Studio Code (VS Code)](https://code.visualstudio.com/). 

Po zainstalowaniu programu VS Code, należy dodać [rozszerzenie narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). To rozszerzenie dodaje wiele funkcji, które upraszczają proces tworzenia szablonu.

![Template Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

Na zrzucie ekranu przedstawiono szablon usługi Resource Manager, otwarty w programie Visual Studio Code. 

Samouczek dotyczący instalowania rozszerzenia narzędzia usługi Resource Manager i sposobu używania programu VS Code można zobaczyć [Szybki Start: Tworzenie szablonów usługi Azure Resource Manager przy użyciu programu Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Zrozumienie struktury szablonu

Omówmy teraz części szablonu w celu poznania sposobu działania tego szablonu. Szablon może nie mieć każdej sekcji. Dostępne są następujące sekcje, które chcesz skupić się na:

* [Parametry](resource-group-authoring-templates.md#parameters) sekcji, która wyświetla wartości można określić podczas wdrażania, aby dostosować infrastrukturę, która jest wdrażana. 

* [Zmienne](resource-group-authoring-templates.md#variables) sekcji znajdują się wartości, które są używane w szablonie.

* [Funkcje](resource-group-authoring-templates.md#functions) sekcji, która zawiera dostosowany szablon wyrażeń, które są używane w szablonie.

* [Zasobów](resource-group-authoring-templates.md#resources) sekcję, która zawiera zasoby platformy Azure, które zostały wdrożone w subskrypcji.

* [Generuje](resource-group-authoring-templates.md#outputs) sekcję, która zawiera wartości, które są zwracane po zakończeniu wdrożenia.

## <a name="look-for-similar-templates"></a>Wyszukaj podobne szablony

Często można znaleźć istniejącego szablonu, który wdraża to rozwiązanie, które jest podobny do potrzebnych składników. [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) ma setki szablonów ze społeczności.

![Szablon szybkiego startu repozytorium](./media/how-to-create-template/template-quickstart-repo.png)

Przeszukaj tego repozytorium dla szablonu, która jest podobna do czego potrzebujesz. Jest to akceptowalne, jeśli szablon nie jest dokładnie co jest potrzebne, można go dostosować.

Po znalezieniu szablonu, wybierz **Przeglądaj w usłudze Github**, a następnie skopiuj **azuredeploy.json** plik z repozytorium. W programie VS Code, Utwórz nowy plik o nazwie **azuredeploy.json** i dodać zawartość pliku szablonu został skopiowany z repozytorium z pakietu Quickstart.

## <a name="add-resources"></a>Dodaj zasoby

Prawdopodobnie chcesz dostosować szablon, aby upewnić się, czy tak jest dokładnie ma. Po pierwsze Przejrzyj zasoby, które są wdrażane. Może być konieczne dodać, usunąć lub zmienić zasoby w szablonie. Opisy i składnia zasobów, zobacz [odwołanie do szablonu usługi Azure Resource Manager](/azure/templates/).

![Dokumentacja dotycząca szablonów](./media/how-to-create-template/template-reference.png)

Po zapoznaniu się z tymi właściwościami, wprowadź wymagane zmiany. Aby uzyskać zalecenia dotyczące sposobu definiowania zasobów, zobacz [zasoby — zalecane praktyki](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Dodaj lub Usuń parametry

Również może być konieczne dostosowanie parametrów szablonu. Można dodawać i usuwać parametrów oparty na dostosowanie, ile chcesz włączyć podczas wdrażania. Aby uzyskać zalecenia dotyczące sposobu używania parametrów, zobacz [parametry — zalecane praktyki](template-best-practices.md#parameters).

## <a name="add-tags"></a>Dodawanie tagów

Dodawanie tagów do zasobów w celu logicznego uporządkowania według kategorii i podziału kosztów. Dodawanie tagów jest proste, zastosuj w formacie JSON dla zasobu. Na przykład następujące konto magazynu zawiera dwa tagi:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Tagi można także zastosować dynamicznie z parametrów. Aby uzyskać więcej informacji, zobacz [tagów w szablonie](resource-group-using-tags.md#templates).

## <a name="review-template-functions"></a>Przejrzyj funkcje szablonów

Można zauważyć wyrażeń w szablonie, które są ujęte w nawiasy klamrowe, takich jak `"[some-expression]"`. Te wyrażenia konstruowania za pomocą funkcji szablonu dynamicznego wartości podczas wdrażania.

Na przykład często Zobacz wyrażeń, takich jak:

```json
"name": "[parameters('siteName')]"
```

To wyrażenie pobiera wartości parametru. Wartość jest przypisywana do właściwości name.

Lub może zostać wyświetlony bardziej złożone wyrażenie, które korzysta z kilku funkcji, takich jak:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

To wyrażenie pobiera obiekt z właściwościami na koncie magazynu.

Aby zrozumieć, jakie funkcje Przejrzyj [odwołanie do funkcji szablonu](resource-group-template-functions.md) dokumentacji.

## <a name="create-more-than-one-instance"></a>Utwórz więcej niż jedno wystąpienie

Czasami trzeba utworzyć więcej niż jedno wystąpienie zasobu. Na przykład możesz potrzebować kilku kont magazynu. Raczej niż powtórzyć zasobów za pomocą szablonu, można użyć `copy` składni, aby określić więcej niż jedno wystąpienie.

Poniższy przykład tworzy trzy konta magazynu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Można również określić liczbę wystąpień dynamicznie z parametrem. Aby uzyskać więcej informacji, zobacz [wdrożenie więcej niż jednego wystąpienia zasobu lub właściwości w szablonach usługi Resource Manager platformy Azure](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Warunkowe wdrażanie zasobu

Czasami musisz określić podczas wdrażania, czy wdrożono zasób w szablonie. Na przykład możesz elastyczność umożliwiającą wdrażanie nowych zasobów lub użyć istniejącego zasobu. `condition` Element umożliwia włączenie lub wyłączenie wdrażania dla zasobu. Gdy wyrażenie w elemencie warunek ma wartość true, zasób został wdrożony. W przypadku wartości FAŁSZ, zasób zostanie pominięte podczas wdrażania.

Warunkowo wdraża konto magazynu w następującym przykładzie:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Aby uzyskać więcej informacji, zobacz [element warunek](resource-group-authoring-templates.md#condition).

## <a name="review-dependencies"></a>Przejrzyj zależności

Niektóre zasoby do szablonu, należy wdrożyć przed innymi zasobami. Na przykład program SQL server musi istnieć przed utworzeniem bazy danych SQL. Menedżer zasobów niejawnie określa kolejność wdrażania dla zasobów podczas [odwoływać się do funkcji](resource-group-template-functions-resource.md#reference) jest używany. Jednak w niektórych przypadkach należy jawnie określić zależności za pomocą `dependsOn` elementu. Sprawdź szablon, aby zobaczyć, jeśli konieczne będzie dodanie wszelkich zależności. Uważaj nie dodawać niepotrzebne zależności one spowolnić wdrażania lub utworzyć odwołania cykliczne.

Na poniższej ilustracji przedstawiono kolejność zależności dla różnych zasobów usługi App Service:

![Zależności aplikacji sieci Web](./media/how-to-create-template/web-dependencies.png)

Poniższy przykład pokazuje części szablonu do definiowania zależności.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Przejrzyj zalecane praktyki

Przed przystąpieniem do wdrażania szablonu, należy przejrzeć [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md) aby zobaczyć, jeśli istnieją zalecane podejścia, który chcesz wdrożyć w szablonie.

Jeśli musisz użyć szablonu w środowiskach inną chmurę platformy Azure, zobacz [szablony Tworzenie usługi Azure Resource Manager w celu zachowania spójności chmury](templates-cloud-consistency.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby wdrożyć szablon, zobacz [Rozmieszczanie za pomocą wiersza polecenia platformy Azure](resource-group-template-deploy-cli.md) lub [wdrażanie przy użyciu programu PowerShell](resource-group-template-deploy.md).
* Aby uzyskać szybki start krok po kroku dotyczące tworzenia szablonu, zobacz [szablonów Tworzenie usługi Azure Resource Manager przy użyciu programu Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](resource-group-template-functions.md).
