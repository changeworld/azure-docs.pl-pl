---
title: Zrozumienie zestawów SDK usługi Azure IoT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje i łącza do różnych Azure IoT urządzeń i usług zestawów SDK, które służy do tworzenia aplikacji zaplecza i aplikacji dla urządzeń.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2380e72083ec0c9453db991be5fbcc89310cee17
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>W zrozumieniu i użytkowaniu zestawów SDK Centrum IoT Azure

Istnieją dwie kategorie software development Kit (SDK) do pracy z Centrum IoT:

* **Zestawy SDK urządzenia** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT. Te aplikacje wysyłania danych telemetrycznych do Centrum IoT i opcjonalnie odbierać komunikaty, zadania, metody lub dwie aktualizacje z Centrum IoT.

* **Usługa SDK** umożliwiają zarządzanie Centrum IoT i opcjonalnie wysyłać wiadomości, planowanie zadań, wywołania metod bezpośrednich lub wysyłanie aktualizacji żądanej właściwości z urządzeniami IoT.

Dowiedz się więcej o zaletach tworzenie przy użyciu zestawów SDK IoT Azure [tutaj][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Urządzenia IoT Azure SDK

Urządzenia Microsoft Azure IoT zestawów SDK zawiera kod, który ułatwia tworzenie urządzenia i aplikacje, które nawiązać połączenie i są zarządzane przez usługi Azure IoT Hub.

Azure IoT Hub urządzenie zestawu SDK dla platformy .NET: 
* Zainstaluj z [Nuget][lnk-nuget-csharp-device]
* [Kod źródłowy][lnk-dotnet-sdk]
* [Dokumentacja interfejsu API][lnk-dotnet-ref]

Azure urządzenia IoT Hub SDK C: napisane w języku C ANSI (C99) dla przenośność i zgodność wielu platform
* Zainstaluj z [stanie get, MBED, Arduino IDE lub Nuget][lnk-c-package]
* [Kod źródłowy][lnk-c-sdk]
* [Dokumentacja interfejsu API][lnk-c-ref]

Azure IoT Hub urządzenie zestawu SDK dla języka Java: 
* Dodaj do [Maven] [ lnk-maven-device] projektu
* [Kod źródłowy][lnk-java-sdk]
* [Dokumentacja interfejsu API][lnk-java-ref]

Azure IoT Hub urządzenie zestawu SDK dla środowiska Node.js: 
* Zainstaluj z [npm][lnk-npm-device]
* [Kod źródłowy][lnk-node-sdk]
* [Dokumentacja interfejsu API][lnk-node-ref]

Azure IoT Hub urządzenie zestawu SDK dla języka Python: 
* Zainstaluj z [pip][lnk-pip-device]
* [Kod źródłowy][lnk-python-sdk]

> [!NOTE]
> Zobacz plik readme repozytoriów GitHub informacji o używaniu języka i menedżerów specyficzne dla platformy pakietu do zainstalowania plików binarnych oraz zależności na komputerze deweloperskim.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Zgodność platformy, sprzętu i systemu operacyjnego

Aby uzyskać więcej informacji na temat zgodności zestawu SDK z określonego urządzenia, zobacz [Azure certyfikowane dla katalogu urządzenia IoT] [ lnk-certified] lub poszczególnych repozytorium.

## <a name="azure-iot-service-sdks"></a>Zestawy Azure IoT usługi SDK

Zestawy SDK usługi Azure IoT zawiera kod, aby ułatwić tworzenie aplikacji współpracujące bezpośrednio z Centrum IoT do zarządzania urządzeniami i zabezpieczeń.

Centrum IoT usługi Azure SDK dla platformy .NET:
* Pobierz z [Nuget][lnk-nuget-csharp-service]
* [Kod źródłowy][lnk-dotnet-sdk]
* [Dokumentacja interfejsu API][lnk-dotnet-service-ref]

Centrum IoT usługi Azure SDK dla języka Java: 
* Dodaj do [Maven] [ lnk-maven-service] projektu
* [Kod źródłowy][lnk-java-sdk]
* [Dokumentacja interfejsu API][lnk-java-service-ref]

Centrum IoT usługi Azure SDK dla środowiska Node.js: 
* Pobierz z [npm][lnk-npm-service]
* [Kod źródłowy][lnk-node-sdk]
* [Dokumentacja interfejsu API][lnk-node-service-ref]

Centrum IoT usługi Azure SDK dla języka Python: 
* Pobierz z [pip][lnk-pip-service]
* [Kod źródłowy][lnk-python-sdk]

Centrum IoT usługi Azure SDK dla C: 
* Pobierz z [stanie get, MBED, Arduino IDE lub Nuget][lnk-c-package]
* [Kod źródłowy][lnk-c-sdk]

> [!NOTE]
> Zobacz plik readme repozytoriów GitHub informacji o używaniu języka i menedżerów specyficzne dla platformy pakietu do zainstalowania plików binarnych oraz zależności na komputerze deweloperskim.


## <a name="next-steps"></a>Kolejne kroki

Inne tematy dokumentacji, w tym przewodniku deweloperów Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT][lnk-devguide-endpoints]
* [Język zapytań Centrum IoT urządzenia twins, zadań i rozsyłania wiadomości][lnk-devguide-query]
* [Przydziały i ograniczenia przepustowości][lnk-devguide-quotas]
* [Obsługa MQTT Centrum IoT][lnk-devguide-mqtt]
* [Dokumentacja interfejsu API REST Centrum IoT][lnk-rest-ref]

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
[lnk-node-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service

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
