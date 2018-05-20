---
title: Zrozumienie metod bezpośredniego Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — użyj bezpośredniego metody do wywołania kodu na urządzeniach z usługi aplikacji.
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: ''
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a35c88ac053b43d4a95b5bef92f3ebfb03567e2b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Informacje o funkcji i wywołanie metody bezpośrednio z Centrum IoT
Centrum IoT daje możliwość wywołania metod bezpośrednio na urządzeniach z chmury. Bezpośrednie metody reprezentują żądanie odpowiedź interakcji z urządzeniem podobna do wywołania HTTP w tym ich powodzenie lub niepowodzenie natychmiast (po limitu określonego przez użytkownika). Ta metoda jest przydatne w scenariuszach, w którym kursu natychmiastowego działania różni się w zależności od tego, czy urządzenie zostało mogą odpowiadać.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Każda metoda urządzenia jest przeznaczony dla jednego urządzenia. [Zadania] [ lnk-devguide-jobs] umożliwiają wywołania metod bezpośrednio na wielu urządzeniach i Zaplanuj wywołania metody odłączone urządzenia.

Każda osoba mająca **usługa połączyć** uprawnień w Centrum IoT mogą wywołać metodę na urządzeniu.

Bezpośrednie metody wykonaj wzorzec żądań i odpowiedzi i są przeznaczone do komunikacji, które wymagają natychmiastowego potwierdzenia ich wyników. Na przykład interakcyjne kontrolę nad urządzeniem, jak włączenie wentylatora.

Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] w razie wątpliwości między przy użyciu żądanej właściwości, bezpośrednie metod lub komunikaty chmury do urządzenia.

## <a name="method-lifecycle"></a>Cykl życia — metoda
Bezpośrednie metody są wdrożone na urządzeniu i może wymagać zero lub więcej danych wejściowych w ładunku metody, aby poprawnie utworzyć wystąpienia. Wywołaj metodę bezpośrednio za pomocą identyfikatora URI usługi połączonej (`{iot hub}/twins/{device id}/methods/`). Urządzenie odbiera metody bezpośredniego za pośrednictwem tematu MQTT specyficzne dla urządzenia (`$iothub/methods/POST/{method name}/`) lub za pośrednictwem protokołu AMQP łączy (`IoThub-methodname` i `IoThub-status` właściwości aplikacji). 

> [!NOTE]
> Po wywołaniu metody bezpośrednio na urządzeniu, nazwy i wartości właściwości mogą zawierać tylko US-ASCII drukowalnych alfanumeryczne, z wyjątkiem tych z następującego zestawu: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Bezpośrednie metody są synchroniczne i albo pomyślnie lub zakończyć się niepowodzeniem po upływie limitu czasu (domyślne: 30 sekund, można ustawić się do 3600 sekund). Bezpośrednie metody są przydatne w scenariuszach interakcyjne miejsce na urządzeniu działa tylko wtedy, gdy urządzenie jest online i odbierania poleceń. Na przykład włączenie światła przez telefon. W tych scenariuszach chcesz wyświetlić natychmiastowego powodzenie lub niepowodzenie, dlatego usługa w chmurze może działać na wynik tak szybko, jak to możliwe. Urządzenie może zwrócić niektóre treści wiadomości wyniku metody, ale nie jest wymagane dla metody to zrobić. Brak żadnej gwarancji, w kolejności lub dowolnego semantyki współbieżności na wywołania metody.

Bezpośrednie metody są HTTPS tylko po stronie chmury i MQTT lub AMQP po stronie urządzenia.

Ładunek dla metod żądań i odpowiedzi jest maksymalnie 128 KB dokument JSON.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Wywoływanie metody bezpośrednio z aplikacji zaplecza
### <a name="method-invocation"></a>Wywołanie metody
Bezpośrednie wywołania metod na urządzeniu są wywołania protokołu HTTPS, które obejmują:

* *URI* specyficzne dla urządzenia (`{iot hub}/twins/{device id}/methods/`)
* WPIS *— metoda*
* *Nagłówki* który zawiera autoryzacji, żądań Identyfikatora, typu zawartości i kodowania zawartości
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

Jest limit czasu w sekundach. Jeśli nie ustawiono limit czasu, domyślnie 30 sekund.

### <a name="response"></a>Odpowiedź
Aplikacja zaplecza odbiera odpowiedź, która obejmuje:

* *Kod stanu HTTP*, używany dla błędów pochodzących z Centrum IoT, łącznie z błędem 404 dla urządzeń nie jest obecnie połączony
* *Nagłówki* który zawiera element ETag, żądania Identyfikatora, typu zawartości i kodowania zawartości
* JSON *treści* w następującym formacie:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Zarówno `status` i `body` udostępnianym przez urządzenie i używane do odpowiedzi z kodem stanu własnych urządzeń i/lub opis.

## <a name="handle-a-direct-method-on-a-device"></a>Dojście metody bezpośrednio na urządzeniu
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Wywołanie metody
Urządzenia odbierania żądań metoda bezpośrednia na temat MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

Jednostkę urządzenie otrzyma znajduje się w następującym formacie:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metoda żądania są QoS 0.

#### <a name="response"></a>Odpowiedź
Urządzenie wysyła odpowiedzi `$iothub/methods/res/{status}/?$rid={request id}`, gdzie:

* `status` Właściwość jest stan wykonanie metody dostarczone przez urządzenie.
* `$rid` Właściwość jest identyfikator żądania z wywołania metody odebranych z Centrum IoT.

Treść jest ustawiana przez urządzenia i może być dowolnym stanie.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Wywołanie metody
Urządzenie odbiera żądania metoda bezpośrednia przez utworzenie łącza receive na adres `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Komunikat protokołu AMQP dociera łącze receive reprezentujący żądanie metody. Ten przewodnik zawiera następujące czynności:
* Właściwość Identyfikatora korelacji, który zawiera identyfikator żądania, które mają być przekazywane z powrotem odpowiadająca mu reakcja — metoda
* Właściwości aplikacji o nazwie `IoThub-methodname`, który zawiera nazwę wywoływanej metody
* Treść komunikatu protokołu AMQP zawierającego ładunek metody w formacie JSON

#### <a name="response"></a>Odpowiedź
Urządzenie tworzy łącze wysyłania zwraca odpowiedź metody na adres `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Odpowiedź metody jest zwracana wysyłania łącze i ma następującą postać:
* Właściwość Identyfikatora korelacji, który zawiera identyfikator żądania przekazano komunikat żądania — metoda
* Właściwości aplikacji o nazwie `IoThub-status`, który zawiera użytkownika dostarczony stan — metoda
* Treść komunikatu protokołu AMQP zawierająca odpowiedź metody w formacie JSON

## <a name="additional-reference-material"></a>Odwołanie dodatkowe materiały
Inne tematy referencyjne w Podręczniku dewelopera Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT] [ lnk-endpoints] opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji zarządzania i środowiska wykonawczego.
* [Ograniczenia przepustowości i przydziały] [ lnk-quotas] opisano przydziałów, które są stosowane i ograniczania przepustowości zachowanie można oczekiwać, gdy używasz Centrum IoT.
* [Zestawy Azure IoT urządzenia i usługi SDK] [ lnk-sdks] wymieniono języka różnych zestawów SDK, można użyć podczas opracowywania aplikacji usług i urządzeń, które współdziałają z Centrum IoT.
* [Język zapytań Centrum IoT urządzenia twins, zadań i rozsyłania wiadomości] [ lnk-query] opisuje język zapytań Centrum IoT można pobrać z Centrum IoT informacji o twins urządzenia i zadania.
* [Obsługa MQTT Centrum IoT] [ lnk-devguide-mqtt] zapewnia więcej informacji na temat Centrum IoT obsługi protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki
Po zapoznaniu bezpośredniego metod, mogą być zainteresowane w następującym artykule przewodnik dewelopera Centrum IoT:

* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, mogą być zainteresowane w następujących instrukcji Centrum IoT:

* [Użyj metody bezpośredniego][lnk-methods-tutorial]

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
