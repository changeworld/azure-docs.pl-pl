---
title: Planowanie środowiska w wersji zapoznawczej — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Najważniejsze wskazówki dotyczące konfigurowania, zarządzania, planowania i wdrażania środowiska usługi Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045721"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planowanie środowiska usługi Azure Time Series Insights Preview

W tym artykule opisano najlepsze rozwiązania dotyczące planowania i szybkiego rozpoczynania pracy przy użyciu usługi Azure Time Series Insights Preview.

> [!NOTE]
> Aby uzyskać najlepsze rozwiązania dotyczące planowania wystąpienia usługi Time Series Insights o ogólnej dostępności, przeczytaj artykuł [Planowanie ogólnego środowiska dostępności usługi Azure Time Series Insights.](time-series-insights-environment-planning.md)

## <a name="best-practices-for-planning-and-preparation"></a>Najlepsze praktyki w zakresie planowania i przygotowywania

Najważniejsze wskazówki dotyczące planowania i przygotowywania środowiska opisano w następujących artykułach:

* Co otrzymujesz po [zainicjowaniu obsługi administracyjnej środowiska usługi Time Series Insights Preview](#the-preview-environment).
* Jakie [są identyfikatory szeregów czasowych i właściwości sygnatury czasowe.](#configure-time-series-ids-and-timestamp-properties)
* Co to jest nowy [model serii czasowych](#understand-the-time-series-model)i jak zbudować własne.
* Jak [skutecznie wysyłać zdarzenia w JSON](#shape-your-events).
* [Opcje odzyskiwania po awarii w biznesie](#business-disaster-recovery)Usługi Time Series Insights .

Usługa Azure Time Series Insights wykorzystuje model biznesowy płatności zgodnie z rzeczywistymu po podróży. Aby uzyskać więcej informacji na temat opłat i pojemności, przeczytaj [informacje o cenach usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Środowisko podglądu

Podczas aprowizowania środowiska usługi Time Series Insights Preview należy utworzyć dwa zasoby platformy Azure:

* Środowisko usługi Azure Time Series Insights w wersji zapoznawczej
* Konto v1 usługi Azure Storage ogólnego przeznaczenia

W ramach procesu inicjowania obsługi administracyjnej należy określić, czy ma zostać włączona magazyn ogrzanie. Ciepły magazyn zapewnia środowisko zapytania warstwowego. Po włączeniu należy określić okres przechowywania od 7 do 30 dni. Zapytania wykonywane w okresie przechowywania magazynu ciepłe zazwyczaj zapewniają szybsze czasy odpowiedzi. Gdy zapytanie obejmuje okres przechowywania ciepłego magazynu, jest ono obsługiwane z chłodni składowej.

Zapytania w ciepłym sklepie są bezpłatne, podczas gdy zapytania dotyczące chłodnią ponoszą koszty. Ważne jest, aby zrozumieć wzorce zapytań i odpowiednio zaplanować konfigurację magazynu ciepłego. Zalecamy, aby interaktywne analizy najnowszych danych były przechowywane w ciepłym magazynie, a analiza wzorców i długoterminowe trendy były zimne.

> [!NOTE]
> Aby dowiedzieć się więcej o tym, jak zbadać ciepłe dane, przeczytaj [odwołanie do interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

Aby rozpocząć, potrzebujesz trzech dodatkowych elementów:

* [Model szeregów czasowych](./time-series-insights-update-tsm.md)
* [Źródło zdarzeń połączone z usługą Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Zdarzenia wpływające do źródła zdarzeń,](./time-series-insights-send-events.md) które są mapowane do modelu i są w prawidłowym formacie JSON

## <a name="review-preview-limits"></a>Przeglądanie limitów podglądu

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurowanie identyfikatorów szeregów czasowych i właściwości sygnatury czasowe

Aby utworzyć nowe środowisko usługi Time Series Insights, wybierz identyfikator szeregów czasowych. W ten sposób działa jako partycja logiczna dla danych. Jak wspomniano, upewnij się, że twoje identyfikatory szeregów czasowych są gotowe.

> [!IMPORTANT]
> Identyfikatorów szeregów czasowych *nie można później zmienić*. Sprawdź każdy przed ostatecznym wyborem i pierwszym użyciem.

Można wybrać maksymalnie trzy klucze, aby jednoznacznie odróżnić swoje zasoby. Aby uzyskać więcej informacji, przeczytaj [najważniejsze wskazówki dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md) i [magazynu i transferu danych przychodzących.](./time-series-insights-update-storage-ingress.md)

Właściwość **sygnatura czasowa** jest również ważna. Tę właściwość można wyznaczyć podczas dodawania źródeł zdarzeń. Każde źródło zdarzenia ma opcjonalną właściwość sygnatury czasowej, która jest używana do śledzenia źródeł zdarzeń w czasie. W wartościach sygnatury czasowej rozróżniana jest wielkość liter i musi być sformatowana zgodnie z indywidualną specyfikacją każdego źródła zdarzeń.

> [!TIP]
> Sprawdź wymagania dotyczące formatowania i analizowania źródeł zdarzeń.

Po lewej puste, czas enqueue zdarzenia źródła zdarzenia jest używany jako zdarzenie sygnatura czasowa. Jeśli wysyłasz dane historyczne lub zdarzenia wsadowe, dostosowywanie właściwości sygnatury czasowej jest bardziej pomocne niż domyślna godzina w kolejce zdarzenia. Aby uzyskać więcej informacji, przeczytaj o [dodaniu źródeł zdarzeń w usłudze Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Opis modelu szeregów czasowych

Teraz można skonfigurować model szeregów czasowych środowiska time series w środowisku szeregów czasowych. Nowy model ułatwia znajdowanie i analizowanie danych IoT. Umożliwia curation, konserwacji i wzbogacania danych szeregów czasowych i pomaga przygotować zestawy danych gotowych do użycia przez konsumenta. Model używa identyfikatorów szeregów czasowych, które mapują do wystąpienia, które kojarzy unikatowy zasób ze zmiennymi, znanymi jako typy i hierarchie. Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

Model jest dynamiczny, dzięki czemu można go zbudować w dowolnym momencie. Aby szybko rozpocząć, skompiluj i przekaż go przed wypchnięciem danych do usługi Time Series Insights. Aby utworzyć model, przeczytaj [artykuł Użyj modelu szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

Dla wielu klientów model szeregów czasowych mapuje istniejący model zasobów lub system ERP już istniejący. Jeśli nie masz istniejącego modelu, wstępnie utworzone środowisko użytkownika jest [dostępna,](https://github.com/Microsoft/tsiclient) aby szybko rozpocząć działanie. Aby wyobrazić sobie, jak model może ci pomóc, zobacz [przykładowe środowisko demo](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Kształtuj swoje wydarzenia

Można sprawdzić sposób wysyłania zdarzeń do usługi Time Series Insights. Idealnie, twoje wydarzenia są denormalized dobrze i skutecznie.

Dobra zasada:

* Przechowuj metadane w modelu szeregów czasowych.
* Upewnij się, że tryb szeregów czasowych, pola wystąpienia i zdarzenia zawierają tylko niezbędne informacje, takie jak identyfikator szeregów czasowych lub właściwość sygnatura czasowa.

Aby uzyskać więcej informacji, przeczytaj shape [events](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [usługę Azure Advisor,](../advisor/advisor-overview.md) aby zaplanować opcje konfiguracji odzyskiwania biznesowego.
- Dowiedz się więcej o [przechowywaniu i przychodzących](./time-series-insights-update-storage-ingress.md) danych w wersji Zapoznawczej usługi Time Series Insights.
- Dowiedz się więcej o [modelowaniu danych](./time-series-insights-update-tsm.md) w wersji Preview usługi Time Series Insights.
