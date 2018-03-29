> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Wprowadzenie

W [Rozpoczynanie pracy z Centrum IoT urządzenia twins][lnk-twin-tutorial], wiesz, jak ustawić metadane urządzenia z pomocą zaplecza rozwiązania *tagi*, raport warunków urządzenia z aplikacjami urządzenia przy użyciu *zgłosił właściwości*oraz badanie tych informacji przy użyciu języka przypominającego SQL.

Z tego samouczka, dowiesz się jak używać dwie urządzenia *żądanego właściwości* wraz z *zgłosił właściwości*, w celu zdalnego konfigurowania aplikacji dla urządzeń. W szczególności w tym samouczku przedstawiono sposób zgłaszania dwie urządzenia oraz odpowiednie właściwości Włącz konfigurację wieloetapowych aplikację dla urządzeń i widoczności do zaplecza rozwiązania stanu tej operacji dla wszystkich urządzeń. Można znaleźć więcej informacji na temat roli konfiguracji urządzeń w [omówienie zarządzania urządzeniami z Centrum IoT][lnk-dm-overview].

Na wysokim poziomie za pomocą urządzenia twins umożliwia zaplecza rozwiązania określić odpowiednią konfigurację dla zarządzanych urządzeń, zamiast wysyłać określonych poleceń. To powoduje przełączenie urządzenia odpowiedzialnym za konfigurowanie najlepszy sposób, aby zaktualizować konfigurację (ważne w scenariuszach IoT, których warunki określonego urządzenia wpłynąć negatywnie na natychmiast wykonać określonych poleceń), podczas raportowania stale do zaplecza rozwiązania bieżący stan i potencjalnych błędów procesu aktualizacji. Ten wzorzec jest urządzeń do zarządzania dużych zestawów urządzeń, ponieważ umożliwia ona zaplecza rozwiązania mieć pełny wgląd w stan procesu konfiguracji na wszystkich urządzeniach.

> [!NOTE]
> W scenariuszach, w którym urządzenia są kontrolowane w sposób większej liczby interaktywnych (Włącz wentylator z aplikacji kontrolowane przez użytkownika), należy rozważyć użycie [bezpośrednie metody][lnk-methods].
> 
> 

W tym samouczku zaplecza rozwiązania umożliwia zmianę konfiguracji telemetrii urządzenia docelowego i, w związku z tym, że aplikacji urządzenia jest zgodna z procesu wieloetapowych, aby zastosować aktualizację konfiguracji (na przykład wymaganie oprogramowania modułu ponownego uruchomienia komputera, którym znajduje się ten samouczek symuluje z opóźnieniem prosty).

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

> [!NOTE]
> Ponieważ konfiguracje mogą zostać obiektów złożonych, są przypisane unikatowe identyfikatory (skróty lub [identyfikatorów GUID][lnk-guid]) aby uprościć ich porównania.
> 
> 

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

Po odebraniu nowego wymaganą konfiguracją aplikacji urządzenia raportów oczekujących konfiguracji, zmieniając informacje:

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

Następnie w późniejszym czasie, aplikacji urządzenia raporty powodzenie lub niepowodzenie tej operacji przez modyfikowanie właściwości powyżej.
Należy zwrócić uwagę, jak zaplecza rozwiązania jest w stanie, w dowolnym momencie można zbadać stanu procesu konfiguracji na wszystkich urządzeniach.

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji symulowane urządzenie, który odbiera aktualizacje konfiguracji z zaplecza rozwiązania, a następnie raportuje wiele aktualizacji jako *zgłosił właściwości* w konfiguracji zaktualizować procesu.
* Tworzenie aplikacji zaplecza, aktualizuje odpowiednią konfigurację urządzenia, a następnie za pośrednictwem procesu aktualizacji konfiguracji.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
