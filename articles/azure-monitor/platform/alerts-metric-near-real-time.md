---
title: Obsługiwane zasoby dla alertów metryk w usłudze Azure Monitor
description: Odwołanie do metryk pomocy technicznej i dzienników alertów metryk w usłudze Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: e4dfc33756a287b6d2f6e41edded3332ca85241d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274933"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Obsługiwane zasoby dla alertów metryk w usłudze Azure Monitor

Usługa Azure Monitor obsługuje teraz [nowy typ alertu metryki,](../../azure-monitor/platform/alerts-overview.md) który ma znaczące korzyści w stosunku do starszych [alertów metryk klasycznych.](../../azure-monitor/platform/alerts-classic.overview.md) Metryki są dostępne dla [dużej listy usług platformy Azure.](../../azure-monitor/platform/metrics-supported.md) Nowsze alerty obsługują (rosnący) podzbiór typów zasobów. W tym artykule wymieniono ten podzbiór.

Można również użyć nowszych alertów metryk w popularnych danych dziennika przechowywanych w obszarze roboczym usługi Log Analytics wyodrębnione jako metryki. Aby uzyskać więcej informacji, wyświetl [alerty metryczne dla dzienników](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Obsługa portalu, programu PowerShell, CLI, REST
Obecnie można tworzyć nowsze alerty metryk tylko w witrynie Azure portal, [interfejs API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/)lub [szablony Menedżera zasobów.](../../azure-monitor/platform/alerts-metric-create-templates.md) Wkrótce pojawi się obsługa konfigurowania nowszych alertów przy użyciu programów PowerShell i interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej.

## <a name="metrics-and-dimensions-supported"></a>Obsługiwane metryki i wymiary
Nowsze alerty metryki obsługują alerty dotyczące metryk korzystających z wymiarów. Za pomocą wymiarów można filtrować dane do odpowiedniego poziomu. Wszystkie obsługiwane metryki wraz z odpowiednimi wymiarami można eksplorować i wizualizować za pomocą [usługi Azure Monitor — Metrics Explorer.](../../azure-monitor/platform/metrics-charts.md)

Oto pełna lista źródeł metryk monitora platformy Azure obsługiwanych przez nowsze alerty:

|Typ zasobu  |Obsługiwane wymiary |Alerty dla wielu zasobów| Dostępne wskaźniki|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Tak| Nie | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Wiosna |Nie| Tak|
|Microsoft.Automation/automationKontacje | Tak| Nie | [Konta automatyzacji](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchKonkludy | Nie dotyczy| Nie | [Konta usługi Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Pamięć podręczna firmy Microsoft/Redis|Tak| Nie |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageKonta/mmxclassic|Nie|Tak|
|Microsoft.ClassicStorage/storageKonta/mmxclassic/blobUsługi|Nie|Tak|
|Microsoft.ClassicStorage/storageKonta/mmxclassic/fileServices|Nie|Tak|
|Microsoft.ClassicStorage/storageKonta/mmxclassic/queueServices|Nie|Tak|
|Microsoft.ClassicStorage/storageKonta/mmxclassic/tableServices|Nie|Tak| |
|Microsoft.CognitiveServices/accounts| Nie dotyczy | Nie | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Tak | Tak | [Maszyny wirtualne](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |Nie dotyczy | Tak |[Zestawy skalowania maszyny wirtualnej](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Instancja kontenera firmy Microsoft/kontenerGrupy | Tak| Nie | [Grupy kontenerów](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Tak | Nie | [Zarządzane klastry](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Tak | Tak | |
|Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.| Tak| Nie | [Fabryki danych V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/fabryki |Tak | Nie |[Fabryki danych V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/konta |Nie| Tak|
|Microsoft.DBforMySQL/servers |Nie dotyczy| Nie |[DB dla MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |Nie dotyczy | Nie | [DB dla PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Urządzenia firmy Microsoft.Devices/IotHubs | Nie dotyczy | Nie |[Metryki usługi IoT Hub](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi| Tak | Nie |[Metryki DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domeny|Nie|Tak| |
|Microsoft.EventGrid/tematy |Tak | Nie |[Tematy siatki zdarzeń](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/klastry |Tak| Nie |[Klastry centrów zdarzeń](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/przestrzenie nazw |Tak| Nie |[Centra zdarzeń](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/przechowalnia| Nie |Nie |[Magazyny](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/przepływy pracy |Nie dotyczy | Nie |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningUsługi/obszary robocze|Tak| Nie | [Uczenie maszynowe](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppKonakony/pojemność Pools |Tak| Nie | [Pule pojemności usługi Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy |Tak| Nie | [Woluminy netapp platformy Azure](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|Nie dotyczy| Nie |  |
|Microsoft.Network/dnsZones | Nie dotyczy| Nie | [Strefy DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Nie dotyczy | Nie |[Obwody usługi ExpressRoute](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (tylko dla standardowych jednostek SKU)| Tak| Nie | [Moduły równoważenia obciążenia](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Nie|Tak|
|Microsoft.Network/privateEndpoints|Nie|Tak|
|Microsoft.Network/privateLinkServices|Nie|Tak|
|Microsoft.Network/publicipaddresses |Nie dotyczy | Nie |[Publiczne adresy IP](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Tak | Nie | [Profile usługi Traffic Manager](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/obszary robocze| Tak | Nie | [Obszary robocze usługi Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/przestrzenie nazw | Tak | Nie | [Przekaźniki](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Usługi komunikacji równorzędnej/komunikacji firmy Microsoft|Nie|Tak|
|Microsoft.PowerBIDedicated/capacitys | Nie dotyczy | Nie | [Pojemności](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchSługs |Nie dotyczy|Nie | [Usługi wyszukiwania](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/przestrzenie nazw |Tak| Nie |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Nie | Tak |
|Microsoft.Sql/serwery/bazy danych    | Nie | Tak |
|Microsoft.Storage/storageKondyje |Tak | Nie | [Konta magazynu](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Magazyn/magazyn microsoftKonta/usługi | Tak| Nie | [Usługi obiektów Blob,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) [usługi plików,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) [usługi kolejek](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) i [usługi tabel](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego |Nie dotyczy| Nie | [Analiza strumienia](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Tak|Nie |[Maszyny wirtualne platformy CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingŚrodowiska/multiRolePools | Tak | Nie | [Pule wielu ról środowiska usługi aplikacji](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingŚrodowienia/pracownicy Pools | Tak | Nie | [Pule procesów roboczych środowiska usługi aplikacji](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/farmy serwerów | Tak | Nie | [Plany obsługi aplikacji](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/witryny | Tak | Nie | [Usługi](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) i [funkcje aplikacji](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Tak | Nie | [Gniazda usługi aplikacji](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schemat ładunku

> [!NOTE]
> Można również użyć [wspólnego schematu alertów](https://aka.ms/commonAlertSchemaDocs), który zapewnia zaletę posiadania jednego rozszerzalnego i ujednoliconego ładunku alertów we wszystkich usługach alertów w usłudze Azure Monitor dla integracji elementu webhook. [Dowiedz się więcej o definicji schematów alertów.](https://aka.ms/commonAlertSchemaDefinitions)


Operacja POST zawiera następujący ładunek JSON i schemat dla wszystkich w pobliżu nowszych alertów metryk, gdy używana jest odpowiednio skonfigurowana [grupa akcji:](../../azure-monitor/platform/action-groups.md)

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

* Dowiedz się więcej o nowym [środowisko alertów](../../azure-monitor/platform/alerts-overview.md).
* Dowiedz się więcej o [alertach dziennika na platformie Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Dowiedz się więcej o [alertach na platformie Azure](../../azure-monitor/platform/alerts-overview.md).
