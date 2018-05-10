## <a name="automatic-device-management"></a>Zarządzanie urządzeniami automatyczne
Automatyczne zarządzanie urządzeniami w Centrum IoT Azure pozwala zautomatyzować wiele powtarzających się i złożonych zadań związanych z zarządzaniem floty dużych urządzenia za pośrednictwem całości ich cyklów. Z automatycznego zarządzania urządzeniami docelowa zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją, a let Centrum IoT aktualizowania urządzeń, które pochodzą do zakresu.  Składa się z [konfiguracji automatycznego urządzeń](/articles/iot-hub/iot-hub-auto-device-config.md) i [wdrożeń automatycznych krawędzi IoT](/articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Krawędź IoT Azure umożliwia oparte na chmurze wdrożenia usług platformy Azure i kod określonego rozwiązania do urządzenia lokalnego. Urządzenia brzegowe IoT agregować dane z innych urządzeń do wykonywania przetwarzania danych i analiza przed wysłaniem danych do chmury. Aby uzyskać więcej informacji zobacz [Azure IoT krawędzi](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent krawędzi IoT
Część odpowiedzialnych za wdrażanie i monitorowanie modułów środowiska uruchomieniowego IoT krawędzi.

## <a name="iot-edge-device"></a>Urządzenie usługi IoT Edge
Urządzenia brzegowe IoT ma krawędzi IoT środowiska uruchomieniowego zainstalowany i są oznaczone jako "Urządzenie brzegowe IoT" w szczegółowych informacji o urządzeniu. Dowiedz się, jak [wdrażanie Azure IoT Edge na urządzeniu symulowane w systemie Linux — Podgląd](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Krawędź IoT wdrażania automatycznego
Automatyczne wdrożenie krawędzi IoT konfiguruje zestaw docelowy urządzenia IoT brzegowe do uruchomienia zestawu krawędzi IoT modułów. Dla każdego wdrożenia stale zapewnia wszystkie urządzenia spełniające warunek jego docelowym są uruchomione określony zestaw modułów, nawet jeśli jest to nowe urządzenia są tworzone lub warunek docelowy są modyfikowane. Poszczególne urządzenia IoT krawędzi odbiera tylko najwyższy priorytet wdrożenia którego warunku docelowy spełnia. Dowiedz się więcej o [wdrażania automatycznego krawędzi IoT](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifest rozmieszczenia krawędzi IoT
Dokument Json zawierający informacje do skopiowania w co najmniej jeden IoT urządzenia brzegowe twin(s) modułu wdrażania zestaw modułów, tras i skojarzone modułu żądana właściwości.

## <a name="iot-edge-gateway-device"></a>Urządzenia IoT bramy
Urządzenie brzegowe IoT z podrzędne. Podrzędne urządzenie może zostać krawędzi IoT lub nie IoT urządzenia.

## <a name="iot-edge-hub"></a>Centrum IoT krawędzi
Część odpowiedzialny za moduł do komunikacji, nadrzędne (kierunku Centrum IoT) i podrzędne (poza Centrum IoT) środowiska uruchomieniowego krawędzi IoT komunikacji. 

## <a name="iot-edge-leaf-device"></a>Urządzenia IoT liścia
Urządzenie brzegowe IoT z żadnego urządzenia podrzędne. 

## <a name="iot-edge-module"></a>Moduł krawędzi IoT
Moduł krawędzi IoT to kontener Docker, który można wdrożyć na urządzenia brzegowe IoT. Wykonywania określonych zadań, takich jak wprowadzania komunikatu z urządzenia, przekształcanie wiadomości lub wysyłania komunikatu do Centrum IoT. Komunikuje się z innymi modułami, a wysyła dane do środowiska wykonawczego IoT krawędzi. [Zrozumieć wymagania i narzędzi do tworzenia modułów krawędzi IoT](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Krawędź IoT modułu tożsamości
Rekord w rejestrze tożsamości modułu Centrum IoT wyszczególnieniem istnienia i zabezpieczeń poświadczenia do użycia przez moduł uwierzytelniania za pomocą Centrum edge lub Centrum IoT.

## <a name="iot-edge-module-image"></a>Obraz modułu krawędzi IoT
Obraz docker, który jest używany przez środowisko uruchomieniowe krawędzi IoT można utworzyć wystąpienia modułu.

## <a name="iot-edge-module-twin"></a>Dwie modułu krawędzi IoT
Dokument Json utrwalone w Centrum IoT, która przechowuje informacje o stanie w przypadku modułu.

## <a name="iot-edge-priority"></a>Priorytet krawędzi IoT
Gdy dwa wdrożenia krawędzi IoT tego samego urządzenia, zostanie zastosowany wdrożenie o wyższym priorytecie. Jeśli dwa wdrożenia mają ten sam priorytet, wdrożenie z nowszej Data utworzenia zostanie zastosowany. Dowiedz się więcej o [priorytet](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe krawędzi IoT zawiera wszystko, co Microsoft dystrybuuje instalowanego na urządzenia IoT. Znajduje się ona agenta krawędzi, Centrum Edge i narzędzie CTL krawędzi.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Krawędź IoT ustawić modułów do jednego urządzenia
Operacja kopiuje zawartość manifestu krawędzi IoT na jednym urządzeniu "dwie modułu. Interfejs API jest rodzajowy "zastosowanie konfiguracji", który po prostu przyjmuje manifest IoT krawędź jako danych wejściowych.

## <a name="iot-edge-target-condition"></a>Warunek docelowy krawędzi IoT
We wdrożeniu krawędzi IoT warunek docelowy jest żadnych warunek typu Boolean na tagów twins urządzeń wybierz urządzenia, wdrażania, np. "tag.environment = prod". Warunek docelowy jest stale obliczenia obejmują nowe urządzenia, które spełniają wymagania lub usuwać urządzenia, które nie. Dowiedz się więcej o [target warunku](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)