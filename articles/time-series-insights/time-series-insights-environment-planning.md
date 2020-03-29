---
title: Planowanie środowiska ga — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Najważniejsze wskazówki dotyczące przygotowywania, konfigurowania i wdrażania środowiska ga usługi Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314814"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planowanie środowiska ga usługi Azure Time Series Insights

W tym artykule opisano sposób planowania środowiska ogólnej dostępności usługi Azure Time Series Insights (GA) na podstawie oczekiwanego wskaźnika transferu danych przychodzących i wymagań dotyczących przechowywania danych.

## <a name="video"></a>Film wideo

**Obejrzyj ten klip wideo, aby dowiedzieć się więcej o zatrzymywaniu danych w usłudze Azure Time Series Insights i planowaniu:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Najlepsze rozwiązania

Aby rozpocząć korzystanie z usługi Azure Time Series Insights, najlepiej, jeśli wiesz, ile danych oczekujesz wypchnąć przez minutę i jak długo trzeba przechowywać dane.  

Aby uzyskać więcej informacji na temat pojemności i przechowywania zarówno jednostek SKU usługi Time Series Insights, przeczytaj [informacje o cenach usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Aby najlepiej zaplanować środowisko usługi Time Series Insights pod kątem długoterminowego sukcesu, należy wziąć pod uwagę następujące atrybuty:

- [Pojemność magazynu](#storage-capacity)
- [Okres przechowywania danych](#data-retention)
- [Pojemność przystawek](#ingress-capacity)
- [Kształtowanie wydarzeń](#shape-your-events)
- [Zapewnienie, że masz dane referencyjne](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Pojemność magazynu

Domyślnie usługa Time Series Insights przechowuje dane na podstawie ilości aprowizowania magazynu (jednostek &#215; ilości miejsca do magazynowania na jednostkę) i transferu danych przychodzących.

## <a name="data-retention"></a>Przechowywanie danych

Można zmienić ustawienie **czasu przechowywania danych** w środowisku usługi Azure Time Series Insights. Można włączyć do 400 dni przechowywania. 

Usługa Azure Time Series Insights ma dwa tryby:

* Jeden tryb optymalizuje najbardziej aktualne dane. Wymusza zasady do **przeczyszczanie starych danych** pozostawiając najnowsze dane dostępne z wystąpieniem. Ten tryb jest domyślnie włączony. 
* Drugi optymalizuje dane, aby pozostać poniżej skonfigurowanych limitów przechowywania. **Wstrzymanie ruchu przychodzącego** zapobiega przedostawaniu się nowych danych po wybraniu jako **przekroczone zachowanie limitu magazynowania.**

Można dostosować przechowywania i przełączać między dwoma trybami na stronie konfiguracji środowiska w witrynie Azure portal.

> [!IMPORTANT]
> W środowisku ga usługi Azure Time Series Insights można skonfigurować maksymalnie 400 dni przechowywania danych.

### <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. W [witrynie Azure portal](https://portal.azure.com)wybierz środowisko usługi Time Series Insights.

1. W okienku **środowiska Usługi Time Series Insights** w obszarze **Ustawienia**wybierz pozycję **Konfiguracja magazynu**.

1. W polu **Czas przechowywania danych (w dniach)** wprowadź wartość z 1 do 400.

   [![Konfigurowanie przechowywania](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Aby dowiedzieć się więcej o tym, jak zaimplementować odpowiednie zasady przechowywania danych, przeczytaj [artykuł Jak skonfigurować przechowywanie](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Pojemność przystawek

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Planowanie środowiska

Drugim obszarem, na którym należy się skupić na planowaniu środowiska usługi Time Series Insights, jest pojemność transferu danych przychodzących. Wydajność transferu przychodzącego jest pochodną alokacji na minutę.

Z punktu widzenia ograniczania przepustowości pakiet danych o rozmiarze 32 KB jest traktowany jako 32 zdarzenia, każdy o rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia wynosi 32 KB. Pakiety danych większe niż 32 KB są obcinane.

Można zwiększyć pojemność jednostki SKU S1 lub S2 do 10 jednostek w jednym środowisku. Nie można przeprowadzić migracji ze środowiska S1 do S2. Nie można przeprowadzić migracji ze środowiska S2 do S1.

W przypadku zdolności przewozowej należy najpierw określić całkowity wymagany ruch przychodzący na miesiąc. Następnie określ, jakie są Twoje potrzeby na minutę. 

Ograniczanie i opóźnienia odgrywają rolę w pojemności na minutę. Jeśli masz skok danych przychodzących, który trwa mniej niż 24 godziny, Time Series Insights można "nadrobić zaległości" przy szybkości transferu danych przychodzących dwa razy stawki wymienione w powyższej tabeli.

Na przykład jeśli masz jedną jednostkę SKU S1, dane przychodzące z szybkością 720 zdarzeń na minutę i skoki szybkości danych przez mniej niż jedną godzinę z szybkością 1440 zdarzeń lub mniej, nie ma zauważalne opóźnienie w danym środowisku. Jednak jeśli przekroczysz 1440 zdarzeń na minutę przez więcej niż jedną godzinę, prawdopodobnie wystąpi opóźnienie w danych, które są wizualizowane i dostępne dla kwerendy w danym środowisku.

Możesz nie wiedzieć z góry, ile danych oczekujesz wypchnięcia. W takim przypadku można znaleźć dane telemetryczne dla [usługi Azure IoT Hub](../iot-hub/iot-hub-metrics.md) i usługi Azure Event Hubs w subskrypcji [witryny](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) Azure portal. Dane telemetryczne mogą pomóc w określeniu sposobu aprowidyzmu środowiska. Użyj **okienka Metryki** w witrynie Azure portal dla odpowiedniego źródła zdarzeń, aby wyświetlić jego dane telemetryczne. Jeśli rozumiesz metryki źródła zdarzeń, można bardziej efektywnie zaplanować i aprowizować środowisko usługi Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Obliczanie wymagań dotyczących transferu danych przychodzących

Aby obliczyć wymagania dotyczące transferu danych przychodzących:

- Sprawdź, czy pojemność transferu przychodzącego przekracza średnią szybkość na minutę i czy środowisko jest wystarczająco duże, aby obsłużyć przewidywany ruch przychodzący odpowiadający dwukrotności pojemności przez mniej niż jedną godzinę.

- Jeśli wystąpią skoki ruchu przychodzącego, które trwają dłużej niż 1 godzinę, użyj współczynnika skoków jako średniej. Aprowizuj środowisko z pojemnością do obsługi współczynnika skoków.

### <a name="mitigate-throttling-and-latency"></a>Ograniczanie ograniczania i opóźnień

Aby uzyskać informacje dotyczące zapobiegania ograniczaniu przepustowości i opóźnieniom, zobacz [Ograniczanie opóźnień i ograniczania przepustowości](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Kształtuj swoje wydarzenia

Ważne jest, aby upewnić się, że sposób wysyłania zdarzeń do usługi Time Series Insights obsługuje rozmiar środowiska, które inicjujesz. (Z drugiej strony można mapować rozmiar środowiska na liczbę odczytów usługi Time Series Insights i rozmiar każdego zdarzenia). Ważne jest również, aby myśleć o atrybuty, które można użyć do plasterków i filtrowania przez podczas kwerendy danych.

> [!TIP]
> Przejrzyj dokumentację kształtowania JSON w [aplikacji Wysyłanie zdarzeń](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Upewnij się, że masz dane referencyjne

*Zestaw danych referencyjnych* to zbiór elementów, które rozszerzają zdarzenia ze źródła zdarzeń. Aparat transferu danych przychodzących usługi Time Series Insights łączy każde zdarzenie ze źródła zdarzeń z odpowiednim wierszem danych w zestawie danych referencyjnych. Rozszerzone zdarzenie jest następnie dostępne dla kwerendy. Sprzężenie jest oparte na kolumnach **klucza podstawowego** zdefiniowanych w zestawie danych referencyjnych.

> [!NOTE]
> Dane referencyjne nie są dołączane z mocą wsteczną. Tylko bieżące i przyszłe dane transferu danych przychodzących są dopasowywać i łączyć się z zestawem danych referencyjnych po jego skonfigurowaniu i przekazaniu. Jeśli planujesz wysłać dużą ilość danych historycznych do usługi Time Series Insights i nie najpierw prześlesz ani nie utworzysz danych referencyjnych w usłudze Time Series Insights, może być trzeba ponowić pracę (podpowiedź: nie zabawa).  

Aby dowiedzieć się więcej o tworzeniu, przekazywaniu i zarządzaniu danymi referencyjnymi w usłudze Time Series Insights, przeczytaj naszą [dokumentację zestawu danych referencyjnych.](time-series-insights-add-reference-data-set.md)

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia nowego środowiska usługi Time Series Insights w witrynie Azure portal.](time-series-insights-get-started.md)

- Dowiedz się, jak [dodać źródło zdarzeń centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) do usługi Time Series Insights.

- Przeczytaj o [konfigurowaniu źródła zdarzeń usługi IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
