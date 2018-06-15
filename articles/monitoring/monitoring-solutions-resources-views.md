---
title: Widoki w rozwiązaniach do zarządzania | Dokumentacja firmy Microsoft
description: 'Rozwiązania do zarządzania zwykle zawiera co najmniej jeden widok do wizualizacji danych.  W tym artykule opisano sposób eksportowania widoku utworzone przez projektanta widoku i dołączyć go w rozwiązaniu do zarządzania. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: b4f54358f4bc1db973d6fe7163411e3a313c3cf4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887867"
---
# <a name="views-in-management-solutions-preview"></a>Widoki w rozwiązaniach do zarządzania (wersja zapoznawcza)
> [!NOTE]
> To jest wstępna dokumentacji do tworzenia rozwiązań do zarządzania, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej może ulec zmianie.    


[Rozwiązania do zarządzania](monitoring-solutions.md) zwykle zawiera co najmniej jeden widok do wizualizacji danych.  W tym artykule opisano sposób eksportowania Widok utworzony przez [Widok projektanta](../log-analytics/log-analytics-view-designer.md) i uwzględnić go w rozwiązaniu do zarządzania.  

> [!NOTE]
> Przykłady w tym artykule, użyj parametrów i zmiennych, które są wymagane ani wspólne dla rozwiązań do zarządzania i opisano w [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](monitoring-solutions-creating.md)
>
>

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że znasz już jak [tworzenie rozwiązania do zarządzania](monitoring-solutions-creating.md) i struktura pliku rozwiązania.

## <a name="overview"></a>Przegląd
Aby uwzględnić widoku w rozwiązaniu do zarządzania, należy utworzyć **zasobów** dla niego w [plik rozwiązania](monitoring-solutions-creating.md).  Dane JSON, które opisano konfigurację szczegółowy widok jest zwykle złożone jednak i nie coś autora rozwiązania typowych będzie można ręcznie utworzyć.  Najczęściej spotykaną metodą jest utworzenie przy użyciu widoku [Widok projektanta](../log-analytics/log-analytics-view-designer.md), go wyeksportować, a następnie dodaj konfigurację szczegółowe do rozwiązania.

Poniżej znajdują się podstawowe kroki, aby dodać widok do rozwiązania.  Każdy krok jest opisany bardziej szczegółowo w poniższych sekcjach.

1. Widok można wyeksportować do pliku.
2. Utwórz zasób widoku w rozwiązaniu.
3. Dodawanie szczegółów widoku.

## <a name="export-the-view-to-a-file"></a>Wyeksportuj do pliku widoku
Postępuj zgodnie z instrukcjami w [Projektant widoków analizy dziennika](../log-analytics/log-analytics-view-designer.md) można wyeksportować do pliku widoku.  Wyeksportowany plik zostanie w formacie JSON o takim samym [elementów jako plik rozwiązania](monitoring-solutions-solution-file.md).  

**Zasobów** element pliku widoku będzie miał zasobu o typie **Microsoft.OperationalInsights/workspaces** reprezentujący obszaru roboczego analizy dzienników.  Ten element będzie mieć podelement typu **widoków** który reprezentuje widok i zawiera szczegółowe konfiguracji.  Zostanie Kopiuj szczegóły tego elementu, a następnie skopiować go do rozwiązania.

## <a name="create-the-view-resource-in-the-solution"></a>Utwórz zasób widoku w rozwiązaniu
Dodaj następujący zasób widoku do **zasobów** element pliku rozwiązania.  Używa zmiennych, które są opisane poniżej, że należy również dodać.  Należy pamiętać, że **pulpitu nawigacyjnego** i **OverviewTile** właściwości symboli zastępczych, które spowoduje zastąpienie odpowiednie właściwości z widoku eksportowanego pliku.

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

Dodaj następujące zmienne do elementu zmienne pliku rozwiązania i Zastąp wartości jak w przypadku rozwiązania.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Należy pamiętać, że można skopiować cały widok zasobów z pliku wyeksportowanego widoku, ale musisz wprowadzić następujące zmiany, aby działał w rozwiązaniu.  

* **Typu** widoku zasobu musi zostać zmienione z **widoków** do **Microsoft.OperationalInsights/workspaces**.
* **Nazwa** właściwości dla zasobu widoku musi zostać zmienione, aby zawierał on nazwę obszaru roboczego.
* Zależność od obszaru roboczego musi można usunąć, ponieważ zasób obszaru roboczego nie jest zdefiniowany w rozwiązaniu.
* **Nazwa wyświetlana** właściwość musi zostać dodany do widoku.  **Identyfikator**, **nazwa**, i **DisplayName** musi wszystkie zgodne.
* Nazwy parametrów, musisz zmienić odpowiadające wymagany zestaw parametrów.
* Zmienne należy określić w rozwiązaniu i używane w odpowiednie właściwości.

### <a name="log-analytics-api-version"></a>Wersja interfejsu API analizy dzienników
Wszystkie zasoby analizy dzienników zdefiniowane w szablonie usługi Resource Manager ma właściwości **apiVersion** określający wersję interfejsu API zasobu należy użyć.  Ta wersja jest różne dla widoków w zapytaniach używających [starsza wersja i język zapytań uaktualnionego](../log-analytics/log-analytics-log-search-upgrade.md).  

 Poniższa tabela zawiera wersje interfejsu API analizy dzienników dla widoków w starszej wersji, jak i uaktualnionych obszarów roboczych: 

| Wersją z obszaru roboczego | Wersja interfejsu API | Zapytanie |
|:---|:---|:---|
| V1 (starsze)   | 2015-11-01-preview | Format starszej wersji.<br> Przykład: Wpisz = EventLevelName zdarzenie błędu =  |
| v2 (uaktualnienia) | 2015-11-01-preview | Format starszej wersji.  Konwertowana na format uaktualniony podczas instalacji.<br> Przykład: Wpisz = EventLevelName zdarzenie błędu =<br>Przekonwertować: zdarzenie &#124; gdzie EventLevelName == "Error"  |
| v2 (uaktualnienia) | 2017-03-03-preview | Uaktualnij format. <br>Przykład: Zdarzenie &#124; gdzie EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Dodawanie widoku szczegółów
Przeglądanie zasobu w widoku wyeksportowanego pliku będzie zawierać dwa elementy w **właściwości** elementu o nazwie **pulpitu nawigacyjnego** i **OverviewTile** zawierających szczegółowe konfiguracji widoku.  Skopiuj następujące dwa elementy i ich zawartość do **właściwości** elementu zasobu widoku w pliku rozwiązania.

## <a name="example"></a>Przykład
Na przykład następujący przykład przedstawia plik prostym rozwiązaniem z widoku.  Wielokropek (...) są wyświetlane dla **pulpitu nawigacyjnego** i **OverviewTile** zawartość ze względów miejsca.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
* Dowiedz się, szczegółowe informacje dotyczące tworzenia [rozwiązań do zarządzania](monitoring-solutions-creating.md).
* Obejmują [elementu runbook usługi Automatyzacja w ramach rozwiązania do zarządzania](monitoring-solutions-resources-automation.md).
