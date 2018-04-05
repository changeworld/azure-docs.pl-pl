> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Wprowadzenie

W [Rozpoczynanie pracy z Centrum IoT urządzenia twins][lnk-twin-tutorial], wiesz, jak ustawić urządzeniami przy użyciu metadanych *tagi*. Odebrano warunki urządzenia z aplikacjami urządzenia przy użyciu *zgłosił właściwości*i następnie te informacje przy użyciu języka przypominającego SQL.

W tym samouczku opisano sposób użycia dwie urządzenia *żądanego właściwości* i *zgłosił właściwości* celu zdalnego konfigurowania aplikacji dla urządzeń. Zgłoszone oraz odpowiednie właściwości w dwie urządzenia Włącz konfigurację wieloetapowych aplikację dla urządzeń i widoczność stan tej operacji dla wszystkich urządzeń. Można znaleźć więcej informacji na temat roli konfiguracji urządzeń w [omówienie zarządzania urządzeniami z Centrum IoT][lnk-dm-overview].

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Na wysokim poziomie za pomocą urządzenia twins umożliwia zaplecza rozwiązania określić odpowiednią konfigurację dla zarządzanych urządzeń, zamiast wysyłać określonych poleceń. Urządzenie jest odpowiedzialny za konfigurowanie najlepszy sposób, aby zaktualizować konfigurację (ważne w scenariuszach IoT, których warunki określonego urządzenia wpłynąć negatywnie na natychmiast wykonać określonych poleceń), podczas raportowania stale bieżący stan i możliwości warunki błędów procesu aktualizacji. Ten wzorzec jest urządzeń do zarządzania dużych zestawów urządzeń, ponieważ udostępnia zaplecza rozwiązania pełny wgląd stanu procesu konfiguracji na wszystkich urządzeniach.

> [!TIP]
> W scenariuszach, w którym urządzenia są kontrolowane w sposób większej liczby interaktywnych (na przykład włączenie wentylator z aplikacji kontrolowane przez użytkownika), należy rozważyć użycie [bezpośrednie metody][lnk-methods].

W tym samouczku zaplecza rozwiązania umożliwia zmianę konfiguracji telemetrii urządzenia docelowego, aby urządzenia aplikacji ma zastosowanie aktualizacji konfiguracji. Na przykład aktualizacji konfiguracji będzie wymagających Uruch moduł oprogramowania, w tym samouczku symuluje z opóźnieniem proste.

Zaplecze rozwiązania przechowuje konfigurację w odpowiednich właściwościach dwie urządzenia w następujący sposób:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

Ponieważ konfiguracje mogą zostać obiektów złożonych, są przypisane unikatowe identyfikatory (skróty lub [identyfikatorów GUID][lnk-guid]).


Aplikacji urządzenia raporty bieżącej konfiguracji dublowania żądanej właściwości **telemetryConfig** we właściwościach zgłoszone:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Uwaga jak opisane **telemetryConfig** ma dodatkowe właściwości **stanu**, używana do raportowania stanu procesu aktualizacji konfiguracji.

Po odebraniu nowego wymaganą konfiguracją aplikacji urządzenia raportów oczekujących konfiguracji przez zmianę stanu:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Następnie w późniejszym czasie, aplikacji urządzenia raporty powodzenie lub niepowodzenie tej operacji, aktualizując właściwości. Zaplecze rozwiązania może zapytać o stan procesu konfiguracji na wszystkich urządzeniach w dowolnym momencie.

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji symulowane urządzenie, który odbiera aktualizacje konfiguracji z zaplecza rozwiązania, a następnie raportuje wiele aktualizacji jako *zgłosił właściwości* w konfiguracji zaktualizować procesu.
* Tworzenie aplikacji zaplecza, aktualizuje odpowiednią konfigurację urządzenia, a następnie za pośrednictwem procesu aktualizacji konfiguracji.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
