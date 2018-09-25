---
title: Obsługiwane zasobów dla alertów dotyczących metryk w usłudze Azure Monitor
description: Dokumentacja na temat pomocy technicznej, metryk i dzienników dla alertów dotyczących metryk w usłudze Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 102f087f31aa304aaaee49e0e39ff8c6874127b4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966803"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Obsługiwane zasobów dla alertów dotyczących metryk w usłudze Azure Monitor

Platforma Azure obsługuje teraz Monitor [nowego typu alertu Metryka](monitoring-overview-unified-alerts.md) mającego znaczące korzyści w starszej wersji [klasycznego alertów dotyczących metryk](insights-alerts-portal.md). Metryki są dostępne dla [obszerne listy usług systemu Azure](monitoring-supported-metrics.md). Nowszych alertów obsługuje podzbiór (rosnący) typów zasobów. W tym artykule wymieniono tego podzbioru.

Umożliwia także nowszych alertów metryk popularnych dzienników usługi Log Analytics, wyodrębnić jako metryki jako część metryk z dzienników 
- [Liczniki wydajności](../log-analytics/log-analytics-data-sources-performance-counters.md) maszyn Windows i Linux
- [Rekordy pulsu dla kondycji agenta](../operations-management-suite/oms-solution-agenthealth.md)
- [Zarządzanie aktualizacjami](../operations-management-suite/oms-solution-update-management.md) rekordów
- [Dane zdarzenia](../log-analytics/log-analytics-data-sources-windows-events.md) dzienników
 
> [!NOTE]
> Określone metryki i/lub wymiaru będą wyświetlane tylko jeśli dane dla niego istnieje w wybranym okresie. Te metryki są dostępne dla klientów korzystających z obszarów roboczych usługi Azure Log Analytics, w regionie wschodnie stany USA, zachodnio-środkowe stany USA i Europa Zachodnia. Metryki z usługi Log Analytics jest obecnie w publicznej wersji zapoznawczej i może ulec zmianie.

## <a name="portal-powershell-cli-rest-support"></a>Portal, programu PowerShell, interfejsu wiersza polecenia, REST pomocy technicznej
Obecnie można tworzyć nowszych alertów metryk, tylko w witrynie Azure portal, [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate) lub [szablonów usługi Resource Manager](monitoring-create-metric-alerts-with-templates.md). Wkrótce zostanie udostępniona Obsługa konfigurowania nowszych alertów przy użyciu programu PowerShell i wiersza polecenia platformy Azure w wersji 2.0 lub nowszej.

## <a name="metrics-and-dimensions-supported"></a>Metryki i wymiary obsługiwane
Nowszych alertów metryk obsługuje alerty dotyczące metryk, używanego przez wymiary. Wymiarów można użyć do filtrowania swoje metryki na odpowiedni poziom. Wszystkie obsługiwane metryki wraz z odpowiednich wymiarów można przeglądać i wizualizować z [usługi Azure Monitor — Eksplorator metryk (wersja zapoznawcza)](monitoring-metric-charts.md).

Poniżej przedstawiono pełną listę źródeł metryk usługi Azure monitor, obsługiwanych przez nowszych alertów:

|Typ zasobu  |Obsługiwane wymiary  | Dostępne metryki|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Yes        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Yes   | [Konta usługi Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | ND| [Konta usługi Batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    ND     |[Pamięć podręczna Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    ND     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    ND     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   ND      |[Zestawy skalowania maszyn wirtualnych](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Yes| [Grupy kontenerów](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Yes | [Zarządzane klastry](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Yes| [Fabryki danych w wersji 1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Yes     |[Fabryki danych w wersji 2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   ND      |[Bazy danych MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    ND     | [DB dla PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Yes      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nie | [Magazyny](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     ND    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    ND     | [Bramy Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | ND |  [Express Route obwodów](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | ND| [Strefy DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (tylko w przypadku standardowej jednostki SKU)| Yes| [Moduły równoważenia obciążenia](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  ND       |[Adres publiczny adres IP](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | ND | [Pojemności](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Yes | [Profile usługi Traffic Manager](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   ND      |[Usługi wyszukiwania](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Yes       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Yes     | [Konta magazynu](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Yes    | [Obiekt blob usługi](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [usługi plików](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [kolejki usług](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) i [tabeli usług](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  ND       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Yes | [Plany usługi App Service](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Yes | [App Services](monitoring-supported-metrics.md#microsoftwebsites-excluding-functions) i [funkcji](monitoring-supported-metrics.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Yes | [Miejsca usługi App Service](monitoring-supported-metrics.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| Yes|[Obszary robocze usługi log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>Ładunek schematu

Operację POST zawiera następujące ładunek w formacie JSON i schematu dla wszystkich nowszych alertów metryk, jeśli jest odpowiednio skonfigurowany w pobliżu [grupy akcji](monitoring-action-groups.md) jest używany:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowym [alerty środowisko](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [alerty dzienników w usłudze Azure](monitor-alerts-unified-log.md).
* Dowiedz się więcej o [alertów na platformie Azure](monitoring-overview-alerts.md).
