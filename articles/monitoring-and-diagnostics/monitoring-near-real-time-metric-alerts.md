---
title: Zasoby obsługiwane dla nowszych alertów metryk usługi Azure Monitor
description: Dokumentacja na temat pomocy technicznej, metryk i dzienników dla nowszej platformy Azure, niemal w czasie rzeczywistym alertów dotyczących metryk.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 01c0b5897ab47a2a5091646aed1977779cf0234c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868033"
---
# <a name="supported-metrics-and-creation-methods-for-new-metric-alerts"></a>Obsługiwane metody metryki i tworzenia nowych alertów dotyczących metryk
Platforma Azure obsługuje teraz Monitor [nowego typu alertu Metryka](monitoring-overview-unified-alerts.md) mającego znaczące korzyści w starszej wersji [klasycznego alertów dotyczących metryk](insights-alerts-portal.md). Obsługuje starsze alerty [duże listy metryk](monitoring-supported-metrics.md). Nowszych alertów obsługuje podzbiór (rosnący) tej listy większe. W tym artykule wymieniono tego podzbioru. 

## <a name="portal-powershell-cli-rest-support"></a>Portal, programu PowerShell, interfejsu wiersza polecenia, REST pomocy technicznej
Obecnie można tworzyć nowszych alertów metryk, tylko w witrynie Azure portal, [interfejsu API REST](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) lub [szablonów usługi Resource Manager](monitoring-create-metric-alerts-with-templates.md). Obsługa konfigurowania nowszych alertów przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure (interfejs wiersza polecenia platformy Azure w wersji 2.0) będzie dostępna wkrótce.

## <a name="metrics-and-dimensions-supported"></a>Metryki i wymiary obsługiwane
Nowszych alertów metryk obsługuje alerty dotyczące metryk, używanego przez wymiary. Wymiarów można użyć do filtrowania swoje metryki na odpowiedni poziom. Wszystkie obsługiwane metryki wraz z odpowiednich wymiarów można przeglądać i wizualizować z [usługi Azure Monitor — Eksplorator metryk (wersja zapoznawcza)](monitoring-metric-charts.md).

Poniżej przedstawiono pełną listę źródeł metryk usługi Azure monitor, obsługiwanych przez nowszych alertów:

|Typ zasobu  |Obsługiwane wymiary  | Dostępne metryki|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Yes        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Yes   | [Konta usługi Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | ND| [Konta usługi Batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    ND     |[Pamięć podręczna Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    ND     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   ND      |[Zestawy skalowania maszyn wirtualnych](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Yes| [Grupy kontenerów](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Yes| [Fabryki danych w wersji 1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Yes     |[Fabryki danych w wersji 2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   ND      |[Bazy danych MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    ND     | [DB dla PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Yes      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nie | [Magazyny](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     ND    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    ND     | [Bramy Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | ND| [Strefy DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (tylko w przypadku standardowej jednostki SKU)| Yes| [Moduły równoważenia obciążenia](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  ND       |[Adres publiczny adres IP](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | ND | [Pojemności](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   ND      |[Usługi wyszukiwania](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Yes       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Yes     | [Konta magazynu](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Yes    | [Obiekt blob usługi](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [usługi plików](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [kolejki usług](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) i [tabeli usług](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  ND       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    ND     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (wersja zapoznawcza) | Yes|[Obszary robocze usługi log Analytics](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Dzienniki usługi log Analytics, jako metryki dotyczące alertów 

Umożliwia także nowszych alertów metryk popularnych dzienników usługi Log Analytics, wyodrębnić jako metryki jako część metryki z dzienników w wersji zapoznawczej.  
- [Liczniki wydajności](../log-analytics/log-analytics-data-sources-performance-counters.md) maszyn Windows i Linux
- [Rekordy pulsu dla kondycji agenta](../operations-management-suite/oms-solution-agenthealth.md)
- [Zarządzanie aktualizacjami](../operations-management-suite/oms-solution-update-management.md) rekordów
 
> [!NOTE]
> Określone metryki i/lub wymiaru będą wyświetlane tylko jeśli dane dla niego istnieje w wybranym okresie. Te metryki są dostępne dla klientów z obszarami roboczymi w regionie wschodnie stany USA, zachodnio-środkowe stany USA i Europa Zachodnia, którzy opracowali w wersji zapoznawczej. Jeśli chcesz być częścią tej wersji zapoznawczej, zarejestruj się przy użyciu [ankietę](https://aka.ms/MetricLogPreview).

Poniższa lista źródeł metryki na podstawie dzienników usługi Log Analytics jest obsługiwane:

Nazwa/szczegóły metryki  |Obsługiwane wymiary  | Typ dziennika  |
|---------|---------|---------|
|Average_Avg. Czas dysku w s/Odczyt     |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Average_Avg. Dysku w s/Zapis     |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Długość kolejki dysku Average_Current   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Average_Disk odczyty/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Average_Disk dyskowe/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
|   Average_ % wolnego miejsca    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Average_Available pamięć (MB)     |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Average_ % Zadeklarowane bajty w użyciu    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
| Average_Bytes odebrane/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
|  Average_Bytes wysłane/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
|  Average_Bytes liczba bajtów/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
|  Average_ czas procesora (%)    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
|   Długość kolejki Average_Processor    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Dane licznika wydajności Windows      |
|   Average_ % wolnych węzłów i   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ % wolnego miejsca   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ % użytych węzłów i  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Używany obszar Average_ %   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Disk Odczytane bajty/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Disk odczyty/s |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Disk dyskowe/s |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Disk zapisane bajty/s   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Disk zapisy/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Free (MB) |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Logical Bajty dysku/s |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ % dostępnej pamięci |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ % dostępnego obszaru wymiany |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ procent wykorzystania pamięci  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Używany obszar wymiany Average_ %  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Pamięć (MB) Average_Available    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Available obszar wymiany  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Page odczyty/s |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Page zapisy/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Pages na sekundę  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Obszar wymiany (MB) Average_Used |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Used pamięć (MB) |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Total Bajty przesłane    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Odebrane bajty Average_Total   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Total bajtów    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Total pakiety przesyłane  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Odebrane pakiety Average_Total |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Błędy Rx Average_Total    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Błędy Tx Average_Total    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Total kolizji   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Avg. Czas dysku w s/Odczyt |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Avg. Dysku w s/Transfer |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Avg. Dysku w s/Zapis    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Physical Bajty dysku/s    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas uprzywilejowany Average_Pct    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas użytkownika Average_Pct  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    KB pamięci Average_Used |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Pamięć współużytkowaną Average_Virtual  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ czas DPC (%) |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas bezczynności (%) Average_    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ czas przerwań (%)   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas oczekiwania operacji We/Wy dla % Average_ |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas nieuprzywilejowany (%) Average_    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas uprzywilejowany Average_ %  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_ czas procesora (%)   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Czas użytkownika % Average_    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Pamięć fizyczna Average_Free   |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Free miejsce w plikach stronicowania |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Free pamięci wirtualnej    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Processes  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Size przechowywane w plikach stronicowania    |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Uptime |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Average_Users  |     Tak — komputera, nazwa obiektu, InstanceName, Ścieżka_licznika i system źródłowy    |   Licznik wydajności systemu Linux      |
|    Puls  |     Tak — komputer, OSType, wersji i SourceComputerId    |   Rekordy pulsu |
|    Aktualizacja |     Tak — komputer, produktów, klasyfikacji, UpdateState, opcjonalnie & zatwierdzone    |   Zarządzanie aktualizacjami |



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
