---
title: Śledzenie OpenCensus za pomocą usługi Azure Application Insights | Microsoft Docs
description: Zawiera instrukcje dotyczące integrowania OpenCensus go z lokalną usługą przesyłania dalej i Application Insights
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: ddb8dfbf14f2c75e4d16693076e56a711d8861ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669985"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Zbierz rozproszone dane śledzenia z języka go (wersja zapoznawcza)

Application Insights teraz obsługuje rozproszone śledzenie aplikacji języka go przez integrację z usługą [OpenCensus](https://opencensus.io) i naszą nową [lokalną usługę przesyłania dalej](./opencensus-local-forwarder.md). W tym artykule przedstawiono krok po kroku przez proces konfigurowania usługi OpenCensus dla języka go i pobierania danych śledzenia do Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczna jest subskrypcja platformy Azure.
- Należy zainstalować go. w tym artykule jest używane [pobieranie](https://golang.org/dl/)wersji 1,11.
- Postępuj zgodnie z instrukcjami, aby zainstalować [usługę przesyłania dalej w systemie Windows](./opencensus-local-forwarder.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Utwórz zasób Application Insights

Najpierw należy utworzyć zasób Application Insights, który spowoduje wygenerowanie klucza Instrumentacji (iKey). IKey jest następnie używany do konfigurowania lokalnego usługi przesyłania dalej do wysyłania dystrybuowanych śladów z OpenCensusej aplikacji Instrumentacji do Application Insights.   

1. Wybierz pozycję **Utwórz zasób** > **Narzędzia deweloperskie** > **Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Jeśli tworzysz zasób Application Insights, możesz dowiedzieć się więcej, odwiedzając artykuł [Tworzenie zasobu Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

   | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów hostującej dane usługi App Insights |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij przycisk **Utwórz**.

## <a name="configure-local-forwarder"></a>Konfigurowanie usługi przesyłania dalej lokalnego

1. Wybierz pozycję **Przegląd** > **Podstawy** i skopiuj **klucz instrumentacji** aplikacji.

   ![Zrzut ekranu klucza Instrumentacji](./media/opencensus-Go/0003-instrumentation-key.png)

2. Edytuj plik `LocalForwarder.config` i Dodaj swój klucz Instrumentacji. Jeśli wykonano instrukcje w [wymaganiu wstępnym](./opencensus-local-forwarder.md) , plik znajduje się w `C:\LF-WindowsServiceHost`

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

3. Uruchom ponownie **lokalną usługę przesyłania dalej** aplikacji.

## <a name="opencensus-go-packages"></a>Pakiety OpenCensus

1. Zainstaluj pakiety Open spisu dla języka go z poziomu wiersza polecenia:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Dodaj następujący kod do pliku..., a następnie Skompiluj i Uruchom. (Ten przykład pochodzi od oficjalnych wskazówek dotyczących OpenCensus z dodanym kodem, który ułatwia integrację z lokalną usługą przesyłania dalej)

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

3. Po uruchomieniu aplikacji Simple go przejdź do `http://localhost:50030`. Każde odświeżenie przeglądarki spowoduje wygenerowanie tekstu "Hello World" wraz z odpowiednimi danymi zakresu, które są pobierane przez lokalną usługę przesyłania dalej.

4. Aby upewnić się, że **lokalna usługa przesyłania dalej** odbiera ślady, sprawdź plik `LocalForwarder.config`. Jeśli wykonano kroki wymagane w ramach [wymagań wstępnych](https://docs.microsoft.com/azure/application-insights/local-forwarder), będzie ono znajdować się w `C:\LF-WindowsServiceHost`.

    Na poniższym obrazie pliku dziennika można zobaczyć, że przed uruchomieniem drugiego skryptu, w którym dodaliśmy eksport `OpenCensus input BatchesReceived` był 0. Po rozpoczęciu pracy z zaktualizowanym skryptem `BatchesReceived` większym niż liczba wprowadzonych wartości:
    
    ![Formularz nowego zasobu usługi App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć stronę **omówienia** Application Insights w Azure Portal, aby wyświetlić szczegóły dotyczące aktualnie uruchomionej aplikacji. Wybierz pozycję **Live Metric Stream**.

   ![Zrzut ekranu przedstawiający okienko przegląd z aktywnym strumieńem metryk w czerwonym polu](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Jeśli ponownie uruchomisz aplikację po raz drugi i rozpocznie się odświeżanie przeglądarki w celu `http://localhost:50030`, zobaczysz dane śledzenia na żywo w miarę docierania do Application Insights z lokalnej usługi przesyłania dalej.

   ![Zrzut ekranu przedstawiający strumień metryk na żywo z wyświetlonymi danymi wydajności](./media/opencensus-go/0006-stream.png)

3. Przejdź z powrotem do strony **Przegląd** i wybierz pozycję **Mapa aplikacji** , aby uzyskać wizualny układ relacji zależności i Wywołaj chronometraż między składnikami aplikacji.

    ![Zrzut ekranu przedstawiający podstawową mapę aplikacji](./media/opencensus-go/0007-application-map.png)

    Ponieważ śledzono tylko jedno wywołanie metody, Mapa aplikacji nie jest tak interesująca. Jednak Mapa aplikacji może być skalowana w celu wizualizowania znacznie większej liczby aplikacji rozproszonych:

   ![Mapa aplikacji](media/opencensus-go/application-map.png)

4. Wybierz pozycję **Zbadaj wydajność** , aby przeprowadzić szczegółową analizę wydajności i określić główną przyczynę powolnej wydajności.

    ![Zrzut ekranu przedstawiający okienko wydajności](./media/opencensus-go/0008-performance.png)

5. Wybranie **próbek** , a następnie kliknięcie dowolnego z przykładów, które pojawiają się w okienku po prawej stronie, spowoduje uruchomienie kompleksowego środowiska z informacjami o transakcji. Mimo że nasza Przykładowa aplikacja wyświetli tylko jedno zdarzenie, bardziej złożona aplikacja umożliwi Eksplorowanie kompleksowej transakcji w dół do poziomu jednego ze stosu wywołań poszczególnych zdarzeń.

     ![Zrzut ekranu przedstawiający kompletny interfejs transakcji](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Śledzenie OpenCensus dla języka go

Poznasz jedynie podstawowe informacje dotyczące integrowania OpenCensus dla języka go z lokalną usługą przesyłania dalej i Application Insights. [Oficjalne wskazówki dotyczące użycia programu OpenCensuse](https://godoc.org/go.opencensus.io) dotyczą bardziej zaawansowanych tematów.

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Kompleksowe monitorowanie wydajności](./../../azure-monitor/learn/tutorial-performance.md)
