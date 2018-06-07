---
title: Portów i protokołów komunikacyjnych Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym artykule opisano protokołów komunikacyjnych obsługiwanych, urządzenia do chmury i chmury do urządzenia komunikację i numery portów, które muszą być otwarte.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 0fe3dd719877dac23410ff1ca00d559636a5ed60
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633006"
---
# <a name="reference---choose-a-communication-protocol"></a>Odwołanie — wybierz protokół komunikacyjny

Centrum IoT umożliwia skonfigurowanie urządzenia do używania następujące protokoły komunikacji po stronie urządzenia:

* [MQTT][lnk-mqtt]
* MQTT za pośrednictwem obiektów WebSocket
* [AMQP][lnk-amqp]
* AMQP za pośrednictwem obiektów WebSocket
* HTTPS

Informacje, jak te protokoły obsługują określone funkcje Centrum IoT, zobacz [wskazówki komunikację urządzenia do chmury] [ lnk-d2c-guidance] i [wskazówki dotyczące komunikacji chmury do urządzenia][lnk-c2d-guidance].

W poniższej tabeli przedstawiono ogólne zalecenia dotyczące wyboru protokołu:

| Protokół | Kiedy należy wybrać tego protokołu |
| --- | --- |
| MQTT <br> MQTT za pośrednictwem protokołu WebSocket |Użyj na wszystkich urządzeniach, które nie wymagają łączyć wielu urządzeń (każde z nich własne poświadczenia na urządzenie) za pośrednictwem tego samego połączenia TLS. |
| AMQP <br> Protokół AMQP za pośrednictwem protokołu WebSocket |Aby móc korzystać z połączenia Multipleksowanie między urządzeniami w systemie bram pola i w chmurze. |
| HTTPS |Zalecany w przypadku urządzeń, które nie obsługują innych protokołów. |

Po wybraniu użytkownika protokołu komunikacji po stronie urządzenia, należy wziąć pod uwagę następujące kwestie:

* **Wzorzec chmury do urządzenia**. Protokół HTTPS nie ma wydajnym sposobem wdrożenia serwera wypychania. Tak korzystając z protokołu HTTPS, urządzenia sondować Centrum IoT wiadomości chmury do urządzenia. Ta metoda jest nieefektywne dla Centrum IoT i urządzeniami. W obszarze bieżące wytyczne HTTPS każde urządzenie powinno sondowania wiadomości co 25 minut lub więcej. MQTT i protokołu AMQP obsługuje wypychania serwera podczas odbierania wiadomości chmury do urządzenia. Umożliwiają one natychmiastowego wypchnięć wiadomości z Centrum IoT na urządzeniu. Jeśli czas oczekiwania na dostarczenie jest istotny, MQTT lub AMQP są protokoły używane. Rzadko połączonych urządzeń HTTPS działa również.
* **Pole bram**. Korzystając z MQTT i HTTPS, nie można połączyć z wieloma urządzeniami (każde z nich własne poświadczenia na urządzenie) przy użyciu tego samego połączenia TLS. Aby uzyskać [pola scenariusze bramy] [ lnk-azure-gateway-guidance] wymagające jednego połączenia TLS między bramą pola i Centrum IoT dla każdego podłączonego urządzenia, te protokoły są nieoptymalne.
* **Mało zasobów urządzeń**. Biblioteki MQTT i HTTPS ma mniejszy wyświetlacz niż bibliotek protokołu AMQP. Tak Jeśli urządzenie ma ograniczone zasoby (na przykład mniejsza niż 1 MB pamięci RAM), te protokoły mogą być dostępne tylko implementacja protokołu.
* **Przechodzenie sieci**. Standardowy protokół AMQP korzysta z portu 5671 i MQTT nasłuchuje na porcie 8883. Korzystanie z tych portów może spowodować problemy w sieciach, które zostały zamknięte do protokołów innych niż HTTPS. Użyj MQTT przez protokół WebSockets, AMQP za pośrednictwem protokołu WebSockets lub HTTPS, w tym scenariuszu.
* **Rozmiar ładunku**. MQTT i AMQP są binarne protokołów, które powoduje ładunki mniejszych niż HTTPS.

> [!WARNING]
> Przy użyciu protokołu HTTPS, poszczególnych urządzeń należy sondować komunikatów chmury do urządzenia, co 25 minut lub więcej. Jednak podczas tworzenia, dopuszczalne jest do sondowania częściej niż co 25 minut.

## <a name="port-numbers"></a>Numery portów

Urządzenia mogą komunikować się z Centrum IoT na platformie Azure przy użyciu różnych protokołów. Zazwyczaj wybór protokół jest wymuszany przez określone wymagania rozwiązania. W poniższej tabeli wymieniono portów wychodzących, które musi być otwarte dla urządzenia można było użyć określonego protokołu:

| Protokół | Port |
| --- | --- |
| MQTT |8883 |
| MQTT za pośrednictwem obiektów WebSocket |443 |
| AMQP |5671 |
| AMQP za pośrednictwem obiektów WebSocket |443 |
| HTTPS |443 |

Po utworzeniu Centrum IoT w regionie Azure, Centrum IoT przechowuje ten sam adres IP przez czas ich istnienia tego Centrum IoT. Jednak jeśli Microsoft przenosi Centrum IoT na jednostkę skalowania różnych utrzymanie jakości usług, następnie przypisano nowego adresu IP.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat sposobu Centrum IoT implementuje ten protokół MQTT, zobacz [komunikacja z Centrum IoT przy użyciu protokołu MQTT][lnk-mqtt-support].

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
