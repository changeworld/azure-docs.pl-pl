---
title: Łączność urządzeń w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono podstawowe pojęcia dotyczące łączności urządzenia w centralnym IoT Azure
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dc9fe144c2258f33ce59c61ce63c15835cc3fa53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628338"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Łączność urządzenia w centralnym IoT Azure

W tym artykule przedstawiono podstawowe pojęcia dotyczące łączności urządzenie w Centrum Microsoft Azure IoT.

Każde urządzenie, które łączy do aplikacji Azure IoT centralnej łączy się z [punkty końcowe](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) które są udostępniane przez Centrum IoT Azure IoT centralnej używa. Centrum IoT umożliwia skalowalne, bezpieczne i niezawodne połączenia z podłączonych urządzeń.

## <a name="sdk-support"></a>Obsługa w zestawie SDK

Oferta zestawy SDK urządzenia Azure Najprostszym sposobem można zaimplementować kod na urządzeniach, które łączy do aplikacji Azure IoT centralnej. Dostępne są następujące urządzenia zestawów SDK:

- [Azure IoT SDK dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK dla środowiska Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Usługa Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

Każde urządzenie łączy, przy użyciu połączenia unikatowy ciąg, który identyfikuje urządzenia. Urządzenie można połączyć tylko z Centrum IoT, w której jest zarejestrowana. Podczas tworzenia rzeczywistego urządzenia w aplikacji Azure IoT centralnej, ta aplikacja generuje ciąg połączenia do użycia.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje zestawu SDK i łączności Centrum IoT

Cała komunikacja urządzenie z Centrum IoT korzysta z następujących opcji łączności Centrum IoT:

- [Urządzenia do chmury do obsługi komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Twins urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

W poniższej tabeli przedstawiono sposób mapowania Azure IoT centralnej funkcji urządzenia do Centrum IoT funkcje:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Pomiar: Telemetrii | Urządzenia do chmury do obsługi komunikatów |
| Właściwości urządzenia | Dwie urządzenia zgłoszone właściwości |
| Ustawienia | Dwie urządzenia żądanego i podać właściwości |

Aby dowiedzieć się więcej o korzystaniu z zestawów SDK urządzenia, zobacz jedną z następujących artykułów na przykład kodu:

- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
- [Podłącz urządzenie Pi malina do aplikacji Azure IoT centralnej](howto-connect-raspberry-pi-python.md)
- [Podłącz urządzenie zestawu DevDiv do aplikacji Azure IoT centralnej](howto-connect-devkit.md).

Poniższe wideo przedstawia podstawowe informacje o rzeczywistym urządzenie podłączone do usługi Azure IoT centralnej:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protokoły

Zestawy SDK urządzenia obsługuje następujące protokoły sieciowe dotyczące połączenia z Centrum IoT:

- MQTT
- AMQP
- HTTPS

Uzyskać informacji o tych protokołów różnica i wskazówki na wybranie jednej, zobacz [wybierz protokół komunikacyjny](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Jeśli urządzenie nie można użyć dowolnego z obsługiwanych protokołów, można użyć Azure IoT krawędzi protokołu konwersji. Krawędź IoT obsługuje inne scenariusze analizy na krawędzi w celu przerzucenia przetwarzania do krawędzi z aplikacji Azure IoT centralna.

## <a name="security"></a>Bezpieczeństwo

Wszystkie dane wymieniane między urządzeniami i sieci centralnej IoT Azure są szyfrowane. Centrum IoT uwierzytelnia każde żądanie z urządzenia, które łączy punkty końcowe Centrum IoT skierowane do urządzenia. Aby uniknąć wymiany poświadczeń przez sieć, urządzenie używa podpisanych tokenów do uwierzytelniania. Aby uzyskać więcej informacji zobacz, [kontrolować dostęp do Centrum IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Obecnie urządzeń łączących się Azure IoT centralnej musi używać tokeny sygnatury dostępu Współdzielonego. Certyfikaty X.509 nie są obsługiwane dla urządzeń łączących się Azure IoT centralnej.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już dotyczące łączności z urządzenia w centralnym IoT Azure, w tym miejscu są Sugerowane następne kroki:

- [Przygotuj i podłącz urządzenie zestaw deweloperski](howto-connect-devkit.md)
- [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
