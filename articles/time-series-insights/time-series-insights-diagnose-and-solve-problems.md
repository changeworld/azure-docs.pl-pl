---
title: Diagnozowanie, rozwiązywanie problemów — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób diagnozowania, rozwiązywania i rozwiązywania typowych problemów w środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152596"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnozowanie i rozwiązywanie problemów w środowisku usługi Time Series Insights

W tym artykule opisano niektóre problemy, które mogą wystąpić w środowisku usługi Azure Time Series Insights. Artykuł oferuje potencjalne przyczyny i rozwiązania do rozwiązania.

## <a name="video"></a>Film wideo

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Dowiedz się więcej o typowych wyzwaniach i ograniczeniach dla klientów usługi Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: nie są wyświetlane żadne dane

Żadne dane w [Eksploratorze usługi Azure Time Series Insights](https://insights.timeseries.azure.com) mogą wystąpić z kilku typowych powodów:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Przyczyna A: dane źródła zdarzenia nie są w formacie JSON

Usługa Azure Time Series Insights obsługuje tylko dane JSON. W przypadku próbek JSON można przeczytać [w obszarze Obsługiwane kształty JSON](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Przyczyna B: klucz źródłowy zdarzenia nie ma wymaganego uprawnienia

* W przypadku centrum IoT hub w centrum Azure IoT Hub należy podać klucz, który ma uprawnienia **do łączenia usługi.** Wybierz zasady **iothubowner** lub **service,** ponieważ oba mają uprawnienia **do łączenia usługi.**

   [![Uprawnienia do łączenia usługi IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* W przypadku centrum zdarzeń w usłudze Azure Event Hubs należy podać klucz, który ma uprawnienia **nasłuchi.** Albo z **zasad odczytu** lub **zarządzania** będzie działać, ponieważ oba mają uprawnienia **nasłuchiwania.**

   [![Uprawnienia nasłuchiuj centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Przyczyna C: podana grupa odbiorców nie jest dostępna wyłącznie w usłudze Time Series Insights

Podczas rejestrowania centrum IoT hub lub centrum zdarzeń, ważne jest, aby ustawić grupę odbiorców, które mają być używane do odczytu danych. Nie *można udostępnić*tej grupy konsumentów . Jeśli grupa odbiorców jest współużytkowana, podstawowe centrum IoT hub lub centrum zdarzeń automatycznie i losowo rozłącza jeden z czytników. Podaj unikatową grupę odbiorców dla usługi Time Series Insights do odczytu.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Przyczyna D: środowisko zostało właśnie aprowizowane

Dane pojawią się w eksploratorze usługi Time Series Insights w ciągu kilku minut po utworzeniu środowiska i jego danych.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: wyświetlane są niektóre dane, ale brakuje danych

Gdy dane pojawiają się tylko częściowo, a dane wydają się być opóźnione, należy rozważyć kilka możliwości.

### <a name="cause-a-your-environment-is-being-throttled"></a>Przyczyna A: twoje środowisko jest ograniczane

[Ograniczanie jest](time-series-insights-environment-mitigate-latency.md) częstym problemem, gdy środowiska są aprowidzone po utworzeniu źródła zdarzeń, które ma dane. Usługa Azure IoT Hub i usługi Azure Events Hubs przechowują dane przez maksymalnie siedem dni. Usługa Time Series Insights zawsze rozpoczyna się od najstarszego zdarzenia w źródle zdarzeń (first-in, first-out lub *FIFO).*

Na przykład jeśli masz 5 milionów zdarzeń w źródle zdarzeń podczas łączenia się z S1, jednojedoczenowe środowisko time series insights, usługa Time Series Insights odczytuje około 1 miliona zdarzeń dziennie. Może się wydawać, że aplikacja Time Series Insights ma pięć dni opóźnienia. Jednak to, co się dzieje, jest to, że środowisko jest ograniczane.

Jeśli masz stare zdarzenia w źródle zdarzeń, możesz podejść do ograniczania przepustowości na jeden z dwóch sposobów:

- Zmień limity przechowywania źródła zdarzeń, aby usunąć stare zdarzenia, których nie chcesz wyświetlać w aplikacji Time Series Insights.
- Aprowizuj większy rozmiar środowiska (liczba jednostek), aby zwiększyć przepływność starych zdarzeń. Przy użyciu poprzedniego przykładu, jeśli zwiększyć tego samego środowiska S1 do pięciu jednostek na jeden dzień, środowisko powinno nadrobić zaległości w ciągu jednego dnia. Jeśli produkcja zdarzeń w stanie ustalonym wynosi 1 milion lub mniej zdarzeń dziennie, można zmniejszyć pojemność zdarzenia do jednej jednostki po jego nadrobieniu zaległości.

Limit ograniczania przepustowości jest wymuszany na podstawie typu jednostki SKU środowiska i pojemności. Wszystkie źródła zdarzeń w środowisku współużytkują tę pojemność. Jeśli źródło zdarzeń dla centrum IoT hub lub centrum zdarzeń wypycha dane poza wymuszone limity, wystąpi ograniczanie przepustowości i opóźnienie.

Na poniższej ilustracji przedstawiono środowisko usługi Time Series Insights, które ma jednostkę SKU s1 i pojemność 3. Może wnikać 3 miliony zdarzeń dziennie.

[![Bieżąca pojemność jednostki SKU środowiska](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Na przykład załóżmy, że środowisko pochłonie komunikaty z centrum zdarzeń. Dzienny współczynnik transferu gresji wynosi ~67 000 wiadomości. Ta szybkość przekłada się na około 46 wiadomości co minutę. 

* Jeśli każdy komunikat centrum zdarzeń jest spłaszczony do pojedynczego zdarzenia usługi Time Series Insights, ograniczanie nie występuje. 
* Jeśli każdy komunikat centrum zdarzeń jest spłaszczony do 100 zdarzeń usługi Time Series Insights, 4600 zdarzeń należy spożywać co minutę. 

Środowisko SKU S1 o pojemności 3 może przychodzących tylko 2100 zdarzeń co minutę (1 milion zdarzeń dziennie = 700 zdarzeń na minutę w trzech jednostkach = 2100 zdarzeń na minutę). 

Aby uzyskać wysoki poziom zrozumienia działania logiki spłaszczania, przeczytaj [obsługiwane kształty JSON](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Zalecane rozdzielczości nadmiernego ograniczania przepustowości

Aby naprawić opóźnienie, zwiększ pojemność jednostki SKU w swoim środowisku. Aby uzyskać więcej informacji, przeczytaj artykuł [Skalowanie środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Przyczyna B: początkowe połknienie danych historycznych spowalnia wnikanie

Jeśli połączysz istniejące źródło zdarzeń, prawdopodobnie centrum IoT hub lub centrum zdarzeń zawiera już dane. Środowisko rozpoczyna ściąganie danych od początku okresu przechowywania komunikatów źródła zdarzeń. Nie można zastąpić tego domyślnego przetwarzania. Możesz zaangażować dławienie. Ograniczanie może zająć trochę czasu, aby nadrobić zaległości, ponieważ pochłonie dane historyczne.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Zalecane rozdzielczości dla dużego połku wstępnego spożycia

Aby naprawić opóźnienie:

1. Zwiększ pojemność jednostki SKU do maksymalnej dozwolonej wartości (10, w tym przypadku). Po zwiększeniu pojemności proces transferu danych przychodzących zaczyna nadrabiać zaległości znacznie szybciej. Opłata jest naliczana za zwiększoną pojemność. Aby zwizualizować, jak szybko nadrabiasz zaległości, możesz wyświetlić wykres dostępności w [Eksploratorze Usługi Time Series Insights](https://insights.timeseries.azure.com).

2. Gdy opóźnienie zostanie przechwycone, zmniejsz pojemność jednostki SKU do normalnej szybkości transferu transferu przychodzącego.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problem: dane były wyświetlane wcześniej, ale nie są już wyświetlane

TSI nie pochłonie już danych, ale zdarzenia nadal są przesyłane strumieniowo do usługi Iot Hub lub Centrum zdarzeń

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>Przyczyna A: klucz dostępu do koncentratora został ponownie wygenerowany, a środowisko wymaga aktualizacji

Ten problem występuje, gdy klucz podany podczas tworzenia źródła zdarzeń nie jest już prawidłowy. Zostaną wyświetleń danych telemetrycznych w centrum, ale nie przychodzące odebrane wiadomości w aplikacji Time Series Insights. Jeśli nie masz pewności, czy klucz został ponownie wygenerowany, możesz przeszukać dziennik aktywności centrum zdarzeń pod kątem "Utwórz lub zaktualizuj reguły autoryzacji obszaru nazw" lub wyszukaj "Utwórz lub zaktualizuj zasób IotHub" dla centrum IoT Hub.

Aby zaktualizować środowisko usługi Time Series Insights za pomocą nowego klucza, otwórz zasób centrum w witrynie Azure portal i skopiuj nowy klucz. Przejdź do zasobu TSI i kliknij źródła zdarzeń. 

   [![Klucz aktualizacji.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Wybierz źródło zdarzeń, z których zatrzymano pozyskiwanie, wklej nowy klucz i kliknij przycisk Zapisz.

   [![Klucz aktualizacji.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: ustawienie nazwy właściwości sygnatury czasowej źródła zdarzeń nie działa

Upewnij się, że nazwa i wartość właściwości sygnatury czasowej są zgodne z następującymi regułami:

* W nazwie właściwości sygnatury czasowej jest rozróżniana wielkość liter.
* Wartość właściwości sygnatury czasowej, która pochodzi ze źródła zdarzenia jako ciąg JSON powinien mieć format _yyyy-MM-ddTHH:mm:ss. FFFFFK_. Przykładem jest **2008-04-12T12:53Z**.

Najprostszym sposobem, aby upewnić się, że nazwa właściwości sygnatury czasowej jest przechwytywany i działa poprawnie jest użycieLornazwis analizy szeregów czasowych. W Eksploratorze usługi Time Series Insights za pomocą wykresu wybierz okres czasu po wprowadzeniu nazwy właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz opcję **Eksploruj wydarzenia.**

Pierwszy nagłówek kolumny powinien być nazwą właściwości sygnatury czasowej. Obok pojawi się słowo **Sygnatura czasowa** **($ts).**

Następujące wartości nie będą wyświetlane:

- *(abc)*: Wskazuje, że usługa Time Series Insights odczytuje wartości danych jako ciągi.
- *Ikona kalendarza*: Wskazuje, że usługa Time Series Insights odczytuje wartość danych jako *datetime*.
- *#*: Wskazuje, że usługa Time Series Insights odczytuje wartości danych jako całkowitą.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [tym, jak ograniczyć opóźnienia w usłudze Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Dowiedz [się, jak skalować środowisko usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).