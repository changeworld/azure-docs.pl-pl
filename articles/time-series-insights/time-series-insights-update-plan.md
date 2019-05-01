---
title: Planowanie środowiska Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Planowanie środowiska Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 0472f53d11ec4c990fcf6face633444fe66ba937
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702348"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planowanie środowiska Azure czas Series Insights w wersji zapoznawczej

W tym artykule opisano najlepsze rozwiązania do planowania i rozpocząć pracę, szybko za pomocą usługi Azure czas Series Insights w wersji zapoznawczej.

## <a name="best-practices-for-planning-and-preparation"></a>Najlepsze rozwiązania dotyczące planowania i przygotowania

Aby rozpocząć korzystanie z usługi Time Series Insights, najlepiej jest jeśli zrozumiesz:

* Co można uzyskać podczas aprowizacji środowiska czasu Series Insights w wersji zapoznawczej.
* Jakie identyfikatory serii czasu i sygnaturę czasową właściwości są.
* Co to jest nowy Model szeregów czasowych oraz sposób tworzenia własnych.
* Jak skutecznie wysyłać zdarzenia w formacie JSON. 
* Czas opcje odzyskiwania po awarii firm Series Insights.

Usługa Time Series Insights wykorzystuje model biznesowy zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat opłat i pojemności, zobacz [cennik usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>Środowisko czasu Series Insights w wersji zapoznawczej

Podczas aprowizowania środowiska czasu Series Insights w wersji zapoznawczej, jest utworzenie dwóch zasobów platformy Azure:

* Środowisko usługi Time Series Insights w wersji zapoznawczej
* Konto platformy Azure Storage ogólnego przeznaczenia w wersji 1

Aby rozpocząć, potrzebne są trzy dodatkowe elementy:
 
- A [czasu modelu szeregów](./time-series-insights-update-tsm.md) 
- [Połączony źródła zdarzeń usługi Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Zdarzeń otrzymywanych przez źródło zdarzenia](./time-series-insights-send-events.md) , są zamapowane na modelu i mają prawidłowy format JSON 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Konfigurowanie właściwości identyfikatorów serii czasu i znacznik czasu:

Aby utworzyć nowego środowiska usługi Time Series Insights, zaznacz serii czasu identyfikatora. Spowoduje to więc działa jako partycji logicznej dla swoich danych. Jak wspomniano, upewnij się, że masz swoim identyfikatorem serii czasu gotowe.

> [!IMPORTANT]
> Identyfikatory serie czasu są *niezmienialnych* i *nie można później zmienić*. Sprawdź każdy z nich przed ostatecznego wyboru i pierwszym użyciu.

Można wybrać maksymalnie trzech (3) klucze, aby jednoznacznie odróżnienia Twoich zasobów. Aby uzyskać więcej informacji, przeczytaj [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md) i [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Ważne jest również właściwość sygnatury czasowej. Można określić tę właściwość, podczas dodawania źródła zdarzeń. Każde źródło zdarzenia ma opcjonalna właściwość sygnatury czasowej, który został użyty ze źródłami zdarzeń śledzenia wraz z upływem czasu. Wartości sygnatury czasowej jest uwzględniana wielkość liter i musi być sformatowany do poszczególnych specyfikacji każdego źródła zdarzeń.

> [!TIP]
> Sprawdź wymagania dotyczące formatowania i analizowania źródeł zdarzeń.

Jeśli pole pozostanie puste, czas umieścić w kolejce zdarzeń źródłem zdarzenia jest używana jako sygnatura czasowa zdarzenia. Jeśli wyślesz dane historyczne lub zdarzenia wsadowej Dostosowywanie właściwości sygnatury czasowej jest bardziej przydatne niż domyślny czas trwania zdarzenia umieścić w kolejce. Aby uzyskać więcej informacji, przeczytaj o [sposób dodawania źródła zdarzeń w usłudze IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). 

## <a name="understand-the-time-series-model"></a>Zrozumienie szeregów czasowych modelu

Teraz można skonfigurować Model szeregów czasowych środowiska usługi Time Series Insights. Nowy model sprawia, że można łatwo znaleźć i analizowanie danych IoT. Go włącza nadzorowaną, konserwacja i Wzbogacanie danych szeregów czasowych i pomaga przygotować przygotowane zestawów danych. Model wykorzystuje **identyfikatory serii czasu**, które mapowania na wystąpienie, które kojarzy unikatowy zasób za pomocą zmiennych, znane jako typy i hierarchii. Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

Model jest dynamiczny, dzięki czemu mogą być wbudowane w dowolnym momencie. Aby szybko rozpocząć pracę, twórz i przekaż go przed wypychanie danych do usługi Time Series Insights. Aby utworzyć model, zobacz [Użyj modelu szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

Dla wielu klientów modelu szeregów czasowych mapuje do istniejącego modelu zasobów lub system ERP już w miejscu. Jeśli nie masz istniejącego modelu jest wstępnie komfortu [podane](https://github.com/Microsoft/tsiclient) szybko pracę. Aby traktować jak model może pomóc, Wyświetl [środowisku pokazu przykładowe](https://insights.timeseries.azure.com/preview/demo). 

## <a name="shape-your-events"></a>Kształt zdarzeń

Możesz sprawdzić sposób, który wysyła zdarzenia do usługi Time Series Insights. W idealnym przypadku zdarzenia są nieznormalizowane również i wydajne.

Regułą:

* Store metadanych w swojej modelu szeregów czasowych
* Tryb serii czasu, pól wystąpień i zdarzenia obejmują tylko niezbędne informacje, takie jak:
  * Identyfikator szeregu czasowego
  * Znacznik czasu

Aby uzyskać więcej informacji, zobacz [kształt zdarzenia](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowych

Usługa Time Series Insights to usługa o wysokiej dostępności, która używa zwolnienia na poziomie regionu świadczenia usługi Azure. Konfiguracja nie jest wymagane do użycia, te funkcje nieprzerwaną pracę. Platforma Microsoft Azure zawiera funkcje ułatwiające tworzenie rozwiązań za pomocą funkcji odzyskiwania po awarii lub dostępności między regionami. Aby zapewnić globalnego, między regionami wysokiej dostępności dla urządzeń lub użytkowników, skorzystaj z tych funkcji odzyskiwania po awarii platformy Azure. 

Aby uzyskać informacji na temat wbudowanych funkcji na platformie Azure dla ciągłości działania i odzyskiwania po awarii (BCDR), zobacz [ciągłości biznesowej platformy Azure — wskazówki techniczne](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). Aby uzyskać wskazówki dotyczące architektury strategii dla aplikacji platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii, zobacz dokument na [odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]
> Usługa Time Series Insights nie ma wbudowanych BCDR. Domyślnie usługi Azure Storage, Azure IoT Hub i Azure Event Hubs mają wbudowane odzyskiwania.

Aby dowiedzieć się więcej, przeczytaj:

* [Nadmiarowość magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Odzyskiwanie po awarii o wysokiej dostępności usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Zasad Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

Jeśli potrzebujesz strategii BCDR, można zaimplementować strategię odzyskiwania. Utwórz drugie środowisko usługi Time Series Insights w regionie świadczenia usługi Azure backup. Wysyłanie zdarzeń do tego pomocniczego środowiska ze źródła zdarzenia podstawowego. Użyj drugiego dedykowanej grupy klientów i wskazówki dotyczące strategii BCDR tego źródła zdarzeń.

Wykonaj następujące kroki, aby utworzyć i użyć dodatkowej środowiska usługi Time Series Insights.

1. Należy utworzyć środowisko, w drugim regionie. Aby uzyskać więcej informacji, zobacz [środowiska usługi Time Series Insights](./time-series-insights-get-started.md).
1. Utwórz drugi dedykowanej grupy klientów dla źródła zdarzenia. Łączenie źródła zdarzeń do nowego środowiska. Pamiętaj wyznaczyć drugi dedykowanej grupy klientów. Aby dowiedzieć się więcej, zobacz [dokumentacja usługi IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) lub [dokumentacja Centrum zdarzeń](./time-series-insights-data-access.md).
1. Jeśli swój region podstawowy jest wpływu zdarzenia po awarii, należy przekierować operacje tworzenia kopii zapasowej środowiska usługi Time Series Insights.

> [!IMPORTANT]
> * Należy zauważyć, że opóźnienie może się pojawić w przypadku przejścia w tryb failover.
> * Podczas przetwarzania komunikatu, ponieważ operacje są przekierowane trybu failover także może spowodować chwilową kolekcji.
> * Aby uzyskać więcej informacji, zobacz [zmniejszyć opóźnienia w usłudze Time Series Insights](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md) w serii czasu Insights (wersja zapoznawcza).

- Dowiedz się więcej o [modelowania danych](./time-series-insights-update-tsm.md) w serii czasu Insights (wersja zapoznawcza).