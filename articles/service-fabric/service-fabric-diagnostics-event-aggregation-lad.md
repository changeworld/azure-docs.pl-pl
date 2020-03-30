---
title: Agregacja zdarzeń za pomocą diagnostyki platformy Azure systemu Linux
description: Dowiedz się więcej o agregowaniu i zbieraniu zdarzeń przy użyciu LAD do monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609965"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregacja i zbieranie zdarzeń przy użyciu diagnostyki platformy Azure systemu Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Podczas uruchamiania klastra sieci szkieletowej usług Azure, warto zbierać dzienniki ze wszystkich węzłów w centralnej lokalizacji. Posiadanie dzienników w centralnej lokalizacji ułatwia analizowanie i rozwiązywanie problemów w klastrze lub problemów w aplikacjach i usługach uruchomionych w tym klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia diagnostyki platformy Azure (LAD) systemu Linux, które przekazuje dzienniki do usługi Azure Storage, a także ma możliwość wysyłania dzienników do usługi Azure Application Insights lub Event Hubs. Można również użyć procesu zewnętrznego, aby odczytać zdarzenia z magazynu i umieścić je w produkcie platformy analizy, takich jak [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-service-fabric.md) lub innego rozwiązania do analizy dzienników.

## <a name="log-and-event-sources"></a>Źródła dzienników i zdarzeń

### <a name="service-fabric-platform-events"></a>Zdarzenia platformy sieci szkieletowej usług
Sieć szkieletowa usług emituje kilka gotowych dzienników za pośrednictwem [protokołu LTTng,](https://lttng.org)w tym zdarzenia operacyjne lub zdarzenia środowiska uruchomieniowego. Te dzienniki są przechowywane w lokalizacji, którą określa szablon Menedżera zasobów klastra. Aby uzyskać lub ustawić szczegóły konta magazynu, wyszukaj tag **AzureTableWinFabETWQueryable** i **poszukaj StoreConnectionString**.

### <a name="application-events"></a>Zdarzenia aplikacji
 Zdarzenia emitowane z kodu aplikacji i usług, jak określono przez użytkownika podczas instrumentacji oprogramowania. Można użyć dowolnego rozwiązania rejestrowania, które zapisuje tekstowe pliki dziennika — na przykład LTTng. Aby uzyskać więcej informacji, zobacz dokumentację LTTng dotyczącą śledzenia aplikacji.

[Monitoruj i diagnozuj usługi w konfiguracji lokalnego rozwoju maszyn](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Wdrażanie rozszerzenia diagnostyki
Pierwszym krokiem w zbieraniu dzienników jest wdrożenie rozszerzenia diagnostyki na każdej z maszyn wirtualnych w klastrze sieci szkieletowej usług. Rozszerzenie diagnostyki zbiera dzienniki na każdej maszynie Wirtualnej i przekazuje je do konta magazynu, które określisz. 

Aby wdrożyć rozszerzenie Diagnostyka na maszyny wirtualne w klastrze w ramach tworzenia klastra, ustaw **diagnostykę** **na Włączone**. Po utworzeniu klastra nie można zmienić tego ustawienia przy użyciu portalu, więc trzeba będzie wprowadzić odpowiednie zmiany w szablonie Menedżera zasobów.

Spowoduje to skonfigurowanie agenta LAD do monitorowania określonych plików dziennika. Za każdym razem, gdy nowy wiersz jest dołączany do pliku, tworzy wpis syslog, który jest wysyłany do magazynu (tabela), który został określony.


## <a name="next-steps"></a>Następne kroki

1. Aby dokładniej zrozumieć, jakie zdarzenia należy zbadać podczas rozwiązywania problemów, zobacz [dokumentację LTTng](https://lttng.org/docs) i [korzystanie z LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Konfigurowanie agenta usługi Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) w celu zbierania danych, monitorowania kontenerów wdrożonych w klastrze i wizualizacji dzienników 
