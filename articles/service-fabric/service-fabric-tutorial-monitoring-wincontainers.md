---
title: Monitorowanie i diagnostyka kontenerów systemu Windows w usłudze Azure Service Fabric | Microsoft Docs
description: W ramach tego scenariusza skonfigurujesz monitorowanie i diagnostykę kontenerów systemu Windows zorganizowanych w usłudze Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9024036c5340e9afb2369feedde140d84e880265
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>Samouczek: monitorowanie kontenerów systemu Windows w usłudze Service Fabric przy użyciu usługi Log Analytics

Niniejszy samouczek jest drugą częścią serii i umożliwia przeprowadzenie konfiguracji usługi Log Analytics na potrzeby monitorowania kontenerów systemu Windows zorganizowanych w usłudze Service Fabric.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie usługi Log Analytics dla klastra usługi Service Fabric
> * Używanie obszaru roboczego usługi Log Analytics w celu wyświetlania dzienników i wykonywania o nie zapytań z poziomu kontenerów i węzłów
> * Konfigurowanie agenta pakietu OMS na potrzeby pobierania metryk kontenerów i węzłów

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka należy:
- Mieć klaster na platformie Azure lub [utworzyć go za pomocą tego samouczka](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- [Wdrożyć w nim konteneryzowaną aplikację](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Konfigurowanie usługi Log Analytics na potrzeby klastra w szablonie usługi Resource Manager

Jeśli używasz [szablonu](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) dostarczonego w pierwszej części tego samouczka, powinien on zawierać następujące dodatki do ogólnego szablonu usługi Azure Resource Manager w ramach usługi Service Fabric. Jeśli masz własny klaster, który chcesz skonfigurować w celu monitorowania kontenerów za pomocą usługi Log Analytics:
* Wprowadź następujące zmiany w szablonie usługi Resource Manager.
* Wdróż je przy użyciu programu PowerShell w celu uaktualnienia klastra przez [wdrożenie szablonu](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Usługa Azure Resource Manager potrafi rozpoznać istniejący zasób, więc wdroży go jako uaktualnienie.

### <a name="adding-log-analytics-to-your-cluster-template"></a>Dodawanie usługi Log Analytics do szablonu klastra

Wprowadź następujące zmiany w szablonie *template.json*:

1. Dodaj lokalizację i nazwę obszaru roboczego usługi Log Analytics w sekcji *parameters*:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Aby zmienić wartość dla dowolnego z tych elementów, dodaj te same parametry do pliku *template.parameters.json* i zmień używane w nim wartości.

2. Dodaj nazwę rozwiązania oraz rozwiązanie do sekcji *variables*: 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Dodaj program Microsoft Monitoring Agent pakietu OMS jako rozszerzenie maszyny wirtualnej. Znajdź zasób zestawów skalowania maszyn wirtualnych: *resources* > *"apiVersion": "[variables('vmssApiVersion')]"*. W obszarze *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* dodaj następujący opis w ramach rozszerzenia *ServiceFabricNode*: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Dodaj obszar roboczy usługi Log Analytics jako pojedynczy zasób. W obszarze *resources* po zasobie zestawów skalowania maszyn wirtualnych dodaj następujący kod:
    
    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[W tym miejscu](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) znajduje się przykładowy szablon (używany w pierwszej części tego samouczka) zawierający wszystkie wprowadzone zmiany, do których można się odwołać w razie potrzeby. Te zmiany spowodują dodanie obszaru roboczego usługi Log Analytics do grupy zasobów. Obszar roboczy zostanie skonfigurowany w celu korzystania ze zdarzeń platformy usługi Service Fabric z poziomu tabel magazynu skonfigurowanych za pomocą agenta [Microsoft Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). Agent pakietu OMS (Microsoft Monitoring Agent) został również dodany do każdego węzła w klastrze jako rozszerzenie maszyny wirtualnej. Oznacza to, że podczas skalowania klastra agent jest automatycznie konfigurowany na każdej maszynie i podłączany do tego samego obszaru roboczego.

Wdróż szablon z wprowadzonymi zmianami, aby uaktualnić bieżący klaster. Po zakończeniu w grupie zasobów powinny zostać wyświetlone zasoby usługi Log Analytics. Gdy klaster będzie gotowy, wdróż w nim konteneryzowaną aplikację. W następnym kroku zostanie skonfigurowane monitorowanie kontenerów.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Dodawanie rozwiązania do monitorowania kontenerów do obszaru roboczego usługi Log Analytics

Aby skonfigurować rozwiązanie kontenera w obszarze roboczym, wyszukaj wyrażenie *Rozwiązanie do monitorowania kontenerów* i utwórz zasób kontenerów (w kategorii Monitorowanie + zarządzanie).

![Dodawanie rozwiązania kontenerów](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Po wyświetleniu monitu o *obszar roboczy usługi Log Analytics* wybierz obszar roboczy, który został utworzony w grupie zasobów, i kliknij pozycję **Utwórz**. Spowoduje to dodanie *rozwiązania do monitorowania kontenerów* do obszaru roboczego, co wywoła automatyczne zbieranie dzienników i statystyk platformy Docker przez agenta pakietu OMS wdrożonego w ramach szablonu. 

Przejdź z powrotem do *grupy zasobów*, gdzie powinno zostać wyświetlone nowo dodane rozwiązanie do monitorowania. Po jego kliknięciu na stronie docelowej powinna wyświetlić się liczba uruchomionych obrazów kontenera. 

*Zwróć uwagę, że zostało uruchomionych 5 wystąpień kontenera fabrikam z [części drugiej](service-fabric-host-app-in-a-container.md) samouczka*

![Strona docelowa rozwiązania kontenera](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Po kliknięciu pozycji **Rozwiązanie do monitorowania kontenerów** nastąpi przejście do bardziej szczegółowego pulpitu nawigacyjnego, który umożliwia przewijanie wielu paneli, a także uruchamianie zapytań w usłudze w Log Analytics. 

*Pamiętaj, że od września 2017 r. w rozwiązaniu są wprowadzane aktualizacje. W związku z tym ignoruj wszelkie błędy dotyczące zdarzeń Kubernetes, które mogą wystąpić, ponieważ trwają prace nad zintegrowaniem wielu orkiestratorów w jednym rozwiązaniu.*

W związku z tym, że agent pobiera dzienniki platformy Docker, domyślnie wyświetla elementy *stdout* i *stderr*. Po przewinięciu w prawo zostanie wyświetlony spis obrazów kontenera, stan, metryki i przykładowe zapytania, które można uruchomić, aby uzyskać bardziej użyteczne dane. 

![Pulpit nawigacyjny rozwiązania kontenera](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Po kliknięciu jednego z tych paneli nastąpi przeniesienie do zapytania usługi Log Analytics, które generuje wyświetlaną wartość. Zmień zapytanie na *\**, aby zobaczyć wszystkie rodzaje zbieranych dzienników. W tym miejscu można wysyłać zapytania dotyczące wydajności kontenerów i dzienników lub filtrować je albo wyszukiwać zdarzenia platformy usługi Service Fabric. Poza tym agenci stale emitują puls z każdego widocznego węzła, aby upewnić się, że dane ze wszystkich maszyn są zbierane nawet w przypadku zmiany konfiguracji klastra.   

![Zapytanie dotyczące kontenera](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Konfigurowanie agenta pakietu OMS w celu zbierania liczników wydajności

Kolejną zaletą korzystania z agenta pakietu OMS jest możliwość zmiany liczników wydajności pobieranych za pośrednictwem interfejsu użytkownika pakietu OMS bez konieczności konfigurowania agenta funkcji Diagnostyka Azure i każdorazowego uaktualniania opartego na szablonie usługi Resource Manager. Aby to zrobić, kliknij pozycję **Portal OMS** na stronie docelowej rozwiązania do monitorowania kontenerów (lub usługi Service Fabric).

![Portal pakietu OMS](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Spowoduje to przejście do obszaru roboczego w portalu OMS, w którym można wyświetlać rozwiązania, tworzyć niestandardowe pulpity nawigacyjne, a także konfigurować agenta pakietu OMS. 
* Kliknij **koło zębate** w prawym górnym rogu ekranu, aby otworzyć menu *Ustawienia*.
* Kliknij pozycję **Połączone źródła** > **Serwery z systemem Windows**, aby upewnić się, że *Połączono komputery z systemem Windows (5)*.
* Kliknij pozycję **Dane** > **Liczniki wydajności systemu Windows**, aby wyszukać i dodać nowe liczniki wydajności. W tym miejscu zostanie wyświetlona lista zaleceń usługi Log Analytics dotyczących liczników wydajności, których dane można zbierać, a także opcja wyszukiwania innych liczników. Kliknij pozycję **Dodaj wybrane liczniki wydajności**, aby rozpocząć zbieranie zalecanych metryk.

    ![Liczniki wydajności](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

W witrynie Azure Portal po kilku minutach **odśwież** rozwiązanie do monitorowania kontenerów, aby wyświetlić dane przychodzące dotyczące *wydajności komputera*. Pomoże to zrozumieć, w jaki sposób zasoby są używane. Tych metryk można również używać na potrzeby podejmowania odpowiednich decyzji dotyczących skalowania klastra lub potwierdzania, że klaster równoważy obciążenie zgodnie z oczekiwaniami.

*Uwaga: upewnij się, że filtry czasu zostały odpowiednio ustawione i umożliwiają korzystanie z tych metryk.* 

![Liczniki wydajności 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie usługi Log Analytics dla klastra usługi Service Fabric
> * Używanie obszaru roboczego usługi Log Analytics w celu wyświetlania dzienników i wykonywania o nie zapytań z poziomu kontenerów i węzłów
> * Konfigurowanie agenta usługi Log Analytics na potrzeby pobierania metryk kontenerów i węzłów

Po skonfigurowaniu monitorowania konteneryzowanej aplikacji spróbuj wykonać następujące czynności:

* Skonfiguruj usługę Log Analytics dla klastra systemu Linux, wykonując kroki podobne do opisanych powyżej. Utwórz odwołanie do [tego szablonu](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux), aby wprowadzić zmiany w szablonie usługi Resource Manager.
* Skonfiguruj usługę Log Analytics, aby skonfigurować [automatyczne alerty](../log-analytics/log-analytics-alerts.md) ułatwiające wykrywanie i przeprowadzanie diagnostyki.
* Zapoznaj się z listą [zalecanych liczników wydajności](service-fabric-diagnostics-event-generation-perf.md) usługi Service Fabric w celu skonfigurowania ich na potrzeby klastrów.
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań względem nich](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach usługi Log Analytics.