---
title: Omówienie usługi Azure IoT Hub metod bezpośrednich | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Użyj metod bezpośrednich do wywołania kodu na urządzeniach z usługi app Service.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: 881262816fc8bd634b7f577fd05aa0c8c062e4ca
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126528"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Zrozumienie i wywoływanie metod bezpośrednich z usługi IoT Hub
Usługa IoT Hub zapewnia możliwość wywoływanie metod bezpośrednich na urządzeniach z chmury. Metody bezpośrednie reprezentują interakcji "żądanie-odpowiedź", za pomocą urządzenia, które są podobne do wywołania HTTP, w tym ich powodzenie lub niepowodzenie natychmiast (po określonej przez użytkownika limit czasu). To podejście jest przydatne w scenariuszach, gdzie kurs natychmiastowego działania różni się w zależności od tego, czy urządzenie zostało przygotowane.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Każda metoda urządzenia jest przeznaczony dla jednego urządzenia. [Zadania] [ lnk-devguide-jobs] umożliwiają wywoływanie metod bezpośrednich na wielu urządzeniach i Zaplanuj wywołania metody odłączone urządzenia.

Każda osoba mająca **połączenie z usługą** uprawnienia w Centrum IoT Hub może wywołać metodę na urządzeniu.

Bezpośrednie metody wzorca odpowiedź na żądanie i są przeznaczone do celów komunikacji, które wymagają natychmiastowego potwierdzenia ich wyników. Na przykład interaktywne kontroli nad urządzeniem, jak włączenie kibicem.

Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] w razie wątpliwości między za pomocą odpowiednich właściwości bezpośrednie metody lub komunikatów z chmury do urządzeń.

## <a name="method-lifecycle"></a>Cykl życia — metoda
Metody bezpośrednie są implementowane na urządzeniu i mogą wymagać zero lub więcej danych wejściowych w ładunku metody, aby prawidłowo utworzyć wystąpienie. Wywoływanie metody bezpośredniej za pomocą identyfikatora URI przeznaczonych dla usługi (`{iot hub}/twins/{device id}/methods/`). Urządzenie odbiera metod bezpośrednich za pośrednictwem tematu MQTT specyficznych dla urządzenia (`$iothub/methods/POST/{method name}/`) lub za pośrednictwem protokołu AMQP łączy (`IoThub-methodname` i `IoThub-status` właściwości aplikacji). 

> [!NOTE]
> Po wywołaniu metody bezpośredniej o urządzeniu nazwy i wartości właściwości mogą zawierać tylko US-ASCII drukowania litery, cyfry, z wyjątkiem tych w następującym zestawie: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Bezpośrednie metody są synchroniczne i albo powodzenie lub Niepowodzenie po upływie limitu czasu (domyślne: 30 sekund do ustawienia zapasowej 3600 sekund). Metody bezpośrednie są przydatne w scenariuszach interaktywne, którego urządzenia, która będzie działać tylko wtedy, gdy urządzenie jest w trybie online i odbieranie poleceń. Na przykład włączenie światła przez telefon. W tych scenariuszach chcesz zobaczyć natychmiastowego powodzenie lub niepowodzenie, więc usługę w chmurze może działać na wynik tak szybko, jak to możliwe. Urządzenie może zwrócić niektóre treści wiadomości, w wyniku metody, ale nie jest wymagana dla metody to zrobić. Brak żadnej gwarancji, w kolejności lub dowolnym semantyki współbieżności na wywołania metody.

Bezpośrednie metody to HTTPS-only z po stronie chmury i protokołu MQTT lub AMQP po stronie urządzenia.

Ładunek metody żądania i odpowiedzi jest maksymalnie 128 KB dokument JSON.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Wywoływanie metody bezpośredniej o w aplikacji zaplecza
### <a name="method-invocation"></a>Wywołanie metody
Wywołań metod bezpośrednich na urządzeniu są wywołania protokołu HTTPS, które obejmują:

* *Identyfikator URI żądania* specyficzne dla urządzenia wraz z [wersji interfejsu API](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* WPIS *— metoda*
* *Nagłówki* , zawierają autoryzację, żądania, Identyfikatora, typu zawartości i kodowania zawartości
* Przezroczysty JSON *treści* w następującym formacie:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Limit czasu jest w ciągu kilku sekund. Jeśli nie ustawiono limit czasu, jego wartość domyślna to 30 sekund.

#### <a name="example"></a>Przykład

Poniżej zamieszczono przykład barebone przy użyciu `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Odpowiedź
Aplikacja wewnętrzna odbiera odpowiedź, która obejmuje:

* *Kod stanu HTTP*, używany błędy pochodzące z usługi IoT Hub, w tym błąd 404 dla urządzeń nie jest obecnie połączony
* *Nagłówki* który zawiera element ETag, żądania, Identyfikatora, typu zawartości i kodowania zawartości
* JSON *treści* w następującym formacie:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Zarówno `status` i `body` udostępnianym przez urządzenie oraz umożliwia odpowiedzi z kodem stanu dla urządzenia i/lub opis.

### <a name="method-invocation-for-iot-edge-modules"></a>Wywołania metody moduły usługi IoT Edge
Wywoływanie metod bezpośrednich za pomocą modułu identyfikator jest obsługiwany w języku C# w wersji zapoznawczej zestawu SDK (dostępne [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.16.0-preview-004)).

W tym celu należy użyć `ServiceClient.InvokeDeviceMethodAsync()` metody i przekaż `deviceId` i `moduleId` jako parametry.

## <a name="handle-a-direct-method-on-a-device"></a>Dojście metody bezpośredniej o urządzeniu
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Wywołanie metody
Urządzenia otrzymują żądań metody bezpośredniej na temat protokołu MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

Treść, która otrzymaniem przez nie znajduje się w następującym formacie:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metoda żądania są QoS 0.

#### <a name="response"></a>Odpowiedź
Urządzenie wysyła odpowiedzi `$iothub/methods/res/{status}/?$rid={request id}`, gdzie:

* `status` Właściwość ma stan podczas wykonywania metody dostarczone przez urządzenie.
* `$rid` Właściwość jest identyfikator żądania z wywołania metody odebranych z Centrum IoT.

Jednostka została ustawiona przez urządzenia i może być dowolny stan.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Wywołanie metody
Urządzenie odbiera żądania metody bezpośredniej, tworząc link odbioru na adres `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Komunikat protokołu AMQP dociera przy użyciu linku receive, reprezentujący żądanie metody. Zawiera ona następujące informacje:
* Właściwość ID korelacji, który zawiera identyfikator żądania, który powinien być przekazywany z powrotem odpowiadająca mu reakcja — metoda
* Właściwości aplikacji o nazwie `IoThub-methodname`, który zawiera nazwę metody wywoływanej
* Treść komunikatu protokołu AMQP, zawierający ładunku metody w formacie JSON

#### <a name="response"></a>Odpowiedź
Urządzenie tworzy łącze wysyłania do zwracania odpowiedzi metody na adres `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Odpowiedź metody jest zwracany w momencie wysyłania łącza i ma następującą postać:
* Właściwość ID korelacji, który zawiera identyfikator żądania przekazywane w komunikacie żądania — metoda
* Właściwości aplikacji o nazwie `IoThub-status`, która zawiera użytkownika podany stan metody
* Odpowiedź metody jako plik JSON zawierający treść komunikatu protokołu AMQP

## <a name="additional-reference-material"></a>Dodatkowe materiały
Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub] [ lnk-endpoints] w tym artykule opisano różne punkty końcowe, które każde Centrum IoT hub udostępnia dla operacji zarządzania i środowiska wykonawczego.
* [Przydziału i ograniczanie przepływności] [ lnk-quotas] opisano przydziały, które są stosowane i zachowanie ograniczania przepływności można oczekiwać, gdy używają usługi IoT Hub.
* [Usługa Azure IoT usługi zestawy SDK urządzeń i] [ lnk-sdks] Wyświetla język różnych zestawów SDK, można użyć podczas tworzenia aplikacji usług i urządzeń, które współdziałają z usługą IoT Hub.
* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości] [ lnk-query] opisuje język zapytań usługi IoT Hub, można użyć, aby pobrać informacje z usługi IoT Hub o bliźniaczych reprezentacji urządzeń i zadań.
* [Obsługa protokołu MQTT Centrum IoT] [ lnk-devguide-mqtt] zawiera więcej informacji na temat obsługi usługi IoT Hub dla protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki
Teraz wiesz jak używanie metod bezpośrednich, mogą być zainteresowane w następującym artykule przewodnik dla deweloperów usługi IoT Hub:

* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, może być zainteresowany następującego samouczka usługi IoT Hub:

* [Używanie metod bezpośrednich][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
