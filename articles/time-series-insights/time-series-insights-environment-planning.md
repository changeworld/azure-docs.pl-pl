---
title: Planowanie skali środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wykonać najważniejsze wskazówki podczas planowania środowisku usługi Azure Time Series Insights, w tym pojemność magazynu, przechowywanie danych, pojemność zdarzeń przychodzących, monitorowania i firm odzyskiwania po awarii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: bf1f570319370fab99e2f52086bc81df259e3d35
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236502"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planowanie środowiska Azure czas serii szczegółowych informacji w wersji Ogólnodostępnej

W tym artykule opisano planowanie środowiska ogólnodostępnej (GA) usługa Azure Time Series Insights na podstawie oczekiwanego transfer przychodzący i wymagania dotyczące przechowywania danych.

## <a name="video"></a>Wideo

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>Dowiedz się więcej o przechowywaniu danych w AzureTime Series Insights oraz sposobu planowania dla niego.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Najlepsze rozwiązania

Aby rozpocząć korzystanie z usługi Time Series Insights, najlepiej Jeśli wiesz, jak dużo danych, które chcą wypchnąć za minutę, a także, jak długo ma być do przechowywania danych.  

Aby uzyskać więcej informacji o wydajności i przechowywania dla jednostki SKU zarówno czas serii szczegółowych informacji, zobacz [cennik usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Weź pod uwagę następujące atrybuty do planu najlepsze środowisko dla długoterminowym sukcesie:

- <a href="#understand-storage-capacity">Pojemność magazynu</a>
- <a href="#understand-data-retention">Okres przechowywania danych</a>
- <a href="#understand-ingress-capacity">Pojemność zdarzeń przychodzących</a>
- <a href="#shape-your-events">Kształtowanie zdarzeń</a>
- <a href="#ensure-you-have-reference-data">Sprawdzając, czy dane referencyjne w miejscu</a>

## <a name="understand-storage-capacity"></a>Zrozumienie pojemność magazynu

Domyślnie usługa Azure Time Series Insights zachowuje danych, w oparciu o ilość miejsca w magazynie, które zostały aprowizowane (jednostki czasu ilość miejsca na jednostkę) i ruch przychodzący.

## <a name="understand-data-retention"></a>Omówienie przechowywania danych

Można skonfigurować środowisko usługi Time Series Insights **czas przechowywania danych** ustawienia, umożliwiając maksymalnie 400 dni przechowywania. Usługa Time Series Insights ma dwa tryby, taki, który optymalizuje zapewniających środowiska ma najbardziej aktualne dane (na domyślnie) i drugiego, który jest zoptymalizowany do zapewnienia przechowywania limitów, gdzie ruch przychodzący jest wstrzymany, jeśli ogólną pojemność magazynu środowisko zostanie osiągnięty.  Można dostosować, przechowywania i przełączania między dwoma trybami na stronie konfiguracji środowiska w witrynie Azure portal.

Można skonfigurować maksymalnie 400 dni przechowywania danych w środowisku usługi Time Series Insights.

### <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. W [witryny Azure portal](https://portal.azure.com), wybierz środowisko usługi Time Series Insights.

1. Na **strony środowiska usługi Time Series Insights**w obszarze **ustawienia** nagłówka, wybierz **Konfiguruj**.

1. W **czas przechowywania danych (w dniach)** wprowadź wartość z zakresu od 1 do 400.

   [![Skonfigurowanie okresu przechowywania](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Dowiedz się więcej na temat wdrażania zasad przechowywania odpowiednie dane, przeglądając [sposobu konfigurowania przechowywania](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Zrozumienie pojemność zdarzeń przychodzących

W obszarze inne skoncentrować się na potrzeby planowania ma pojemność zdarzeń przychodzących, która jest pochodną alokacji na minutę.

Z punktu widzenia ograniczania pakiet ingressed danych o rozmiarze pakietów wynosi 32 KB jest traktowane jako 32 zdarzenia, każdy o rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia wynosi 32 KB; pakiety danych większe niż 32 KB są obcinane.

Poniższa tabela podsumowuje pojemność zdarzeń przychodzących dla każdej jednostki SKU:

|Jednostka SKU  |Liczba zdarzeń / miesiąc / jednostkę  |Rozmiar zdarzenia / miesiąc / jednostkę  |Liczba zdarzeń na minutę / jednostkę  | Rozmiar / minutę / jednostkę   |
|---------|---------|---------|---------|---------|
|S1     |   30 mln     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milionów    |   300 GB   | 7,200   | 7200 KB  |

Można zwiększyć pojemność S1 lub S2 SKU do 10 jednostek w ramach jednego środowiska. Nie można migrować ze środowiska S1, s2 lub ze środowiska S2 do S1.

Ruch przychodzący pojemności najpierw należy określić łączny ruch przychodzący, które są wymagane na co miesiąc. Następnie należy określić co użytkownika na minutę wymaga się, jak jest to, gdzie ograniczania przepustowości i opóźnienia odtwarzanie roli.

W przypadku kolekcji w swoje dane przychodzące trwające krócej niż 24 godziny, usługi Time Series Insights można "— Wyrównywanie" szybkości transferu danych przychodzących, 2 x stawek wymienionych powyżej.

Na przykład w przypadku pojedynczej jednostki SKU S1 i danych przychodzących z szybkością 720 zdarzeń na minutę, kolekcji przez mniej niż 1 godzinę z szybkością 1440 zdarzeń lub mniej, może istnieć nie zauważalnego opóźnienia dla danego środowiska. Jednak jeśli przekroczysz zdarzenia 1440 minutę w przypadku więcej niż jedna godzina, będzie prawdopodobnie wystąpią opóźnienia z danymi, które ułatwia wizualizowanie i dostępne dla zapytania w danym środowisku.

Może nie wiedzieć z wyprzedzeniem jak dużo danych oczekujesz, że do wypychania. W takim przypadku można znaleźć danych telemetrii dla [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) i [usługi Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) w witrynie Azure portal. Tych danych telemetrycznych może pomóc w określeniu, jak aprowizować środowiska. Użyj **metryki** strony w witrynie Azure portal dla źródła zdarzenia odpowiednich wyświetlić jego dane telemetryczne. Jeśli zrozumiesz metryk źródła zdarzeń, bardziej efektywne zaplanować i aprowizować środowiska usługi Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Obliczyć wymagania dotyczące ruchu przychodzącego

- Upewnij się, pojemności ruch przychodzący jest powyżej średniej stawka za minutę i czy środowisko jest wystarczająco duży, aby obsłużyć Twojego przewidywanego transferu danych przychodzących równoważna 2 x pojemności dla mniej niż 1 godzinę.

- Jeśli występują skoki transferu danych przychodzących, ostatniej dłużej niż 1 godzina, użyj współczynnik kolekcji Twoja średnia i aprowizować środowiska z możliwością obsługi szybkość, z kolekcji.

### <a name="mitigate-throttling-and-latency"></a>Eliminowanie ograniczania przepustowości i opóźnienia

Aby dowiedzieć się, jak zapobiec ograniczania przepustowości i opóźnienia, uzyskać informacje dotyczące sposobu [zmniejszyć opóźnienia i ograniczania przepustowości](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Kształt zdarzeń

Jest ważne upewnić się, w sposób wysyłania zdarzeń do usługi TSI obsługuje rozmiar środowiska, w przypadku udostępniania (i odwrotnie, można mapować rozmiar środowiska, ile zdarzeń odczytuje TSI i rozmiar każdego zdarzenia). Podobnie należy wziąć pod uwagę atrybuty, które chcesz dokonać fragmentacji i Filtruj według podczas wykonywania zapytania o dane.

> [!TIP]
> Przejrzyj JSON kształtowania w dokumentacji [wysyłanie zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Upewnij się, że dane referencyjne

A **zestawu danych referencyjnych** jest kolekcją elementów, które polepszają zdarzenia ze źródła zdarzenia. Aparat transferu danych przychodzących Series Insights czasu łączy każde zdarzenie ze źródła zdarzeń z odpowiednich wiersza danych w zestawie danych referencyjnych. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. To połączenie jest oparte na kolumny klucza podstawowego, zdefiniowane w zestawie danych referencyjnych.

Należy zauważyć, że w danych referencyjnych nie jest częścią wstecznie. Oznacza to, że tylko danych przychodzących bieżących i przyszłych jest dopasowane i przyłączone do odwołania zestawu daty, po jego skonfigurowaniu i przekazać.  Jeśli planujesz wysyłać dużej ilości danych historycznych TSI i nie przekazywania lub Utwórz dane referencyjne w usłudze TSI najpierw, a następnie może być konieczne ponowne wykonywać swoją pracę (podpowiedzi, nie było przyjemne).  

Aby dowiedzieć się więcej na temat sposobu tworzenia, przekazywanie i Zarządzaj danymi w usłudze TSI odwołania, przejdź do naszego [dokumentacji zestawu danych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zacznij od utworzenia [nowego środowiska usługi Time Series Insights w witrynie Azure portal](time-series-insights-get-started.md).

- Dowiedz się, jak [dodawania źródła zdarzeń Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) do usługi Time Series Insights.

- Uzyskać informacje dotyczące sposobu [skonfigurować źródła zdarzeń usługi IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
