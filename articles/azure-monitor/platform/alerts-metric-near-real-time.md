---
title: Obsługiwane zasoby dla alertów metryk w Azure Monitor
description: Informacje na temat metryk i dzienników pomocy technicznej na temat alertów dotyczących metryk w Azure Monitor
author: harelbr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 14dc7b33a82b490f005d9684e4c9cb76bd947a7c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364496"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Obsługiwane zasoby dla alertów metryk w Azure Monitor

Azure Monitor teraz obsługuje [nowy typ alertu metryki](../../azure-monitor/platform/alerts-overview.md) , który ma znaczące korzyści w stosunku do starszych [klasycznych alertów dotyczących metryk](../../azure-monitor/platform/alerts-classic.overview.md). Metryki są dostępne dla [dużej listy usług platformy Azure](../../azure-monitor/platform/metrics-supported.md). Nowsze alerty obsługują (rosnący) podzbiór typów zasobów. W tym artykule wymieniono ten podzbiór.

Możesz również używać nowszych alertów metryk dla popularnych danych dziennika przechowywanych w Log Analytics obszarze roboczym wyodrębnionym jako metryki. Aby uzyskać więcej informacji, zobacz [alerty metryk dla dzienników](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, interfejsu wiersza polecenia, obsługa REST
Obecnie można tworzyć nowsze alerty metryk tylko w [szablonach](../../azure-monitor/platform/alerts-metric-create-templates.md)Azure Portal, [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/)lub Menedżer zasobów. Wkrótce będzie dostępna obsługa konfigurowania nowszych alertów przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej.

## <a name="metrics-and-dimensions-supported"></a>Obsługiwane metryki i wymiary
Nowsze alerty metryk obsługują alerty dotyczące metryk, które korzystają z wymiarów. Wymiarów można użyć do filtrowania metryki na odpowiedni poziom. Wszystkie obsługiwane metryki wraz z odpowiednimi wymiarami mogą być zbadane i wizualizacje z [Azure Monitor Eksplorator metryk](../../azure-monitor/platform/metrics-charts.md).

Oto pełna lista źródeł metryk usługi Azure monitor obsługiwanych przez nowsze alerty:

|Typ zasobu  |Obsługiwane wymiary |Alerty dotyczące obsługi zasobów| Dostępne metryki|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Tak| Nie | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. AppPlatform/Sprężyna |Nie| Tak|
|Microsoft.Automation/automationAccounts | Tak| Nie | [Konta usługi Automation](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | ND| Nie | [Konta zadań wsadowych](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Tak| Nie |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic|Nie|Tak|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/blobServices|Nie|Tak|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/fileServices|Nie|Tak|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/queueServices|Nie|Tak|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/tableServices|Nie|Tak| |
|Microsoft.CognitiveServices/accounts| ND | Nie | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Tak | Tak | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |ND | Tak |[Zestawy skalowania maszyn wirtualnych](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Tak| Nie | [Grupy kontenerów](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Tak | Nie | [Zarządzane klastry](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Tak | Tak | |
|Microsoft.DataFactory/datafactories| Tak| Nie | [Fabryki danych v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |Tak | Nie |[Fabryki danych v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. dataudział/konta |Nie| Tak|
|Microsoft.DBforMySQL/servers |ND| Nie |[Baza danych dla programu MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |ND | Nie | [Baza danych dla PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | ND | Nie |[Metryki IoT Hub](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Tak | Nie |[Metryki DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft. EventGrid/domeny|Nie|Tak| |
|Microsoft. EventGrid/tematy |Tak | Nie |[Tematy Event Grid](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft. EventHub/klastry |Tak| Nie |[Klastry Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Tak| Nie |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nie |Nie |[Magazynów](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |ND | Nie |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Tak| Nie | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Network/applicationGateways|ND| Nie |  |
|Microsoft.Network/dnsZones | ND| Nie | [Strefy DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | ND | Nie |[Obwody usługi Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/loadBalancers (tylko w przypadku standardowych jednostek SKU)| Tak| Nie | [Moduły równoważenia obciążenia](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|Nie|Tak|
|Microsoft. Network/privateEndpoints|Nie|Tak|
|Microsoft. Network/privateLinkServices|Nie|Tak|
|Microsoft.Network/publicipaddresses |ND | Nie |[Publiczne adresy IP](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Tak | Nie | [Profile Traffic Manager](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Tak | Nie | [Log Analytics obszary robocze](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. Relay/przestrzenie nazw | Tak | Nie | [Komunikaty](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. Komunikacja równorzędna/peeringServices|Nie|Tak|
|Microsoft.PowerBIDedicated/capacities | ND | Nie | [Produkcyjnych](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |ND|Nie | [Wyszukaj usługi](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Tak| Nie |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Nie | Tak |
|Microsoft.Sql/servers/databases    | Nie | Tak |
|Microsoft.Storage/storageAccounts |Tak | Nie | [Konta magazynu](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Tak| Nie | [Usługi BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [usługi plików](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [usługi kolejkowania](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) i [usługi tabel](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |ND| Nie | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. VMWareCloudSimple/virtualMachines |Tak|Nie |[CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/serverfarms | Tak | Nie | [Plany App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Tak | Nie | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) i [funkcje](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Tak | Nie | [App Service gniazd](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schemat ładunku

> [!NOTE]
> Można również użyć [typowego schematu alertu](https://aka.ms/commonAlertSchemaDocs), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor, dla integracji elementów webhook. [Dowiedz się więcej na temat typowych definicji schematu alertów.](https://aka.ms/commonAlertSchemaDefinitions)


Operacja POST zawiera następujący ładunek i schemat JSON dla wszystkich niemal nowszych alertów metryk, gdy zostanie użyta odpowiednio skonfigurowana [Grupa akcji](../../azure-monitor/platform/action-groups.md) :

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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
            "metricValue": 1
          }
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

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o nowych [alertach](../../azure-monitor/platform/alerts-overview.md).
* Dowiedz się więcej [na temat alertów dziennika na platformie Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Dowiedz się więcej o [alertach na platformie Azure](../../azure-monitor/platform/alerts-overview.md).
