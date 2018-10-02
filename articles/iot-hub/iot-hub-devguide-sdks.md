---
title: Omówienie zestawów SDK usługi Azure IoT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje i łącza do różnych usługi Azure IoT urządzeniem i usługą zestawy SDK, które służy do tworzenia aplikacji dla urządzeń i aplikacji zaplecza.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 1eeb0afdd5ffcbe00357914d6a98c8d0b3d452ec
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017963"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Zrozumienia i użycia, zestawami SDK Azure IoT Hub

Istnieją trzy kategorie software development Kit (SDK) do pracy z usługą IoT Hub:

* **Zestawy SDK urządzeń** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT przy użyciu klienta modułu lub klienta urządzenia. Te aplikacje wysyłanie danych telemetrycznych do Centrum IoT i opcjonalnie odbierać komunikaty, zadania, metody lub aktualizacji bliźniaczej reprezentacji z Centrum IoT hub.  Umożliwia także klienta modułu do tworzenia [modułów](../iot-edge/iot-edge-modules.md) dla [środowisko uruchomieniowe usługi Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Zestawy SDK usług** umożliwiają zarządzanie Centrum IoT i opcjonalnie wysyłanie komunikatów, planowania zadań, wywoływanie metod bezpośrednich lub wysyłania aktualizacji żądaną właściwość do Twojego urządzenia IoT lub modułów.

* **Zestawy SDK aprowizacji urządzeń** pozwoli na aprowizację urządzenia do usługi IoT Hub przy użyciu [usługi Device Provisioning](../iot-dps/about-iot-dps.md).

Dowiedz się więcej o [korzyści związanych z tworzeniem, za pomocą usługi Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Zestawy SDK platformy Azure w zakresie urządzeń IoT

Urządzenia Microsoft Azure IoT SDK zawiera kod, który ułatwia tworzenie urządzenia i aplikacje, nawiązać połączenie, które są zarządzane przez usługi Azure IoT Hub.

Usługa Azure IoT Hub device zestawu SDK dla platformy .NET: 

* Zainstaluj z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odwołania do modułu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Usługa Azure IoT Hub device zestawu SDK dla języka C, napisany w ANSI C (C99) do przenoszenia i zgodności dla wielu platform:

* Zainstaluj z [polecenia apt-get, MBED, środowisku IDE Arduino lub Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)
* [Dokumentacja interfejsu API](https://azure.github.io/azure-iot-sdk-c/index.html)
* [Odwołania do modułu](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Usługa Azure IoT Hub device zestawu SDK dla języka Java: 

* Dodaj do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projektu
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Odwołania do modułu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

Usługa Azure IoT Hub device zestawu SDK dla środowiska Node.js: 

* Zainstaluj z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odwołania do modułu](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Usługa Azure IoT Hub device zestawu SDK dla języka Python: 

* Zainstaluj z [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* Dokumentacja interfejsu API: zobacz [dokumentacja interfejsu API języka C](https://azure.github.io/azure-iot-sdk-c/index.html)

Usługa Azure IoT Hub device zestawu SDK dla systemu iOS: 

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Przykłady](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Dokumentacja interfejsu API: zobacz [dokumentacja interfejsu API języka C](https://azure.github.io/azure-iot-sdk-c/index.html)

> [!NOTE]
> Zobacz pliki readme w repozytoriach usługi GitHub, informacji o używaniu języka i menedżerów pakietów specyficznych dla platformy, aby zainstalować pliki binarne i zależności na komputerze deweloperskim.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Zgodność sprzętu i platformy systemu operacyjnego

Platformy obsługiwane przez zestawy SDK można znaleźć w [pomocy technicznej systemu Azure IoT SDKs platformy](iot-hub-device-sdk-platform-support.md).

Aby uzyskać więcej informacji na temat zestawu SDK zgodność z określonego urządzenia, zobacz [certyfikatu platformy Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsuite.com/) lub poszczególnych repozytorium.

## <a name="azure-iot-service-sdks"></a>Zestawy SDK usługi Azure IoT

Zestawy SDK usług Azure IoT zawiera kod, aby ułatwić tworzenie aplikacji wchodzących w interakcję bezpośrednio z usługą IoT Hub do zarządzania urządzeniami i zabezpieczeń.

Usługa Azure IoT Hub zestawu SDK usługi dla platformy .NET:

* Pobieranie z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub zestawu SDK usługi Java: 

* Dodaj do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
) projektu
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Usługa Azure IoT Hub zestawu SDK usługi dla środowiska Node.js: 

* Pobieranie z [npm](https://www.npmjs.com/package/azure-iothub)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Usługa IoT Hub usługa zestawu SDK dla języka Python: 

* Pobieranie z [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)

Usługi platformy Azure IoT Hub zestawu SDK dla języka C: 

* Pobieranie z [polecenia apt-get, MBED, środowisku IDE Arduino lub Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)

Usługa Azure IoT Hub zestawu SDK usługi dla systemu iOS: 

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Przykłady](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Zobacz pliki readme w repozytoriach usługi GitHub, informacji o używaniu języka i menedżerów pakietów specyficznych dla platformy, aby zainstalować pliki binarne i zależności na komputerze deweloperskim.

## <a name="device-provisioning-sdks"></a>Zestawy SDK aprowizacji urządzeń

**Zestawami SDK Microsoft Azure aprowizacji** pozwoli na aprowizację urządzenia do usługi IoT Hub przy użyciu [usługi Device Provisioning](../iot-dps/about-iot-dps.md).

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka C#:

* [Aprowizacja urządzenia klienckiego zestawu SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Zestaw SDK klienta usługi aprowizowania](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka Java:

* [Aprowizacja urządzenia klienckiego zestawu SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [Zestaw SDK klienta usługi aprowizowania](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla środowiska Node.js:

* [Aprowizacja urządzenia klienckiego zestawu SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Zestaw SDK klienta usługi aprowizowania](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka Python:

* [Aprowizacja urządzenia klienckiego zestawu SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Zestaw SDK klienta usługi aprowizowania](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka C:

* [Aprowizacja urządzenia klienckiego zestawu SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Zestaw SDK klienta usługi aprowizowania](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>Kolejne kroki

Usługa Azure IoT SDKs udostępniają zestaw narzędzi, aby pomóc w rozwoju:
* [narzędzia iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): narzędziem wiersza polecenia dla wielu platform, aby pomóc zdiagnozować problemy związane z połączenia z usługą IoT Hub.
* [Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): aplikacji pulpitu Windows, aby nawiązać połączenie z Centrum IoT Hub.

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md)
* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md)
* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md)
* [Dokumentacja interfejsu API REST Centrum IoT](/rest/api/iothub/)
* [Pomoc techniczna platformy zestawu SDK usługi Azure IoT](iot-hub-device-sdk-platform-support.md)