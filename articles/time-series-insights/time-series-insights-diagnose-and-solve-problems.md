---
title: Diagnozowanie i rozwiązywanie problemów w usłudze Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób diagnozowania, rozwiązywanie problemów i rozwiązywania typowych problemów, które mogą wystąpić w środowisku Azure czas serii Insights.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: f0c1b8aa99e9ac9c73f57af17490dd3a465a9cac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnozowanie i rozwiązywanie problemów w środowisku Insights serii czasu

## <a name="problem-1-no-data-is-shown"></a>Problem 1: Dane nie są wyświetlane.
Istnieje kilka przyczyn typowe przyczyny mogą nie być widoczne dane w [Eksplorator Insights serii czasu Azure](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Możliwa przyczyna A: zdarzenia źródła danych nie jest w formacie JSON
Azure czas serii Insights obsługuje tylko dane JSON. Przykłady JSON dla [kształtów JSON obsługiwany](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Klucz źródła zdarzenia B: możliwą przyczyną jest brak wymaganych uprawnień
* Centrum IoT należy podać klucz, który ma **usługa połączyć** uprawnienia.

   ![Uprawnienia do połączenia z usługi Centrum IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Jak pokazano na poprzedniej ilustracji, albo zasad **iothubowner** i **usługi** będzie działać, ponieważ mają **usługa połączyć** uprawnienia.
   
* Do Centrum zdarzeń, musisz podać klucz, który ma **nasłuchiwania** uprawnienia.

   ![Uprawnienie nasłuchiwania Centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Jak pokazano na poprzedniej ilustracji, albo zasad **odczytu** i **zarządzanie** będzie działać, ponieważ mają **nasłuchiwania** uprawnienia.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Możliwe, że C: podano grupy konsumentów nie jest zarezerwowana do szczegółowych danych serii czasu
Podczas rejestracji am Centrum IoT i Centrum zdarzeń można określić grupy odbiorców, które mają być używane do odczytywania danych. Ta grupa odbiorców musi **nie** można udostępniać. Jeśli grupy odbiorców jest udostępniana, podstawowe Centrum zdarzeń automatycznie rozłącza jeden czytników losowo. Podaj grupy odbiorców unikatowy dla czasu serii wgląd do odczytu.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problem 2: Niektóre dane są wyświetlane, ale brakuje
Zostaną wyświetlone dane częściowo, ale opóźnionych danych, istnieje kilka możliwości należy wziąć pod uwagę:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Możliwa przyczyna A: środowiska jest pobieranie ograniczany.
Jest to powszechny problem, podczas przydzielania środowiskach po utworzeniu źródła zdarzenia z danymi.  Azure centra IoT i centrów zdarzeń maksymalnie danych siedem dni.  Zawsze TSI rozpocznie się od najstarszych zdarzenia (FIFO) w źródle zdarzeń.  Dlatego jeśli masz pięć milionów zdarzenia w źródle zdarzeń podczas łączenia z S1 środowiska TSI jednostkowy, TSI odczyta około miliona zdarzeń na dzień.  To może się wydawać, wyglądać tak, jakby TSI występują na pierwszy rzut oka pięć dni opóźnienia.  Faktycznie wykonywane jest środowisko jest ograniczane.  Jeśli masz starych zdarzeń w źródle zdarzeń mogą ustalić jeden z dwóch sposobów:

- Zmiana źródła zdarzeń przechowywania limity, aby ułatwić usuwanie starych zdarzeń, które nie mają być wyświetlane w TSI
- Udostępnić większy rozmiar środowiska (pod względem liczby jednostek) w celu zwiększenia przepływności starych zdarzeń.  Środowisko przy użyciu powyższego przykładu po zwiększeniu tym samym środowisku S1 pięciu sztuk przez jeden dzień, powinien przechwycenie się teraz w ciągu dnia.  W przypadku 1M lub mniej zdarzeń/dzień produkcyjnego zdarzeń stanie stabilności następnie można zmniejszyć pojemność zdarzeń wstecz do jednej jednostki po przechwyciło w.  

Limit ograniczania są wymuszane na podstawie typu jednostki SKU w środowisku i pojemności. Wszystkie źródła zdarzeń w środowisku udostępnianie tej pojemności. Jeśli źródło zdarzenia z Centrum IoT i Centrum zdarzeń jest przekazywanie danych poza granicami wymuszone, zobacz ograniczania przepustowości i opóźnienia.

Na poniższym diagramie przedstawiono środowisku czasu serii Insights SKU S1 i pojemności 3. Może on ruch przychodzący 3 miliony zdarzeń na dzień.

![Obecna pojemność jednostki SKU środowiska](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Na przykład załóżmy, że środowisko jest wprowadzania komunikaty z Centrum zdarzeń. Zwróć prędkości wejściowej pokazano na poniższym diagramie:

![Transfer danych przychodzących przykład szybkość Centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Jak pokazano na diagramie, codzienne prędkości wejściowej jest ~ 67,000 wiadomości. Ta stawka tłumaczy około 46 wiadomości co minutę. Jeśli każdy komunikat Centrum zdarzeń jest jako spłaszczone do jednego zdarzenia Insights serii czasu, to środowisko widzi nie ograniczania. Jeśli każdy komunikat Centrum zdarzeń jest jako spłaszczone do 100 zdarzeń Insights serii czasu, następnie 4,600 zdarzenia powinny być pozyskanych co minutę. Środowisku S1 SKU o pojemności 3 można tylko 2100 zdarzeń wejściowych co minutę (1 miliona zdarzeń na dzień = 700 zdarzenia na minutę na poziomie jednostki 3 = 2100 zdarzenia na minutę). W związku z tym zostanie wyświetlony opóźnienia z powodu ograniczania. 

Ogólny zrozumieć sposób działania spłaszczania logiki, zobacz [kształtów JSON obsługiwany](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Procedura ograniczania nadmiernego zalecane rozwiązanie
Aby usunąć opóźnienie, zwiększyć pojemność jednostki SKU środowiska. Aby uzyskać więcej informacji, zobacz [sposób skalowania środowiska czasu serii Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Możliwa przyczyna początkowej B: wprowadzanie danych historycznych powoduje powolne wejściowych
Jeśli łączysz istniejące źródło zdarzenia jest prawdopodobne, że Centrum IoT Hub lub zdarzenie zawiera już dane w nim. Środowisko rozpoczyna ściąganie danych z początku okres przechowywania komunikatów źródło zdarzenia.

To zachowanie jest zachowaniem domyślnym i nie może zostać zastąpiona. Można Uwzględnij, ograniczania i może upłynąć trochę czasu, aby odnaleźć wprowadzania danych historycznych.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Kroki zalecane rozwiązanie dużych wprowadzanie początkowej
Aby usunąć opóźnienie, należy wykonać następujące czynności:
1. Zwiększ pojemność jednostki SKU na maksymalną dozwoloną wartość (10, w tym przypadku). Po zwiększeniu możliwości uruchamiania procesu wejściowych Przechwytywanie się znacznie szybciej. Można zwizualizować, jak szybko jest przechwytywanie za pomocą wykresu dostępności w [Eksplorator czasu serii Insights](https://insights.timeseries.azure.com). Naliczane są opłaty za zwiększenia pojemności.
2. Po zawiera opóźnienie zmniejszyć pojemność jednostki SKU do szybkość normalne wejściowych.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem z 3: Moje źródła zdarzeń *nazwa właściwości sygnatury czasowej* ustawienie nie działa
Upewnij się, że nazw i wartości są zgodne z następujących reguł:
* Nazwa właściwości sygnatury czasowej jest _z uwzględnieniem wielkości liter_.
* Wartość właściwości sygnatury czasowej pochodzące ze źródła zdarzenia w postaci ciągu JSON, powinien mieć format _RRRR-MM-Ddtgg. FFFFFFFK_. Na przykład taki ciąg "2008-04-12T12:53Z".

Najprostszym sposobem upewnij się, że Twoje *nazwa właściwości sygnatury czasowej* są przechwytywane i działa poprawnie za pomocą Eksploratora TSI.  W Eksploratorze TSI za pomocą wykresu, wybierz okres czasu po podane *nazwa właściwości sygnatury czasowej*.  Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz pozycję *Eksploruj zdarzenia* opcji.  Pierwszy nagłówek kolumny powinien być z *nazwa właściwości sygnatury czasowej* i powinien mieć *($ts)* obok wyraz *sygnatury czasowej*, zamiast:
- *(abc)* , który może wskazywać TSI odczytywania wartości danych jako ciągi
- *Ikona kalendarza*, który może wskazywać TSI odczytuje wartość danych, jak *daty i godziny*
- *#*, które wskazują TSI odczytuje wartości danych jako liczba całkowita


## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać dodatkową pomoc, należy rozpocząć konwersację na [MSDN forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) lub [przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Można również użyć [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) opcji z pomocą techniczną.
