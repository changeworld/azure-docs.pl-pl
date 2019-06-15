---
title: Usługa Azure IoT Hub komunikacji protokoły i porty | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym artykule opisano protokołów komunikacyjnych obsługiwanych, komunikacji urządzenia do chmury i z chmury do urządzeń i numery portów, które muszą być otwarte.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 7082ebc4ca3066f84ca9790797cfa04e437f78a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626183"
---
# <a name="reference---choose-a-communication-protocol"></a>Odwołania — wybór protokołu komunikacyjnego

Usługa IoT Hub umożliwia urządzeniom używane następujące protokoły na potrzeby komunikacji po stronie urządzenia:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT za pośrednictwem obiektów WebSocket
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP za pośrednictwem obiektów WebSocket
* HTTPS

Aby uzyskać informacje o współpracy tych protokołów określonych funkcji usługi IoT Hub, zobacz [wskazówki dotyczące komunikacji z urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) i [wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Poniższa tabela zawiera ogólne zalecenia dotyczące wybranego protokołu:

| Protocol | Kiedy należy wybrać ten protokół |
| --- | --- |
| MQTT <br> MQTT za pośrednictwem protokołu WebSocket |Użyj tej opcji na wszystkich urządzeniach, które nie wymagają nawiązać połączenie za pośrednictwem tego samego połączenia TLS między wieloma urządzeniami (każdy z jej własnych poświadczeń na urządzenie). |
| AMQP <br> Protokół AMQP przez WebSocket |Aby móc korzystać z połączenia Multipleksowanie między urządzeniami w systemie bramy pola i w chmurze. |
| HTTPS |Zastosowanie w przypadku urządzeń, które nie obsługują innych protokołów. |

W przypadku wybrania usługi protokołu komunikacji po stronie urządzenia, należy wziąć pod uwagę następujące kwestie:

* **Wzorzec chmury do urządzenia**. Protokół HTTPS nie ma efektywny sposób, aby zaimplementować wypychania przez serwer. Jako takie korzystając z protokołu HTTPS, urządzenia sondować usługę IoT Hub dla komunikatów z chmury do urządzeń. To podejście jest nieefektywne dla Centrum IoT i urządzeń. W obszarze bieżące wytyczne HTTPS każde urządzenie należy wykonać sondowanie komunikatów, co 25 minut lub dłużej. MQTT i protokół AMQP obsługuje wypychania przez serwer podczas odbierania komunikatów z chmury do urządzeń. Umożliwiają one bezpośredniego wypchnięcia wiadomości z usługi IoT Hub do urządzenia. Jeśli opóźnienie dostarczania jest istotna, MQTT lub AMQP są protokoły używane. Rzadko połączonych urządzeń protokół HTTPS działa również.

* **Pole bram**. Korzystając z protokołu MQTT i HTTPS, nie można połączyć wiele urządzeń (każdy z jej własnych poświadczeń na urządzenie) przy użyciu tego samego połączenia TLS. Aby uzyskać [pola scenariusze bramy](iot-hub-devguide-endpoints.md#field-gateways) wymagające jednego połączenia TLS między bramy w terenie i IoT Hub dla każdego urządzenia połączone, te protokoły są suboptymalny.

* **Mało zasobów urządzeń**. Biblioteki protokołu MQTT i HTTPS ma mniejszy wyświetlacz niż biblioteki protokołu AMQP. Jako takie Jeśli urządzenie ma ograniczone zasoby (na przykład, mniejsze niż 1 MB pamięci RAM), te protokoły może być tylko dostępne implementacji protokołu.

* **Sieci przechodzenie**. Standardowy protokół AMQP korzysta z portu 5671 i MQTT nasłuchuje na porcie 8883. Użycie tych portów mogą powodować problemy w sieciach, które zostały zamknięte do protokołów innych niż HTTPS. Użyj protokołu MQTT za pośrednictwem funkcji WebSockets, AMQP za pośrednictwem funkcji WebSockets lub HTTPS w tym scenariuszu.

* **Rozmiar ładunku**. MQTT i protokół AMQP są binarne protokołów, które powodują w bardziej zwarty ładunków niż HTTPS.

> [!WARNING]
> Przy użyciu protokołu HTTPS, każde urządzenie powinno sondowania dla komunikatów z chmury do urządzeń, co 25 minut lub dłużej. Jednak podczas tworzenia aplikacji, dopuszczalne jest sondowania częściej niż co 25 minut.

## <a name="port-numbers"></a>Numery portów

Urządzenia mogą komunikować się z usługą IoT Hub na platformie Azure przy użyciu różnych protokołów. Zazwyczaj wybranego protokołu są sterowane określonych wymagań rozwiązania. Poniższa tabela zawiera listę portów wychodzących, które muszą być otwarte dla urządzenia można było korzystać z określonym protokołem:

| Protocol | Port |
| --- | --- |
| MQTT |8883 |
| MQTT za pośrednictwem obiektów WebSocket |443 |
| AMQP |5671 |
| AMQP za pośrednictwem obiektów WebSocket |443 |
| HTTPS |443 |

Po utworzeniu Centrum IoT hub w regionie świadczenia usługi Azure IoT hub przechowuje ten sam adres IP okres istnienia tego Centrum IoT hub. Jednak jeśli Microsoft przenosi usługi IoT hub na jednostkę skalowania innego, aby zachować jakość usług, jego jest przypisywany nowy adres IP.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat sposobu IoT Hub zaimplementowano protokołu MQTT, zobacz [komunikacja z Centrum IoT hub przy użyciu protokołu MQTT](iot-hub-mqtt-support.md).
