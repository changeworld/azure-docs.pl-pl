---
title: Diagnozowanie, rozwiązywanie problemów i rozwiązywaniu problemów w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowania, rozwiązywanie problemów i rozwiązywanie typowych problemów, które mogą wystąpić w danym środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 68d46b54bf066724d8f3474f421d2d1f751b75e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235046"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnozowanie i rozwiązywanie problemów w danym środowisku usługi Time Series Insights

W tym artykule opisano niektóre problemy, które mogą wystąpić w danym środowisku usługi Azure Time Series Insights. Artykuł zawiera potencjalne przyczyny i potencjalne rozwiązania do rozpoznania.

## <a name="video"></a>Połączenia wideo

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Poznaj wymagania klientów Series Insights wspólnego czasu i środki zaradcze.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: dane nie są wyświetlane

Brak danych w [Eksploratora usługi Azure Time Series Insights](https://insights.timeseries.azure.com) może wystąpić z kilku typowych powodów:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Przyczyna A: zdarzenia źródła danych nie jest w formacie JSON

Usługa Azure Time Series Insights obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Przyczyna B: klucz źródła zdarzeń nie ma wymaganych uprawnień

* Dla usługi IoT hub w usłudze Azure IoT Hub, musisz podać klucz, który ma **połączenie z usługą** uprawnienia. Jedną z **iothubowner** lub **usługi** zasady będą działać, ponieważ mają one zarówno **połączenie z usługą** uprawnienia.

   [![Usługa IoT Hub połączyć uprawnienia](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Centrum zdarzeń w usłudze Azure Event Hubs, należy podać klucz, który ma **nasłuchiwania** uprawnienia. Jedną z **odczytu** lub **zarządzanie** zasady będą działać, ponieważ mają one zarówno **nasłuchiwania** uprawnienia.

   [![Uprawnienia nasłuchiwania Centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Przyczyna C: podanej grupy odbiorców nie jest wyłącznie dla usługi Time Series Insights

Po zarejestrowaniu Centrum IoT hub lub Centrum zdarzeń, jest ważne, aby ustawić grupy odbiorców, które chcesz użyć do odczytywania danych. Ta grupa odbiorców *nie może być współużytkowana*. Jeśli grupa odbiorców jest udostępniony, podstawowej usługi IoT hub lub Centrum zdarzeń losowo i automatycznie Odłącza jeden czytników. Podaj grupy odbiorców unikatowy dla usługi Time Series Insights do odczytu.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: niektóre dane są wyświetlane, ale brakuje danych

Gdy dane są wyświetlane tylko częściowo i dane wydaje się być opóźnione, rozważ kilka możliwości.

### <a name="cause-a-your-environment-is-being-throttled"></a>Odp.: środowisko jest ograniczane spowodować

Ograniczanie przepływności jest typowym problemem, gdy środowiska są aprowizowane po utworzeniu źródła zdarzeń, która zawiera dane. Usługa Azure IoT Hub i centra zdarzeń Azure przechowywać dane przez maksymalnie siedem dni. Usługa Time Series Insights zawsze zaczynać najstarszych zdarzeń w zdarzeniu źródła (pierwszy in, first-out lub *FIFO*).

Na przykład jeśli masz 5 milionów zdarzeń źródła zdarzeń po podłączeniu do S1, środowiska usługi Time Series Insights pojedynczej jednostki, usługi Time Series Insights odczytuje około 1 mln zdarzeń dziennie. Wygląda, takich jak usługi Time Series Insights występuje pięć dni opóźnienia. Co się dzieje jest jednak, że środowisko jest ograniczane.

Jeśli masz stare zdarzenia w źródle zdarzenia masz podejście ograniczania przepustowości w jeden z dwóch sposobów:

- Zmień limity przechowywania źródła zdarzeń, aby usunąć stare zdarzenia, które nie mają być wyświetlane w usłudze Time Series Insights.
- Aprowizuj większy rozmiar środowiska (liczbę jednostek) w celu zwiększenia przepływności starych zdarzeń. Użyty w poprzednim przykładzie, jeśli zwiększyć w tym samym środowisku S1 do pięciu jednostek przez jeden dzień, środowisko należy zapoznać się z nimi w ciągu dnia. Jeśli stan zdarzenia w środowisku produkcyjnym jest 1 miliona lub mniej zdarzeń dziennie, można zmniejszyć pojemność zdarzeń jedną jednostkę po jej wyrównywane.

Limit ograniczania są wymuszane na podstawie typu jednostki SKU środowiska i pojemności. Wszystkie źródła zdarzeń w środowisku udostępnianie tej pojemności. Jeśli źródła zdarzeń dla usługi IoT hub lub Centrum zdarzeń przekazujący dane poza granicami wymuszone, zobaczysz ograniczania przepustowości i aktualizowany z opóźnieniem.

Na poniższej ilustracji przedstawiono środowisko usługi Time Series Insights, które ma jednostkę SKU S1 i pojemność 3. Jest to możliwe ruch przychodzący 3 mln zdarzeń każdego dnia.

![Bieżąca pojemność jednostki SKU środowiska](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Na przykład załóżmy, że to środowisko pozyskuje komunikaty z Centrum zdarzeń. Na poniższej ilustracji przedstawiono szybkość transferu danych przychodzących:

[![Przykład szybkość transferu danych przychodzących dla Centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)](media/diagnose-and-solve-problems/eventhub-ingress-rate.png#lightbox)

Stawka dzienna ruch przychodzący jest ~ 67,000 wiadomości. Ten kurs przekłada się na około 46 komunikatów na minutę. Jeśli każdy komunikat do Centrum zdarzeń jest spłaszczany do jednego zdarzenia usługi Time Series Insights, ograniczanie przepustowości nie zachodzi. Jeśli każdy komunikat do Centrum zdarzeń jest spłaszczany 100 zdarzeń usługi Time Series Insights, 4,600 zdarzenia powinny przetwarzanych na minutę. Środowisko S1 SKU usługi, które ma pojemność wynoszącą 3 można tylko 2100 zdarzeń przychodzących na minutę (1 mln zdarzeń każdego dnia = 700 zdarzeń na minutę na trzech jednostek = 2100 zdarzeń na minutę). Dla tej konfiguracji zobaczysz opóźnienie z powodu dławienia.

Ogólne zrozumieć sposób działania spłaszczania logiki, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Zalecane rozwiązania nadmierne ograniczania

Aby usunąć opóźnienie, należy zwiększyć pojemność jednostki SKU środowiska. Aby uzyskać więcej informacji, zobacz [Skalowanie środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Przyczyna B: początkowej pozyskiwania danych historycznych spowalnia transferu danych przychodzących

Jeśli możesz połączyć istniejące źródło zdarzenia, istnieje prawdopodobieństwo, że usłudze IoT hub lub Centrum zdarzeń zawiera już dane. Środowisko rozpoczyna ściągania danych z początkiem okresu przechowywania wiadomości źródła zdarzeń. Nie można zastąpić tego przetwarzania domyślne. Możesz zaangażować ograniczania przepustowości. Ograniczanie może potrwać nadążyć go pozyskuje dane historyczne.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Zalecane rozwiązania dużej początkowy pozyskiwania

Aby naprawić opóźnienie:

1. Zwiększ możliwości SKU maksymalną dozwoloną wartość (w tym przypadku 10). Po zwiększenie pojemności nadążyć znacznie szybciej rozpoczyna się proces transferu danych przychodzących. Opłaty są naliczane za zwiększenia pojemności. Aby zwizualizować, jak szybko są Przechwytywanie up, możesz wyświetlić wykres dostępności w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).

2. Gdy opóźnienie zostaje przechwycony w, należy zmniejszyć pojemność jednostki SKU do normalnego transfer przychodzący.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: ustawienie nazwę właściwości sygnatury czasowej źródła zdarzeń nie działa.

Upewnij się, że sygnatura czasowa nazwy i wartości właściwości są zgodne z następujących reguł:

* Nazwę właściwości sygnatury czasowej jest uwzględniana wielkość liter.
* Wartość właściwości sygnatury czasowej, która pochodzi ze źródła zdarzenia, zgodnie z ciągu JSON powinien mieć format _RRRR-MM-Ddtgg. FFFFFFFK_. Na przykład **2008-04-12T12:53Z**.

Najprostszym sposobem zapewnienia, że nazwę właściwości sygnatury czasowej są przechwytywane i działa poprawnie za pomocą Eksploratora usługi Time Series Insights. W Eksploratorze usługi Time Series Insights za pomocą wykresu wybierz okres czasu, po wprowadzeniu nazwę właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz **Eksploruj zdarzenia** opcji.

Pierwszy nagłówek kolumny powinien być nazwę właściwości sygnatury czasowej. Obok wyraz **sygnatura czasowa**, powinien zostać wyświetlony **($ts)** .

Nie powinny zostać wyświetlone następujące wartości:

- *(abc)* : Wskazuje, że usługa Time Series Insights odczytuje wartości danych jako ciągi.
- *Ikona kalendarza*: Wskazuje, że usługi Time Series Insights odczytuje wartości danych jako *daty/godziny*.
- *#* : Wskazuje, że usługa Time Series Insights odczytuje wartości danych jako liczba całkowita.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pomoc, Rozpocznij konwersację [forum MSDN dotyczącym](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights).

- Opcje asystowanej pomocy technicznej, można użyć [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).
