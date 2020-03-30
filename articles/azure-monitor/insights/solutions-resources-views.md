---
title: Widoki w rozwiązaniach do zarządzania | Dokumenty firmy Microsoft
description: 'Rozwiązania do zarządzania zazwyczaj zawierają jeden lub więcej widoków do wizualizacji danych.  W tym artykule opisano sposób eksportowania widoku utworzonego przez projektanta widoku i dołączania go do rozwiązania do zarządzania. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663049"
---
# <a name="views-in-management-solutions-preview"></a>Widoki w rozwiązaniach do zarządzania (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja do tworzenia rozwiązań do zarządzania, które są obecnie w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.    


[Rozwiązania do zarządzania](solutions.md) zazwyczaj zawierają jeden lub więcej widoków do wizualizacji danych.  W tym artykule opisano sposób eksportowania widoku utworzonego przez [projektanta widoku](../../azure-monitor/platform/view-designer.md) i dołączania go do rozwiązania do zarządzania.  

> [!NOTE]
> Przykłady w tym artykule używają parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązań do zarządzania i opisane w [Projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że jesteś już zaznajomiony z jak utworzyć rozwiązanie do [zarządzania](solutions-creating.md) i strukturę pliku rozwiązania.

## <a name="overview"></a>Omówienie
Aby uwzględnić widok w rozwiązaniu do zarządzania, należy utworzyć **zasób** dla niego w [pliku rozwiązania](solutions-creating.md).  JSON, który opisuje szczegółową konfigurację widoku jest zazwyczaj skomplikowane, choć i nie coś, co typowy autor rozwiązania będzie w stanie utworzyć ręcznie.  Najczęstszą metodą jest utworzenie widoku przy użyciu [projektanta widoku,](../../azure-monitor/platform/view-designer.md)wyeksportowanie go, a następnie dodanie jego szczegółowej konfiguracji do rozwiązania.

Podstawowe kroki, aby dodać widok do rozwiązania są następujące.  Każdy krok jest opisany bardziej szczegółowo w poniższych sekcjach.

1. Wyeksportowanie widoku do pliku.
2. Utwórz zasób widoku w rozwiązaniu.
3. Dodaj szczegóły widoku.

## <a name="export-the-view-to-a-file"></a>Eksportowanie widoku do pliku
Postępuj zgodnie z instrukcjami w [Projektancie widoku usługi Log Analytics,](../../azure-monitor/platform/view-designer.md) aby wyeksportować widok do pliku.  Wyeksportowany plik będzie w formacie JSON z tymi [samymi elementami](solutions-solution-file.md)co plik rozwiązania .  

Element **zasobów** pliku widoku będzie miał zasób o typie **Microsoft.OperationalInsights/workspaces reprezentującym** obszar roboczy usługi Log Analytics.  Ten element będzie miał podelement z typem **widoków,** który reprezentuje widok i zawiera jego szczegółową konfigurację.  Skopiujesz szczegóły tego elementu, a następnie skopiujesz go do rozwiązania.

## <a name="create-the-view-resource-in-the-solution"></a>Tworzenie zasobu widoku w rozwiązaniu
Dodaj następujący zasób widoku do elementu **zasobów** pliku rozwiązania.  Spowoduje to użycie zmiennych, które są opisane poniżej, które należy również dodać.  Należy zauważyć, że **pulpit** nawigacyjny i OverviewTile właściwości są symbole **zastępcze,** które zostaną zastąpione odpowiednimi właściwościami z pliku widoku eksportowanego.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Dodaj następujące zmienne do elementu zmiennych pliku rozwiązania i zastąp wartości do wartości dla rozwiązania.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Należy zauważyć, że można skopiować cały zasób widoku z wyeksportowanego pliku widoku, ale należy wprowadzić następujące zmiany, aby działał w rozwiązaniu.  

* **Typ** zasobu widoku musi zostać zmieniony z **widoków** na **Microsoft.OperationalInsights/workspaces**.
* Właściwość **name** zasobu widoku musi zostać zmieniona, aby zawierała nazwę obszaru roboczego.
* Zależność od obszaru roboczego musi zostać usunięta, ponieważ zasób obszaru roboczego nie jest zdefiniowany w rozwiązaniu.
* **Właściwość DisplayName** musi zostać dodana do widoku.  **Identyfikator ,** **nazwa**i **Nazwa wyświetlenia** muszą być zgodne.
* Nazwy parametrów muszą zostać zmienione, aby dopasować się do wymaganego zestawu parametrów.
* Zmienne powinny być zdefiniowane w roztworze i używane w odpowiednich właściwościach.

### <a name="log-analytics-api-version"></a>Wersja interfejsu API usługi Log Analytics
Wszystkie zasoby usługi Log Analytics zdefiniowane w szablonie Menedżera zasobów mają **apiVersion** właściwości, która definiuje wersję interfejsu API, którego zasób powinien używać.  Ta wersja jest inna dla widoków z kwerendami, które używają [starszego i uaktualnionego języka zapytań](../../azure-monitor/log-query/log-query-overview.md).  

 W poniższej tabeli określono wersje interfejsu API usługi Log Analytics dla widoków w starszych i uaktualnionych obszarach roboczych: 

| Wersja obszaru roboczego | Wersja API | Zapytanie |
|:---|:---|:---|
| v1 (dziedzictwo)   | 2015-11-01-podgląd | Starszy format.<br> Przykład: Type=EventLevelName = Błąd  |
| v2 (ulepszone) | 2015-11-01-podgląd | Starszy format.  Przekonwertowany na uaktualniony format w instalacji.<br> Przykład: Type=EventLevelName = Błąd<br>Przekonwertowane na: &#124; zdarzenia, w których EventLevelName == "Error"  |
| v2 (ulepszone) | 2017-03-03-podgląd | Format uaktualnienia. <br>Przykład: &#124; zdarzenia, w którym EventLevelName == "Błąd"  |


## <a name="add-the-view-details"></a>Dodawanie szczegółów widoku
Zasób widoku w pliku eksportowanego widoku będzie zawierał dwa elementy w **elemencie właściwości** o nazwie **Dashboard** i **OverviewTile,** które zawierają szczegółową konfigurację widoku.  Skopiuj te dwa elementy i ich zawartość do elementu **właściwości** zasobu widoku w pliku rozwiązania.

## <a name="example"></a>Przykład
Na przykład w poniższym przykładzie przedstawiono prosty plik rozwiązania z widokiem.  Wielokropek (...) są wyświetlane dla **pulpitu nawigacyjnego** i **OverviewTile** zawartości ze względu na miejsce.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Następne kroki
* Poznaj szczegółowe informacje na temat tworzenia [rozwiązań do zarządzania.](solutions-creating.md)
* Uwzględnij [elementy runbook automatyzacji w rozwiązaniu do zarządzania.](solutions-resources-automation.md)
