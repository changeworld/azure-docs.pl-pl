---
title: Dodaj identyfikatory korelacji do komunikatów Internetu rzeczy za pomocą rozproszonego śledzenia (wersja zapoznawcza)
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: 302c382a7e19e9dcc4c979d31ddc0768655a1465
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501385"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Śledzenie komunikatów urządzenie chmura usługi Azure IoT za pomocą rozproszonego śledzenia (wersja zapoznawcza)

Microsoft Azure IoT Hub obsługuje obecnie rozproszonego śledzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa IoT Hub jest jednym z pierwszej usługi platformy Azure służące do obsługi rozproszonego śledzenia. Zgodnie z innymi usługami platformy Azure obsługuje rozproszonego śledzenia, będzie możliwe śledzenie komunikatów Internetu rzeczy w całym usług platformy Azure zaangażowanych w rozwiązaniu. W tle na rozproszonego śledzenia, zobacz [rozproszonego śledzenia](../azure-monitor/app/distributed-tracing.md).

Włączenie rozproszonego śledzenia dla usługi IoT Hub udostępnia możliwości:

- Dokładnie monitorować przepływ każdego komunikatu za pośrednictwem usługi IoT Hub przy użyciu [kontekst śledzenia](https://github.com/w3c/trace-context). Ten kontekst śledzenia obejmuje korelacji identyfikatorów, które umożliwiają korelowanie zdarzeń z jednego składnika ze zdarzeniami z innego składnika. Można je stosować podzbiór lub wszystkie komunikaty urządzeń IoT za pomocą [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md).
- Automatycznie rejestruj kontekst śledzenia do [dzienników diagnostycznych usługi Azure Monitor](iot-hub-monitor-resource-health.md).
- Zmierz i zrozumieć przepływ komunikatów i opóźnienia z urządzeń do usługi IoT Hub i routingu punktów końcowych.
- Rozpocznij, biorąc pod uwagę sposób zaimplementuj rozproszone śledzenie z usług platformy Azure w rozwiązaniu IoT.

W tym artykule używasz [zestaw SDK urządzeń Azure IoT dla języka C](./iot-hub-device-sdk-c-intro.md) za pomocą rozproszonego śledzenia. Obsługa rozproszonego śledzenia jest nadal w toku dla innych zestawów SDK.

## <a name="prerequisites"></a>Wymagania wstępne

- Rozproszonego śledzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT Hub są tworzone w następujących regionach:

  - **Europa Północna**
  - **Azja południowo-wschodnia**
  - **Zachodnie stany USA 2**

- W tym artykule założono, że znasz wysyła komunikaty telemetryczne do Centrum IoT. Upewnij się, że zostały wykonane [wysyłanie danych telemetrycznych Szybki Start języka C](./quickstart-send-telemetry-c.md).

- Rejestrowanie urządzenia z usługi IoT hub (kroków opisanych w każdym Szybki Start) i zanotuj parametry połączenia.

- Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurowanie usługi IoT Hub

W tej sekcji skonfigurujesz Centrum IoT Hub do logowania się atrybutów rozproszonego śledzenia (identyfikatory korelacji i sygnatury czasowe).

1. Przejdź do Centrum IoT w [witryny Azure portal](https://portal.azure.com/).

1. W lewym okienku Centrum IoT, przewiń w dół do **monitorowanie** sekcji, a następnie kliknij przycisk **ustawień diagnostycznych**.

1. Jeśli ustawienia diagnostyczne nie są jeszcze włączone, kliknij przycisk **Włącz diagnostykę**. Jeśli została już włączona ustawień diagnostycznych, kliknij przycisk **Dodaj ustawienie diagnostyczne**.

1. W **nazwa** wprowadź nazwę dla nowego ustawienia diagnostycznego. Na przykład **DistributedTracingSettings**.

1. Wybierz co najmniej jeden z następujących opcji, które określają, w których będą wysyłane rejestrowanie:

    - **Zarchiwizuj na koncie magazynu**: Skonfiguruj konto magazynu, aby zawierała informacje rejestrowania.
    - **Stream do usługi event hub**: Konfigurowanie Centrum zdarzeń zawierają informacje rejestrowania.
    - **Wysyłanie do usługi Log Analytics**: Skonfiguruj obszar roboczy usługi log analytics zawiera informacje rejestrowania.

1. W **dziennika** wybierz operacje, które mają informacje rejestrowania dla.

    Upewnij się uwzględnić **DistributedTracing**i skonfiguruj **przechowywania** ile dni mają Rejestrowanie zatrzymane. Przechowywanie dziennika mają wpływ na koszty magazynowania.

    ![Zrzut ekranu przedstawiający, gdzie kategoria DistributedTracing dotyczy ustawień diagnostycznych IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Kliknij przycisk **Zapisz** nowe ustawienie.

1. (Opcjonalnie) Aby wyświetlić komunikaty przepływać do różnych miejsc, skonfiguruj [reguły routingu do co najmniej dwóch różnych punktów końcowych](iot-hub-devguide-messages-d2c.md).

Po włączeniu rejestrowania usługi IoT Hub rejestruje dziennika po napotkaniu komunikat zawierający właściwości śledzenia prawidłowy w następujących sytuacjach:

- Komunikaty dociera do bramy usługi IoT Hub.
- Komunikat jest przetwarzany przez usługę IoT Hub.
- Komunikat jest kierowany do niestandardowych punktów końcowych. Można włączyć routing.

Aby dowiedzieć się więcej na temat tych dzienników i ich schematów, zobacz [rozproszonego śledzenia w dzienniki diagnostyczne usługi IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Konfigurowanie urządzenia

W tej sekcji przygotować środowisko projektowe służące do użytku z [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c). Następnie należy zmodyfikować jest jednym z przykładów umożliwiające rozproszonego śledzenia na komunikaty telemetryczne z urządzeń.

Te instrukcje dotyczą tworzenia przykładu na Windows. W innych środowiskach, zobacz [kompilacji zestawu SDK języka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) lub [opakowane zestawu SDK języka C dla określonych programowanie na platformie](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klonowanie kodu źródłowego i zainicjuj

1. Zainstaluj [obciążenie "Programowanie aplikacji klasycznych w języku C++"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017) dla programu Visual Studio 2015 lub 2017.

1. Zainstaluj [CMake](https://cmake.org/). Upewnij się, że jest Twoje `PATH` , wpisując `cmake -version` z poziomu wiersza polecenia.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu.

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Jeśli `cmake` nie można znaleźć kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Edytuj wysyłania danych telemetrycznych próby włączenia rozproszonego śledzenia

1. Użyj edytora, aby otworzyć `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` pliku źródłowego.

1. Znajdź deklarację stałej `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Zastąp wartość `connectionString` stałej przy użyciu parametrów połączenia urządzenia należy zanotować w [rejestrowanie urządzenia](./quickstart-send-telemetry-c.md#register-a-device) części [wysyłanie danych telemetrycznych Szybki Start języka C](./quickstart-send-telemetry-c.md).

1. Zmiana `MESSAGE_COUNT` zdefiniować `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Znajdź wiersz kodu, który wywołuje `IoTHubDeviceClient_LL_SetConnectionStatusCallback` zarejestrować funkcji wywołania zwrotnego stanu połączenia przed pętlą komunikatów wysyłania. Dodaj kod, w tym wierszu, jak pokazano poniżej, aby wywołać `IoTHubDeviceClient_LL_EnablePolicyConfiguration` włączenie rozproszonego śledzenia dla urządzenia:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration` Funkcja pozwala zasad dla określonych funkcji IoTHub, które są skonfigurowane za pomocą [bliźniaczych reprezentacji urządzeń](./iot-hub-devguide-device-twins.md). Gdy `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` jest włączone za pomocą wiersza powyższy kod, zachowanie urządzenia w zakresie śledzenia będą odzwierciedlać rozproszonego śledzenia zmian w bliźniaczej reprezentacji urządzenia.

1. Aby zachować przykładową aplikację bez zużycie wszystkich limitu przydziału, należy dodać opóźnienie jednosekundowych na końcu pętli wysyłania komunikatów:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilowanie i uruchamianie

1. Przejdź do *iothub_ll_telemetry_sample* katalogu projektu z katalogu CMake (`azure-iot-sdk-c/cmake`) utworzone wcześniej i skompiluj przykładowy:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Uruchom aplikację. Urządzenie wysyła dane telemetryczne, Obsługa rozproszonego śledzenia.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Nie zatrzymuj aplikacji. Opcjonalnie można obserwować wiadomości wysyłanych do usługi IoT Hub, analizując okna konsoli.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Rozwiązania dla klientów innych firm

Ma ona **nie uproszczonych** się bez korzystania z zestawu SDK języka C w wersji zapoznawczej funkcja rozproszonego śledzenia. W związku z tym takie podejście nie jest zalecane.

Najpierw musi implementować wszystkich podstawowych protokołu IoT Hub w wiadomości, postępując zgodnie z przewodnikiem dev [tworzenie i odczytu komunikatów usługi IoT Hub](iot-hub-devguide-messages-construct.md). Następnie należy zmodyfikować właściwości protokołu MQTT/AMQP wiadomości w celu dodania `tracestate` jako **właściwość systemu**. Są to:

* Dla protokołu MQTT, Dodaj `%24.tracestate=timestamp%3d1539243209` do tematu wiadomości, gdzie `1539243209` powinny być zastąpione przez czas utworzenia wiadomości w formacie sygnatura czasowa systemu unix. Na przykład można znaleźć implementacji [zestawu SDK języka C](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* W przypadku protokołu AMQP, dodać `key("tracestate")` i `value("timestamp=1539243209")` komunikatu jako adnotacji. Aby uzyskać implementację referencyjną, zobacz [tutaj](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Aby określić procent komunikaty zawierające tę właściwość, należy zaimplementować logikę do nasłuchiwania zdarzeń zainicjowane w chmurze, takich jak liczba aktualizacji bliźniaczej reprezentacji.

## <a name="update-sampling-options"></a>Opcje próbkowania aktualizacji 

Aby zmienić wartość procentową wiadomości, które mają być śledzone w chmurze, należy zaktualizować bliźniaczej reprezentacji urządzenia. Osiągniesz to wiele sposobów, łącznie z edytora JSON w portalu i zestawu SDK usługi IoT Hub. Przykłady można znaleźć w poniższych podsekcjach.

### <a name="update-using-the-portal"></a>Aktualizacja przy użyciu portalu

1. Przejdź do Centrum IoT w [witryny Azure portal](https://portal.azure.com/), następnie kliknij przycisk **urządzeń IoT**.

1. Kliknij swoje urządzenie.

1. Wyszukaj **włączenie rozproszonego śledzenia (wersja zapoznawcza)**, a następnie wybierz **Włącz**.

    ![Włącz rozproszonego śledzenia w witrynie Azure portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Wybierz **częstotliwość próbkowania** od 0 do 100%.

1. Kliknij pozycję **Zapisz**.

1. Odczekaj kilka sekund, a trafienia **Odśwież**, a następnie, jeśli pomyślnie potwierdzona przez urządzenie, pojawi się ikona synchronizacji, ze znacznikiem wyboru.

1. Wróć do okna konsoli, dane telemetryczne aplikacji wiadomości. Zostanie wyświetlony komunikaty są wysyłane przy użyciu `tracestate` we właściwościach aplikacji.

    ![Stan śledzenia](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcjonalnie) Zmień częstotliwość próbkowania na inną wartość, a następnie obserwować zmiany w obszarze częstotliwość, który obejmuje komunikaty `tracestate` we właściwościach aplikacji.

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>Aktualizacja przy użyciu usługi Azure IoT Hub Toolkit dla programu VS Code

1. Instalowanie programu VS Code, a następnie zainstaluj najnowszą wersję usługi Azure IoT Hub Toolkit dla programu VS Code z [tutaj](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Otwórz program VS Code i [skonfigurować parametry połączenia Centrum IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Rozwiń węzeł urządzenia i poszukaj **rozproszonego śledzenia ustawienie (wersja zapoznawcza)**. Kliknij pozycję **rozproszonego śledzenia ustawienie aktualizacji (wersja zapoznawcza)** węzła podrzędnego.

    ![Włącz rozproszonego śledzenia w usłudze Azure IoT Hub Toolkit](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. W oknie podręcznym wybierz **Włącz**, naciśnij klawisz Enter, aby potwierdzić 100 jako częstotliwość próbkowania.

    ![Tryb próbkowania aktualizacji](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Częstotliwość próbkowania aktualizacji](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Aktualizacja zbiorcza dla wielu urządzeń

Aby zaktualizować konfigurację rozproszonego śledzenia próbkowania dla wielu urządzeń, użyj [konfiguracji urządzenia automatycznego](iot-hub-auto-device-config.md). Upewnij się, że postępuj zgodnie z tym schemacie bliźniaczej reprezentacji:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Nazwa elementu | Wymagane | Typ | Opis |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Yes | Liczba całkowita | Dwie wartości tryb są obecnie obsługiwane włączyć próbkowanie włączać i wyłączać. `1` znajduje się na oraz `2` jest wyłączona. |
| `sampling_rate` | Yes | Liczba całkowita | Ta wartość jest procentem. Tylko wartości z kolekcji `0` do `100` (włącznie) są dozwolone.  |

## <a name="query-and-visualize"></a>Zapytania i wizualizacji

Aby wyświetlić wszystkie ślady zarejestrowane przez usługę IoT Hub, wysyłać zapytania magazynu dziennika, które wybrano w konfiguracji ustawień diagnostyki. W tej sekcji przedstawiono kilka różnych opcji.

### <a name="query-using-log-analytics"></a>Wykonywanie zapytań przy użyciu usługi Log Analytics

Jeśli skonfigurowano [usługi Log Analytics przy użyciu dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-stream-log-store.md), na zapytanie uzyskiwana przez wyszukiwanie dzienników w `DistributedTracing` kategorii. Na przykład to zapytanie Wyświetla wszystkie ślady zarejestrowane:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Dzienniki przykład przedstawiony przez usługę Log Analytics:

| TimeGenerated | OperationName | Kategoria | Poziom | CorrelationId | durationMs | Właściwości |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Aby poznać różne typy dzienników, zobacz [dzienników diagnostycznych usługi Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa aplikacji

Aby zwizualizować przepływ komunikatów Internetu rzeczy, należy skonfigurować mapowanie aplikacji przykładowej aplikacji. Przykładowa aplikacja wysyła dzienniki rozproszonego śledzenia, aby [mapy aplikacji](../application-insights/app-insights-app-map.md) przy użyciu funkcji platformy Azure i Centrum zdarzeń.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Pobieranie przykładu w usłudze Github</a>

Poniższy obraz przedstawia rozproszonego śledzenia na mapie aplikacji z trzech routingu punktów końcowych:

![IoT rozproszonego śledzenia w Mapa aplikacji](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Omówienie usługi Azure IoT rozproszonych śledzenia

### <a name="context"></a>Kontekst

Wiele rozwiązań IoT, w tym własną [architektury referencyjnej](https://aka.ms/iotrefarchitecture) (tylko w języku angielskim), należy wykonać w wariancie [opartych na architekturze mikrousług](https://docs.microsoft.com/azure/architecture/microservices/). Wraz z rozwojem bardziej złożone rozwiązania IoT na końcu przy użyciu mikrousług tuzina lub więcej. Takich mikrousług mogą być lub może nie być na platformie Azure. Precyzyjne określenie, gdzie porzucenie lub spowalniania komunikatów Internetu rzeczy może być trudne. Na przykład masz rozwiązania IoT, które korzysta z 5 różnych usług platformy Azure i 1500 aktywnych urządzeń. Każde urządzenie wysyła 10 urządzenia do chmury komunikaty/sekundę (dla daje w sumie 15 000 komunikatów na sekundę), ale Zwróć uwagę, że Twoja aplikacja sieci web widzi tylko 10 000 komunikatów/sekundę. Gdzie jest problem? Jak znaleźć Przyczyna nadmiernego?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>W architekturze mikrousług przy użyciu wzorca rozproszonego śledzenia

Odtworzenie przepływ komunikat IoT w różnych usługach, każda usługa powinna Propagacja *identyfikator korelacji* , który jednoznacznie identyfikuje wiadomości. Po zebraniu w scentralizowanym systemie identyfikatory korelacji umożliwiają stwierdzenie przepływu wiadomości. Ta metoda jest wywoływana [wzorzec rozproszonego śledzenia](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Aby zapewnić obsługę szerszego przyjmowania dla rozproszonego śledzenia, Współtworzenie Microsoft [W3C standardowe propozycję rozproszonego śledzenia](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Obsługa usługi IoT Hub

Po włączeniu obsługi rozproszonego śledzenia dla Centrum IoT Hub będzie zgodnie z tym przepływem:

1. Komunikat jest generowany na urządzeniu IoT.
1. Urządzenia IoT decyduje (dzięki chmurze), że ten komunikat powinien być przypisany kontekst śledzenia.
1. Dodaje zestaw SDK `tracestate` komunikat właściwości aplikacji, zawierający sygnatura czasowa tworzenia komunikatu.
1. Urządzenia IoT wysyła komunikat do Centrum IoT Hub.
1. Komunikat dociera do bramy Centrum IoT.
1. Szuka usługi IoT Hub `tracestate` w wiadomości właściwości aplikacji, a następnie sprawdza, czy jest w poprawnym formacie.
1. Jeśli tak, usługa IoT Hub generuje i rejestruje `trace-id` i `span-id` do dzienników diagnostycznych usługi Azure Monitor w kategorii `DiagnosticIoTHubD2C`.
1. Po zakończeniu przetwarzania komunikatów usługi IoT Hub generuje innego `span-id` i rejestruje go wraz z istniejącą `trace-id` kategorii `DiagnosticIoTHubIngress`.
1. Włączenie routingu wiadomości usługi IoT Hub zapisuje je do niestandardowego punktu końcowego i rejestruje innego `span-id` o takiej samej `trace-id` kategorii `DiagnosticIoTHubEgress`.
1. Powyższe kroki są powtarzane dla każdego komunikatu wygenerowany.

## <a name="public-preview-limits-and-considerations"></a>Ograniczenia publicznej wersji zapoznawczej i zagadnienia

- Propozycja standardu kontekst śledzenia W3C jest obecnie dostępna wersja robocza pracy.
- Obecnie tylko rozwoju języka obsługiwanych przez zestaw SDK klienta jest C.
- Możliwości chmury do urządzeń, bliźniacza reprezentacja jest niedostępna dla [usługi IoT Hub w warstwie podstawowa](iot-hub-scaling.md#basic-and-standard-tiers). Jednak usługi IoT Hub nadal będzie rejestrować do usługi Azure Monitor, jeśli nagłówek kontekst śledzenia prawidłowo złożone.
- W celu zapewnienia skutecznego działania, usługi IoT Hub będzie nakładają ograniczenia na szybkość rejestrowania, które mogą wystąpić w ramach rozproszonego śledzenia.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorca ogólnego rozproszonego śledzenia w mikrousługach, zobacz [wzorzec architektury Mikrousług: rozproszonego śledzenia](https://microservices.io/patterns/observability/distributed-tracing.html).
- Aby ustawić konfigurację, aby zastosować ustawienia rozproszonego śledzenia dużą liczbę urządzeń, zobacz [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę](iot-hub-auto-device-config.md).
- Aby dowiedzieć się więcej na temat usługi Azure Monitor, zobacz [co to jest usługa Azure Monitor?](../azure-monitor/overview.md).
