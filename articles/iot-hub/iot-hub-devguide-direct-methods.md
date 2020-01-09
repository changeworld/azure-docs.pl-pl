---
title: Informacje na temat metod bezpośrednich IoT Hub platformy Azure | Microsoft Docs
description: Przewodnik dla deweloperów — Używanie metod bezpośrednich do wywoływania kodu na urządzeniach z poziomu aplikacji usługi.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.openlocfilehash: f4125aae954519beead99db45fc8a35264d5731e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429266"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Zrozumienie i wywoływanie metod bezpośrednich z IoT Hub

IoT Hub umożliwia wywoływanie metod bezpośrednich na urządzeniach z chmury. Metody bezpośrednie reprezentują interakcję typu żądanie-odpowiedź z urządzeniem podobnym do wywołania HTTP, które powiodło się lub natychmiast kończą się niepowodzeniem (po upływie limitu czasu określonego przez użytkownika). Takie podejście jest przydatne w scenariuszach, w których kurs natychmiastowego działania różni się w zależności od tego, czy urządzenie mogło reagować.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Każda metoda urządzenia odwołuje się do jednego urządzenia. [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md) pokazuje, jak umożliwić wywoływanie metod bezpośrednich na wielu urządzeniach i planowanie wywoływania metody dla odłączonych urządzeń.

Każda osoba z uprawnieniami do **łączenia usług** w IoT Hub może wywołać metodę na urządzeniu.

Metody bezpośrednie są zgodne ze wzorcem żądanie-odpowiedź i są przeznaczone do komunikacji wymagającej natychmiastowego potwierdzenia ich wyniku. Na przykład interaktywna kontrola nad urządzeniem, taka jak Włączanie wentylatorów.

Zapoznaj się z tematem [wskazówki dotyczące komunikacji między chmurą i urządzeniem](iot-hub-devguide-c2d-guidance.md) , jeśli istnieją wątpliwości dotyczące używania żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzenia.

## <a name="method-lifecycle"></a>Cykl życia metody

Metody bezpośrednie są implementowane na urządzeniu i mogą wymagać zero lub więcej danych wejściowych w ładunku metody, aby można było poprawnie utworzyć wystąpienie. Metodę bezpośrednią wywołuje się za pośrednictwem identyfikatora URI opartego na usłudze (`{iot hub}/twins/{device id}/methods/`). Urządzenie odbiera bezpośrednie metody za pośrednictwem tematu MQTT (`$iothub/methods/POST/{method name}/`) lub linków AMQP (właściwości aplikacji `IoThub-methodname` i `IoThub-status`). 

> [!NOTE]
> Po wywołaniu metody bezpośredniej na urządzeniu nazwy właściwości i wartości mogą zawierać tylko znaki alfanumeryczne drukowalne US-ASCII, z wyjątkiem któregokolwiek z następujących zestawów: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Metody bezpośrednie są synchroniczne i kończą się powodzeniem lub niepowodzeniem po upływie limitu czasu (domyślnie: 30 sekund, settable do 300 sekund). Metody bezpośrednie są przydatne w scenariuszach interaktywnych, w których urządzenie ma działać, jeśli i tylko wtedy, gdy urządzenie jest w trybie online i pobiera polecenia. Na przykład włączenie światła od telefonu. W tych scenariuszach chcesz zobaczyć natychmiastowe sukces lub niepowodzenie, aby usługa w chmurze mogła działać na skutek tak szybko, jak to możliwe. Urządzenie może zwrócić część treści komunikatu w wyniku metody, ale nie jest wymagana do wykonania metody. Nie ma gwarancji związanych z porządkowaniem ani semantyką współbieżności dla wywołań metod.

Metody bezpośrednie są tylko HTTPS — tylko po stronie chmury i MQTT lub AMQP po stronie urządzenia.

Ładunek dla żądań metod i odpowiedzi jest dokumentem JSON o wysokości do 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Wywoływanie metody bezpośredniej z poziomu aplikacji zaplecza

Teraz Wywołaj metodę bezpośrednią z aplikacji zaplecza.

### <a name="method-invocation"></a>Wywołanie metody

Bezpośrednie wywołania metod na urządzeniu to wywołania HTTPS, które składają się z następujących elementów:

* *Identyfikator URI żądania* charakterystyczny dla urządzenia wraz z [wersją interfejsu API](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *Metoda* post

* *Nagłówki* zawierające autoryzację, identyfikator żądania, typ zawartości i kodowanie zawartości.

* Przezroczysty *treść* JSON w następującym formacie:

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

Limit czasu w sekundach. Jeśli limit czasu nie jest ustawiony, wartość domyślna to 30 sekund.

#### <a name="example"></a>Przykład

Poniżej znajduje się przykład barebone z użyciem `curl`. 

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

Aplikacja zaplecza odbiera odpowiedź składającą się z następujących elementów:

* *Kod stanu HTTP*, który jest używany w przypadku błędów pochodzących z IoT Hub, w tym błąd 404 dla urządzeń, które nie są obecnie połączone.

* *Nagłówki* , które zawierają element ETag, identyfikator żądania, typ zawartości i kodowanie zawartości.

* *Treść* JSON w następującym formacie:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Zarówno `status`, jak i `body` są dostarczane przez urządzenie i używane do odpowiadania na własny kod stanu i/lub opis urządzenia.

### <a name="method-invocation-for-iot-edge-modules"></a>Wywołanie metody dla modułów IoT Edge

Wywoływanie metod bezpośrednich przy użyciu identyfikatora modułu jest obsługiwane w [zestawie SDK klienta C# usługi IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

W tym celu należy użyć metody `ServiceClient.InvokeDeviceMethodAsync()` i przekazać `deviceId` i `moduleId` jako parametry.

## <a name="handle-a-direct-method-on-a-device"></a>Obsługa metody bezpośredniej na urządzeniu

Przyjrzyjmy się sposobom obsługi metody bezpośredniej na urządzeniu IoT.

### <a name="mqtt"></a>MQTT

Poniższa sekcja dotyczy protokołu MQTT.

#### <a name="method-invocation"></a>Wywołanie metody

Urządzenia odbierają żądania metody bezpośredniej w temacie MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. Liczba subskrypcji na urządzenie jest ograniczona do 5. Dlatego zaleca się, aby nie subskrybować każdej metody bezpośredniej osobno. Zamiast tego Rozważ zasubskrybowanie `$iothub/methods/POST/#` a następnie przefiltrowanie dostarczonych komunikatów na podstawie żądanych nazw metod.

Treść odbierana przez urządzenie jest w następującym formacie:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Żądania metod są QoS 0.

#### <a name="response"></a>Odpowiedź

Urządzenie wysyła odpowiedzi do `$iothub/methods/res/{status}/?$rid={request id}`, gdzie:

* Właściwość `status` to stan dostarczony przez urządzenie do wykonania metody.

* Właściwość `$rid` jest IDENTYFIKATORem żądania z wywołania metody otrzymanego z IoT Hub.

Treść jest ustawiana przez urządzenie i może być dowolnym stanem.

### <a name="amqp"></a>AMQP

Poniższa sekcja dotyczy protokołu AMQP.

#### <a name="method-invocation"></a>Wywołanie metody

Urządzenie odbiera bezpośrednie żądania metod przez utworzenie linku odbierającego na adres `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Komunikat AMQP dociera do linku odbierającego, który reprezentuje żądanie metody. Ten temat zawiera następujące sekcje:

* Właściwość identyfikatora korelacji, która zawiera identyfikator żądania, który powinien zostać przesłany z powrotem do odpowiedniej metody odpowiedzi.

* Właściwość aplikacji o nazwie `IoThub-methodname`, która zawiera nazwę wywoływanej metody.

* Treść komunikatu AMQP zawierającego ładunek metody jako kod JSON.

#### <a name="response"></a>Odpowiedź

Urządzenie tworzy link do wysyłania, aby zwrócić odpowiedź metody na adres `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Odpowiedź metody jest zwracana w łączu nadawczym i ma następującą strukturę:

* Właściwość identyfikatora korelacji, która zawiera identyfikator żądania przesłany w komunikacie żądania metody.

* Właściwość aplikacji o nazwie `IoThub-status`, która zawiera stan metody dostarczonej przez użytkownika.

* Treść komunikatu AMQP zawierającego metodę odpowiedź jako kod JSON.

## <a name="additional-reference-material"></a>Dodatkowe materiały referencyjne

Inne tematy referencyjne w przewodniku dewelopera IoT Hub obejmują:

* [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) opisują różne punkty końcowe, które są uwidaczniane przez każde Centrum IoT Hub na potrzeby operacji w czasie wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisują przydziały, które mają zastosowanie, oraz zachowanie ograniczania, które należy oczekiwać przy użyciu IoT Hub.

* [Zestawy SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md) wymieniają różne zestawy SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współpracują z usługą IoT Hub.

* [IoT Hub język zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md) zawiera opis języka zapytań IoT Hub, za pomocą którego można pobrać informacje z IoT Hub o urządzeniu bliźniaczych reprezentacji i zadaniach.

* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi IoT Hub protokołu MQTT.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak korzystać z metod bezpośrednich, ale może Cię zainteresować następujące IoT Hub artykułu przewodnika dla deweloperów:

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Jeśli chcesz wypróbować niektóre koncepcje opisane w tym artykule, możesz chcieć zainteresować się w następującym samouczku IoT Hub:

* [Korzystanie z metod bezpośrednich](quickstart-control-device-node.md)
* [Zarządzanie urządzeniami za pomocą narzędzi Azure IoT Tools for VS Code](iot-hub-device-management-iot-toolkit.md)
