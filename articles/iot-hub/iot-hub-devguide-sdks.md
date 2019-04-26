---
title: Omówienie zestawów SDK usługi Azure IoT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje i łącza do różnych usługi Azure IoT urządzeniem i usługą zestawy SDK, które służy do tworzenia aplikacji dla urządzeń i aplikacji zaplecza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: e51313bbed21459de9f717edd123887caed18f4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400664"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Zrozumienia i użycia, zestawami SDK Azure IoT Hub

Istnieją dwie kategorie software development Kit (SDK) do pracy z usługą IoT Hub:

* **Zestawy SDK urządzeń IoT w Centrum** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT przy użyciu klienta modułu lub klienta urządzenia. Te aplikacje wysyłanie danych telemetrycznych do Centrum IoT i opcjonalnie odbierać komunikaty, zadania, metody lub aktualizacji bliźniaczej reprezentacji z Centrum IoT hub.  Umożliwia także klienta modułu do tworzenia [modułów](../iot-edge/iot-edge-modules.md) dla [środowisko uruchomieniowe usługi Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Zestawów SDK usługi IoT Hub Service** Włącz tworzenie zaplecza aplikacji do zarządzania Centrum IoT hub i opcjonalnie przesyłania wiadomości, planowania zadań, wywoływanie metod bezpośrednich lub wysyłania aktualizacji żądaną właściwość do Twojego urządzenia IoT lub modułów.

Ponadto firma Microsoft również zapewnia zestaw SDK do pracy z [usługi Device Provisioning](../iot-dps/about-iot-dps.md).
* **Inicjowanie obsługi administracyjnej zestawów SDK urządzeń** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT do komunikowania się z usługą Device Provisioning.

* **Zestawy SDK usługi aprowizacji** umożliwiają tworzenie aplikacji wewnętrznej bazy danych do zarządzania Twojej rejestracji w usłudze Device Provisioning.

Dowiedz się więcej o [korzyści związanych z tworzeniem, za pomocą usługi Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Zgodność sprzętu i platformy systemu operacyjnego

Platformy obsługiwane przez zestawy SDK można znaleźć w [pomocy technicznej systemu Azure IoT SDKs platformy](iot-hub-device-sdk-platform-support.md).

Aby uzyskać więcej informacji na temat zestawu SDK zgodność z określonego urządzenia, zobacz [certyfikatu platformy Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/) lub poszczególnych repozytorium.

## <a name="azure-iot-hub-device-sdks"></a>Zestawy SDK urządzeń Azure IoT Hub

Urządzenia Microsoft Azure IoT SDK zawiera kod, który ułatwia tworzenie aplikacji, nawiązać połączenie, które są zarządzane przez usługi Azure IoT Hub.

Usługa Azure IoT Hub device zestawu SDK dla platformy .NET: 

* Pobieranie z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Przestrzeń nazw jest Microsoft.Azure.Devices.Clients, która zawiera klientów urządzeń w Centrum IoT (DeviceClient, ModuleClient).
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odwołania do modułu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Usługa Azure IoT Hub device zestawu SDK dla języka C (ANSI C - C99):

* Zainstaluj z [polecenia apt-get, MBED, środowisku IDE Arduino lub z systemem iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)
* [Skompiluj zestaw SDK urządzeń C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Odwołania do modułu](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Przenoszenie zestawu SDK języka C dla innych platform](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentacja dla deweloperów](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) informacji na temat między — kompilowanie, uruchamianie na różnych platformach, itd.
* [Usługi Azure information zużycie zasobów zestawu SDK C usługi IoT Hub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Usługa Azure IoT Hub device zestawu SDK dla języka Java: 

* Dodaj do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projektu
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Odwołania do modułu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Usługa Azure IoT Hub device zestawu SDK dla środowiska Node.js: 

* Zainstaluj z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odwołania do modułu](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Usługa Azure IoT Hub device zestawu SDK dla języka Python: 

* Zainstaluj z [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* Dokumentacja interfejsu API: zobacz [dokumentacja interfejsu API języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Usługa Azure IoT Hub device zestawu SDK dla systemu iOS: 

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Przykłady](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Dokumentacja interfejsu API: zobacz [dokumentacja interfejsu API języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Zestawy SDK usług Azure IoT Hub

Zestawy SDK usług Azure IoT zawiera kod, aby ułatwić tworzenie aplikacji wchodzących w interakcję bezpośrednio z usługą IoT Hub do zarządzania urządzeniami i zabezpieczeń.

Usługa Azure IoT Hub zestawu SDK usługi dla platformy .NET:

* Pobieranie z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Przestrzeń nazw jest Microsoft.Azure.Devices, która zawiera klientów usługi Centrum IoT (RegistryManager, ServiceClients).
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub zestawu SDK usługi Java: 

* Dodaj do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projektu
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

## <a name="microsoft-azure-provisioning-sdks"></a>Inicjowanie obsługi administracyjnej platformy Microsoft Azure w zestawy SDK

**Zestawami SDK Microsoft Azure aprowizacji** pozwoli na aprowizację urządzenia do usługi IoT Hub przy użyciu [usługi Device Provisioning](../iot-dps/about-iot-dps.md).

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka C#:

* Pobieranie z [zestawu SDK urządzenia](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) i [zestawu SDK usługi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) z pakietów NuGet.
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka C:

* Zainstaluj z [polecenia apt-get, MBED, środowisku IDE Arduino lub z systemem iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka Java:

* Dodaj do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projektu
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla środowiska Node.js:

* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Pobierz [zestawu SDK urządzenia](https://badge.fury.io/js/azure-iot-provisioning-device) i [zestawu SDK usługi](https://badge.fury.io/js/azure-iot-provisioning-service) z poziomu narzędzia npm

Aprowizacja usług i urządzeń zestawy SDK platformy Azure dla języka Python:

* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* Pobierz [zestawu SDK urządzenia](https://pypi.org/project/azure-iot-provisioning-device-client/) i [zestawu SDK usługi](https://pypi.org/project/azure-iothub-provisioningserviceclient/) PIP

## <a name="next-steps"></a>Kolejne kroki

Usługa Azure IoT SDKs udostępniają zestaw narzędzi, aby pomóc w rozwoju:
* [narzędzia iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): narzędziem wiersza polecenia dla wielu platform, aby pomóc zdiagnozować problemy związane z połączenia z usługą IoT Hub.
* [Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): aplikacji pulpitu Windows, aby nawiązać połączenie z Centrum IoT Hub.

Dokumentacja odpowiednie dotyczące programowania przy użyciu zestawów SDK usługi Azure IoT:
* Dowiedz się więcej o [sposobu zarządzania łącznością i niezawodną obsługę komunikatów](iot-hub-reliability-features-in-sdks.md) za pomocą zestawów SDK Centrum IoT.
* Dowiedz się więcej o sposobie [programowanie dla platform urządzeń przenośnych](iot-hub-how-to-develop-for-mobile-devices.md) takich jak systemy iOS i Android.
* [Pomoc techniczna platformy zestawu SDK usługi IoT platformy Azure](iot-hub-device-sdk-platform-support.md)


Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md)
* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md)
* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md)
* [Dokumentacja interfejsu API REST Centrum IoT](/rest/api/iothub/)
