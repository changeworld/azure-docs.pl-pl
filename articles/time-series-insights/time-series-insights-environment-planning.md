---
title: Zaplanuj skalę środowiska Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób postępowania z najlepszymi rozwiązaniami w przypadku planowania środowiska Azure Time Series Insights. Obszary, które są objęte usługą, obejmują pojemność magazynu, przechowywanie danych, wydajność, monitorowanie i ciągłość działania oraz odzyskiwanie po awarii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 659a6357736817f4a590b97e585230ec8c2b7dae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332917"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Zaplanuj środowisko Azure Time Series Insights GA

W tym artykule opisano sposób planowania środowiska Azure Time Series Insights ogólnej dostępności (GA) w oparciu o oczekiwany współczynnik ruchu przychodzącego i wymagania dotyczące przechowywania danych.

## <a name="video"></a>Wideo

**Obejrzyj ten film wideo, aby dowiedzieć się więcej na temat przechowywania danych w Azure Time Series Insights i jak planować go**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Najlepsze praktyki

Aby rozpocząć pracę z Azure Time Series Insights, najlepiej, Jeśli wiesz, ile danych ma być wypchnięcich przez minutę, i jak długo chcesz przechowywać dane.  

Aby uzyskać więcej informacji o pojemności i przechowywaniu dla obu Time Series Insights jednostek SKU, zobacz [cennik Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Aby najlepiej zaplanować środowisko Time Series Insights na potrzeby długotrwałego sukcesu, należy wziąć pod uwagę następujące atrybuty:

- [Pojemność magazynu](#storage-capacity)
- [Okres przechowywania danych](#data-retention)
- [Pojemność transferu danych przychodzących](#ingress-capacity)
- [Kształtowanie zdarzeń](#shape-your-events)
- [Upewnienie się, że masz dane referencyjne na miejscu](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Pojemność magazynu

Domyślnie Time Series Insights zachowuje dane na podstawie ilości udostępnianych magazynów (jednostki &#215; ilość miejsca do magazynowania na jednostkę) i ruchu przychodzącego.

## <a name="data-retention"></a>Przechowywanie danych

Można zmienić ustawienie **czas przechowywania danych** w środowisku Azure Time Series Insights. Możesz włączyć do 400 dni przechowywania. 

Azure Time Series Insights ma dwa tryby:

* Jeden tryb optymalizuje najbardziej aktualne dane. Wymusza zasady umożliwiające **przeczyszczenie starych danych** z ostatnich dostępnych danych z wystąpieniem. Ten tryb jest domyślnie włączony. 
* Druga optymalizuje dane, aby pozostawały poniżej skonfigurowanych limitów przechowywania. **Wstrzymaj** ruch przychodzący uniemożliwia ingressed nowych danych, gdy zostanie wybrany z **zachowaniem limitu magazynu**. 

Można dostosować przechowywanie i przełączać się między dwoma trybami na stronie konfiguracji środowiska w Azure Portal.

> [!IMPORTANT]
> W środowisku Azure Time Series Insights GA można skonfigurować maksymalnie 400 dni przechowywania danych.

### <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. W [Azure Portal](https://portal.azure.com)wybierz środowisko Time Series Insights.

1. W okienku **środowisko Time Series Insights** w obszarze **Ustawienia**wybierz pozycję **Konfiguruj**.

1. W polu **czas przechowywania danych (w dniach)** wprowadź wartość z zakresu od 1 do 400.

   [@no__t — przechowywanie 1Configure](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Aby dowiedzieć się więcej na temat implementowania odpowiednich zasad przechowywania danych, zobacz [jak skonfigurować przechowywanie](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Pojemność transferu danych przychodzących

Drugi obszar, na którym należy skoncentrować się podczas planowania środowiska Time Series Insights, to *zdolność*transferu danych przychodzących. Pojemność transferu danych przychodzących jest pochodną przydziału na minutę.

Z punktu widzenia ograniczenia przepustowości pakiet danych ingressed, który ma rozmiar pakietu 32 KB jest traktowany jako zdarzenia 32, o rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia to 32 KB. Pakiety danych o rozmiarze większym niż 32 KB są obcinane.

Poniższa tabela zawiera podsumowanie pojemności przychodzącej na jednostkę dla każdej jednostki SKU Time Series Insights:

|JSZ  |Liczba zdarzeń miesięcznie  |Rozmiar zdarzenia miesięcznie  |Liczba zdarzeń na minutę  |Rozmiar zdarzenia na minutę  |
|---------|---------|---------|---------|---------|
|S1     |   30 000 000     |  30 GB     |  720    |  720 KB   |
|S2     |   300 000 000    |   300 GB   | 7 200   | 7 200 KB  |

Można zwiększyć pojemność jednostki SKU S1 lub S2 do 10 jednostek w jednym środowisku. Nie można migrować ze środowiska S1 do warstwy S2. Nie można migrować ze środowiska S2 do warstwy S1.

W przypadku pojemności związanych z transferem danych przychodzących należy najpierw określić łączną liczbę transferów przychodzących wymaganych w ciągu miesiąca. Następnie ustal, co to są potrzeby na minutę. 

Ograniczanie i opóźnienie odgrywają rolę w wydajności na minutę. Jeśli Twoje dane wejściowe są dostępne przez czas krótszy niż 24 godziny, Time Series Insights może "wychwycić" w tempie przychodzącym wielokrotne wynoszącym dwie częstotliwości wymienione w powyższej tabeli.

Na przykład jeśli masz pojedynczą jednostkę SKU S1, przyniesiesz dane przy użyciu stawki 720 zdarzeń na minutę, a szybkość danych jest większa niż jedna godzina według stawki 1 440 zdarzeń lub mniejszej, a w środowisku nie ma zauważalnego opóźnienia. Jednakże w przypadku przekroczenia 1 440 zdarzeń na minutę przez więcej niż godzinę, prawdopodobnie wystąpi opóźnienie w danych, które są wizualizowane i dostępne dla zapytań w środowisku.

Użytkownik może nie wiedzieć, jak dużo danych, które powinny zostać wypchnięcie. W takim przypadku można znaleźć dane telemetryczne dla [usługi azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) i [platformy Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) w ramach subskrypcji Azure Portal. Dane telemetryczne mogą pomóc w ustaleniu, jak zainicjować obsługę środowiska. Aby wyświetlić dane telemetryczne, użyj okienka **metryki** w Azure Portal dla odpowiedniego źródła zdarzenia. Jeśli zrozumiesz metryki źródła zdarzeń, możesz efektywnie zaplanować i zainicjować obsługę środowiska Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Oblicz wymagania dotyczące transferu danych przychodzących

Aby obliczyć wymagania dotyczące transferu danych przychodzących:

- Sprawdź, czy pojemność transferu danych przychodzących przekracza średnią stawkę za minutę i czy środowisko jest wystarczająco duże, aby obsłużyć przewidywane przekroczenie wartości równorzędnej równej dwukrotności przez czas krótszy niż jedna godzina.

- Jeśli nastąpi przekroczenia przez czas dłuższy niż 1 godzina, użyj wskaźnika wzrostu jako średniej. Zapewnij środowisko o pojemności umożliwiającej obsługę wskaźnika wzrostu.

### <a name="mitigate-throttling-and-latency"></a>Ograniczanie przepustowości i opóźnienia

Aby dowiedzieć się, jak zapobiegać ograniczaniu i opóźnieniu, zobacz [ograniczanie opóźnień i ograniczanie przepustowości](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Kształtowanie zdarzeń

Ważne jest, aby upewnić się, że sposób wysyłania zdarzeń do Time Series Insights obsługuje rozmiar środowiska, w którym jest inicjowane Inicjowanie obsługi administracyjnej. (Z kolei można zamapować rozmiar środowiska na liczbę zdarzeń, Time Series Insights odczytuje i rozmiar każdego zdarzenia). Ważne jest również, aby myśleć o atrybutach, których można użyć do wycinania i filtrowania według zapytania o dane.

> [!TIP]
> Zapoznaj się z dokumentacją kształtowania JSON w temacie [wysyłanie zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Upewnij się, że masz dane referencyjne

*Zestaw danych referencyjnych* to kolekcja elementów, które rozszerzają zdarzenia ze źródła zdarzenia. Time Series Insightsy aparat transferu danych przychodzących łączy każde zdarzenie ze źródła zdarzeń z odpowiednim wierszem danych w zestawie danych referencyjnych. Zdarzenie rozszerzone jest następnie dostępne dla zapytania. Sprzężenie jest oparte na kolumnach **klucza podstawowego** , które są zdefiniowane w zestawie danych referencyjnych.

> [!NOTE]
> Dane referencyjne nie są przyłączone wstecz. Tylko bieżące i przyszłe dane wejściowe są dopasowywane i przyłączone do zestawu danych referencyjnych po jego skonfigurowaniu i przekazaniu. Jeśli planujesz wysłanie dużej ilości danych historycznych do Time Series Insights i nie przekażesz ani nie utworzysz danych referencyjnych w Time Series Insights, może być konieczne ponowne wykonanie pracy (Wskazówka: brak zabawy).  

Aby dowiedzieć się więcej na temat sposobu tworzenia, przekazywania i zarządzania danymi referencyjnymi w Time Series Insights, zobacz [dokumentację zestawu danych referencyjnych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od utworzenia [nowego środowiska Time Series Insights w Azure Portal](time-series-insights-get-started.md).

- Dowiedz się, jak [dodać Event Hubs Źródło zdarzenia](time-series-insights-how-to-add-an-event-source-eventhub.md) do Time Series Insights.

- Przeczytaj [, jak skonfigurować źródło zdarzeń IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
