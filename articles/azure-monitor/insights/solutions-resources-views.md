---
title: Widoki w rozwiązaniach do zarządzania | Dokumentacja firmy Microsoft
description: 'Rozwiązania do zarządzania zwykle zawierają widoki co najmniej do wizualizacji danych.  W tym artykule opisano, jak wyeksportować widok utworzone przez projektanta widoku i uwzględnić go w rozwiązaniu do zarządzania. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596629"
---
# <a name="views-in-management-solutions-preview"></a>Widoki w rozwiązaniach do zarządzania (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępne dokumentację dotyczącą tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.    


[Rozwiązania do zarządzania](solutions.md) zwykle zawierają jeden lub więcej widoków, które umożliwiają wizualizację danych.  W tym artykule opisano sposób eksportowania widoku, który został utworzony przez [Projektant widoków](../../azure-monitor/platform/view-designer.md) i dołączyć go w rozwiązaniu do zarządzania.  

> [!NOTE]
> Przykłady w tym artykule użyć parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązania do zarządzania i opisem w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz już sposób [utworzyć to rozwiązanie do zarządzania](solutions-creating.md) i struktury pliku rozwiązania.

## <a name="overview"></a>Omówienie
Aby uwzględnić w widoku w rozwiązaniu do zarządzania, należy utworzyć **zasobów** dla niego w [plik rozwiązania](solutions-creating.md).  Za pomocą pliku JSON opisujące szczegółową konfigurację tego widoku jest zwykle złożone jednak i nie mielibyśmy mieć czegoś Autor typowe rozwiązanie będzie można tworzyć ręcznie.  Najczęściej spotykaną metodą jest utworzenie, przy użyciu widoku [Projektant widoków](../../azure-monitor/platform/view-designer.md), wyeksportuj go, a następnie dodaj jego szczegółową konfigurację do rozwiązania.

Poniżej znajdują się podstawowe kroki, aby dodać widok do rozwiązania.  Każdy etap jest opisany bardziej szczegółowo w poniższych sekcjach.

1. Widok można wyeksportować do pliku.
2. Utwórz zasób widoku w rozwiązaniu.
3. Dodawanie szczegółów widoku.

## <a name="export-the-view-to-a-file"></a>Eksportuj widok do pliku
Postępuj zgodnie z instrukcjami w artykule [Projektant widoków programu Log Analytics](../../azure-monitor/platform/view-designer.md) można wyeksportować w widoku do pliku.  Będzie wyeksportowany plik w formacie JSON z takimi samymi [elementy jako plik rozwiązania](solutions-solution-file.md).  

**Zasobów** element pliku widok będzie mieć zasobu o typie **Microsoft.OperationalInsights/workspaces** reprezentujący obszar roboczy usługi Log Analytics.  Ten element będzie mieć podelement typu **widoków** który reprezentuje widok oraz jego szczegółowa konfiguracja zawiera.  Będzie kopiować szczegóły tego elementu, a następnie skopiuj go do rozwiązania.

## <a name="create-the-view-resource-in-the-solution"></a>Utwórz zasób widoku w rozwiązaniu
Dodaj następujący zasób widok do **zasobów** elementu z pliku rozwiązania.  Ta metoda korzysta zmiennych, które zostały opisane poniżej, musisz również dodać.  Należy pamiętać, że **pulpit nawigacyjny** i **OverviewTile** właściwości symboli zastępczych, które zostaną zastąpione za pomocą odpowiednich właściwości z pliku wyeksportowanego widoku.

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

Dodaj następujące zmienne do elementu zmienne plik rozwiązania i Zastąp wartości do tych, dla Twojego rozwiązania.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Należy pamiętać, że można skopiować zasobu całego widoku z pliku wyeksportowanego widoku, ale należy wprowadzić następujące zmiany, aby działał w rozwiązaniu.  

* **Typu** widoku zasobu musi zostać zmienione z **widoków** do **Microsoft.OperationalInsights/workspaces**.
* **Nazwa** właściwości zasobu widoku musi zostać zmienione, aby zawierał on nazwę obszaru roboczego.
* Zależność od obszaru roboczego musi można usunąć, ponieważ zasób obszaru roboczego nie jest zdefiniowane w rozwiązaniu.
* **DisplayName** właściwość musi zostać dodane do widoku.  **Identyfikator**, **nazwa**, i **DisplayName** musi wszystkie dopasowania.
* Można zmienić nazw parametrów, aby dopasować żądany zestaw parametrów.
* Zmienne powinny zdefiniowane w rozwiązaniu i używane w odpowiednich właściwości.

### <a name="log-analytics-api-version"></a>Wersja interfejsu API analizy dziennika
Wszystkie zasoby usługi Log Analytics, zdefiniowane w szablonie usługi Resource Manager mają właściwość **apiVersion** definiujący wersję interfejsu API, należy użyć zasobu.  Ta wersja jest inny dla widoków z zapytań, które używają [starsza wersja i język zapytań uaktualnionego](../../azure-monitor/log-query/log-query-overview.md).  

 W poniższej tabeli określono wersji interfejsu API usługi Log Analytics dla widoków w starszej wersji, jak i uaktualnionych obszarów roboczych: 

| Wersja z obszaru roboczego | Wersja interfejsu API | Zapytanie |
|:---|:---|:---|
| Wersja 1 (starsza wersja)   | 2015-11-01-preview | Format starszej wersji.<br> Przykład: Typ = EventLevelName zdarzenia = błąd  |
| (uaktualniony) w wersji 2 | 2015-11-01-preview | Format starszej wersji.  Konwertowanie na format uaktualnionego związane z instalacją.<br> Przykład: Typ = EventLevelName zdarzenia = błąd<br>Przekonwertowane na: Zdarzenie &#124; gdzie EventLevelName == "Error"  |
| (uaktualniony) w wersji 2 | 2017-03-03-preview | Uaktualnić format. <br>Przykład: Zdarzenie &#124; gdzie EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Dodawanie szczegółów widoku
Wyświetl zasób w pliku wyeksportowanego widoku będzie zawierać dwa elementy w **właściwości** elementu o nazwie **pulpit nawigacyjny** i **OverviewTile** zawierających szczegółowe Konfiguracja tego widoku.  Skopiuj te dwa elementy i ich zawartość do **właściwości** elementu Wyświetl zasób w pliku rozwiązania.

## <a name="example"></a>Przykład
Na przykład poniższy przykład pokazuje plik prostego rozwiązania przy użyciu widoku.  Wielokropek (...) są wyświetlane dla **pulpit nawigacyjny** i **OverviewTile** zawartość ze względów miejsca.

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




## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, szczegółowe informacje dotyczące tworzenia [rozwiązań do zarządzania](solutions-creating.md).
* Obejmują [elementów runbook usługi Automation w ramach rozwiązania do zarządzania](solutions-resources-automation.md).
