---
title: Usługa Azure sieci szkieletowej zdarzeń agregacji Diagnostyka Azure systemu Linux | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agregowania i zbierania zdarzeń przy użyciu LAD monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 9ad63a67d3b51cad1376eb9e97e68fb9102013e3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregacja zdarzeń i kolekcji za pomocą diagnostyki Azure systemu Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Jeśli korzystasz z klastra usługi sieć szkieletowa usług Azure, jest dobrym pomysłem jest zebrać dzienniki ze wszystkich węzłów w centralnej lokalizacji. Posiadanie dzienniki w centralnej lokalizacji ułatwiają analizowanie i rozwiązywanie problemów w klastrze lub problemy w aplikacji i usług działających w klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników to próbę użycia rozszerzenia Linux Azure Diagnostics (LAD), która przekazuje dzienniki usługi Azure Storage, a także ma możliwość przesyłania dzienników usługi Application Insights dla platformy Azure lub usługi Event Hubs. Umożliwia także procesu zewnętrznego do odczytywania zdarzenia z magazynu i umieścić w produkcie platformy analizy, takich jak [analizy dzienników](../log-analytics/log-analytics-service-fabric.md) lub innego rozwiązania do analizowania dziennika.

## <a name="log-and-event-sources"></a>Źródła dziennika i zdarzenie

### <a name="service-fabric-platform-events"></a>Zdarzenia platformy Service Fabric
Sieć szkieletowa usług emituje kilka poza pole dzienniki za pośrednictwem [LTTng](http://lttng.org), w tym zdarzenia operacyjne lub zdarzeniach środowiska uruchomieniowego. Te dzienniki są przechowywane w lokalizacji, która określa szablon Menedżera zasobów klastra. Można pobrać lub ustawić szczegóły konta magazynu, wyszukaj tagu **AzureTableWinFabETWQueryable** i poszukaj **StoreConnectionString**.

### <a name="application-events"></a>Zdarzenia aplikacji
 Zdarzenia wysyłanego z Twojej aplikacji i usług kodu określonych przez użytkownika podczas Instrumentacji oprogramowania. Można użyć dowolnego rozwiązania rejestrowania, który zapisuje pliki dzienników tekstowych — na przykład LTTng. Aby uzyskać więcej informacji zobacz dokumentację LTTng na śledzenia aplikacji.

[Monitorowanie i diagnozowanie usług w Instalatorze programowanie komputera lokalnego](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Wdrażanie rozszerzenia diagnostyki
Pierwszym etapem zbierania dzienników jest wdrażanie rozszerzenia diagnostyki na wszystkich maszynach wirtualnych w klastrze usługi sieć szkieletowa usług. Rozszerzenie diagnostycznych zbiera dzienniki na każdej maszynie Wirtualnej i przekazuje je do konta magazynu, który określisz. 

Aby wdrożyć rozszerzenie diagnostyki maszyny wirtualne w klastrze, w ramach tworzenia klastra, należy ustawić **diagnostyki** do **na**. Po utworzeniu klastra nie można zmienić to ustawienie, korzystając z portalu, dzięki czemu będzie trzeba wprowadzić odpowiednie zmiany w szablonie usługi Resource Manager.

Spowoduje to skonfigurowanie agenta LAD do monitorowania określonych plików dziennika. Zawsze, gdy nowy wiersz jest dołączany do pliku, tworzy wpisu syslog, który jest wysyłany do magazynu (tabeli), określony.


## <a name="next-steps"></a>Kolejne kroki

1. Aby bardziej szczegółowo zrozumieć, jakie zdarzenia należy sprawdzić podczas rozwiązywania problemów, zobacz [dokumentacji LTTng](http://lttng.org/docs) i [przy użyciu LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
2. [Skonfiguruj agenta analizy dzienników](service-fabric-diagnostics-event-analysis-oms.md) ułatwiające zbieranie metryk monitorowanie kontenery wdrożone w klastrze i wizualizacji dzienników 