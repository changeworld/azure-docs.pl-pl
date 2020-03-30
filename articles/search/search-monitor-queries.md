---
title: Monitorowanie zapytań
titleSuffix: Azure Cognitive Search
description: Monitorowanie metryk zapytań pod kątem wydajności i przepływności. Zbieranie i analizowanie danych wejściowych ciągów zapytań w dziennikach diagnostycznych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462529"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorowanie żądań zapytań w usłudze Azure Cognitive Search

W tym artykule wyjaśniono, jak mierzyć wydajność i wolumin kwerend przy użyciu metryk i rejestrowania diagnostycznego. Wyjaśniono również, jak zbierać terminy wejściowe używane w zapytaniach - niezbędne informacje, gdy trzeba ocenić użyteczność i skuteczność korpusu wyszukiwania.

Dane historyczne, które są przesyłane do danych, są zachowywane przez 30 dni. W przypadku dłuższego przechowywania lub do raportowania danych operacyjnych i ciągów zapytań, należy włączyć [ustawienie diagnostyczne,](search-monitor-logs.md) które określa opcję magazynu dla utrwalania zarejestrowanych zdarzeń i metryk.

Warunki, które maksymalizują integralność pomiaru danych obejmują:

+ Użyj usługi podlegalnej do rozliczenia (usługi utworzonej w warstwie Podstawowa lub Standardowa). Bezpłatna usługa jest współdzielona przez wielu subskrybentów, co wprowadza pewną zmienność w miarę zmiany obciążeń.

+ Użyj pojedynczej repliki i partycji, jeśli to możliwe, aby utworzyć zamknięte i izolowane środowisko. Jeśli używasz wielu replik, metryki kwerendy są uśredniane w wielu węzłach, co może obniżyć dokładność wyników. Podobnie wiele partycji oznacza, że dane są podzielone, z potencjałem, że niektóre partycje mogą mieć różne dane, jeśli indeksowanie jest również w toku. Podczas dostrajania wydajności kwerendy pojedynczy węzeł i partycja zapewnia bardziej stabilne środowisko do testowania.

> [!Tip]
> Dzięki dodatkowemu kodowi po stronie klienta i usłudze Application Insights można również przechwytywać dane klikalności, aby uzyskać lepszy wgląd w to, co przyciąga zainteresowanie użytkowników aplikacji. Aby uzyskać więcej informacji, zobacz [Analiza ruchu wyszukuj](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Wolumin zapytania (QPS)

Wolumin jest mierzony jako **zapytania wyszukiwania na sekundę** (QPS), wbudowana metryka, która może być zgłaszana jako średnia, liczba, minimalna lub maksymalna wartość zapytań wykonywanych w ciągu jednej minuty. Jednominutowe interwały (TimeGrain = "PT1M") dla metryk jest ustalona w systemie.

Jest to typowe dla kwerend do wykonania w milisekundach, więc tylko kwerendy, które mierzą jako sekundy pojawią się w metrykach.

| Typ agregacji | Opis |
|------------------|-------------|
| Średnia | Średnia liczba sekund w ciągu minuty, podczas której wystąpiło wykonanie kwerendy.|
| Liczba | Liczba metryk emitowanych do dziennika w odstępie jednej minuty. |
| Maksimum | Największa liczba zapytań na sekundę zarejestrowana w ciągu minuty. |
| Minimalne | Najmniejsza liczba zapytań na sekundę zarejestrowana w ciągu minuty.  |
| Suma | Suma wszystkich zapytań wykonanych w ciągu minuty.  |

Na przykład w ciągu jednej minuty może mieć wzorzec tak: jedna sekunda wysokiego obciążenia, który jest maksymalna dla SearchQueriesPerSecond, a następnie 58 sekund średniego obciążenia i wreszcie jedną sekundę z tylko jedną kwerendą, która jest minimalna.

Inny przykład: jeśli węzeł emituje 100 metryk, gdzie wartość każdej metryki wynosi 40, a następnie "Count" wynosi 100, "Suma" wynosi 4000, "Średnia" wynosi 40, a "Max" to 40.

## <a name="query-performance"></a>Wydajność zapytań

Wydajność kwerend w całej usłudze jest mierzona jako opóźnienie wyszukiwania (jak długo trwa wykonywanie kwerendy) i ograniczone kwerendy, które zostały usunięte w wyniku rywalizacji o zasoby.

### <a name="search-latency"></a>Opóźnienie wyszukiwania

| Typ agregacji | Opóźnienie | 
|------------------|---------|
| Średnia | Średni czas trwania kwerendy w milisekundach. | 
| Liczba | Liczba metryk emitowanych do dziennika w odstępie jednej minuty. |
| Maksimum | Najdłużej działające zapytanie w przykładzie. | 
| Minimalne | Najkrótsza kwerenda uruchomiona w przykładzie.  | 
| Łącznie | Całkowity czas wykonywania wszystkich zapytań w próbce, wykonywanie w ramach interwału (jedna minuta).  |

Rozważmy następujący przykład metryk **opóźnienia wyszukiwania:** próbkowano 86 zapytań, ze średnim czasem trwania 23,26 milisekund. Co najmniej 0 wskazuje, że niektóre zapytania zostały usunięte. Najdłuższa działająca kwerenda trwała 1000 milisekund. Całkowity czas wykonania wynosił 2 sekundy.

![Agregacje opóźnień](./media/search-monitor-usage/metrics-latency.png "Agregacje opóźnień")

### <a name="throttled-queries"></a>Ograniczone zapytania

Ograniczone zapytania odnosi się do kwerend, które są usuwane zamiast procesu. W większości przypadków ograniczanie jest normalną częścią uruchamiania usługi.  Niekoniecznie jest to wskazówka, że coś jest nie tak.

Ograniczanie występuje, gdy liczba aktualnie przetwarzanych żądań przekracza dostępne zasoby. Może pojawić się wzrost ograniczone żądania, gdy replika jest wyjęty z obrotu lub podczas indeksowania. Żądania kwerendy i indeksowania są obsługiwane przez ten sam zestaw zasobów.

Usługa określa, czy żądania mają być upuszczane na podstawie zużycia zasobów. Procent zasobów zużywanych w pamięci, procesorze CPU i we/wy dysku są uśredniane w określonym czasie. Jeśli ta wartość procentowa przekracza próg, wszystkie żądania do indeksu są ograniczane, dopóki liczba żądań zostanie zmniejszona. 

W zależności od klienta, ograniczone żądanie może być wskazane w następujących sposobów:

+ Usługa zwraca błąd "Wysyłasz zbyt wiele żądań. Spróbuj ponownie później”. 
+ Usługa zwraca kod błędu 503 wskazujący, że usługa jest obecnie niedostępna. 
+ Jeśli korzystasz z portalu (na przykład Eksplorator wyszukiwania), kwerenda jest odrzucana w trybie cichym i należy ponownie kliknąć przycisk Wyszukaj.

Aby potwierdzić ograniczone zapytania, należy użyć **metryki kwerend wyszukiwania ograniczonego.** Możesz eksplorować metryki w portalu lub utworzyć metrykę alertu zgodnie z opisem w tym artykule. W przypadku kwerend, które zostały usunięte w interwale próbkowania, użyj *sumy,* aby uzyskać procent zapytań, które nie zostały wykonane.

| Typ agregacji | Ograniczanie przepływności |
|------------------|-----------|
| Średnia | Procent zapytań porzuconych w interwale. |
| Liczba | Liczba metryk emitowanych do dziennika w odstępie jednej minuty. |
| Maksimum | Procent zapytań porzuconych w interwale.|
| Minimalne | Procent zapytań porzuconych w interwale. |
| Łącznie | Procent zapytań porzuconych w interwale. |

W przypadku **procentu kwerend wyszukiwania ograniczonego**, minimalna, maksymalna, średnia i całkowita, wszystkie mają tę samą wartość: procent zapytań wyszukiwania, które zostały ograniczone, z całkowitej liczby zapytań wyszukiwania w ciągu jednej minuty.

Na poniższym zrzucie ekranu pierwsza liczba to liczba (lub liczba metryk wysłanych do dziennika). Dodatkowe agregacje, które pojawiają się u góry lub po najechaniu kursorem na metrykę, obejmują średnią, maksymalną i sumę. W tym przykładzie nie żądania zostały usunięte.

![Agregacje ograniczone](./media/search-monitor-usage/metrics-throttle.png "Agregacje ograniczone")

## <a name="explore-metrics-in-the-portal"></a>Eksplorowanie danych w portalu

Aby uzyskać szybkie spojrzenie na bieżące liczby, na karcie **Monitorowanie** na stronie Przegląd usługi są wyświetlane trzy metryki (**opóźnienie wyszukiwania**, **zapytania wyszukiwania na sekundę (na jednostkę wyszukiwania),** **ograniczona wartość procentowa zapytań wyszukiwania)** w ustalonych odstępach czasu mierzonych w godzinach, dniach i tygodniach z opcją zmiany typu agregacji.

Aby uzyskać głębszą eksplorację, otwórz eksploratora metryk z menu **Monitorowanie,** aby można było warstwować, powiększać i wizualizować dane w celu eksplorowania trendów lub anomalii. Dowiedz się więcej o Eksploratorze metryk, wykonując ten [samouczek dotyczący tworzenia wykresu metryk](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer).

1. W sekcji Monitorowanie wybierz **metryki,** aby otworzyć Eksploratora metryk z zakresem ustawionym na usługę wyszukiwania.

1. W obszarze Metryka wybierz jedną z listy rozwijanej i przejrzyj listę dostępnych agregacji dla preferowanego typu. Agregacja definiuje sposób pobierania próbek zebranych wartości w każdym przedziale czasu.

   ![Eksplorator metryk dla metryki QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Eksplorator metryk dla metryki QPS")

1. W prawym górnym rogu ustaw przedział czasu.

1. Wybierz wizualizację. Wartością domyślną jest wykres liniowy.

1. Warstwa dodatkowych agregacji, wybierając **pozycję Dodaj metrykę** i wybierając różne agregacje.

1. Powiększ obszar zainteresowania na wykresie liniowym. Umieść wskaźnik myszy na początku obszaru, kliknij i przytrzymaj lewy przycisk myszy, przeciągnij na drugą stronę obszaru i zwolnij przycisk. Wykres powiększy ten zakres czasu.

## <a name="identify-strings-used-in-queries"></a>Identyfikowanie ciągów używanych w kwerendach

Po włączeniu rejestrowania diagnostycznego system przechwytuje żądania zapytań w tabeli **AzureDiagnostics.** Jako warunek wstępny musi być już włączone [rejestrowanie diagnostyczne,](search-monitor-logs.md)określając obszar roboczy analizy dzienników lub inną opcję magazynu.

1. W sekcji Monitorowanie wybierz **pozycję Dzienniki,** aby otworzyć puste okno kwerendy w usłudze Log Analytics.

1. Uruchom następujące wyrażenie, aby wyszukać operacje Query.Search, zwracając zestaw wyników tabelaryczne składający się z nazwy operacji, ciągu zapytania, indeksu, o który kwerenda i liczby znalezionych dokumentów. Ostatnie dwie instrukcje wykluczają ciągi zapytań składające się z pustego lub nieokreślonego wyszukiwania za pomocą przykładowego indeksu, który zmniejsza szum w wynikach.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Opcjonalnie ustaw filtr Kolumny na *Query_s,* aby przeszukiwać określoną składnię lub ciąg. Na przykład, można filtrować nad *jest równa* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Zarejestrowane ciągi zapytań](./media/search-monitor-usage/log-query-strings.png "Zarejestrowane ciągi zapytań")

Chociaż ta technika działa w przypadku badania ad hoc, tworzenie raportu umożliwia konsolidację i prezentowanie ciągów zapytań w układzie bardziej sprzyjającym analizie.

## <a name="identify-long-running-queries"></a>Identyfikowanie długotrwałych zapytań

Dodaj kolumnę czas trwania, aby uzyskać liczby dla wszystkich zapytań, a nie tylko tych, które są pobierane jako metryka. Sortowanie tych danych pokazuje, które zapytania mają czas najdłuższy do wykonania.

1. W sekcji Monitorowanie wybierz **pozycję Dzienniki,** aby zbadać informacje dziennika.

1. Uruchom następującą kwerendę, aby zwrócić kwerendy, posortowane według czasu trwania w milisekundach. Najdłużej działające zapytania znajdują się u góry.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Sortowanie zapytań według czasu trwania](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Sortowanie zapytań według czasu trwania")

## <a name="create-a-metric-alert"></a>Tworzenie alertu metrycznego

Alert metryki ustanawia próg, przy którym otrzymasz powiadomienie lub wyzwolisz z góry akcję naprawcze. 

W przypadku usługi wyszukiwania często tworzy się alert metryki dla opóźnień wyszukiwania i ograniczone zapytania. Jeśli wiesz, kiedy kwerendy są odrzucane, możesz szukać środków zaradczych, które zmniejszają obciążenie lub zwiększają pojemność. Na przykład jeśli ograniczone kwerendy zwiększyć podczas indeksowania, można odroczyć go, dopóki działanie kwerendy ustępuje.

Podczas przesuwania limitów określonej konfiguracji repliki partycji pomocne jest również konfigurowanie alertów dla progów woluminów zapytań (QPS).

1. W sekcji Monitorowanie wybierz pozycję **Alerty,** a następnie kliknij pozycję **+ Nowa reguła alertu**. Upewnij się, że usługa wyszukiwania jest zaznaczona jako zasób.

1. W obszarze Warunek kliknij pozycję **Dodaj**.

1. Skonfiguruj logikę sygnału. W przypadku typu sygnału wybierz **metryki,** a następnie wybierz sygnał.

1. Po wybraniu sygnału można użyć wykresu do wizualizacji danych historycznych w celu podjęcia świadomej decyzji o sposobie konfigurowania warunków.

1. Następnie przewiń w dół do logiki alertu. W przypadku weryfikacji koncepcji można określić sztucznie niską wartość do celów testowych.

   ![Logika alertu](./media/search-monitor-usage/alert-logic-qps.png "Logika alertu")

1. Następnie określ lub utwórz grupę akcji. Jest to odpowiedź do wywołania po osiągnięciu progu. Może to być powiadomienie wypychanie lub automatyczna odpowiedź.

1. Ostatnio określ szczegóły alertu. Nazwij i opisz alert, przypisz wartość ważności i określ, czy reguła ma być tworzęona w stanie włączonym czy wyłączonym.

   ![Szczegóły alertu](./media/search-monitor-usage/alert-details.png "Szczegóły alertu")

Jeśli określono powiadomienie e-mail, otrzymasz wiadomość e-mail z "Microsoft Azure" z wierszem `<your rule name>`tematu "Azure: Activated Severity: 3 ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, zapoznaj się z podstawami monitorowania usługi wyszukiwania, aby dowiedzieć się więcej o pełnym zakresie możliwości nadzoru.

> [!div class="nextstepaction"]
> [Monitorowanie operacji i aktywności w usłudze Azure Cognitive Search](search-monitor-usage.md)