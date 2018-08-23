---
title: Omówienie zestawów SDK usługi Azure IoT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje i łącza do różnych usługi Azure IoT urządzeniem i usługą zestawy SDK, które służy do tworzenia aplikacji dla urządzeń i aplikacji zaplecza.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: dobett
ms.openlocfilehash: ad1ce768ea5be2356f141d10a53ea0166546a2b7
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "42060678"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Zrozumienia i użycia, zestawami SDK Azure IoT Hub

Istnieją dwie kategorie software development Kit (SDK) do pracy z usługą IoT Hub:

* **Zestawy SDK urządzeń** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT. Te aplikacje wysyłanie danych telemetrycznych do Centrum IoT i opcjonalnie odbierać komunikaty, zadania, metody lub aktualizacji bliźniaczej reprezentacji z Centrum IoT hub.

* **Zestawy SDK usług** umożliwiają zarządzanie Centrum IoT i opcjonalnie wysyłanie komunikatów, planowania zadań, wywoływanie metod bezpośrednich lub wysyłać aktualizacje żądanej właściwości urządzeń IoT.

Dowiedz się więcej o korzyściach związanych z tworzeniem, za pomocą usługi Azure IoT SDKs [tutaj][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Zestawy SDK platformy Azure w zakresie urządzeń IoT

Urządzenia Microsoft Azure IoT SDK zawiera kod, który ułatwia tworzenie urządzenia i aplikacje, nawiązać połączenie, które są zarządzane przez usługi Azure IoT Hub.

Usługa Azure IoT Hub device zestawu SDK dla platformy .NET: 
* Zainstaluj z [Nuget][lnk-nuget-csharp-device]
* [Kod źródłowy][lnk-dotnet-sdk]
* [Dokumentacja interfejsu API][lnk-dotnet-ref]

Usługa Azure IoT Hub device zestawu SDK dla języka C: napisane w ANSI C (C99) do przenoszenia i zgodności dla wielu platform
* Zainstaluj z [polecenia apt-get, MBED, środowisku IDE Arduino lub Nuget][lnk-c-package]
* [Kod źródłowy][lnk-c-sdk]
* [Dokumentacja interfejsu API][lnk-c-ref]

Usługa Azure IoT Hub device zestawu SDK dla języka Java: 
* Dodaj do [Maven] [ lnk-maven-device] projektu
* [Kod źródłowy][lnk-java-sdk]
* [Dokumentacja interfejsu API][lnk-java-ref]

Usługa Azure IoT Hub device zestawu SDK dla środowiska Node.js: 
* Zainstaluj z [npm][lnk-npm-device]
* [Kod źródłowy][lnk-node-sdk]
* [Dokumentacja interfejsu API][lnk-node-ref]

Usługa Azure IoT Hub device zestawu SDK dla języka Python: 
* Zainstaluj z [pip][lnk-pip-device]
* [Kod źródłowy][lnk-python-sdk]

Usługa Azure IoT Hub device zestawu SDK dla systemu iOS: 
* Zainstaluj z [CocoaPod][lnk-cocoa-device]
* [Przykłady][lnk-ios-sample]

> [!NOTE]
> Zobacz pliki readme w repozytoriach usługi GitHub, informacji o używaniu języka i menedżerów pakietów specyficznych dla platformy, aby zainstalować pliki binarne i zależności na komputerze deweloperskim.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Zgodność sprzętu i platformy systemu operacyjnego

Platformy obsługiwane przez zestawy SDK można znaleźć w tym [dokumentu](iot-hub-device-sdk-platform-support.md).
Aby uzyskać więcej informacji na temat zestawu SDK zgodność z określonego urządzenia, zobacz [certyfikatu platformy Azure dla IoT — wykaz urządzeń] [ lnk-certified] lub poszczególnych repozytorium.

## <a name="azure-iot-service-sdks"></a>Zestawy SDK usługi Azure IoT

Zestawy SDK usług Azure IoT zawiera kod, aby ułatwić tworzenie aplikacji wchodzących w interakcję bezpośrednio z usługą IoT Hub do zarządzania urządzeniami i zabezpieczeń.

Usługa Azure IoT Hub zestawu SDK usługi dla platformy .NET:
* Pobieranie z [Nuget][lnk-nuget-csharp-service]
* [Kod źródłowy][lnk-dotnet-sdk]
* [Dokumentacja interfejsu API][lnk-dotnet-service-ref]

Azure IoT Hub zestawu SDK usługi Java: 
* Dodaj do [Maven] [ lnk-maven-service] projektu
* [Kod źródłowy][lnk-java-sdk]
* [Dokumentacja interfejsu API][lnk-java-service-ref]

Usługa Azure IoT Hub zestawu SDK usługi dla środowiska Node.js: 
* Pobieranie z [npm][lnk-npm-service]
* [Kod źródłowy][lnk-node-sdk]
* [Dokumentacja interfejsu API][lnk-node-service-ref]

Usługa IoT Hub usługa zestawu SDK dla języka Python: 
* Pobieranie z [pip][lnk-pip-service]
* [Kod źródłowy][lnk-python-sdk]

Usługi platformy Azure IoT Hub zestawu SDK dla języka C: 
* Pobieranie z [polecenia apt-get, MBED, środowisku IDE Arduino lub Nuget][lnk-c-package]
* [Kod źródłowy][lnk-c-sdk]

Usługa Azure IoT Hub zestawu SDK usługi dla systemu iOS: 
* Zainstaluj z [CocoaPod][lnk-cocoa-service]
* [Przykłady][lnk-ios-sample]

> [!NOTE]
> Zobacz pliki readme w repozytoriach usługi GitHub, informacji o używaniu języka i menedżerów pakietów specyficznych dla platformy, aby zainstalować pliki binarne i zależności na komputerze deweloperskim.



## <a name="next-steps"></a>Kolejne kroki

Usługa Azure IoT SDKs udostępniają zestaw narzędzi, aby pomóc w rozwoju:
* [narzędzia iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): narzędziem wiersza polecenia dla wielu platform, aby pomóc zdiagnozować problemy związane z połączenia z usługą IoT Hub.
* [Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): aplikacji pulpitu Windows, aby nawiązać połączenie z Centrum IoT Hub.

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub][lnk-devguide-endpoints]
* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości][lnk-devguide-query]
* [Limity przydziału i ograniczenia przepustowości][lnk-devguide-quotas]
* [Obsługa protokołu MQTT Centrum IoT][lnk-devguide-mqtt]
* [Dokumentacja interfejsu API REST Centrum IoT][lnk-rest-ref]
* [Pomoc techniczna platformy zestawu SDK usługi Azure IoT](iot-hub-device-sdk-platform-support.md)

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
[lnk-cocoa-device]: https://cocoapods.org/pods/AzureIoTHubClient
[lnk-ios-sample]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-cocoa-service]: https://cocoapods.org/pods/AzureIoTHubServiceClient