---
title: Planowanie skali środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wykonać najważniejsze wskazówki podczas planowania środowiska usługi Azure Time Series Insights. Obszary, które są objęte obejmują pojemność magazynu, przechowywanie danych, pojemność zdarzeń przychodzących, monitorowania i firm ciągłości działania i odzyskiwania po awarii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431023"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planowanie środowiska Azure czas serii szczegółowych informacji w wersji Ogólnodostępnej

W tym artykule opisano planowanie środowiska ogólnodostępnej (GA) usługa Azure Time Series Insights na podstawie oczekiwanego transfer przychodzący i wymagania dotyczące przechowywania danych.

## <a name="video"></a>Połączenia wideo

**Obejrzyj ten film wideo, aby dowiedzieć się więcej o przechowywaniu danych w usłudze Azure Time Series Insights oraz sposobu planowania jego**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Najlepsze praktyki

Aby rozpocząć korzystanie z usługi Time Series Insights, najlepiej Jeśli wiesz, jak dużo danych, które chcą wypchnąć przez minutę i czas potrzebny do przechowywania danych.  

Aby uzyskać więcej informacji o wydajności i przechowywania dla jednostki SKU zarówno czas serii szczegółowych informacji, zobacz [cennik usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Aby najlepiej zaplanować środowiska usługi Time Series Insights z długoterminowym sukcesie, należy wziąć pod uwagę następujące atrybuty:

- <a href="#storage-capacity">Pojemność magazynu</a>
- <a href="#data-retention">Okres przechowywania danych</a>
- <a href="#ingress-capacity">Pojemność zdarzeń przychodzących</a>
- <a href="#shape-your-events">Kształtowanie zdarzeń</a>
- <a href="#ensure-that-you-have-reference-data">Zapewnienia, że mają dane referencyjne w miejscu</a>

## <a name="storage-capacity"></a>Pojemność magazynu

Domyślnie usługi Time Series Insights przechowuje dane, w oparciu o ilość miejsca w magazynie obsługi administracyjnej (jednostki &#215; wielkość magazynu na jednostkę) i ruch przychodzący.

## <a name="data-retention"></a>Przechowywanie danych

Możesz zmienić **czas przechowywania danych** ustawienie w danym środowisku usługi Time Series Insights. Można włączyć do 400 dni przechowywania. 

Usługa Time Series Insights ma dwa tryby. Jeden tryb optymalizuje za zapewnienie, że w środowisku zawierającym najbardziej aktualne dane. Ten tryb jest włączona, domyślnie. 

Inny tryb optymalizuje za zapewnienie, że spełnione są limity przechowywania. W trybie drugi ruch przychodzący jest wstrzymany, po spełnieniu łączna pojemność magazynu środowiska. 

Można dostosować, przechowywania i przełączania między dwoma trybami na stronie konfiguracji środowiska w witrynie Azure portal.

Można skonfigurować maksymalnie 400 dni przechowywania danych w środowisku usługi Time Series Insights.

### <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. W [witryny Azure portal](https://portal.azure.com), wybierz środowisko usługi Time Series Insights.

1. W **środowiska usługi Time Series Insights** okienku w obszarze **ustawienia**, wybierz opcję **Konfiguruj**.

1. W **czas przechowywania danych (w dniach)** pole, wprowadź wartość zawierającą od 1 do 400.

   [![Skonfigurowanie okresu przechowywania](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Aby dowiedzieć się więcej o tym, jak można zaimplementować zasady przechowywania danych, zobacz [sposobu konfigurowania przechowywania](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Pojemność zdarzeń przychodzących

Drugi skoncentrować się na Planowanie środowiska usługi Time Series Insights ma pojemność zdarzeń przychodzących. Pojemność zdarzeń przychodzących jest pochodną alokacji na minutę.

Z punktu widzenia ograniczania pakiet ingressed danych, który ma rozmiar pakietu wynosi 32 KB jest traktowane jako 32 zdarzenia, każdy rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia wynosi 32 KB. Pakiety danych większe niż 32 KB są obcinane.

Poniższa tabela podsumowuje pojemność zdarzeń przychodzących na jednostkę dla każdego SKU usługi Time Series Insights:

|SKU  |Liczba zdarzeń miesięcznie  |Rozmiar zdarzenia na miesiąc  |Liczba zdarzeń na minutę  |Rozmiar zdarzenia na minutę  |
|---------|---------|---------|---------|---------|
|S1     |   30 mln     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milionów    |   300 GB   | 7,200   | 7200 KB  |

Można zwiększyć pojemność S1 lub S2 SKU do 10 jednostek w ramach jednego środowiska. Nie można migrować ze środowiska S1, s2. Nie można migrować ze środowiska S2 do S1.

Pojemność transferu danych przychodzących należy najpierw określić łączny ruch przychodzący, które są wymagane na co miesiąc. Następnie należy określić, jakie usługi na minutę wymaga są. 

Ograniczanie przepływności i opóźnień odtwarzanie roli pojemność na minutę. W przypadku kolekcji w swojej transferu danych przychodzących trwa mniej niż 24 godziny, usługi Time Series Insights może "Łap" stawki transferu danych przychodzących dwa razy stawek wymienionych w powyższej tabeli.

Na przykład jeśli masz pojedynczy SKU S1, dane transferu danych przychodzących z szybkością 720 zdarzeń na minutę, a szybkość danych gwałtowne wzrosty przez mniej niż godzinę z szybkością 1440 zdarzeń lub mniej, występuje nie zauważalnego opóźnienia w danym środowisku. Jednak po przekroczeniu 1440 zdarzenia na minutę dla więcej niż jedna godzina, prawdopodobnie wystąpią opóźnienia w danych, które są wizualizowany i dostępne dla zapytania w danym środowisku.

Możesz nie znać wcześniej jak dużo danych oczekujesz, że wypychanie. W takim przypadku można znaleźć danych telemetrii dla [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) i [usługi Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) w portalu subskrypcji platformy Azure. Dane telemetryczne może pomóc w określeniu, jak aprowizować środowiska. Użyj **metryki** okienko w witrynie Azure portal dla źródła zdarzenia odpowiednich wyświetlić jego dane telemetryczne. Jeśli zrozumiesz metryk źródła zdarzeń, bardziej efektywne zaplanować i aprowizować środowiska usługi Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Obliczyć wymagania dotyczące ruchu przychodzącego

Aby obliczyć wymagania transferu danych przychodzących:

- Zweryfikuj swoje pojemność zdarzeń przychodzących powyżej średniej stawka za minutę i czy środowisko jest wystarczająco duży, aby obsłużyć Twojego przewidywanego ruchu przychodzącego odpowiednikiem dwa razy pojemności przez mniej niż godzinę.

- Jeśli występują skoki transferu danych przychodzących, używających ostatniej dłużej niż 1 godzina, szybkość, z kolekcji jako Twoja średnia. Aprowizować środowiska z możliwością obsługi szybkość, z kolekcji.

### <a name="mitigate-throttling-and-latency"></a>Eliminowanie ograniczania przepustowości i opóźnienia

Aby uzyskać informacji na temat sposobu zapobiegania ograniczania przepustowości i opóźnienia, zobacz [zmniejszyć opóźnienia i ograniczania przepustowości](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Kształt zdarzeń

Należy upewnić się, że sposób wysyłania zdarzeń do usługi Time Series Insights obsługuje rozmiar środowiska, które są Inicjowanie obsługi administracyjnej. (I odwrotnie, można mapować rozmiar środowiska, ile zdarzeń usługi Time Series Insights odczytuje i rozmiar każdego zdarzenia.) Należy również wziąć pod uwagę te atrybuty, które warto używać filtrowania według podczas wysyłania zapytania danych.

> [!TIP]
> Przejrzyj JSON kształtowania w dokumentacji [wysyłanie zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Upewnij się, że dane referencyjne

A *odwoływać się do zestawu danych* jest kolekcją elementów, które polepszają zdarzenia ze źródła zdarzenia. Aparat transferu danych przychodzących usługi Time Series Insights łączy każde zdarzenie ze źródła zdarzeń z odpowiednich wiersza danych w zestawie danych odwołania. Rozszerzone zdarzenie jest dostępna dla zapytania. Sprzężenia opiera się na **klucz podstawowy** kolumn, które są zdefiniowane w zestawie danych odwołania.

> [!NOTE]
> Dane referencyjne nie jest przyłączona wstecznie. Tylko ruch przychodzący z obecnym i przyszłym dane są dopasowane i połączone z zestawem danych odwołania po skonfigurować i przekazać. Jeśli użytkownik chce wysyłać dużych ilości danych historycznych usługi Time Series Insights i nie pierwszy przekazywania lub Utwórz dane referencyjne w usłudze Time Series Insights, może być konieczne ponowne wykonywanie pracy (Wskazówka: nie było przyjemne).  

Aby dowiedzieć się więcej na temat sposobu tworzenia, przekazywanie i Zarządzaj danymi odwołania w usługi Time Series Insights, zobacz nasze [dokumentacji zestawu danych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Kolejne kroki

- Zacznij od utworzenia [nowego środowiska usługi Time Series Insights w witrynie Azure portal](time-series-insights-get-started.md).

- Dowiedz się, jak [dodawania źródła zdarzeń usługi Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) do usługi Time Series Insights.

- Uzyskać informacje dotyczące sposobu [skonfigurować źródła zdarzeń usługi IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
