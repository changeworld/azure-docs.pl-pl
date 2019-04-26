---
title: Usługa Azure Service Fabric zdarzenie agregacji przy użyciu diagnostyki Azure dla systemu Linux | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agregowania i zbieranie zdarzeń za pomocą LAD do monitorowania i diagnostyki klastrów usługi Azure Service Fabric.
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
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 212158d9a76fa2e49c60be0b5c52f281497c155b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393133"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Zdarzenie agregacji i kolekcji przy użyciu diagnostyki Azure Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Po uruchomieniu klastra usługi Azure Service Fabric to dobry pomysł, aby zbierać dzienniki z wszystkimi węzłami w centralnej lokalizacji. Posiadanie dzienniki w centralnej lokalizacji, ułatwiają analizowanie i rozwiązywanie problemów w klastrze lub problemy w aplikacji i usług działających w klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia diagnostyki Azure Linux (LAD), przekazuje dzienniki do usługi Azure Storage, która ma również możliwość przesyłania dzienników do usługi Azure Application Insights lub centrów zdarzeń. Umożliwia także procesu zewnętrznego do odczytywania zdarzeń z magazynu i umieszczenie ich w produkcie platformy analizy, takie jak [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-service-fabric.md) lub innego rozwiązania do analizowania dziennika.

## <a name="log-and-event-sources"></a>Źródła dzienników i zdarzeń

### <a name="service-fabric-platform-events"></a>Zdarzenia platformy usługi Service Fabric
Usługa Service Fabric emituje kilka dzienników out-of--box za pomocą [LTTng](https://lttng.org), w tym zdarzenia operacyjne lub zdarzeniach środowiska uruchomieniowego. Te dzienniki są przechowywane w lokalizacji, która określa szablon usługi Resource Manager klastra. Do pobierania lub ustawiania szczegółów konta magazynu, wyszukaj tagu **AzureTableWinFabETWQueryable** i poszukaj **StoreConnectionString**.

### <a name="application-events"></a>Zdarzenia aplikacji
 Zdarzenia emitowane w kodzie twojej aplikacji i usług określonych przez Ciebie podczas Instrumentacji oprogramowania. Możesz użyć dowolnego rozwiązania rejestrowania, który zapisuje pliki dzienników tekstowych — na przykład LTTng. Aby uzyskać więcej informacji zobacz dokumentację LTTng na śledzenie aplikacji.

[Monitorowanie i diagnozowanie usług w lokalnym komputerze deweloperskim](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Wdrażanie rozszerzenia diagnostyki
Pierwszym krokiem podczas zbierania dzienników jest wdrażanie rozszerzenia diagnostyki na wszystkich maszynach wirtualnych w klastrze usługi Service Fabric. Rozszerzenie diagnostyki zbiera dzienniki na każdej maszynie Wirtualnej i przekazuje je do konta magazynu, który określisz. 

Aby wdrożyć rozszerzenie diagnostyki maszyn wirtualnych w klastrze jako część tworzenia klastra, należy ustawić **diagnostyki** do **na**. Po utworzeniu klastra, nie możesz zmienić to ustawienie przy użyciu portalu, dzięki czemu będzie trzeba wprowadzić odpowiednie zmiany w szablonie usługi Resource Manager.

Pozwoli to na skonfigurowanie agenta LAD, aby monitorować określone pliki dziennika. Zawsze, gdy nowy wiersz jest dołączany do pliku, tworzy wpis dziennika systemu, które są wysyłane do magazynu (tabela), który określiłeś.


## <a name="next-steps"></a>Kolejne kroki

1. Aby zrozumieć, w bardziej szczegółowo, jakie zdarzenia należy sprawdzić podczas rozwiązywania problemów, zobacz [dokumentacji LTTng](https://lttng.org/docs) i [przy użyciu LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Konfigurowanie agenta usługi Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) ułatwiające zbieranie metryk monitorowanie kontenerów wdrożonych w klastrze i wizualizacji dzienników 
