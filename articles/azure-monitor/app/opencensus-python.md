---
title: Monitorowanie aplikacji języka Python za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zawiera instrukcje dotyczące Podłączanie OpenCensus Python z usługą Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541431"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Zbierać ślady rozproszonych za pomocą języka Python (wersja zapoznawcza)

Usługa Application Insights teraz obsługuje rozproszone śledzenie aplikacji Python dzięki integracji z usługą [OpenCensus](https://opencensus.io). W tym artykule opisano krok po kroku przez proces konfigurowania OpenCensus dla języka Python i pobierania danych monitorowania do usługi Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczna jest subskrypcja platformy Azure.
- Python powinien być zainstalowany, w tym artykule wykorzystano [Python numer 3.7.0](https://www.python.org/downloads/), ale za pomocą drobnych korekt prawdopodobnie działać w starszych wersjach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Utwórz zasób usługi Application Insights

Najpierw należy utworzyć zasób usługi Application Insights, który generuje key(ikey) instrumentacji. Klucz Instrumentacji jest następnie używany do konfigurowania zestawu SDK OpenCensus z wysyłania danych telemetrycznych do usługi Application Insights.

1. Wybierz **Utwórz zasób** > **narzędzi deweloperskich** > **usługi Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/opencensus-python/0001-create-resource.png)

   Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów hostującej dane usługi App Insights |
   | **Location** | East US | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij pozycję **Utwórz**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Zainstaluj OpenCensus Azure Monitor eksporterów

1. Zainstaluj eksportera OpenCensus Azure Monitor:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` przyjęto założenie, że masz zmiennej środowiskowej PATH, ustaw dla Twojej instalacji środowiska Python. Jeśli nie skonfigurowano to, będziesz potrzebować zapewniają pełną ścieżką do której Python plik wykonywalny znajduje się którego chcieliby wynik za pomocą polecenia: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Pierwszy teraz wygenerować niektóre dane śledzenia lokalnie. W bezczynności języka Python lub edytorze wyboru wprowadź następujący kod:

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

3. Uruchamiając kod wielokrotnie poprosi o podanie wartości. Każdy wpis wartości będą wypisywane w powłoce, a odpowiedni fragment **SpanData** będą generowane przez moduł OpenCensus języka Python. Definiuje projektu OpenCensus [ _śledzenia jako drzewo zakresy_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Podczas, gdy jest to przydatne dla celów demonstracyjnych, ostatecznie chcemy emitować SpanData do usługi Application Insights. Zmień swój kod z poprzedniego kroku, w oparciu o poniższy przykładowy kod:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Teraz po uruchomieniu skryptu języka Python powinny nadal wyświetlony monit o podanie wartości, ale teraz w powłoce drukowany jest tylko wartość.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć usługę Application Insights **Przegląd** strony w witrynie Azure portal, aby wyświetlić szczegółowe informacje o obecnie uruchomionej aplikacji. Wybierz **Live Stream metryki**.

   ![Zrzut ekranu przedstawiający okienko omówienia z zaznaczona czerwonym prostokątem strumień metryk na żywo](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Przejdź z powrotem do **Przegląd** strony i wybierz **mapy aplikacji** Aby uzyskać wizualny układ relacji zależności i czasu wywołania między składnikami aplikacji.

    ![Zrzut ekranu przedstawiający mapę aplikacji w warstwie podstawowa](./media/opencensus-python/0007-application-map.png)

    Ponieważ firma Microsoft była tylko śledzenia jedno wywołanie metody, nasza Mapa aplikacji jest jak interesujące. Ale mapy aplikacji mogą być skalowane do wizualizacji o wiele bardziej rozproszonych:

   ![Mapa aplikacji](media/opencensus-python/application-map.png)

3. Wybierz **badanie wydajności** wykonywania szczegółową analizę wydajności i określenia głównej przyczyny niskiej wydajności.

    ![Zrzut ekranu przedstawiający okienko wyników](./media/opencensus-python/0008-performance.png)

4. Wybieranie **przykłady** , a następnie klikając na dowolnym z przykładów, które są wyświetlane w okienku po prawej stronie spowoduje uruchomienie środowiska szczegółów transakcji end-to-end. Gdy nasza Przykładowa aplikacja właśnie pokaże nam to pojedyncze zdarzenie, bardziej złożonych aplikacji będzie pozwalają zapoznać się transakcji end-to-end do poziomu poszczególnych zdarzeń stosu wywołań.

     ![Zrzut ekranu przedstawiający interfejs transakcji end-to-end](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>OpenCensus dla języka Python

* [Dostosowywanie](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integracja Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integracja Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integracja z MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Kolejne kroki

* [OpenCensus Python w usłudze GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Monitorowanie wydajności end-to-end](./../../azure-monitor/learn/tutorial-performance.md)