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
ms.openlocfilehash: aec2126369f45a89050dbd8b2d3cae7e00ccb8ed
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>W zrozumieniu i użytkowaniu zestawów SDK IoT Azure

Istnieją trzy kategorie software development Kit (SDK) do pracy z Centrum IoT:

* **Zestawy SDK urządzenia** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT. Te aplikacje wysyłania danych telemetrycznych do Centrum IoT i opcjonalnie odbierać komunikaty z Centrum IoT.

* **Usługa SDK** pozwala na zarządzanie Centrum IoT i opcjonalnie wysyłanie komunikatów do urządzenia IoT.

* **Usługa Azure IoT krawędzi** umożliwia tworzenie bram dla urządzeń, które nie używają jednego z obsługiwanych protokołów. Bramy może także przetwarzać komunikatów na krawędzi.

Zestawy SDK są dostarczane do obsługi wielu języków programowania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Urządzenia IoT Azure SDK

Urządzenia Microsoft Azure IoT zestawów SDK zawiera kod, który ułatwia tworzenie urządzenia i aplikacje, które nawiązać połączenie i są zarządzane przez usługi Azure IoT Hub.

Następujące urządzenia Azure IoT SDK są dostępne do pobrania z witryny GitHub:

* [Azure urządzenia IoT zestawu SDK dla platformy .NET][lnk-dotnet-device-sdk]
* [Azure urządzenia IoT zestawu SDK dla języka Java][lnk-java-device-sdk]
* [Azure urządzenia IoT zestawu SDK dla środowiska Node.js][lnk-node-device-sdk]
* [Azure urządzenia IoT zestawu SDK dla języka Python][lnk-python-device-sdk]
* [Azure urządzenia IoT zestawu SDK dla języka C] [ lnk-c-device-sdk] napisane w języku C ANSI (C99) dla przenośność i zgodność wielu platform. Istnieją dwie urządzenia klienta biblioteki C niskiego poziomu **iothub_client** i **serializator**.

> [!NOTE]
> Zobacz plik readme repozytoriów GitHub informacji o używaniu języka i menedżerów specyficzne dla platformy pakietu do zainstalowania plików binarnych oraz zależności na komputerze deweloperskim.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Zgodność platformy, sprzętu i systemu operacyjnego

Aby uzyskać więcej informacji na temat zgodności zestawu SDK z określonego urządzenia, zobacz [Azure certyfikowane dla katalogu urządzenia IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Zestawy Azure IoT usługi SDK

Zestawy SDK usługi Azure IoT zawiera kod, aby ułatwić tworzenie aplikacji współpracujące bezpośrednio z Centrum IoT do zarządzania urządzeniami i zabezpieczeń.

Następująca usługa Azure IoT zestawów SDK są dostępne do pobrania z witryny GitHub:

* [IoT usługi Azure SDK dla platformy .NET][lnk-dotnet-service-sdk]
* [IoT usługi Azure SDK dla języka Java][lnk-java-service-sdk]
* [IoT usługi Azure SDK dla środowiska Node.js][lnk-node-service-sdk]
* [IoT usługi Azure SDK dla języka Python][lnk-python-service-sdk]
* [IoT usługi Azure SDK dla języka C][lnk-c-service-sdk]

> [!NOTE]
> Zobacz plik readme repozytoriów GitHub informacji o używaniu języka i menedżerów specyficzne dla platformy pakietu do zainstalowania plików binarnych oraz zależności na komputerze deweloperskim.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Usługa Azure IoT krawędzi zawiera infrastruktury i modułów w celu utworzenia rozwiązania IoT bramy. Można rozszerzyć krawędzi IoT można utworzyć bramy dostosowane do sytuacja end-to-end.

Możesz pobrać [Azure IoT krawędzi] [ lnk-iot-edge] z usługi GitHub.

## <a name="online-api-reference-documentation"></a>Online dokumentacji interfejsu API

Poniższa lista zawiera łącza do trybu online dokumentacji interfejsu API urządzenia Azure IoT, usługi i bibliotek bramy:

* [Internet of Things (IoT) .NET][lnk-dotnet-ref]
* [Azure urządzenia IoT zestawu SDK dla języka Java][lnk-java-ref]
* [IoT usługi Azure SDK dla języka Java][lnk-java-service-ref]
* [Azure urządzenia IoT zestawu SDK dla środowiska Node.js][lnk-node-ref]
* [IoT usługi Azure SDK dla środowiska Node.js][lnk-node-service-ref]
* [Azure urządzenia IoT zestawu SDK dla języka C][lnk-c-ref]
* [Centrum IoT REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Następne kroki

Inne tematy dokumentacji, w tym przewodniku deweloperów Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT][lnk-devguide-endpoints]
* [Język zapytań Centrum IoT urządzenia twins, zadań i rozsyłania wiadomości][lnk-devguide-query]
* [Przydziały i ograniczenia przepustowości][lnk-devguide-quotas]
* [Obsługa MQTT Centrum IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
