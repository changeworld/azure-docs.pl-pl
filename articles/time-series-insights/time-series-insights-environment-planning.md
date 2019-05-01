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
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: f73f814321abbb75624ac18c9191c69a99cfe925
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64693574"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planowanie środowiska usługi Azure Time Series Insights

W tym artykule opisano planowanie środowiska usługi Azure Time Series Insights na podstawie oczekiwanego transfer przychodzący i wymagania dotyczące przechowywania danych.

## <a name="video"></a>Połączenia wideo

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>W tym filmie omówione przechowywanie danych usługi Time Series Insights oraz sposobu planowania dla niego.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Najlepsze praktyki

Aby rozpocząć korzystanie z usługi Time Series Insights, najlepiej Jeśli wiesz, jak dużo danych, które chcą wypchnąć za minutę, a także, jak długo ma być do przechowywania danych.  

Aby uzyskać więcej informacji o wydajności i przechowywania dla jednostki SKU zarówno czas serii szczegółowych informacji, zobacz [cennik usługi Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Weź pod uwagę następujące atrybuty do planu najlepsze środowisko dla długoterminowym sukcesie:

- Pojemność magazynu
- Okres przechowywania danych
- Pojemność zdarzeń przychodzących
- Kształtowanie zdarzeń
- Sprawdzając, czy dane referencyjne w miejscu

## <a name="understand-storage-capacity"></a>Zrozumienie pojemność magazynu

Domyślnie usługa Time Series Insights zachowuje danych, w oparciu o ilość miejsca w magazynie, które zostały aprowizowane (jednostki czasu ilość miejsca na jednostkę) i ruch przychodzący.

## <a name="understand-data-retention"></a>Omówienie przechowywania danych

Można skonfigurować środowisko usługi Time Series Insights **czas przechowywania danych** ustawienia, umożliwiając maksymalnie 400 dni przechowywania.  Usługa Time Series Insights ma dwa tryby, taki, który optymalizuje zapewniających środowiska ma najbardziej aktualne dane (na domyślnie) i drugiego, który jest zoptymalizowany do zapewnienia przechowywania limitów, gdzie ruch przychodzący jest wstrzymany, jeśli ogólną pojemność magazynu środowisko zostanie osiągnięty.  Można dostosować, przechowywania i przełączania między dwoma trybami na stronie konfiguracji środowiska w witrynie Azure portal.

Można skonfigurować maksymalnie 400 dni przechowywania danych w środowisku usługi Time Series Insights.

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. W [witryny Azure portal](https://portal.azure.com), wybierz środowisko usługi Time Series Insights.

2. Na **strony środowiska usługi Time Series Insights**w obszarze **ustawienia** nagłówka, wybierz **Konfiguruj**. 

3. W **czas przechowywania danych (w dniach)** wprowadź wartość z zakresu od 1 do 400.

   ![Skonfiguruj przechowywanie](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Zrozumienie pojemność zdarzeń przychodzących

W obszarze inne skoncentrować się na potrzeby planowania ma pojemność zdarzeń przychodzących, która jest pochodną alokacji na minutę. 

Z punktu widzenia ograniczania pakiet ingressed danych o rozmiarze pakietów wynosi 32 KB jest traktowane jako 32 zdarzenia, każdy o rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia wynosi 32 KB; pakiety danych większe niż 32 KB są obcinane.

Poniższa tabela podsumowuje pojemność zdarzeń przychodzących dla każdej jednostki SKU:

|SKU  |Liczba zdarzeń miesięcznie na jednostkę  |Zdarzenia rozmiar na miesiąc na jednostkę  |Liczba zdarzeń na minutę na jednostkę  | Rozmiar na minutę na jednostkę   |
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

Aby uzyskać informacji na temat sposobu zapobiegania ograniczania przepustowości i opóźnienia, zobacz [zmniejszyć opóźnienia i ograniczania przepustowości](time-series-insights-environment-mitigate-latency.md).

## <a name="shaping-your-events"></a>Kształtowanie zdarzeń

Jest ważne upewnić się, w sposób wysyłania zdarzeń do usługi TSI obsługuje rozmiar środowiska, w przypadku udostępniania (i odwrotnie, można mapować rozmiar środowiska, ile zdarzeń odczytuje TSI i rozmiar każdego zdarzenia).  Podobnie należy wziąć pod uwagę atrybuty, które chcesz dokonać fragmentacji i Filtruj według podczas wykonywania zapytania o dane.  Pamiętając o tym, Sugerujemy, przeglądając JSON kształtowania części naszych [wysyłanie zdarzeń dokumentacji](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  Jest w dolnej części strony.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Sprawdzając, czy dane referencyjne w miejscu

Zestawu danych referencyjnych jest kolekcją elementów, które polepszają zdarzenia ze źródła zdarzenia. Aparat transferu danych przychodzących Series Insights czasu łączy każde zdarzenie ze źródła zdarzeń z odpowiednich wiersza danych w zestawie danych referencyjnych. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. To połączenie jest oparte na kolumny klucza podstawowego, zdefiniowane w zestawie danych referencyjnych.

Należy zauważyć, że w danych referencyjnych nie jest częścią wstecznie. Oznacza to, że tylko danych przychodzących bieżących i przyszłych jest dopasowane i przyłączone do odwołania zestawu daty, po jego skonfigurowaniu i przekazać.  Jeśli planujesz wysyłać dużej ilości danych historycznych TSI i nie przekazywania lub Utwórz dane referencyjne w usłudze TSI najpierw, a następnie może być konieczne ponowne wykonywać swoją pracę (podpowiedzi, nie było przyjemne).  

Aby dowiedzieć się więcej na temat sposobu tworzenia, przekazywanie i Zarządzaj danymi w usłudze TSI odwołania, przejdź do naszego [dokumentacji zestawu danych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii biznesowych

Jako usługi Azure Time Series Insights zapewnia wysoką dostępność (HA) przy użyciu nadmiarowość na poziomie regionu platformy Azure, bez konieczności wykonywania dodatkowej pracy wymaganego przez rozwiązanie. Platforma Microsoft Azure zawiera funkcje ułatwiające tworzenie rozwiązań za pomocą funkcji odzyskiwania po awarii lub dostępności między regionami. Jeśli chcesz zapewnić globalnego, między regionami wysokiej dostępności dla urządzeń lub użytkowników, korzystać z tych funkcji odzyskiwania po awarii z platformy Azure. Artykuł [wskazówek technicznych ciągłości biznesowej Azure](../resiliency/resiliency-technical-guidance.md) opis wbudowanych funkcji na platformie Azure zapewniającej ciągłość biznesową i odzyskiwanie po awarii. [Odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/index) dokument zawiera wskazówki dotyczące architektury, strategii dla aplikacji platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii.

Usługa Azure Time Series Insights nie ma wbudowanych firm odzyskiwania po awarii (BCDR).
Domyślnie usługa Azure IoT Hub i Event Hubs mają wbudowane odzyskiwania.

Aby dowiedzieć się więcej na temat zasad bcdr można wykorzystać usługi IoT Hub, odwiedź [tutaj](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).  

Aby dowiedzieć się więcej na temat zasad BCDR Centrum zdarzeń, head [tutaj](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Jednak klienci, którzy wymagają strategii BCDR, nadal można zaimplementować strategię odzyskiwania przy użyciu następującej metody.
Tworząc drugie środowisko usługi Time Series Insights w zdarzenia kopii zapasowej Azure region i Wyślij do tego środowiska dodatkowej ze źródła zdarzeń głównej, wykorzystując drugi dedykowanej grupy klientów i wytycznych BCDR tego źródła zdarzeń.  

1. Utwórz środowisko, w drugim regionie.  Więcej informacji na temat tworzenia środowiska usługi Time Series Insights [tutaj](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Utwórz drugi dedykowanej grupy klientów dla źródła zdarzeń i łączenie źródła zdarzeń do nowego środowiska.  Pamiętaj wyznaczyć grupy odbiorców drugi, dedykowanych.  Więcej informacji na ten temat można znaleźć, wykonując jedną [dokumentacja usługi IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) lub [dokumentacja Centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access).
1. Gdyby Twojego regionu podstawowego przestaną działać w przypadku wystąpienia zdarzenia awarii, przeprowadź operacje tworzenia kopii zapasowej środowiska usługi Time Series Insights.  

Jest **pamiętać** podczas dowolnego scenariusza trybu failover może wystąpić opóźnienie przed TSI ponownie rozpocząć przetwarzanie komunikatów: może to spowodować nagły podczas przetwarzania komunikatu. Aby uzyskać więcej informacji, zapoznaj się [tego dokumentu](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [dodawania źródła zdarzeń Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) do usługi Time Series Insights.

- Uzyskać informacje dotyczące sposobu [skonfigurować źródła zdarzeń usługi IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).