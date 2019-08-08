---
title: Planowanie środowiska Azure Time Series Insights w wersji zapoznawczej | Microsoft Docs
description: Zaplanuj środowisko Azure Time Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 72d98e0ca4f75835e7dcc2e7a14b037ccf0d6d7f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840545"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planowanie środowiska Azure Time Series Insights w wersji zapoznawczej

W tym artykule opisano najlepsze rozwiązania dotyczące szybkiego planowania i rozpoczynania pracy przy użyciu wersji zapoznawczej Azure Time Series Insights.

> [!NOTE]
> Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi planowania ogólnej dostępności Time Series Insights, zobacz [planowanie Azure Time Series Insights ogólnego środowiska dostępności](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Najlepsze rozwiązania dotyczące planowania i przygotowywania

Aby rozpocząć pracę z Time Series Insights, najlepiej zrozumieć następujące informacje:

* Co otrzymujesz po [udostępnieniu środowiska Time Series Insights w wersji](#the-preview-environment)zapoznawczej.
* Jakie [są identyfikatory szeregów czasowych i właściwości sygnatur czasowych](#configure-time-series-ids-and-timestamp-properties).
* Nowy [model szeregów czasowych](#understand-the-time-series-model)i sposób tworzenia własnych.
* Jak [efektywnie wysyłać zdarzenia w formacie JSON](#shape-your-events).
* Time Series Insights [Opcje odzyskiwania po awarii firmy](#business-disaster-recovery).

Azure Time Series Insights wykorzystuje model biznesowy z płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji o opłatach i pojemności, zobacz [Time Series Insights Cennik](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Środowisko wersji zapoznawczej

Po udostępnieniu środowiska Time Series Insights w wersji zapoznawczej można utworzyć dwa zasoby platformy Azure:

* Środowisko wersji zapoznawczej Azure Time Series Insights
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1

Aby rozpocząć, potrzebne są trzy dodatkowe elementy:

* [Model szeregów czasowych](./time-series-insights-update-tsm.md)
* [Źródło zdarzenia połączone z Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Zdarzenia przepływające do źródła zdarzeń](./time-series-insights-send-events.md) , które są zamapowane na model i są w prawidłowym formacie JSON

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurowanie identyfikatorów szeregów czasowych i właściwości sygnatur czasowych

Aby utworzyć nowe środowisko Time Series Insights, wybierz identyfikator szeregów czasowych. Wykonanie tej czynności jako partycji logicznej dla danych. Upewnij się, że identyfikatory szeregów czasowych są gotowe.

> [!IMPORTANT]
> Identyfikatory szeregów czasowych są *niezmienne* i *nie można ich później zmienić*. Sprawdź każdy z nich przed ostatecznym zaznaczeniem i pierwszym użyciem.

Można wybrać maksymalnie trzy klucze, aby jednoznacznie odróżnić zasoby. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md) i [danych wejściowych](./time-series-insights-update-storage-ingress.md).

Właściwość sygnatury czasowej jest również ważna. Tę właściwość można wyznaczyć podczas dodawania źródeł zdarzeń. Każde źródło zdarzenia ma opcjonalną właściwość sygnatury czasowej, która jest używana do śledzenia źródeł zdarzeń w czasie. W wartościach sygnatury czasowej jest uwzględniana wielkość liter i muszą one być sformatowane do poszczególnych specyfikacji każdego źródła zdarzenia.

> [!TIP]
> Sprawdź wymagania dotyczące formatowania i analizowania źródeł zdarzeń.

Po podaniu pustego pola czas do kolejki zdarzeń źródła zdarzeń jest używany jako sygnatura czasowa zdarzenia. W przypadku wysyłania danych historycznych lub zdarzeń wsadowych dostosowanie właściwości timestamp jest bardziej pomocne niż domyślny czas dodawania do kolejki zdarzeń. Aby uzyskać więcej informacji, przeczytaj temat jak [dodać źródła zdarzeń w usłudze Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Omówienie modelu szeregów czasowych

Teraz można skonfigurować model szeregów czasowych środowiska Time Series Insights. Nowy model ułatwia znajdowanie i analizowanie danych IoT. Umożliwia ona nadzorowanie, konserwację i wzbogacanie danych szeregów czasowych i pomaga przygotować zestawy danych gotowe do użycia przez klientów. Model używa identyfikatorów szeregów czasowych, które mapują do wystąpienia, które kojarzy unikatowy zasób ze zmiennymi, znanymi jako typy i hierarchie. Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

Model jest dynamiczny, więc można go skompilować w dowolnym momencie. Aby szybko rozpocząć pracę, skompiluj ją i przekaż przed wypchnięciem danych do Time Series Insights. Aby skompilować model, zobacz [Korzystanie z modelu szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

W przypadku wielu klientów model szeregów czasowych jest mapowany na istniejący model zasobów lub system ERP. Jeśli nie masz istniejącego modelu, zapewnione zostanie wbudowane środowisko użytkownika umożliwiające szybkie [](https://github.com/Microsoft/tsiclient) rozpoczęcie pracy. Aby Envision, jak może pomóc model, zobacz [przykładowe środowisko demonstracyjne](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Kształtowanie zdarzeń

Możesz sprawdzić sposób wysyłania zdarzeń do Time Series Insights. W idealnym przypadku zdarzenia są bardziej znormalizowane i wydajne.

Dobrym regułą dla elementu kciuk:

* Przechowuj metadane w modelu szeregów czasowych.
* Tryb szeregów czasowych, pola wystąpień i zdarzenia obejmują tylko niezbędne informacje, takie jak identyfikator szeregów czasowych lub sygnatura czasowa.

Aby uzyskać więcej informacji, zobacz [Shape Events](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [Azure Advisor](../advisor/advisor-overview.md) , aby zaplanować opcje konfiguracji odzyskiwania firmy.

- Przeczytaj więcej na temat [magazynu i transferu danych](./time-series-insights-update-storage-ingress.md) przychodzących w wersji zapoznawczej Time Series Insights.

- Dowiedz się więcej na temat [modelowania danych](./time-series-insights-update-tsm.md) w wersji zapoznawczej Time Series Insights.