---
title: Monitorowanie wydajności za pomocą diagnostyki platformy Windows Azure
description: Diagnostyka systemu Windows Azure służy do zbierania liczników wydajności dla klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609914"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorowanie wydajności za pomocą rozszerzenia Diagnostyka systemu Windows Azure

W tym dokumencie opisano kroki wymagane do skonfigurowania kolekcji liczników wydajności za pośrednictwem rozszerzenia diagnostyki systemu Windows Azure (WAD) dla klastrów systemu Windows. W przypadku klastrów systemu Linux należy skonfigurować [agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) do zbierania liczników wydajności dla węzłów. 

 > [!NOTE]
> Rozszerzenie WAD należy wdrożyć w klastrze dla tych kroków do pracy dla Ciebie. Jeśli nie jest skonfigurowany, przejdź do [agregacji i kolekcji zdarzeń przy użyciu diagnostyki systemu Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Zbieraj liczniki wydajności za pośrednictwem WadCfg

Aby zebrać liczniki wydajności za pośrednictwem wad, należy odpowiednio zmodyfikować konfigurację w szablonie Menedżera zasobów klastra. Wykonaj następujące kroki, aby dodać licznik wydajności, który chcesz zebrać do szablonu i uruchomić uaktualnienie zasobu Menedżera zasobów.

1. Znajdź konfigurację WAD w szablonie `WadCfg`klastra - znajdź . Będziesz dodawać liczniki wydajności do `DiagnosticMonitorConfiguration`zbierania w obszarze .

2. Skonfiguruj konfigurację, aby zbierać liczniki wydajności, dodając do programu `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    Definiuje, `scheduledTransferPeriod` jak często wartości liczników, które są zbierane są przenoszone do tabeli magazynu platformy Azure i do dowolnego skonfigurowanego ujścia. 

3. Dodaj liczniki wydajności, które chcesz `PerformanceCounterConfiguration` zebrać do tego, który został zadeklarowany w poprzednim kroku. Każdy licznik, który chcesz zebrać, `sampleRate` `unit`jest `annotation`zdefiniowany `sinks`za `counterSpecifier`pomocą , , , i wszelkich istotnych .

Oto przykład konfiguracji z licznikiem *dla całkowitego czasu procesora* (czas procesora był używany do przetwarzania operacji) i *wywołania metody aktora sieci szkieletowej usług na sekundę*, jeden z liczników wydajności niestandardowej sieci szkieletowej usług. Aby uzyskać pełną listę niestandardowych liczników wydajności sieci szkieletowej usług, należy zapoznać się z [licznikami wydajności niezawodnego aktora](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) i [licznikami niezawodnych wydajności](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) usług.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Częstotliwość próbkowania licznika może być modyfikowana zgodnie z potrzebami. Format dla niego `PT<time><unit>`jest , więc jeśli chcesz licznik zbierane co `"sampleRate": "PT15S"`sekundę, a następnie należy ustawić .

 Można również użyć zmiennych w szablonie ARM, aby zebrać tablicę liczników wydajności, które mogą się przydać podczas zbierania liczników wydajności na proces. W poniższym przykładzie zbieramy czas procesora i czas modułu zbierającego elementy bezużyteczne na proces, a następnie 2 liczniki wydajności w samych węzłach przy użyciu zmiennych. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Po dodaniu odpowiednich liczników wydajności, które muszą zostać zebrane, należy uaktualnić zasób klastra, tak aby te zmiany zostały odzwierciedlone w uruchomionym klastrze. Zapisz zmodyfikowany `template.json` i otwórz program PowerShell. Klaster można uaktualnić za pomocą programu `New-AzResourceGroupDeployment`. Wywołanie wymaga nazwy grupy zasobów, zaktualizowanego pliku szablonu i pliku parametrów i monituje Menedżera zasobów o wprowadzenie odpowiednich zmian w zaktualizowanych zasobach. Po zalogowaniu się na konto i przejściu do odpowiedniej subskrypcji użyj następującego polecenia, aby uruchomić uaktualnienie:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Po zakończeniu uaktualniania (trwa od 15 do 45 minut w zależności od tego, czy jest to pierwsze wdrożenie i rozmiar grupy zasobów), WAD powinny zbierać liczniki wydajności i wysyłać je do tabeli o nazwie WADPerformanceCountersTable na koncie magazynu skojarzonym z klastrem. Zobacz liczniki wydajności w usłudze Application Insights, [dodając ujście AI do szablonu Menedżera zasobów](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Następne kroki
* Zbierz więcej liczników wydajności dla klastra. Zobacz [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md) dla listy liczników, które należy zebrać.
* [Użyj monitorowania i diagnostyki za pomocą szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager,](../virtual-machines/windows/extensions-diagnostics-template.md) aby wprowadzić dalsze modyfikacje do swojego `WadCfg`programu, w tym skonfigurowanie dodatkowych kont magazynu w celu wysyłania danych diagnostycznych.
* Odwiedź [konstruktora WadCfg,](https://azure.github.io/azure-diagnostics-tools/config-builder/) aby utworzyć szablon od podstaw i upewnij się, że składnia jest poprawna. (https://azure.github.io/azure-diagnostics-tools/config-builder/) aby utworzyć szablon od podstaw i upewnij się, że składnia jest poprawna.
