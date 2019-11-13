---
title: Diagnozowanie, rozwiązywanie problemów i rozwiązywanie problemów — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób diagnozowania i rozwiązywania typowych problemów w środowisku Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: ca38ebb015552042591fb4cc6b7edfe99527e79f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007063"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnozowanie i rozwiązywanie problemów w środowisku Time Series Insights

W tym artykule opisano niektóre problemy, które mogą wystąpić w środowisku Azure Time Series Insights. Artykuł zawiera potencjalne przyczyny i rozwiązania do rozwiązania.

## <a name="video"></a>Połączenia wideo

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Dowiedz się więcej na temat typowych wyzwań i rozwiązań związanych z Time Series Insights klientami.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: nie są wyświetlane żadne dane

Żadne dane w [eksploratorze Azure Time Series Insights](https://insights.timeseries.azure.com) nie mogą występować z kilku typowych przyczyn:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Przyczyna: dane źródłowe zdarzenia nie są w formacie JSON

Azure Time Series Insights obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Przyczyna B: klucz źródła zdarzenia nie ma wymaganego uprawnienia

* W przypadku usługi IoT Hub w usłudze Azure IoT Hub należy podać klucz z uprawnieniami do **łączenia usług** . **Iothubowner** lub zasady **usługi** będą działały, ponieważ oba mają uprawnienia do **łączenia usług** .

   [uprawnienia ![IoT Hub usługi Connect](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* W przypadku centrum zdarzeń w usłudze Azure Event Hubs należy podać klucz, który ma uprawnienia do **nasłuchiwania** . Każda z zasad **odczytu** lub **zarządzania** będzie działała, ponieważ obie osoby mają uprawnienia do **nasłuchiwania** .

   [![uprawnień nasłuchiwania centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Przyczyna C: podana Grupa odbiorców nie ma wyłącznej Time Series Insights

Po zarejestrowaniu Centrum IoT Hub lub centrum zdarzeń należy ustawić grupę odbiorców, która ma być używana do odczytywania danych. *Nie można udostępnić*tej grupy odbiorców. Jeśli grupa konsumentów jest udostępniona, podstawowe Centrum IoT lub centrum zdarzeń automatycznie i losowo rozłącza jednego z nich. Podaj unikatową grupę odbiorców, w której ma zostać odczytana Time Series Insights.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: wyświetlane są pewne dane, ale brakuje danych

Gdy dane pojawiają się tylko częściowo i pojawiają się, że dane są opóźnione, należy rozważyć kilka możliwości.

### <a name="cause-a-your-environment-is-being-throttled"></a>Przyczyna: środowisko jest ograniczane

Ograniczanie jest typowym problemem, gdy środowiska są inicjowane po utworzeniu źródła zdarzeń zawierającego dane. Usługa Azure IoT Hub i usługi Azure Events Hub przechowują dane przez maksymalnie siedem dni. Time Series Insights zawsze rozpoczynać się od najstarszego zdarzenia w źródle zdarzeń (pierwszy na początku, w pierwszej kolejności lub *FIFO*).

Jeśli na przykład w źródle zdarzeń znajdują się zdarzenia 5 000 000 w przypadku łączenia się ze środowiskiem S1 (Single-Unit Time Series Insights), Time Series Insights odczytuje około 1 000 000 zdarzeń dziennie. Może wyglądać tak, jak Time Series Insights występuje pięć dni opóźnienia. Jednak dzieje się tak, że środowisko jest ograniczane.

Jeśli masz stare zdarzenia w źródle zdarzeń, możesz przyejść do ograniczania na jeden z dwóch sposobów:

- Zmień limity przechowywania źródła zdarzeń, aby pomóc w usunięciu starych zdarzeń, które nie mają być wyświetlane w Time Series Insights.
- Zapewnij większy rozmiar środowiska (liczbę jednostek), aby zwiększyć przepływność starych zdarzeń. Korzystając z powyższego przykładu, jeśli zwiększy się to samo środowisko S1 do pięciu jednostek przez jeden dzień, środowisko powinno przechwycić w ciągu dnia. Jeśli produkcja zdarzeń o stałym stanie wynosi 1 000 000 lub mniej zdarzeń dziennie, można zmniejszyć pojemność zdarzenia do jednej jednostki po wychwyceniu.

Limit ograniczania jest wymuszany na podstawie typu i pojemności jednostki SKU środowiska. Wszystkie źródła zdarzeń w środowisku współużytkują tę pojemność. Jeśli źródło zdarzeń dla Centrum IoT Hub lub centrum zdarzeń wypycha dane poza wymuszonymi limitami, zobaczysz ograniczenie i opóźnienie.

Na poniższej ilustracji przedstawiono środowisko Time Series Insights, które ma jednostkę SKU S1 i pojemność 3. Może on przypadać na 3 000 000 zdarzeń dziennie.

[Bieżąca pojemność jednostki SKU środowiska ![](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Załóżmy na przykład, że środowisko przejmuje komunikaty z centrum zdarzeń. Dzienny współczynnik transferu danych przychodzących to ~ 67 000 komunikatów. Ta częstotliwość tłumaczy na około 46 komunikatów co minutę. 

* Jeśli każdy komunikat centrum zdarzeń zostanie spłaszczony do pojedynczego zdarzenia Time Series Insights, ograniczenie nie wystąpi. 
* Jeśli każdy komunikat centrum zdarzeń zostanie spłaszczony do 100 zdarzeń Time Series Insights, zdarzenia 4 600 należy pozyskać co minutę. 

Środowisko jednostki SKU S1 o pojemności 3 może przypadać tylko 2 100 zdarzeń co minutę (1 000 000 zdarzeń dziennie = 700 zdarzeń na minutę w trzech jednostkach = 2 100 zdarzeń na minutę). 

Aby zapoznać się z ogólnym zrozumieniem działania logiki spłaszczania, zobacz [obsługiwane kształty JSON](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Zalecane rozwiązania do nadmiernego ograniczania przepustowości

Aby rozwiązać zwłokę, Zwiększ pojemność jednostki SKU środowiska. Aby uzyskać więcej informacji, zobacz [skalowanie środowiska Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Przyczyna B: początkowe pozyskiwanie danych historycznych spowalnia ruch przychodzący

Jeśli połączysz istniejące źródło zdarzeń, prawdopodobnie Centrum IoT lub centrum zdarzeń zawiera już dane. Środowisko rozpoczyna ściąganie danych od początku okresu przechowywania komunikatów źródła zdarzeń. Nie można zastąpić tego domyślnego przetwarzania. Ograniczenie można obsłużyć. Ograniczanie wydajności może potrwać trochę czasu, aby można było wychwycić dane historyczne.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Zalecane rozwiązania dla dużych początkowych pozyskiwania

Aby rozwiązać zwłokę:

1. Zwiększ pojemność jednostki SKU do maksymalnej dozwolonej wartości (10, w tym przypadku). Po zwiększeniu wydajności proces transferu danych przychodzących rozpocznie się znacznie szybciej. Opłata jest naliczana za zwiększoną pojemność. Aby wizualizować, jak szybko przechwytuje, możesz wyświetlić wykres dostępności w [Eksploratorze Time Series Insights](https://insights.timeseries.azure.com).

2. Gdy zwłoka zostanie przechwycona, Zmniejsz pojemność jednostki SKU do normalnej stawki za transfer danych przychodzących.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: ustawienie nazwy właściwości sygnatury czasowej źródła zdarzenia nie działa

Upewnij się, że nazwa właściwości sygnatury czasowej i wartość są zgodne z następującymi regułami:

* W nazwie właściwości sygnatury czasowej jest rozróżniana wielkość liter.
* Wartość właściwości timestamp, która pochodzi ze źródła zdarzenia jako ciąg JSON powinien mieć format _RRRR-MM-DDTgg: mm: SS. FFFFFFFK_. Przykładem jest **2008-04-12T12:53Z**.

Najprostszym sposobem, aby upewnić się, że nazwa właściwości sygnatury czasowej została przechwycona i działa prawidłowo, to użycie Eksploratora Time Series Insights. W Eksploratorze Time Series Insights przy użyciu wykresu Wybierz okres czasu po wprowadzeniu nazwy właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz opcję **Eksploruj zdarzenia** .

Pierwszy nagłówek kolumny powinien być nazwą właściwości sygnatury czasowej. Obok **sygnatury czasowej**wyrazów powinna zostać wyświetlona wartość **($TS)** .

Nie powinny być widoczne następujące wartości:

- *(ABC)* : wskazuje, że Time Series Insights odczytuje wartości danych jako ciągi.
- *Ikona kalendarza*: wskazuje, że Time Series Insights odczytuje wartość danych jako *datę i godzinę*.
- *#* : wskazuje, że Time Series Insights odczytuje wartości danych jako liczbę całkowitą.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [, jak ograniczyć opóźnienia w Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Dowiedz się [, jak skalować środowisko Time Series Insights](time-series-insights-how-to-scale-your-environment.md).