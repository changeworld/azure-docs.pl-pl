---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236521"
---
## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowych

W tej sekcji opisano funkcje usługi Azure Time Series Insights zachować aplikacje i usługi działające nawet w przypadku wystąpienia awarii (**odzyskiwania po awarii firm**).

### <a name="high-availability"></a>Duża dostępność

Jako usługi Azure Time Series Insights zapewnia pewne **wysokiej dostępności** funkcje na poziomie regionu platformy Azure przy użyciu zwolnienia. Na przykład platforma Microsoft Azure obsługuje możliwości odzyskiwania po awarii za pośrednictwem platformy Azure **dostępności między regionami** funkcji.

Funkcje dodatkowe wysokiej dostępności, dostępne za pośrednictwem platformy Azure (i również dostępne dla każdego wystąpienia usługi Time Series Insights):

1. **Tryb failover**: Platforma Azure udostępnia [replikacji geograficznej i ładowania równoważenia](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Przywracanie danych** i **odzyskiwania magazynu**: Platforma Azure udostępnia [kilka opcji zachowywania i odzyskiwania danych](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Site Recovery** funkcje za pośrednictwem [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Upewnij się włączyć te funkcje platformy Azure umożliwia globalny między regionami, wysoką dostępność dla używanych urządzeń i użytkowników.

> [!NOTE]
> W przypadku platformy Azure jest skonfigurowane do umożliwiania **dostępności między regionami**, nie jest wymagane w ramach usługi Azure Time Series Insights żadna konfiguracja wyższą dostępność między regionami.

### <a name="iot-and-event-hubs"></a>IoT i centrów zdarzeń

Niektóre usługi Azure IoT obejmują również wbudowanych funkcji odzyskiwania po awarii:

1. [Odzyskiwanie po awarii o wysokiej dostępności usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) tym nadmiarowość wewnątrzlokacyjnej między regionami.
1. [Zasad Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Nadmiarowość magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrowanie usługi Time Series Insights za pomocą tych innych usług zapewnia możliwości odzyskiwania dodatkowe po awarii. Na przykład dane telemetryczne wysyłane do Centrum zdarzeń może być utrwalone w kopii zapasowej bazy danych usługi Azure Blob Storage.

### <a name="time-series-insights"></a>Time Series Insights

Istnieje kilka sposobów, aby zachować dane usługi Time Series Insights, aplikacje i usługi działające nawet wtedy, gdy są one przerwane. Należy także określić, że kopię środowiska Azure Time Series pełną, duplikatów kopii zapasowej jest wymagane:

1. Jako usługa TSI Przesyła specyficznych **wystąpienia trybu failover** można przekierować danych, a ruch do.
1. Na potrzeby inspekcji i dane zachowanie.

Ogólnie rzecz biorąc najlepszym sposobem zduplikowane w środowisku usługi TSI jest utworzenie drugiej środowiska TSI w regionie świadczenia usługi Azure backup. Zdarzenia również są wysyłane do tego środowiska dodatkowej ze źródła zdarzenia podstawowego. Upewnij się, aby używają grupy odbiorców drugi, dedykowanym i postępuj zgodnie z tego źródła biznesowe awarii odzyskiwania wskazówki (powyżej).

W szczególności do utworzenia zduplikowanych środowiska:

1. Utworzyć środowisko w drugim regionie ([Utwórz nowe środowisko usługi Time Series Insights w witrynie Azure portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Utwórz drugi dedykowanej grupy klientów dla źródła zdarzenia.
1. Łączenie źródła zdarzeń do nowego środowiska, upewniając się wyznaczyć grupy odbiorców drugi, dedykowanej.
1. Przejrzyj usługi Time Series Insights [usługi IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) i [Centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentacji.

Na koniec:

* Jeśli swój region podstawowy jest wpływu zdarzenia po awarii, należy przekierować operacje tworzenia kopii zapasowej środowiska usługi Time Series Insights.
* Użyj drugiego regionu, do tworzenia kopii zapasowych i odzyskiwania wszystkie dane telemetryczne i zapytania TSI.

> [!IMPORTANT]
> * Należy zauważyć, że opóźnienie może się pojawić w przypadku przejścia w tryb failover.
> * Tryb failover także może spowodować chwilową kolekcji podczas przetwarzania komunikatu, ponieważ operacje są przekierowane.
> * Aby uzyskać więcej informacji, zobacz [zmniejszyć opóźnienia w usłudze Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).