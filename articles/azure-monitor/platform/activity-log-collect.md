---
title: Zbieranie i analizowanie dziennika aktywności platformy Azure w usłudze Azure Monitor
description: Zbieranie dziennika aktywności platformy Azure w dziennikach usługi Azure Monitor i używanie rozwiązania do monitorowania do analizowania i przeszukiwania dziennika aktywności platformy Azure we wszystkich subskrypcjach platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382867"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Zbieranie i analizowanie dziennika aktywności platformy Azure w usłudze Azure Monitor
[Dziennik aktywności platformy Azure](platform-logs-overview.md) to dziennik [platformy,](platform-logs-overview.md) który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik aktywności można wyświetlić w portalu Azure portal, ale należy skonfigurować go do wysyłania do obszaru roboczego usługi Log Analytics, aby włączyć dodatkowe funkcje usługi Azure Monitor. W tym artykule opisano sposób wykonywania tej konfiguracji i wysyłanie dziennika aktywności do usługi Azure storage i centrów zdarzeń.

Zbieranie dziennika aktywności w obszarze roboczym usługi Log Analytics zapewnia następujące zalety:

- Brak pobierania danych lub pobierania opłat za przechowywanie danych dziennika aktywności przechowywanych w obszarze roboczym usługi Log Analytics.
- Skoreluj dane dziennika aktywności z innymi danymi monitorowania zebranymi przez usługę Azure Monitor.
- Użyj zapytań dziennika do wykonywania złożonych analiz i uzyskać szczegółowe informacje na temat wpisów dziennika aktywności.
- Użyj alertów dziennika z wpisami aktywności, co pozwala na bardziej złożoną logikę alertów.
- Zapisy dziennika aktywności sklepu przez okres dłuższy niż 90 dni.
- Konsolidacja wpisów dziennika z wielu subskrypcji platformy Azure i dzierżaw w jedną lokalizację do analizy razem.

> [!IMPORTANT]
> Zbieranie dzienników między dzierżawami wymaga [latarni morskiej platformy Azure.](/azure/lighthouse)

## <a name="collecting-activity-log"></a>Zbieranie dziennika aktywności
Dziennik aktywności jest zbierany automatycznie do [przeglądania w witrynie Azure portal](activity-log-view.md). Aby zebrać go w obszarze roboczym usługi Log Analytics lub wysłać go do magazynu platformy Azure lub centrów zdarzeń, utwórz [ustawienie diagnostyczne](diagnostic-settings.md). Jest to ta sama metoda, która jest używana przez dzienniki zasobów, dzięki czemu jest spójna dla wszystkich [dzienników platformy.](platform-logs-overview.md)  

Aby utworzyć ustawienie diagnostyczne dla dziennika aktywności, wybierz **ustawienia diagnostyczne** z menu **Dziennik aktywności** w usłudze Azure Monitor. Zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników platformy i metryk na platformie Azure,](diagnostic-settings.md) aby uzyskać szczegółowe informacje na temat tworzenia ustawienia. Opis kategorii, które można filtrować, zobacz [Kategorie w dzienniku aktywności.](activity-log-view.md#categories-in-the-activity-log) Jeśli masz jakieś starsze ustawienia, upewnij się, że zostały one wyłączone przed utworzeniem ustawienia diagnostycznego. Włączenie obu tych danych może spowodować zduplikowanie danych.

![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Obecnie można utworzyć tylko ustawienie diagnostyczne poziomu subskrypcji przy użyciu witryny Azure portal i szablonu Menedżera zasobów. 


## <a name="legacy-settings"></a>Starsze ustawienia 
Podczas gdy ustawienia diagnostyczne są preferowaną metodą wysyłania dziennika aktywności do różnych miejsc docelowych, starsze metody będą nadal działać, jeśli nie zdecydujesz się zastąpić ustawieniem diagnostycznym. Ustawienia diagnostyczne mają następujące zalety w stosunku do starszych metod i zaleca się zaktualizowanie konfiguracji:

- Spójna metoda zbierania wszystkich dzienników platformy.
- Zbieranie dziennika aktywności w wielu subskrypcjach i dzierżawach.
- Zbieranie filtrów do zbierania tylko dzienników dla określonych kategorii.
- Zbierz wszystkie kategorie dziennika aktywności. Niektóre kategorie nie są zbierane przy użyciu starszej metody.
- Szybsze opóźnienie pozyskiwania dziennika. Poprzednia metoda ma około 15 minut opóźnienia, podczas gdy ustawienia diagnostyczne dodaje tylko około 1 minuty.



### <a name="log-profiles"></a>Profile dzienników
Profile dzienników są starszą metodą wysyłania dziennika aktywności do usługi Azure storage lub centrum zdarzeń. Poniższa procedura służy do kontynuowania pracy z profilem dziennika lub wyłączania go w ramach przygotowań do migracji do ustawienia diagnostycznego.

1. Z menu **Azure Monitor** w witrynie Azure portal wybierz **dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kliknij fioletowy baner, aby zapoznać się ze starszą wersji.

    ![Starsze doświadczenie](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Starsza metoda zbierania dziennika aktywności do obszaru roboczego usługi Log Analytics łączy dziennik w konfiguracji obszaru roboczego. 

1. Z menu **obszarów roboczych usługi Log Analytics** w witrynie Azure portal wybierz obszar roboczy do zbierania dziennika aktywności.
1. W sekcji **Źródła danych obszaru roboczego** w menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
1. Kliknij subskrypcję, którą chcesz połączyć.

    ![Obszary robocze](media/activity-log-collect/workspaces.png)

1. Kliknij **przycisk Połącz,** aby połączyć dziennik aktywności w subskrypcji z wybranym obszarem roboczym. Jeśli subskrypcja jest już połączona z innym obszarem roboczym, kliknij przycisk **Rozłącz** najpierw, aby ją odłączyć.

    ![Łączenie obszarów roboczych](media/activity-log-collect/connect-workspace.png)


Aby wyłączyć to ustawienie, wykonaj tę samą procedurę i kliknij przycisk **Rozłącz,** aby usunąć subskrypcję z obszaru roboczego.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analizowanie dziennika aktywności w obszarze roboczym usługi Log Analytics
Po podłączeniu dziennika aktywności do obszaru roboczego usługi Log Analytics wpisy zostaną zapisane w obszarze roboczym do tabeli o nazwie *AzureActivity,* którą można pobrać za pomocą [kwerendy dziennika.](../log-query/log-query-overview.md) Struktura tej tabeli różni się w zależności od [kategorii wpisu dziennika](activity-log-view.md#categories-in-the-activity-log). Zobacz [schemat zdarzeń dziennika aktywności platformy Azure,](activity-log-schema.md) aby uzyskać opis każdej kategorii.


### <a name="data-structure-changes"></a>Zmiany struktury danych
Ustawienia diagnostyczne zbierają te same dane, co starsza metoda używana do zbierania dziennika aktywności z pewnymi zmianami w strukturze tabeli *AzureActivity.*

Kolumny w poniższej tabeli zostały przestarzałe w zaktualizowanym schemacie. Nadal istnieją w *usłudze AzureActivity,* ale nie będą miały żadnych danych. Zastąpienie tych kolumn nie są nowe, ale zawierają te same dane, co przestarzała kolumna. Są one w innym formacie, więc może być konieczne zmodyfikowanie zapytań dziennika, które ich używają. 

| Przestarzała kolumna | Kolumna zastępcza |
|:---|:---|
| ActivityStatus (Stan aktywności)    | ActivityStatusValue    |
| AktywnośćSubstatus | DziałanieSubstatusValue |
| OperationName     | OperacjaNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> W niektórych przypadkach wartości w tych kolumnach mogą być we wszystkich wielkich literach. Jeśli masz kwerendę, która zawiera te kolumny, należy użyć [operatora =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) do porównania bez uwzględniania wielkości liter.

Następująca kolumna została dodana do *usługi AzureActivity* w zaktualizowanym schemacie:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Rozwiązanie do monitorowania usługi Activity Logs Analytics
Rozwiązanie do monitorowania usługi Azure Log Analytics zostanie wkrótce przestarzałe i zastąpione skoroszytem przy użyciu zaktualizowanego schematu w obszarze roboczym usługi Log Analytics. Nadal można użyć rozwiązania, jeśli masz już włączone, ale może być używany tylko wtedy, gdy zbierasz dziennik aktywności przy użyciu starszych ustawień. 



### <a name="use-the-solution"></a>Użyj rozwiązania
Rozwiązania do monitorowania są dostępne z menu **Monitor** w witrynie Azure portal. Wybierz **pozycję Więcej** w sekcji **Statystyki,** aby otworzyć stronę **Przegląd** z kafelkami rozwiązania. Kafelek **Dzienniki aktywności platformy Azure** wyświetla liczbę rekordów **usługi AzureActivity** w obszarze roboczym.

![Kafelek dzienników aktywności platformy Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknij **kafelek Dzienniki aktywności platformy Azure,** aby otworzyć widok **dzienniki aktywności platformy Azure.** Widok zawiera części wizualizacji w poniższej tabeli. Każda część zawiera listę do 10 elementów pasujących do kryteriów tej części dla określonego zakresu czasu. Można uruchomić kwerendę dziennika, która zwraca wszystkie pasujące rekordy, klikając **zobacz wszystko** u dołu części.

![Pulpit nawigacyjny dzienników aktywności platformy Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Włącz rozwiązanie dla nowych subskrypcji
Wkrótce nie będzie już można dodać rozwiązania analizy dzienników aktywności do subskrypcji przy użyciu witryny Azure portal. Można go dodać za pomocą poniższej procedury z szablonem Menedżera zasobów. 

1. Skopiuj następujący json do pliku o nazwie *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Wdrażanie szablonu przy użyciu następujących poleceń programu PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dzienniku aktywności](platform-logs-overview.md).
- Dowiedz się więcej o [platformie danych Usługi Azure Monitor](data-platform.md).
- Użyj [zapytań dziennika,](../log-query/log-query-overview.md) aby wyświetlić szczegółowe informacje z dziennika aktywności.
