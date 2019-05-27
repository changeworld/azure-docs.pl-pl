---
title: Śledzenie Python OpenCensus za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Instrukcje w celu podłączenia do śledzenia OpenCensus Python za pomocą lokalnej usługi przesyłania dalej i Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9db483e15197e6cdaaaa5981410630184cc6ca
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957246"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Zbierać ślady rozproszonych za pomocą języka Python (wersja zapoznawcza)

Usługa Application Insights teraz obsługuje rozproszone śledzenie aplikacji Python dzięki integracji z usługą [OpenCensus](https://opencensus.io) i naszej nowej [lokalnej usługi przesyłania dalej](./../../azure-monitor/app/opencensus-local-forwarder.md). W tym artykule opisano krok po kroku przez proces konfigurowania OpenCensus dla języka Python i pobieranie danych śledzenia do usługi Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczna jest subskrypcja platformy Azure.
- Python powinien być zainstalowany, w tym artykule wykorzystano [Python numer 3.7.0](https://www.python.org/downloads/), ale za pomocą drobnych korekt prawdopodobnie działać w starszych wersjach.
- Postępuj zgodnie z instrukcjami, aby zainstalować [lokalnej usługi przesyłania dalej, co usługa Windows](./../../azure-monitor/app/opencensus-local-forwarder.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Najpierw należy utworzyć zasób usługi Application Insights, który generuje key(ikey) instrumentacji. Klucz Instrumentacji jest następnie używany do konfigurowania lokalnego usługi przesyłania dalej wysyłanie rozproszone śledzenie z OpenCensus instrumentacji aplikacji do usługi Application Insights.   

1. Wybierz **Utwórz zasób** > **narzędzi deweloperskich** > **usługi Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/opencensus-python/0001-create-resource.png)

   Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Typ aplikacji** | Ogólne | Typ monitorowanej aplikacji |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów hostującej dane usługi App Insights |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij pozycję **Utwórz**.

## <a name="configure-local-forwarder"></a>Skonfiguruj lokalne usługi przesyłania dalej

1. Wybierz pozycję **Przegląd**  >  **Podstawy** i skopiuj **klucz instrumentacji** aplikacji.

   ![Zrzut ekranu przedstawiający klucz Instrumentacji](./media/opencensus-python/0003-instrumentation-key.png)

2. Edytuj swoje `LocalForwarder.config` pliku i Dodaj klucz instrumentacji. Jeśli postąpiono zgodnie z instrukcjami w [wstępny](./../../azure-monitor/app/opencensus-local-forwarder.md) plik znajduje się w `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Uruchom ponownie aplikację **lokalnej usługi przesyłania dalej** usługi.

## <a name="opencensus-python-package"></a>Pakiet języka OpenCensus Python

1. Zainstaluj pakiet Open spisu dla języka Python i eksportu przy użyciu narzędzia pip lub pipenv z wiersza polecenia:

    ```console
    python -m pip install opencensus
    python -m pip install opencensus-ext-ocagent

    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` przyjęto założenie, że masz zmiennej środowiskowej PATH, ustaw dla Twojej instalacji środowiska Python. Jeśli nie skonfigurowano to, będziesz potrzebować zapewniają pełną ścieżką do której Python plik wykonywalny znajduje się którego chcieliby wynik za pomocą polecenia: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. Pierwszy teraz wygenerować niektóre dane śledzenia lokalnie. W bezczynności języka Python lub edytorze wyboru wprowadź następujący kod:

    ```python
    from opencensus.trace.tracer import Tracer

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

3. Uruchamiając kod wielokrotnie poprosi o podanie wartości. Każdy wpis wartości będą wypisywane w powłoce, a odpowiedni fragment **SpanData** będą generowane przez moduł OpenCensus języka Python. Definiuje projektu OpenCensus [ _śledzenia jako drzewo zakresy_](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Gdy jest to przydatne dla celów demonstracyjnych, ostatecznie chcemy, aby emitować SpanData w taki sposób, aby go może zostać pobrana przez naszych **usługi modułu przekazującego lokalnego** i wysłane do usługi Application Insights. Zmień swój kod z poprzedniego kroku następujące czynności:

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.ext.ocagent.trace_exporter import TraceExporter
    from opencensus.trace import tracer as tracer_module

    import os

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        export_LocalForwarder = TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))

        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

5. Jeśli zostanie zapisany i spróbuj uruchomić modułem, możesz otrzymać `ModuleNotFoundError` dla `grpc`. Jeśli ten problem wystąpi, uruchom następujące polecenie, aby zainstalować [pakietu grpcio](https://pypi.org/project/grpcio/) za pomocą:

    ```console
    python -m pip install grpcio
    ```

6. Teraz po uruchomieniu skryptu języka Python z powyższych powinien nadal pojawić się monit o podanie wartości, ale teraz w powłoce drukowania jest tylko wartość.

7. Aby potwierdzić, że **lokalnej usługi przesyłania dalej** jest pobrania Sprawdź ślady `LocalForwarder.config` pliku. Jeśli wykonano kroki opisane w [wstępnie wymaganego składnika](https://docs.microsoft.com/azure/application-insights/local-forwarder), będą znajdować się w `C:\LF-WindowsServiceHost`.

    Na ilustracji poniżej w pliku dziennika możesz zobaczyć, że przed uruchomieniem drugi skrypt, w którym dodaliśmy eksporter `OpenCensus input BatchesReceived` : 0. Gdy Rozpoczęliśmy uruchamianie ze zaktualizowanego skryptu `BatchesReceived` zwiększona równy wartości, które możemy wprowadzić:
    
    ![Formularz nowego zasobu usługi App Insights](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć usługę Application Insights **Przegląd** strony w witrynie Azure portal, aby wyświetlić szczegółowe informacje o obecnie uruchomionej aplikacji. Wybierz **Live Stream metryki**.

   ![Zrzut ekranu przedstawiający okienko omówienia z zaznaczona czerwonym prostokątem strumień metryk na żywo](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Jeśli uruchomisz ponownie drugi skrypt języka Python i rozpoczęcia wprowadzanie wartości, zostanie wyświetlony na żywo dane śledzenia niezapisywanie ich w usłudze Application Insights z usługi lokalnej usługi przesyłania dalej.

   ![Zrzut ekranu przedstawiający transmisji strumieniowej metryk na żywo z danymi wydajności wyświetlane](./media/opencensus-python/0006-stream.png)

3. Przejdź z powrotem do **Przegląd** strony i wybierz **mapy aplikacji** Aby uzyskać wizualny układ relacji zależności i czasu wywołania między składnikami aplikacji.

    ![Zrzut ekranu przedstawiający mapę aplikacji w warstwie podstawowa](./media/opencensus-python/0007-application-map.png)

    Ponieważ firma Microsoft była tylko śledzenia jedno wywołanie metody, nasza Mapa aplikacji jest jak interesujące. Ale mapy aplikacji mogą być skalowane do wizualizacji o wiele bardziej rozproszonych:

   ![Mapa aplikacji](media/opencensus-python/application-map.png)

4. Wybierz **badanie wydajności** wykonywania szczegółową analizę wydajności i określenia głównej przyczyny niskiej wydajności.

    ![Zrzut ekranu przedstawiający okienko wyników](./media/opencensus-python/0008-performance.png)

5. Wybieranie **przykłady** , a następnie klikając na dowolnym z przykładów, które są wyświetlane w okienku po prawej stronie spowoduje uruchomienie środowiska szczegółów transakcji end-to-end. Gdy nasza Przykładowa aplikacja właśnie pokaże nam to pojedyncze zdarzenie, bardziej złożonych aplikacji będzie pozwalają zapoznać się transakcji end-to-end do poziomu poszczególnych zdarzeń stosu wywołań.

     ![Zrzut ekranu przedstawiający interfejs transakcji end-to-end](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>Możliwe jest śledzenie OpenCensus dla języka Python

Tylko pokrótce informacje omówione podstawy okablowania się OpenCensus dla języka Python za pomocą lokalnej usługi przesyłania dalej i Application Insights. Wskazówki dotyczące oficjalne użycia obejmuje bardziej zaawansowanych tematów, takich jak:

* [Próbki](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Integracja Flask](https://opencensus.io/api/python/trace/usage.html#flask)
* [Integracja Django](https://opencensus.io/api/python/trace/usage.html#django)
* [Integracja z MySQL](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>Kolejne kroki

* [Podręcznik użycia OpenCensus języka Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Monitorowanie wydajności end-to-end](./../../azure-monitor/learn/tutorial-performance.md)
