---
title: OpenCensus Przejdź śledzenie za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zawiera instrukcje dotyczące integracji OpenCensus Przejdź śledzenie za pomocą lokalnej usługi przesyłania dalej i Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577908"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Zbierać ślady rozproszonego z rzeczywistym użyciem (wersja zapoznawcza)

Usługa Application Insights teraz obsługuje rozproszone śledzenie aplikacji w języku Go dzięki integracji z usługą [OpenCensus](https://opencensus.io) i naszej nowej [lokalnej usługi przesyłania dalej](./opencensus-local-forwarder.md). W tym artykule opisano krok po kroku przez proces konfigurowania OpenCensus dla języka Go i pobieranie danych śledzenia do usługi Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczna jest subskrypcja platformy Azure.
- Należy zainstalować go, w tym artykule używa wersji 1.11 [Go pobrać](https://golang.org/dl/).
- Postępuj zgodnie z instrukcjami, aby zainstalować [lokalnej usługi przesyłania dalej, co usługa Windows](./opencensus-local-forwarder.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Najpierw należy utworzyć zasób usługi Application Insights, który generuje klucz Instrumentacji (klucz Instrumentacji). Klucz Instrumentacji jest następnie używany do konfigurowania lokalnego usługi przesyłania dalej wysyłanie rozproszone śledzenie z OpenCensus instrumentacji aplikacji do usługi Application Insights.   

1. Wybierz **Utwórz zasób** > **narzędzi deweloperskich** > **usługi Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/opencensus-Go/0001-create-resource.png)

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

   ![Zrzut ekranu przedstawiający klucz Instrumentacji](./media/opencensus-Go/0003-instrumentation-key.png)

2. Edytuj swoje `LocalForwarder.config` pliku i Dodaj klucz instrumentacji. Jeśli postąpiono zgodnie z instrukcjami w [wstępny](./opencensus-local-forwarder.md) plik znajduje się w `C:\LF-WindowsServiceHost`

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

## <a name="opencensus-go-packages"></a>Pakiety OpenCensus z rzeczywistym użyciem

1. Zainstaluj pakiety Otwórz spis dla języka Go z poziomu wiersza polecenia:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Dodaj następujący kod do pliku .go i następnie skompilować i uruchomić. (W tym przykładzie jest tworzony na podstawie oficjalne wskazówki OpenCensus dodano kod, który ułatwia integrację z lokalnej usługi przesyłania dalej)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Gdy zostanie uruchomiona prosta aplikacja Przejdź przejdź do `http://localhost:50030`. Każdym odświeżeniu przeglądarki wygeneruje tekst "hello world" wraz z odpowiedniego zakresu danych, które są pobierane przez lokalne usługi przesyłania dalej.

4. Aby potwierdzić, że **lokalnej usługi przesyłania dalej** jest pobrania Sprawdź ślady `LocalForwarder.config` pliku. Jeśli wykonano kroki opisane w [wstępnie wymaganego składnika](https://docs.microsoft.com/azure/application-insights/local-forwarder), będą znajdować się w `C:\LF-WindowsServiceHost`.

    Na ilustracji poniżej w pliku dziennika możesz zobaczyć, że przed uruchomieniem drugi skrypt, w którym dodaliśmy eksporter `OpenCensus input BatchesReceived` : 0. Gdy Rozpoczęliśmy uruchamianie ze zaktualizowanego skryptu `BatchesReceived` zwiększona równy wartości, które możemy wprowadzić:
    
    ![Formularz nowego zasobu usługi App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć usługę Application Insights **Przegląd** strony w witrynie Azure portal, aby wyświetlić szczegółowe informacje o obecnie uruchomionej aplikacji. Wybierz **Live Stream metryki**.

   ![Zrzut ekranu przedstawiający okienko omówienia z zaznaczona czerwonym prostokątem strumień metryk na żywo](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Możesz ponownie uruchomić drugą aplikację języka Go i Rozpocznij odświeżanie przeglądarki pod kątem `http://localhost:50030`, zostanie wyświetlony na żywo dane śledzenia niezapisywanie ich w usłudze Application Insights z usługi lokalnej usługi przesyłania dalej.

   ![Zrzut ekranu przedstawiający transmisji strumieniowej metryk na żywo z danymi wydajności wyświetlane](./media/opencensus-go/0006-stream.png)

3. Przejdź z powrotem do **Przegląd** strony i wybierz **mapy aplikacji** Aby uzyskać wizualny układ relacji zależności i czasu wywołania między składnikami aplikacji.

    ![Zrzut ekranu przedstawiający mapę aplikacji w warstwie podstawowa](./media/opencensus-go/0007-application-map.png)

    Ponieważ firma Microsoft była tylko śledzenia jedno wywołanie metody, nasza Mapa aplikacji jest jak interesujące. Ale mapy aplikacji mogą być skalowane do wizualizacji o wiele bardziej rozproszonych:

   ![Mapa aplikacji](media/opencensus-go/application-map.png)

4. Wybierz **badanie wydajności** wykonywania szczegółową analizę wydajności i określenia głównej przyczyny niskiej wydajności.

    ![Zrzut ekranu przedstawiający okienko wyników](./media/opencensus-go/0008-performance.png)

5. Wybieranie **przykłady** , a następnie klikając na dowolnym z przykładów, które są wyświetlane w okienku po prawej stronie spowoduje uruchomienie środowiska szczegółów transakcji end-to-end. Gdy nasza Przykładowa aplikacja właśnie pokaże nam to pojedyncze zdarzenie, bardziej złożonych aplikacji będzie pozwalają zapoznać się transakcji end-to-end do poziomu poszczególnych zdarzeń stosu wywołań.

     ![Zrzut ekranu przedstawiający interfejs transakcji end-to-end](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Możliwe jest śledzenie OpenCensus dla języka Go

Pokrótce informacje omówione tylko podstawowe informacje dotyczące integrowania OpenCensus dla języka Go z lokalnej usługi przesyłania dalej i usługi Application Insights. [Oficjalne wskazówki dotyczące użycia OpenCensus Go](https://godoc.org/go.opencensus.io) obejmuje bardziej zaawansowanych tematów.

## <a name="next-steps"></a>Kolejne kroki

* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Monitorowanie wydajności end-to-end](./../../azure-monitor/learn/tutorial-performance.md)
