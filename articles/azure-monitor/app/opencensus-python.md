---
title: Monitorowanie aplikacji języka Python za pomocą usługi Azure Monitor (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Zawiera instrukcje dotyczące przewodów Języka OpenCensus Python za pomocą usługi Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537112"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurowanie usługi Azure Monitor dla aplikacji języka Python

Usługa Azure Monitor obsługuje śledzenie rozproszone, zbieranie metryk i rejestrowanie aplikacji Języka Python poprzez integrację z [programem OpenCensus.](https://opencensus.io) W tym artykule przejdziesz przez proces konfigurowania programu OpenCensus dla języka Python i wysyłania danych monitorowania do usługi Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
- Instalacja Pythona. W tym artykule użyto [języka Python 3.7.0](https://www.python.org/downloads/), choć wcześniejsze wersje prawdopodobnie będą działać z niewielkimi zmianami.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Tworzenie zasobu usługi Application Insights w usłudze Azure Monitor

Najpierw musisz utworzyć zasób usługi Application Insights w usłudze Azure Monitor, który wygeneruje klucz instrumentacji (ikey). Ikey jest następnie używany do konfigurowania opencensus SDK do wysyłania danych telemetrycznych do usługi Azure Monitor.

1. Wybierz pozycję **Utwórz narzędzie** > **Developer tools** > deweloperskie aplikacji **.**

   ![Dodawanie zasobu usługi Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Pojawi się okno konfiguracji. Poniższa tabela służy do wypełniania pól wejściowych.

   | Ustawienie        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Unikatowa wartość globalna | Nazwa identyfikująca monitorowana aplikacja |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów do obsługi danych usługi Application Insights |
   | **Lokalizacja** | Wschodnie stany USA | Lokalizacja w pobliżu lub w pobliżu miejsca, w którym znajduje się aplikacja |

1. Wybierz **pozycję Utwórz**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument z zestawem SDK Języka Python OpenCensus dla usługi Azure Monitor

Zainstaluj eksporterów OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Aby uzyskać pełną listę pakietów i integracji, zobacz [Pakiety OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Polecenie `python -m pip install opencensus-ext-azure` zakłada, że masz `PATH` zestaw zmiennych środowiskowych dla instalacji języka Python. Jeśli ta zmienna nie została skonfigurowana, musisz podać pełną ścieżkę katalogu do miejsca, w którym znajduje się plik wykonywalny języka Python. Rezultatem jest polecenie takie `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`jak: .

Zestaw SDK używa trzech eksporterów usługi Azure Monitor do wysyłania różnych typów danych telemetrycznych do usługi Azure Monitor: śledzenia, metryk i dzienników. Aby uzyskać więcej informacji na temat tych typów telemetrii, zobacz [omówienie platformy danych](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Poniższe instrukcje można wysłać te typy telemetrii za pośrednictwem trzech eksporterów.

## <a name="telemetry-type-mappings"></a>Mapowania typów telemetrii

Oto eksporterzy, które udostępnia program OpenCensus mapowane do typów danych telemetrycznych, które zostaną wyświetlene w usłudze Azure Monitor.

![Zrzut ekranu przedstawiający mapowanie typów danych telemetrycznych z usługi OpenCensus na platformę Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Ślad

> [!NOTE]
> `Trace`w OpenCensus odnosi się do [śledzenia rozproszonego](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). Wysyła `AzureExporter` `requests` i `dependency` telemetrii do usługi Azure Monitor.

1. Najpierw wygenerujmy dane śledzenia lokalnie. W pythonie IDLE lub wybranym edytorze wprowadź następujący kod.

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

2. Uruchomienie kodu będzie wielokrotnie monitować o wprowadzenie wartości. Z każdym wpisem wartość zostanie wydrukowana na powłoce, a moduł OpenCensus Python wygeneruje odpowiedni fragment `SpanData`. Projekt OpenCensus definiuje [ślad jako drzewo przęseł](https://opencensus.io/core-concepts/tracing/).
    
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

3. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować `SpanData` do usługi Azure Monitor. Przekaż swój ciąg połączenia bezpośrednio do eksportera lub można `APPLICATIONINSIGHTS_CONNECTION_STRING`go określić w zmiennej środowiskowej . Zmodyfikuj kod z poprzedniego kroku na podstawie następującego przykładu kodu:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
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

4. Teraz po uruchomieniu skryptu Pythona nadal powinien zostać wyświetlony monit o wprowadzenie wartości, ale tylko wartość jest drukowana w powłoce. Utworzony `SpanData` zostanie wysłany do usługi Azure Monitor. Dane emitowanego zakresu można `dependencies`znaleźć w obszarze . Aby uzyskać więcej informacji na temat wychodzących żądań, zobacz [zależności](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)języka Python OpenCensus .
Aby uzyskać więcej informacji na temat żądań przychodzących, zobacz Żądania Języka [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)OpenCensus .

#### <a name="sampling"></a>Próbkowanie

Aby uzyskać informacje na temat pobierania próbek w Programie OpenCensus, zapoznaj się z [próbkowaniem w programie OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Korelacja śledzenia

Aby uzyskać szczegółowe informacje na temat korelacji telemetrii w danych śledzenia, spójrz na [korelację telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)OpenCensus Python .

#### <a name="modify-telemetry"></a>Modyfikowanie danych telemetrycznych

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzonych danych telemetrycznych przed wysłaniem ich do usługi Azure Monitor, zobacz [Procesory telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Języka Python OpenCensus .

### <a name="metrics"></a>Metryki

1. Najpierw wygenerujmy niektóre lokalne dane metryki. Utworzymy prostą metrykę, aby śledzić, ile razy użytkownik naciska enter.

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
2. Uruchomienie kodu będzie wielokrotnie monitowane o naciśnięcie klawisza Enter. Metryka jest tworzona w celu śledzenia liczby naciśniętych klawisz enter. Z każdym wpisem wartość zostanie dodana, a informacje metryczne będą wyświetlane w konsoli. Informacje obejmują bieżącą wartość i bieżącą sygnaturę czasową po zaktualizowaniu metryki.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane metryki do usługi Azure Monitor. Przekaż swój ciąg połączenia bezpośrednio do eksportera lub można `APPLICATIONINSIGHTS_CONNECTION_STRING`go określić w zmiennej środowiskowej . Zmodyfikuj kod z poprzedniego kroku na podstawie następującego przykładu kodu:

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

4. Eksporter będzie wysyłać dane metryki do usługi Azure Monitor w ustalonych odstępach czasu. Wartość domyślna to co 15 sekund. Śledzimy pojedynczą metrykę, więc te dane metryki, niezależnie od wartości i sygnatury czasowej, które zawiera, będą wysyłane co interwał. Dane można znaleźć `customMetrics`w obszarze .

#### <a name="standard-metrics"></a>Standardowe metryki

Domyślnie eksporter metryk wyśle zestaw standardowych metryk do usługi Azure Monitor. Można wyłączyć to, `enable_standard_metrics` ustawiając `False` flagę w konstruktorze eksportera metryk.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Poniżej znajduje się lista standardowych metryk, które są obecnie wysyłane:

- Dostępna pamięć (bajty)
- Czas procesora (procent)
- Szybkość żądania przychodzącego (na sekundę)
- Średni czas wykonania żądania przychodzącego (milisekundy)
- Stawka za żądanie wychodzące (na sekundę)
- Przetwarzanie użycia procesora (procent)
- Przetwarzanie bajtów prywatnych (bajtów)

Powinny być w stanie zobaczyć te `performanceCounters`dane w . Stawka za przychodzące `customMetrics`żądanie będzie w obszarze . Aby uzyskać więcej informacji, zobacz [liczniki wydajności](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modyfikowanie danych telemetrycznych

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzonych danych telemetrycznych przed wysłaniem ich do usługi Azure Monitor, zobacz [Procesory telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Języka Python OpenCensus .

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

2.  Kod będzie stale prosić o wartość, która ma zostać wprowadzona. Wpis dziennika jest emitowany dla każdej wprowadzonej wartości.

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

3. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane dziennika do usługi Azure Monitor. Przekaż swój ciąg połączenia bezpośrednio do eksportera lub można `APPLICATIONINSIGHTS_CONNECTION_STRING`go określić w zmiennej środowiskowej . Zmodyfikuj kod z poprzedniego kroku na podstawie następującego przykładu kodu:

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

4. Eksporter wyśle dane dziennika do usługi Azure Monitor. Dane można znaleźć `traces`w obszarze . 

> [!NOTE]
> `traces`w tym kontekście nie `Tracing`jest to samo, co . `traces`odnosi się do typu danych telemetrycznych, które będą `AzureLogHandler`widoczne w usłudze Azure Monitor podczas korzystania z . `Tracing`odnosi się do koncepcji w OpenCensus i odnosi się do [śledzenia rozproszonego](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Aby sformatować komunikaty `formatters` dziennika, można użyć we wbudowanym [interfejsie API rejestrowania](https://docs.python.org/3/library/logging.html#formatter-objects)języka Python .

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Można również dodać właściwości niestandardowe do wiadomości dziennika w *dodatkowym* argumentem słowa kluczowego przy użyciu pola custom_dimensions. Będą one wyświetlane jako pary `customDimensions` klucz-wartość w usłudze Azure Monitor.
> [!NOTE]
> Aby ta funkcja działała, musisz przekazać słownik do pola custom_dimensions. Jeśli przekażesz argumenty innego typu, rejestrator zignoruje je.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>Wysyłanie wyjątków

OpenCensus Python nie automatycznie śledzi `exception` i nie wysyła danych telemetrycznych. Są one wysyłane `AzureLogHandler` za pośrednictwem wyjątków za pośrednictwem biblioteki rejestrowania języka Python. Można dodać właściwości niestandardowe, podobnie jak w normalnym rejestrowaniu.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Ponieważ należy zarejestrować wyjątki jawnie, to zależy od użytkownika, w jaki sposób chcą rejestrować nieobsługiwał wyjątki. OpenCensus nie nakłada ograniczeń w sposób użytkownik chce to zrobić, tak długo, jak jawnie rejestrować dane telemetryczne wyjątku.

#### <a name="sampling"></a>Próbkowanie

Aby uzyskać informacje na temat pobierania próbek w Programie OpenCensus, zapoznaj się z [próbkowaniem w programie OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Korelacja dzienników

Aby uzyskać szczegółowe informacje na temat wzbogacania dzienników o dane kontekstowe śledzenia, zobacz [Integracja dzienników](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)języka Python OpenCensus.

#### <a name="modify-telemetry"></a>Modyfikowanie danych telemetrycznych

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzonych danych telemetrycznych przed wysłaniem ich do usługi Azure Monitor, zobacz [Procesory telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Języka Python OpenCensus .

## <a name="view-your-data-with-queries"></a>Wyświetlanie danych za pomocą zapytań

Można wyświetlić dane telemetryczne, które zostały wysłane z aplikacji za pośrednictwem **dzienników (Analytics)** kartę.

![Zrzut ekranu przedstawiający okienko przeglądu z zaznaczoną w czerwonym polu "Dzienniki (Analytics)"](./media/opencensus-python/0010-logs-query.png)

Na liście w obszarze **Aktywne:**

- W przypadku danych telemetrycznych wysyłanych z `requests`eksporterem śledzenia usługi Azure Monitor przychodzące żądania są wyświetlane w obszarze . Żądania wychodzące lub w `dependencies`trakcie są wyświetlane w obszarze .
- W przypadku danych telemetrycznych wysyłanych z eksporterem metryk usługi Azure Monitor wysłane metryki są wyświetlane w obszarze `customMetrics`.
- W przypadku danych telemetrycznych wysyłanych z eksporterem dzienników usługi Azure Monitor dzienniki są wyświetlane w obszarze `traces`. Wyjątki pojawiają `exceptions`się w obszarze .

Aby uzyskać bardziej szczegółowe informacje dotyczące używania zapytań i dzienników, zobacz [Dzienniki w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Dowiedz się więcej o OpenCensus for Python

* [OpenCensus Python na GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Dostosowywanie](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Eksporterzy usługi Azure Monitor w usłudze GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integracje OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Przykładowe aplikacje monitora Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Następne kroki

* [Śledzenie żądań przychodzących](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Śledzenie żądań wychodzących](./../../azure-monitor/app/opencensus-python-request.md)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Kompleksowe monitorowanie wydajności](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alerty

* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md): Utwórz testy, aby upewnić się, że Twoja witryna jest widoczna w sieci Web.
* [Inteligentne diagnostyki](../../azure-monitor/app/proactive-diagnostics.md): Te testy są uruchamiane automatycznie, więc nie trzeba wykonywać żadnych czynności, aby je skonfigurować. Ta funkcja powiadomi Cię, jeśli w aplikacji występuje nietypowa liczba nieudanych żądań.
* [Alerty metryki:](../../azure-monitor/app/alerts.md)Ustaw alerty, aby ostrzegać, jeśli metryka przekroczy próg. Możesz je ustawić dla metryk niestandardowych, które zakodujesz w aplikacji.
