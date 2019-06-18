---
title: Planowanie środowiska Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Planowanie środowiska Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 435e5f2163270672ac5f1f5695ca2fe9be22ee6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388732"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planowanie środowiska Azure czas Series Insights w wersji zapoznawczej

W tym artykule opisano najlepsze rozwiązania do planowania i szybkie rozpoczynanie pracy przy użyciu usługi Azure czas Series Insights w wersji zapoznawczej.

> [!NOTE]
> Najlepsze rozwiązania dotyczące zaplanować wystąpienia usługi Time Series Insights ogólnej dostępności, zobacz [Planowanie środowiska ogólnej dostępności usługi Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Najlepsze rozwiązania dotyczące planowania i przygotowania

Aby rozpocząć korzystanie z usługi Time Series Insights, najlepiej jest jeśli zrozumiesz:

* Co można uzyskać, kiedy użytkownik [aprowizować środowiska czasu Series Insights w wersji zapoznawczej](#the-preview-environment).
* Co Twoja [identyfikatory serii czasu i sygnaturę czasową właściwości są](#configure-time-series-ids-and-timestamp-properties).
* Jakie nowe [jest Model szeregów czasowych](#understand-the-time-series-model)oraz sposób tworzenia własnych.
* Jak [efektywnie wysyła zdarzenia w formacie JSON](#shape-your-events).
* Time Series Insights [opcje odzyskiwania po awarii dla firmy](#business-disaster-recovery).

Usługa Azure Time Series Insights wykorzystuje model biznesowy zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat opłat i pojemności, zobacz [cennik usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Środowisko wersji zapoznawczej

Podczas aprowizowania środowiska czasu Series Insights w wersji zapoznawczej, jest utworzenie dwóch zasobów platformy Azure:

* Środowisko Azure czas Series Insights w wersji zapoznawczej
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1

Aby rozpocząć, potrzebne są trzy dodatkowe elementy:

* A [czasu modelu szeregów](./time-series-insights-update-tsm.md)
* [Połączony źródła zdarzeń usługi Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Zdarzeń otrzymywanych przez źródło zdarzenia](./time-series-insights-send-events.md) , są zamapowane na modelu i mają prawidłowy format JSON

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurowanie właściwości identyfikatorów serii czasu i znacznik czasu:

Aby utworzyć nowego środowiska usługi Time Series Insights, zaznacz serii czasu identyfikatora. Spowoduje to więc działa jako partycji logicznej dla swoich danych. Jak wspomniano, upewnij się, że masz swoim identyfikatorem serii czasu gotowe.

> [!IMPORTANT]
> Identyfikatory serie czasu są *niezmienialnych* i *nie można później zmienić*. Sprawdź każdy z nich przed ostatecznego wyboru i pierwszym użyciu.

Można wybrać maksymalnie trzy klucze, aby jednoznacznie odróżnienia Twoich zasobów. Aby uzyskać więcej informacji, przeczytaj [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md) i [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Ważne jest również właściwość sygnatury czasowej. Można określić tę właściwość, podczas dodawania źródła zdarzeń. Każde źródło zdarzenia ma opcjonalna właściwość sygnatury czasowej, który został użyty ze źródłami zdarzeń śledzenia wraz z upływem czasu. Wartości sygnatury czasowej jest uwzględniana wielkość liter i musi być sformatowany do poszczególnych specyfikacji każdego źródła zdarzeń.

> [!TIP]
> Sprawdź wymagania dotyczące formatowania i analizowania źródeł zdarzeń.

Jeśli pole pozostanie puste, czas umieścić w kolejce zdarzeń źródłem zdarzenia jest używana jako sygnatura czasowa zdarzenia. Jeśli wyślesz dane historyczne lub zdarzenia wsadowej Dostosowywanie właściwości sygnatury czasowej jest bardziej przydatne niż domyślny czas trwania zdarzenia umieścić w kolejce. Aby uzyskać więcej informacji, przeczytaj temat [dodawania źródła zdarzeń w usłudze Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Zrozumienie szeregów czasowych modelu

Teraz można skonfigurować Model szeregów czasowych środowiska usługi Time Series Insights. Nowy model sprawia, że można łatwo znaleźć i analizowanie danych IoT. Go włącza nadzorowaną, konserwacja i Wzbogacanie danych szeregów czasowych i pomaga przygotować przygotowane zestawów danych. Model wykorzystuje identyfikatory serii czasu, w mapowanie na wystąpienie, które kojarzy unikatowy zasób za pomocą zmiennych, znane jako typy i hierarchii. Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

Model jest dynamiczny, dzięki czemu mogą być wbudowane w dowolnym momencie. Aby szybko rozpocząć pracę, twórz i przekaż go przed wypychanie danych do usługi Time Series Insights. Aby utworzyć model, zobacz [Użyj modelu szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

Dla wielu klientów modelu szeregów czasowych mapuje do istniejącego modelu zasobów lub system ERP już w miejscu. Jeśli nie masz istniejącego modelu jest wstępnie komfortu [podane](https://github.com/Microsoft/tsiclient) szybko pracę. Aby traktować jak model może pomóc, Wyświetl [środowisku pokazu przykładowe](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Kształt zdarzeń

Możesz sprawdzić sposób, który wysyła zdarzenia do usługi Time Series Insights. W idealnym przypadku zdarzenia są nieznormalizowane również i wydajne.

Regułą:

* Store metadanych w swojej modelu szeregów czasowych.
* Tryb serii czasu, pól wystąpień i zdarzenia obejmują tylko niezbędne informacje, takie jak identyfikator serii czasu lub sygnatury czasowej.

Aby uzyskać więcej informacji, zobacz [kształt zdarzenia](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md) w serii czasu Insights (wersja zapoznawcza).
- Dowiedz się więcej o [modelowania danych](./time-series-insights-update-tsm.md) w serii czasu Insights (wersja zapoznawcza).