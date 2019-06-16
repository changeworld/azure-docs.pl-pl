---
title: Usługa Azure Service Fabric — monitorowanie wydajności za pomocą rozszerzenia diagnostyki Azure Windows | Dokumentacja firmy Microsoft
description: Korzystanie z diagnostyki Azure Windows, można zebrać liczników wydajności w przypadku klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 20fa8945f01a3431d2fd78d545c43d6215c83f56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110301"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorowanie wydajności za pomocą rozszerzenia diagnostyki Azure Windows

W tym dokumencie opisano kroki wymagane do skonfigurowania zbierania liczników wydajności za pośrednictwem rozszerzenia diagnostyki Azure Windows (WAD) w przypadku klastrów Windows. W przypadku klastrów systemu Linux skonfiguruj [agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) można zebrać liczników wydajności dla węzłów. 

 > [!NOTE]
> Rozszerzenie WAD powinny być wdrażane w klastrze dla tych kroków pracować za Ciebie. Jeśli go nie ustawiono, przejdź do [agregowania zdarzeń i kolekcji przy użyciu Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Liczniki wydajności są zbierane za pomocą WadCfg

Można zebrać liczników wydajności za pomocą funkcji WAD, należy zmodyfikować konfigurację odpowiednio w szablonie usługi Resource Manager klastra. Wykonaj następujące kroki, aby dodać licznika wydajności, które chcesz zebrać do szablonu, a następnie uruchom Uaktualnianie zasobu usługi Resource Manager.

1. Znajdź konfiguracji WAD w szablonie usługi klastra — Znajdź `WadCfg`. Spowoduje dodanie liczników wydajności zbierających dane w ramach `DiagnosticMonitorConfiguration`.

2. Konfigurowanie konfiguracji można zebrać liczników wydajności, dodając następującą sekcję, aby Twoje `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` Definiuje, jak często wartości liczników, które są zbierane są przekazywane do tabeli usługi Azure storage i do dowolnego ujścia skonfigurowany. 

3. Dodaj liczniki wydajności chcesz zbierać do `PerformanceCounterConfiguration` , została zadeklarowana w poprzednim kroku. Wszystkich liczników, które chcesz zbierać jest zdefiniowana za pomocą `counterSpecifier`, `sampleRate`, `unit`, `annotation`, wszelkie odpowiednie `sinks`.

Poniżej przedstawiono przykład konfiguracji za pomocą licznika dla *łączny czas procesora* (ilość czasu Procesor był używany w operacjach przetwarzania) i *wywołań metod usługi Service Fabric aktora na sekundę*, jeden z usługi Service Fabric niestandardowych liczników wydajności. Zapoznaj się [liczników wydajności usługi Reliable Actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) i [liczniki wydajności usługi Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) pełną listę liczników wydajności niestandardowe usługi Service Fabric.

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

 Częstotliwość próbkowania licznika można zmodyfikować zgodnie z potrzebami. Jego format jest `PT<time><unit>`, więc jeśli chcesz, aby zbiera dane licznika co sekundę, następnie należy ustawić `"sampleRate": "PT15S"`.

 Można również używać zmiennych do szablonu ARM do zbierania szereg liczników wydajności, które mogą okazać się przydatna podczas zbierania liczników wydajności na proces. W poniższym przykładzie są zbierane czas modułu zbierającego elementy bezużyteczne procesu i czasu procesora, a następnie 2 liczników wydajności na poszczególnych węzłach za pomocą zmiennych. 

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

 >[!NOTE]
 >Chociaż można użyć `*` do określania grup liczników wydajności, które są nazwane w podobny sposób, wysyłania żadnych liczników za pośrednictwem obiektu sink (do usługi Application Insights) wymaga indywidualnie one zadeklarowane. 

1. Po dodaniu odpowiednich liczników, których muszą być zbierane, musisz uaktualnić zasobu klastra, aby te zmiany zostaną odzwierciedlone w uruchomionej klastra. Zapisz zmodyfikowany `template.json` i Otwórz program PowerShell. Można uaktualnić za pomocą klastra `New-AzResourceGroupDeployment`. Połączenie wymaga nazwy grupy zasobów, plik zaktualizowanego szablonu i pliku parametrów i wyświetla monit o wprowadzenie odpowiednich zmian do zasobów, które można zaktualizować Menedżera zasobów. Gdy zalogowano się do swojego konta i znajdują się w odpowiednią subskrypcję, użyj następującego polecenia w celu przeprowadzenia uaktualnienia:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Po zakończeniu uaktualniania wprowadza (trwa od 15 do 45 minut w zależności od tego, czy jest pierwszym wdrożeniu i rozmiar grupy zasobów), WAD powinien być zbierania liczników wydajności i wysyła je do tabeli o nazwie WADPerformanceCountersTable w ramach konta magazynu skojarzone z klastrem. Zobacz liczniki wydajności w usłudze Application Insights przez [Dodawanie ujścia sztucznej Inteligencji do szablonu usługi Resource Manager](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Kolejne kroki
* Zbieranie większej liczby liczników wydajności dla klastra. Zobacz [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md) listę liczników, które należy zebrać.
* [Użyj monitorowania i diagnostyki za pomocą szablonów maszyn wirtualnych Windows i usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) może wprowadzać dalszych modyfikacji swoje `WadCfg`, np. Konfigurowanie dodatkowych kont magazynu do wysyłania danych diagnostycznych.
* Odwiedź stronę [konstruktora WadCfg](https://azure.github.io/azure-diagnostics-tools/config-builder/) Utwórz szablon od podstaw i upewnij się, Twoje składnia jest poprawna. () https://azure.github.io/azure-diagnostics-tools/config-builder/) Utwórz szablon od podstaw i upewnij się, Twoje składnia jest poprawna.