---
title: Monitoruj aplikacje języka Python za pomocą Azure Monitor | Microsoft Docs
description: Zawiera instrukcje dotyczące szkieletu OpenCensus Python z Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294013"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurowanie Azure Monitor dla aplikacji języka Python

Azure Monitor obsługuje śledzenie rozproszone, zbieranie metryk i rejestrowanie aplikacji języka Python poprzez integrację z usługą [OpenCensus](https://opencensus.io). W tym artykule przedstawiono krok po kroku przez proces konfigurowania OpenCensus dla języka Python i uzyskiwania danych monitorowania do Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczna jest subskrypcja platformy Azure.
- Środowisko Python powinno być zainstalowane, w tym artykule jest stosowana [3.7.0 języka Python](https://www.python.org/downloads/), chociaż wcześniejsze wersje będą prawdopodobnie działać z drobnymi korektami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Utwórz zasób Application Insights w Azure Monitor

Najpierw należy utworzyć zasób Application Insights w Azure Monitor, co spowoduje wygenerowanie klucza Instrumentacji (iKey). IKey jest następnie używany do konfigurowania zestawu SDK OpenCensus w celu wysyłania danych telemetrycznych do Azure Monitor.

1. Wybierz pozycję **Utwórz zasób** > **Narzędzia deweloperskie** > **Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/opencensus-python/0001-create-resource.png)

   Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów hostującej dane usługi App Insights |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij przycisk **Utwórz**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentacja przy użyciu zestawu OpenCensus Python SDK dla Azure Monitor

1. Zainstaluj OpenCensus Azure Monitor eksportu:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` zakłada, że masz zmienną środowiskową PATH ustawioną dla instalacji języka Python. Jeśli nie skonfigurowano tego elementu, należy podać pełną ścieżkę do katalogu, w którym znajduje się plik wykonywalny języka Python, co spowoduje następujące polecenie: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Zestaw SDK wykorzystuje trzy Azure Monitor eksportujący do wysyłania różnych typów telemetrii do Azure Monitor: śledzenia, metryk i dzienników. Zapoznaj się z [omówieniem platformy danych,](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) Aby uzyskać więcej informacji na temat tych różnych typów. Postępuj zgodnie z poniższymi instrukcjami, aby dowiedzieć się, jak wysyłać te różne typy za pośrednictwem trzech eksporterów.

### <a name="trace"></a>Ślad

1. Najpierw wygenerujmy pewne dane śledzenia lokalnie. W bezczynnym języku Python lub wybranym przez Ciebie edytorze wpisz poniższy kod.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Uruchomienie kodu spowoduje wielokrotne wyświetlenie monitu o wprowadzenie wartości. W przypadku każdego wpisu wartość zostanie wydrukowany w powłoce, a odpowiadający jej element **SpanData** zostanie wygenerowany przez moduł OpenCensus języka Python. Projekt OpenCensus definiuje [_śledzenie jako drzewo zakresów_](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Chociaż jest to przydatne w celach demonstracyjnych, ostatecznie chcemy emitować `SpanData`, aby Azure Monitor. Zmodyfikuj swój kod z poprzedniego kroku w oparciu o następujący przykład kodu:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Teraz po uruchomieniu skryptu języka Python nadal powinien zostać wyświetlony monit o wprowadzenie wartości, ale teraz tylko wartość jest drukowana w powłoce. Utworzona `SpanData` zostanie wysłana do Azure Monitor. Emitowane dane zakresu można znaleźć w obszarze `dependencies`.

### <a name="metrics"></a>Metryki

1. Najpierw wygenerujmy pewne dane lokalnej metryki. Utworzymy prostą metrykę do śledzenia liczby prób wprowadzenia przez użytkownika.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Uruchomienie kodu będzie kilkukrotnie monitować o naciśnięcie klawisza ENTER. Zostanie utworzona Metryka umożliwiająca śledzenie liczby naciśniętych trafień. W przypadku każdego wpisu wartość zostanie zwiększona, a informacje o metrykach będą wyświetlane w konsoli, z bieżącą wartością oraz z bieżącą sygnaturą czasową, gdy Metryka została zaktualizowana.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Chociaż jest to przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane metryk do Azure Monitor. Zmodyfikuj swój kod z poprzedniego kroku w oparciu o następujący przykład kodu:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. Eksporter wyśle dane metryk do Azure Monitor w stałym interwale, domyślnie co 15 sekund. Śledzimy jedną metrykę, aby dane metryk, z dowolną wartością i sygnaturą czasową, która zawiera, zostaną wysłane każdy interwał. Dane można znaleźć w obszarze `customMetrics`.

### <a name="logs"></a>Dzienniki

1. Najpierw wygenerujmy niektóre lokalne dane dziennika.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  Kod będzie monitować o podanie wartości, która ma zostać wprowadzona. Wpis dziennika jest emitowany dla każdej wprowadzonej wartości zawierającej wymienioną wartość.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Chociaż jest to przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane metryk do Azure Monitor. Zmodyfikuj swój kod z poprzedniego kroku w oparciu o następujący przykład kodu:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. Eksporter wyśle dane dziennika do Azure Monitor. Dane można znaleźć w obszarze `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć stronę **omówienia** Application Insights w Azure Portal, aby wyświetlić szczegóły dotyczące aktualnie uruchomionej aplikacji. Wybierz pozycję **Live Metric Stream**.

   ![Zrzut ekranu przedstawiający okienko przegląd z aktywnym strumieńem metryk w czerwonym polu](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Przejdź z powrotem do strony **Przegląd** i wybierz pozycję **Mapa aplikacji** , aby uzyskać wizualny układ relacji zależności i Wywołaj chronometraż między składnikami aplikacji.

    ![Zrzut ekranu przedstawiający podstawową mapę aplikacji](./media/opencensus-python/0007-application-map.png)

    Ponieważ śledzono tylko jedno wywołanie metody, Mapa aplikacji nie jest tak interesująca. Jednak Mapa aplikacji może być skalowana w celu wizualizowania znacznie większej liczby aplikacji rozproszonych:

   ![Mapa aplikacji](media/opencensus-python/application-map.png)

3. Wybierz pozycję **Zbadaj wydajność** , aby przeprowadzić szczegółową analizę wydajności i określić główną przyczynę powolnej wydajności.

    ![Zrzut ekranu przedstawiający okienko wydajności](./media/opencensus-python/0008-performance.png)

4. Wybranie **próbek** , a następnie kliknięcie dowolnego z przykładów, które pojawiają się w okienku po prawej stronie, spowoduje uruchomienie kompleksowego środowiska z informacjami o transakcji. Mimo że nasza Przykładowa aplikacja wyświetli tylko jedno zdarzenie, bardziej złożona aplikacja umożliwi Eksplorowanie kompleksowej transakcji w dół do poziomu jednego ze stosu wywołań poszczególnych zdarzeń.

     ![Zrzut ekranu przedstawiający kompletny interfejs transakcji](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Wyświetlanie danych za pomocą zapytań

1. Dane telemetryczne, które zostały wysłane z aplikacji, można wyświetlić za pomocą karty Dzienniki (analiza).

    ![Zrzut ekranu przedstawiający okienko przegląd z dziennikami (analiza) wybraną w czerwonym polu](./media/opencensus-python/0010-logs-query.png)

2. W przypadku danych telemetrycznych wysyłanych za pomocą Azure Monitorgo eksportu śledzenia żądania przychodzące będą wyświetlane w obszarze `requests` i żądania out/in-on-in będą wyświetlane w obszarze `dependencies`.

3. W przypadku danych telemetrycznych wysyłanych za pomocą eksportu metryk Azure Monitor w obszarze `customMetrics` będą wyświetlane metryki.

4. W przypadku danych telemetrycznych wysyłanych za pomocą eksportu dzienników Azure Monitor dzienniki będą wyświetlane w obszarze `traces`, a wyjątki będą widoczne w obszarze `exceptions`.

5. Zapoznaj się z [dziennikami w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) , aby uzyskać bardziej szczegółowe informacje na temat korzystania z zapytań i dzienników.

## <a name="opencensus-for-python"></a>OpenCensus dla języka Python

* [OpenCensus Python w witrynie GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Customization](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integracja z kolbą](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integracja Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integracja MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Następne kroki

* [Podsumowanie interfejsu API](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Kompleksowe monitorowanie wydajności](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alerty

* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md): Utwórz testy, aby upewnić się, że Twoja witryna jest widoczna w sieci Web.
* [Inteligentne diagnostyki](../../azure-monitor/app/proactive-diagnostics.md): Te testy są uruchamiane automatycznie, więc nie trzeba wykonywać żadnych czynności, aby je skonfigurować. Ta funkcja powiadomi Cię, jeśli w aplikacji występuje nietypowa liczba nieudanych żądań.
* [Alerty metryk](../../azure-monitor/app/alerts.md): Ustaw alerty, aby ostrzec użytkownika, gdy Metryka przekroczy próg. Możesz je ustawić dla metryk niestandardowych, które zakodujesz w aplikacji.