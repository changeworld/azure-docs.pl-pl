---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431037"
---
## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowych

W tej sekcji opisano funkcje usługi Azure Time Series Insights zachować aplikacje i usługi działające, nawet w przypadku wystąpienia awarii (nazywane *odzyskiwania po awarii firm*).

### <a name="high-availability"></a>Wysoka dostępność

Jako usługi Azure Time Series Insights zapewnia pewne *wysokiej dostępności* funkcji przy użyciu narzędzia zwolnienia na poziomie regionu platformy Azure. Na przykład platforma Azure obsługuje możliwości odzyskiwania po awarii za pośrednictwem platformy Azure *dostępności między regionami* funkcji.

Dodatkowe funkcje wysokiej dostępności, dostępne za pośrednictwem platformy Azure (i również dostępne dla każdego wystąpienia usługi Time Series Insights):

- **Tryb failover**: Platforma Azure udostępnia [replikacji geograficznej i równoważenie obciążenia](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Przywracanie danych** i **odzyskiwania magazynu**: Platforma Azure udostępnia [kilka opcji zachowywania i odzyskiwania danych](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Site recovery**: Platforma Azure udostępnia funkcje odzyskiwania lokacji za pośrednictwem [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Upewnij się, że włączono odpowiednie funkcje platformy Azure w celu zapewnienia wysokiej dostępności globalnej, między regionami urządzeniami i użytkownikami.

> [!NOTE]
> Skonfigurowanie usługi Azure umożliwiające dostępności między regionami w usłudze Azure Time Series Insights jest wymagana żadna konfiguracja wyższą dostępność między regionami.

### <a name="iot-and-event-hubs"></a>IoT i centrów zdarzeń

Niektóre usługi Azure IoT obejmują również wbudowanych funkcji odzyskiwania po awarii:

- [Odzyskiwanie po awarii o wysokiej dostępności usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), w tym nadmiarowość wewnątrzlokacyjnej między regionami
- [Zasady centrów zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Nadmiarowość magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrowanie usługi Time Series Insights z innymi usługami zapewnia możliwości odzyskiwania dodatkowe po awarii. Na przykład dane telemetryczne wysyłane do Centrum zdarzeń może być utrwalone w kopii zapasowej bazy danych z magazynu obiektów Blob platformy Azure.

### <a name="time-series-insights"></a>Time Series Insights

Istnieje kilka sposobów, aby zachować swoje dane usługi Time Series Insights, aplikacje i usługi działające, nawet wtedy, gdy są one przerwane. 

Jednakże należy określić pełną kopię zapasową środowiska Azure Time Series również to wymagane, do następujących celów:

- Jako *wystąpienia trybu failover* specjalnie dla usługi Time Series Insights przekierować dane, a ruch do
- Aby zachować dane i informacje dotyczące inspekcji

Ogólnie rzecz biorąc najlepszym sposobem zduplikowane środowiska usługi Time Series Insights jest utworzenie drugiej środowiska usługi Time Series Insights w regionie świadczenia usługi Azure backup. Zdarzenia również są wysyłane do tego środowiska dodatkowej ze źródła zdarzenia podstawowego. Należy upewnić się, że grupy odbiorców drugi, dedykowanych. Postępuj zgodnie z tego źródła wytycznych odzyskiwania po awarii biznesowe, zgodnie z wcześniejszym opisem.

Aby utworzyć duplikat środowiska:

1. Należy utworzyć środowisko, w drugim regionie. Aby uzyskać więcej informacji, zobacz [Utwórz nowe środowisko usługi Time Series Insights w witrynie Azure portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Utwórz drugi dedykowanej grupy klientów dla źródła zdarzenia.
1. Łączenie źródła zdarzeń do nowego środowiska. Upewnij się, wyznaczonych grupy odbiorców drugi, dedykowanych.
1. Przejrzyj usługi Time Series Insights [usługi IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) i [usługi Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentacji.

Jeśli wystąpi zdarzenie:

1. Jeśli swój region podstawowy jest wpływu zdarzenia po awarii, należy przekierować operacje tworzenia kopii zapasowej środowiska usługi Time Series Insights.
1. Tworzenie kopii zapasowych i odzyskiwania wszystkie dane telemetryczne i zapytania usługi Time Series Insights, użyj drugiego regionu.

> [!IMPORTANT]
> W przypadku przejścia do trybu failover:
> 
> * Może również wystąpić opóźnienie.
> * Przechwytują wzrost przetwarzanie komunikatów mogą wystąpić, ponieważ operacje są przekierowane.
> 
> Aby uzyskać więcej informacji, zobacz [zmniejszyć opóźnienia w usłudze Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

