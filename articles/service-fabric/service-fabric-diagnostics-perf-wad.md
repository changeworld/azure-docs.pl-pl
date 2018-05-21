---
title: Azure Service Fabric — wydajności monitorowania za pomocą rozszerzenia Windows Azure Diagnostics | Dokumentacja firmy Microsoft
description: Użyj diagnostyki Windows Azure można zebrać liczników wydajności dla klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: srrengar
ms.openlocfilehash: 5c8c1f107e9e70e72c48ea93ef211b2a760ffb5c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorowanie wydajności z rozszerzeniem diagnostyki Windows Azure

W tym dokumencie opisano kroki wymagane do skonfigurowania kolekcji liczników wydajności za pośrednictwem rozszerzenia systemu Windows Azure Diagnostics (WAD) w przypadku klastrów systemu Windows. W przypadku klastrów systemu Linux, skonfiguruj [Agent pakietu OMS](service-fabric-diagnostics-oms-agent.md) można zebrać liczników wydajności dla węzłów. 

 > [!NOTE]
> Rozszerzenie WAD powinny zostać wdrożone w klastrze dla tej procedury do działania. Jeśli go nie ustawiono, przejdź do [agregacji zdarzeń i kolekcji przy użyciu systemu Windows Azure Diagnostics](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

## <a name="collect-performance-counters-via-the-wadcfg"></a>Zbieraj liczniki wydajności za pośrednictwem WadCfg

Można zebrać liczników wydajności za pośrednictwem WAD, należy zmodyfikować konfigurację odpowiednio w szablonie usługi Resource Manager z klastra. Wykonaj następujące kroki, aby dodać licznika wydajności, które chcesz zebrać do szablonu, a następnie uruchom uaktualnianie zasobów Menedżera zasobów.

1. Znajdź konfiguracji WAD w szablonie klastra — Znajdź `WadCfg`. Dodasz liczniki wydajności do w obszarze `DiagnosticMonitorConfiguration`.

2. Konfigurowanie konfiguracji można zebrać liczników wydajności, dodając w poniższej sekcji, aby Twoje `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` Definiuje konfiguracji frquently wartości liczników, które są zbierane są przekazywane do tabeli magazynu systemu Azure i dla każdego obiektu sink. 

3. Dodaj liczniki wydajności, które chcesz zebrać na potrzeby `PerformanceCounterConfiguration` który został zadeklarowany w poprzednim kroku. Każdego licznika chcesz zbierać jest zdefiniowana z `counterSpecifier`, `sampleRate`, `unit`, `annotation`, a wszelkie odpowiednie `sinks`.

Poniżej przedstawiono przykład konfiguracji licznika dla *łączny czas procesora* (ilość czasu procesora CPU był używany w operacjach przetwarzania) i *wywołań metod aktora sieci szkieletowej usług na sekundę*, jeden z liczników wydajności niestandardowe sieci szkieletowej usług. Zapoznaj się [liczników wydajności usługi niezawodnego aktora](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) i [niezawodnej liczników wydajności usługi](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) pełną listę liczników niestandardowych wydajności sieci szkieletowej usług.

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

 Częstotliwość próbkowania licznika można zmodyfikować zgodnie z potrzebami. Format dla niego `PT<time><unit>`, tak aby licznik zbierane w ciągu sekundy, następnie należy ustawić `"sampleRate": "PT15S"`.

 >[!NOTE]
 >Chociaż można użyć `*` do określania grup liczniki wydajności, które są nazywane podobnie, wysyłanie liczników za pośrednictwem obiektu sink (do usługi Application Insights) wymaga indywidualnie była zadeklarowana. 

4. Po dodaniu liczników wydajności odpowiednie, które mają być zbierane, musisz uaktualnić zasobu klastra, aby te zmiany zostaną odzwierciedlone w uruchomionej klastra. Zapisz z zmodyfikowanych `template.json` i otwórz programu PowerShell. Można uaktualnić za pomocą klastra `New-AzureRmResourceGroupDeployment`. Wywołanie wymaga nazwy grupy zasobów, plik zaktualizowany szablon i plik parametrów i wyświetla monit o Resource Manager, aby wprowadzić zmiany odpowiednie zasoby, które można zaktualizować. Po zalogowano do konta i w prawo subskrypcji, użyj następującego polecenia w celu przeprowadzenia uaktualnienia:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Po zakończeniu uaktualniania zetknie (trwa od 15 do 45 minut), WAD powinny być zbierania liczników wydajności i wysyłania ich do tabeli o nazwie WADPerformanceCountersTable w ramach konta magazynu skojarzone z klastra. Zobacz liczniki wydajności w usłudze Application Insights przez [Dodawanie zbiornika AI do szablonu usługi Resource Manager](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Kolejne kroki
* Zbieranie większej liczby liczników wydajności dla klastra. Zobacz [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md) listę liczników, które należy zebrać.
* [Użyj monitorowania i diagnostyki z szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) na dalsze zmiany w Twojej `WadCfg`, łącznie z konfigurowaniem dodatkowych kont magazynu do wysyłania danych diagnostycznych.
