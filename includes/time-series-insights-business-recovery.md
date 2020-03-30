---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013656"
---
## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii w biznesie

W tej sekcji opisano funkcje usługi Azure Time Series Insights, które utrzymują aplikacje i usługi uruchomione, nawet jeśli wystąpi awaria (znana jako *odzyskiwanie po awarii*biznesowej).

### <a name="high-availability"></a>Wysoka dostępność

Jako usługa platformy Azure usługa Time Series Insights zapewnia pewne funkcje *wysokiej dostępności* przy użyciu zwolnień na poziomie regionu platformy Azure. Na przykład platforma Azure obsługuje funkcje odzyskiwania po awarii za pośrednictwem funkcji *dostępności między regionami* platformy Azure.

Dodatkowe funkcje o wysokiej dostępności udostępniane za pośrednictwem platformy Azure (dostępne również dla dowolnego wystąpienia usługi Time Series Insights) obejmują:

- **Przewijanie w błąd:** Platforma Azure zapewnia [replikację geograficzną i równoważenie obciążenia](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Przywracanie** i **odzyskiwanie danych:** Platforma Azure oferuje [kilka opcji przechowywania i odzyskiwania danych.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)
- **Usługa Azure Site Recovery:** Platforma Azure udostępnia funkcje odzyskiwania witryny za pośrednictwem [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Usługa Azure Backup:** [Usługa Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) obsługuje zarówno lokalną, jak i w chmurze kopię zapasową maszyn wirtualnych platformy Azure.

Upewnij się, że włączysz odpowiednie funkcje platformy Azure, aby zapewnić globalną, wysoką dostępność dla urządzeń i użytkowników w różnych regionach.

> [!NOTE]
> Jeśli platforma Azure jest skonfigurowana w celu włączenia dostępności między regionami, w usłudze Azure Time Series Insights nie jest wymagana żadna dodatkowa konfiguracja dostępności między regionami.

### <a name="iot-and-event-hubs"></a>IoT i centra zdarzeń

Niektóre usługi Azure IoT zawierają również wbudowane funkcje odzyskiwania po awarii w firmie:

- [Usługa Azure IoT Hub odzyskiwanie po awarii o wysokiej dostępności](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), które obejmuje nadmiarowość wewnątrz regionu
- [Zasady usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Nadmiarowość usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integracja usługi Time Series Insights z innymi usługami zapewnia dodatkowe możliwości odzyskiwania po awarii. Na przykład dane telemetryczne wysyłane do Centrum zdarzeń mogą być utrwalone do zapasowej bazy danych magazynu obiektów Blob platformy Azure.

### <a name="time-series-insights"></a>Time Series Insights

Istnieje kilka sposobów, aby zachować dane, aplikacje i usługi usługi usługi Time Series Insights, nawet jeśli zostały zakłócone. 

Można jednak ustalić, że wymagana jest również pełna kopia kopii zapasowej środowiska szeregów czasowych platformy Azure, aby następujące cele:

- Jako *wystąpienie trybu failover* specjalnie dla usługi Time Series Insights, aby przekierować dane i ruch do
- Aby zachować dane i informacje inspekcji

Ogólnie rzecz biorąc najlepszym sposobem zduplikowania środowiska usługi Time Series Insights jest utworzenie drugiego środowiska usługi Time Series Insights w regionie platformy Azure kopii zapasowej. Zdarzenia są również wysyłane do tego środowiska pomocniczego z głównego źródła zdarzeń. Upewnij się, że używasz drugiej dedykowanej grupy odbiorców. Postępuj zgodnie z wytycznymi tego źródła odzyskiwania po awarii, jak opisano wcześniej.

Aby utworzyć zduplikowane środowisko:

1. Tworzenie środowiska w drugim regionie. Aby uzyskać więcej informacji, zobacz [Tworzenie nowego środowiska usługi Time Series Insights w witrynie Azure portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Utwórz drugą dedykowaną grupę odbiorców dla źródła zdarzeń.
1. Połącz to źródło zdarzeń z nowym środowiskiem. Upewnij się, że wyznaczysz drugą dedykowaną grupę odbiorców.
1. Przejrzyj dokumentację [usługi IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) i [Centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) usługi Time Series Insights.

Jeśli wystąpi zdarzenie:

1. Jeśli region podstawowy dotyczy awarii, należy przekierować operacje do środowiska kopii zapasowej usługi Time Series Insights.
1. Użyj drugiego regionu, aby uzyskać kopii zapasowej i odzyskać wszystkie dane telemetryczne i zapytania usługi Time Series Insights.

> [!IMPORTANT]
> W przypadku pracy awaryjnej:
> 
> * Może również wystąpić opóźnienie.
> * Chwilowy skok przetwarzania wiadomości może wystąpić, jak operacje są przekierowywał.
> 
> Aby uzyskać więcej informacji, zobacz [Ograniczanie opóźnień w usłudze Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

