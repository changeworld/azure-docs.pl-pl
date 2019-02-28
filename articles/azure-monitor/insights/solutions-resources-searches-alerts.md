---
title: Zapisane wyszukiwania w rozwiązaniach do zarządzania | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania zwykle zawierają zapisane wyszukiwania w usłudze Log Analytics do analizowania danych zbieranych przez to rozwiązanie. W tym artykule opisano sposób definiowania zapisane wyszukiwania w szablonie usługi Resource Manager, aby mogły one zostać uwzględnione w rozwiązaniach do zarządzania w usłudze Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64b79d613463ac2097a89a8e3ca3870b885a3332
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985261"
---
# <a name="adding-log-analytics-saved-searches-to-management-solution-preview"></a>Dodawanie usługi Log Analytics zapisane wyszukiwania do rozwiązania do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Poprzednią wersję tego artykułu dołączone szczegółowe informacje na temat tworzenia alertu przy użyciu szablonu usługi Resource Manager. Te informacje są poza Data teraz [alertów usługi Log Analytics zostały rozszerzone do usługi Azure Monitor](../platform/alerts-extend.md). Aby uzyskać więcej informacji na temat tworzenia alertu dziennika za pomocą szablonu usługi Resource Manager, zobacz [alertów dzienników zarządzanie przy użyciu szablonu usługi Azure Resource](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Jest to wstępne dokumentację dotyczącą tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.

W tym artykule opisano sposób definiowania zapisanego wyszukiwania analizy dzienników [szablonu Resource Management](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) , dzięki czemu mogą być zawarte w [rozwiązań do zarządzania](solutions-creating.md).

> [!NOTE]
> Przykłady w tym artykule użyć parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązania do zarządzania i opisem w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz już sposób [utworzyć to rozwiązanie do zarządzania](solutions-creating.md) i struktury [szablonu usługi Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) i plik rozwiązania.


## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Wszystkie zasoby w usłudze Log Analytics są zawarte w [obszaru roboczego](../../azure-monitor/platform/manage-access.md). Zgodnie z opisem w [obszar roboczy usługi Log Analytics i konto usługi Automation](solutions.md#log-analytics-workspace-and-automation-account), obszar roboczy nie jest zawarty w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania. Jeśli nie jest dostępne, zainstaluj rozwiązania kończy się niepowodzeniem.

Nazwa obszaru roboczego jest nazwę każdego zasobu usługi Log Analytics. Jest to realizowane w rozwiązaniu za pomocą **obszaru roboczego** parametru, jak w poniższym przykładzie zasobów zapisanego wyszukiwania.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Wersja interfejsu API analizy dziennika
Wszystkie zasoby usługi Log Analytics, zdefiniowane w szablonie usługi Resource Manager mają właściwość **apiVersion** definiujący wersję interfejsu API, należy użyć zasobu.

Poniższa tabela zawiera listę wersji interfejsu API dla zasobów używanych w tym przykładzie.

| Typ zasobu | Wersja interfejsu API | Zapytanie |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Zdarzenie &#124; gdzie EventLevelName == "Error"  |


## <a name="saved-searches"></a>Zapisane wyszukiwania
Obejmują [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w ramach rozwiązania, aby umożliwić użytkownikom zapytania o dane zebrane przez rozwiązania. Zapisane wyszukiwania są wyświetlane w obszarze **zapisane wyszukiwania** w witrynie Azure portal. Zapisane wyszukiwanie jest także wymagane dla każdego alertu.

[Zapisane wyszukiwanie analizy dzienników](../../azure-monitor/log-query/log-query-overview.md) zasoby mają typ `Microsoft.OperationalInsights/workspaces/savedSearches` i mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Każda właściwość zapisanego kryterium wyszukiwania jest opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| category | Kategoria dla zapisanego wyszukiwania.  Wszystkie zapisane wyszukiwania, w tym samym rozwiązaniu często współużytkują jednej kategorii, dzięki czemu są one zgrupowane razem w konsoli. |
| displayname | Nazwa do wyświetlenia dla zapisanego wyszukiwania w portalu. |
| query | Zapytanie do uruchomienia. |

> [!NOTE]
> Może być konieczne użycie znaków ucieczki w zapytaniu, jeśli zawiera znaki, które może zostać zinterpretowane jako kod JSON. Na przykład, jeśli zapytanie zostało **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, powinny być zapisywane w pliku rozwiązania jako **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.


## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie widoków](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodawanie elementów runbook usługi Automation i innych zasobów](solutions-resources-automation.md) do rozwiązania do zarządzania.
