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
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388744"
---
## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowych

W tej sekcji opisano funkcje usługi Azure Time Series Insights zachować aplikacje i usługi działające nawet w przypadku wystąpienia awarii. Takie podejście jest nazywane biznesowej, odzyskiwanie po awarii.

### <a name="high-availability"></a>Wysoka dostępność

Jako usługi Azure Time Series Insights udostępnia pewne funkcje wysokiej dostępności przy użyciu nadmiarowość na poziomie regionu świadczenia usługi Azure. Na przykład platforma Microsoft Azure obsługuje możliwości odzyskiwania po awarii za pomocą funkcji dostępności między regionami platformy Azure.

Dodatkowe funkcje wysokiej dostępności, dostępne za pośrednictwem platformy Azure, a także są dostępne do dowolnego wystąpienia usługi Time Series Insights, obejmują:

* **Tryb failover**: Platforma Azure udostępnia [replikacji geograficznej i równoważenie obciążenia](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Przywracanie danych** i **odzyskiwania magazynu**: Platforma Azure udostępnia [kilka opcji zachowywania i odzyskiwania danych](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Site recovery**: Funkcje, które są dostępne za pośrednictwem [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Upewnij się włączyć te funkcje platformy Azure w celu zapewnienia wysokiej dostępności globalnej, między regionami urządzeniami i użytkownikami.

> [!NOTE]
> Jeśli Azure jest skonfigurowany do włączenia dostępności między regionami, konfiguracja wyższą dostępność między regionami, nie jest wymagane w ramach usługi Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT i centrów zdarzeń

Niektóre usługi Azure IoT obejmują również wbudowanych funkcji odzyskiwania po awarii:

* [Odzyskiwanie po awarii o wysokiej dostępności w usłudze Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), w tym nadmiarowość wewnątrzlokacyjnej między regionami.
* [Zasady usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Nadmiarowość magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrowanie usługi Time Series Insights za pomocą tych innych usług zapewnia możliwości odzyskiwania dodatkowe po awarii. Na przykład dane telemetryczne wysyłane do Centrum zdarzeń może być utrwalone w kopii zapasowej bazy danych z magazynu obiektów Blob platformy Azure.

### <a name="time-series-insights"></a>Time Series Insights

Istnieje kilka sposobów, aby zachować dane usługi Time Series Insights, aplikacje i usługi działające nawet wtedy, gdy są one przerwane. Należy także określić, że pełną kopię zapasową środowiska Azure Time Series jest wymagane:

* Jako wystąpienie trybu failover szczegółowych informacji specyficznych dla szeregu czasowego można przekierować danych, a ruch do.
* Na potrzeby inspekcji i dane zachowanie.

Ogólnie rzecz biorąc najlepszym sposobem zduplikowane środowiska usługi Time Series Insights jest utworzenie drugiej środowiska usługi Time Series Insights w regionie świadczenia usługi Azure backup. Zdarzenia również są wysyłane do tego środowiska dodatkowej ze źródła zdarzenia podstawowego. Upewnij się, że do użycia po drugie dedykowanej grupy klientów i postępuj zgodnie z tego źródła biznesowe awarii odzyskiwania wskazówki, jak wcześniej.

W szczególności do utworzenia zduplikowanych środowiska:

1. Należy utworzyć środowisko, w drugim regionie. Aby uzyskać instrukcje, zobacz [Utwórz nowe środowisko usługi Time Series Insights w witrynie Azure portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Utwórz drugi dedykowanej grupy klientów dla źródła zdarzenia.
1. Łączenie źródła zdarzeń do nowego środowiska. Upewnij się wyznaczyć drugi dedykowanej grupy klientów.
1. Przejrzyj usługi Time Series Insights [usługi IoT hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) i [Centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentacji.

Na koniec:

1. Jeśli swój region podstawowy jest wpływu zdarzenia po awarii, należy przekierować operacje tworzenia kopii zapasowej środowiska usługi Time Series Insights.
1. Tworzenie kopii zapasowych i odzyskiwania wszystkie dane telemetryczne i zapytania usługi Time Series Insights, użyj drugiego regionu.

> [!IMPORTANT]
> * Należy zauważyć, że opóźnienie może się pojawić w przypadku przejścia w tryb failover.
> * Tryb failover także może spowodować chwilową kolekcji podczas przetwarzania komunikatu, ponieważ operacje są przekierowane.
> * Aby uzyskać więcej informacji, zobacz [zmniejszyć opóźnienia w usłudze Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).
