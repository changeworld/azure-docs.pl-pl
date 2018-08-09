---
title: Diagnozowanie i rozwiązywanie problemów w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowania, rozwiązywanie problemów i rozwiązywanie typowych problemów, które mogą wystąpić w danym środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: b05b824d8d35351030ca466566f14e4249d4b99d
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626624"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnozowanie i rozwiązywanie problemów w danym środowisku usługi Time Series Insights

## <a name="problem-1-no-data-is-shown"></a>Problem 1: Brak danych jest wyświetlany.
Istnieje kilka przyczyn typowych, dlaczego mogą nie być wyświetlane dane w [Azure Eksplorator usługi Time Series Insights](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Możliwa przyczyna A: zdarzenia źródła danych nie jest w formacie JSON
Usługa Azure Time Series Insights obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Klucz źródła zdarzeń B: możliwą przyczyną jest brak wymaganych uprawnień
* Dla usługi IoT Hub, musisz podać klucz, który ma **połączenie z usługą** uprawnień.

   ![Uprawnienia do połączenia z usługi IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Pokazany na wcześniejszej ilustracji, albo zasady **iothubowner** i **usługi** będzie działać, ponieważ mają **połączenie z usługą** uprawnień.
   
* Centrum zdarzeń, musisz podać klucz, który ma **nasłuchiwania** uprawnień.

   ![Uprawnienie nasłuchiwania Centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Pokazany na wcześniejszej ilustracji, albo zasady **odczytu** i **zarządzanie** będzie działać, ponieważ mają **nasłuchiwania** uprawnień.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Możliwe, że C: Podana grupa odbiorców, nie jest dostępna wyłącznie dla usługi Time Series Insights
Podczas rejestracji am usługi IoT Hub lub Centrum zdarzeń można określić grupy odbiorców, które mają być używane do odczytywania danych. Ta grupa odbiorców musi **nie** można udostępniać. Jeśli grupa odbiorców jest udostępniony, podstawowe Centrum zdarzeń automatycznie Odłącza jeden czytników losowo. Podaj grupy odbiorców unikatowy dla usługi Time Series Insights do odczytu.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problem 2: Niektóre dane są wyświetlane, ale brakuje
Gdy dane można wyświetlić częściowo, ale opóźnionych dane, istnieją należy wziąć pod uwagę kilka możliwości:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Odp.: środowisko z możliwych przyczyn jest ograniczany
Jest to powszechny problem, gdy środowiska są aprowizowane po utworzeniu źródła zdarzeń z danymi.  Usługi Azure IoT Hub i centrów zdarzeń maksymalnie danych siedem dni.  Usługa TSI Przesyła zawsze rozpocznie od najstarszych zdarzeń (FIFO) w źródle zdarzenia.  Dlatego jeśli masz pięć milionów zdarzeń w źródle zdarzenia podczas łączenia z S1 środowiska TSI pojedynczej jednostki, TSI odczyta około 1 milion zdarzeń dziennie.  To może pojawić się wyglądać tak, jakby TSI występuje pięć dni czas oczekiwania na pierwszy rzut oka.  Co naprawdę dzieje się to środowisko jest ograniczane.  Jeśli masz stare zdarzenia w źródle zdarzenia może zbliżać się jeden z dwóch sposobów:

- Zmień limity przechowywania źródło zdarzeń, aby pomóc usunąć stare zdarzenia, które nie mają być wyświetlane w usłudze TSI
- Aprowizuj większy rozmiar środowiska (pod względem liczby jednostek) w celu zwiększenia przepływności starych zdarzeń.  W powyższym przykładzie, korzystając z zwiększenie tym samym środowisku S1 do pięciu jednostek przez jeden dzień, środowisko należy — wyrównywanie do chwili w ciągu dnia.  Jeśli w środowisku produkcyjnym zdarzenie stanie stabilności jest 1 MB lub mniej zdarzeń na dzień, następnie można zmniejszyć pojemność zdarzeń wróci ponownie do jednej jednostki po złapał w.  

Limit ograniczania są wymuszane na podstawie typu jednostki SKU środowiska i pojemności. Wszystkie źródła zdarzeń w środowisku udostępnianie tej pojemności. Jeśli źródła zdarzeń dla usługi IoT Hub lub Centrum zdarzeń jest wypychania danych poza granicami wymuszone, zobaczysz ograniczania przepustowości i aktualizowany z opóźnieniem.

Na poniższym diagramie przedstawiono środowisko usługi Time Series Insights, które ma jednostkę SKU S1 i pojemność 3. Jest to możliwe ruch przychodzący 3 mln zdarzeń każdego dnia.

![Bieżąca pojemność jednostki SKU środowiska](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Na przykład załóżmy, że to środowisko jest wprowadzane komunikaty z Centrum zdarzeń. Zwróć szybkość transferu danych przychodzących, pokazano na poniższym diagramie:

![Przykład szybkość transferu danych przychodzących dla Centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Jak pokazano na diagramie, dziennych ruch przychodzący jest ~ 67,000 wiadomości. Ten kurs tłumaczy około 46 komunikatów na minutę. Jeśli każdy komunikat do Centrum zdarzeń jest spłaszczany do jednego zdarzenia usługi Time Series Insights, to środowisko zobaczy, bez ograniczania przepływności. Jeśli każdy komunikat do Centrum zdarzeń jest spłaszczany 100 zdarzeń usługi Time Series Insights, następnie 4,600 zdarzenia powinny być pozyskiwane co minutę. Środowisko S1 SKU usługi, które ma pojemność wynoszącą 3 można tylko 2100 zdarzeń przychodzących na minutę (1 mln zdarzeń każdego dnia = 700 zdarzeń na minutę na poziomie 3 jednostki = 2100 zdarzeń na minutę). W związku z tym zostanie wyświetlony opóźnienie z powodu dławienia. 

Ogólne zrozumieć sposób działania spłaszczania logiki, zobacz [kształty JSON obsługiwany](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Kroki zalecane rozwiązanie nadmierne ograniczania
Aby usunąć opóźnienie, należy zwiększyć pojemność jednostki SKU środowiska. Aby uzyskać więcej informacji, zobacz [sposób skalowania środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Powoduje wolnego transferu danych przychodzących z możliwych przyczyn B: początkowej pozyskiwania danych historycznych
Jeśli łączysz się z istniejącym źródłem zdarzeń, istnieje prawdopodobieństwo, że Twojego Centrum IoT Hub lub zdarzenie zawiera już dane w nim. Środowisko rozpoczyna ściągania danych z początkiem okresu przechowywania wiadomości źródła zdarzeń.

To zachowanie jest zachowaniem domyślnym i nie może być zastąpiona. Angażowanie, ograniczania i może potrwać chwilę, aby uzyskać informacje na historyczne dane wprowadzane.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Zalecane rozwiązanie kroki dużej początkowy pozyskiwania
Aby usunąć opóźnienie, wykonaj następujące czynności:
1. Zwiększ możliwości SKU maksymalną dozwoloną wartość (10, w tym przypadku). Po jest zwiększenie pojemności, rozpocznie się proces transferu danych przychodzących, przechwytywanie o wiele szybciej. Można zwizualizować, jak szybko są Przechwytywanie poprzez wykres dostępności w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com). Opłaty są naliczane za zwiększenia pojemności.
2. Po zawiera opóźnienie zmniejszyć pojemność jednostki SKU powrót do normalnego transfer przychodzący.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem 3: Moje źródła zdarzeń *nazwie właściwości sygnatury czasowej* ustawienie nie działa
Upewnij się, że z nazwą i wartością są zgodne z następującymi zasadami:
* Nazwa właściwości sygnatury czasowej jest _liter_.
* Wartość właściwości sygnatury czasowej, która pochodzi ze źródła zdarzenia użytkownika jako ciąg JSON powinien mieć format _RRRR-MM-Ddtgg. FFFFFFFK_. Na przykład taki ciąg "2008-04-12T12:53Z".

Najprostszym sposobem zapewnienia, że Twoje *nazwie właściwości sygnatury czasowej* są przechwytywane i działa poprawnie przy użyciu Eksploratora usługi TSI.  W Eksploratorze TSI, za pomocą wykresu, wybierz okres czasu, po podane *nazwie właściwości sygnatury czasowej*.  Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz *Eksploruj zdarzenia* opcji.  Pierwszy nagłówek kolumny powinna być Twoje *nazwie właściwości sygnatury czasowej* i powinien mieć *($ts)* obok wyraz *sygnatura czasowa*, zamiast:
- *(abc)* , który będzie wskazywać TSI odczytuje wartości danych jako ciągi
- *Ikona kalendarza*, który będzie wskazywać TSI odczytuje wartości danych jako *daty/godziny*
- *#*, który będzie wskazywać TSI odczytuje wartości danych jako liczba całkowita


## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać dodatkową pomoc, Rozpocznij konwersację na [forum MSDN dotyczącym](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Można również użyć [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) opcji asystowanej pomocy technicznej.
