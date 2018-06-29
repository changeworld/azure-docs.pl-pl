---
title: Rozpoczynanie pracy z Centrum IoT Azure modułu tożsamości i moduł dwie (C) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć moduł tożsamości i zaktualizuj dwie modułu przy użyciu zestawów SDK IoT C.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 06/25/2018
ms.author: menchi
ms.openlocfilehash: 5a9e3fb741563d2804c916dc3db3e02a79147b6c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036460"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-c-back-end-and-c-device"></a>Rozpoczynanie pracy z Centrum IoT modułu tożsamości i moduł dwie przy użyciu zaplecza C i urządzenie C

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Na końcu tego samouczka masz dwie C aplikacji:

* **CreateIdentities**, która tworzy tożsamość urządzenia, tożsamość modułu oraz skojarzony klucz zabezpieczeń na potrzeby łączenia klientów modułu i urządzenia.
* **UpdateModuleTwinReportedProperties**, która wysyła zaktualizowane zgłoszone właściwości bliźniaczej reprezentacji modułu do Twojego centrum IoT Hub.

> [!NOTE]
> Artykuł [Azure IoT SDKs][lnk-hub-sdks] (Zestawy SDK usługi Azure IoT) zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).
* Centrum IoT.
* Zainstaluj najnowszą [C SDK](https://github.com/Azure/azure-iot-sdk-c).


Centrum IoT zostało już utworzone i masz nazwę hosta oraz parametry połączenia usługi IoT Hub potrzebne do ukończenia pozostałej części tego samouczka.

<a id="DeviceIdentity_csharp"></a>
## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Tworzenie tożsamości urządzenia i tożsamości modułu w Centrum IoT

W tej sekcji utworzysz aplikację C, która tworzy tożsamości urządzenia i tożsamości modułu w rejestrze tożsamości w Centrum IoT. Urządzenie lub moduł nie mogą łączyć się z centrum IoT Hub, jeśli nie mają odpowiedniego wpisu w rejestrze tożsamości. Więcej informacji znajduje się w sekcji „Identity registry” (Rejestr tożsamości) artykułu [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera). Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Urządzenie i moduł korzystają z tych wartości w celu identyfikowania się podczas wysyłania komunikatów urządzenie-chmura do usługi IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.

Dodaj następujący kod do pliku C:

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"

#include "iothub_service_client_auth.h"
#include "iothub_registrymanager.h"

static const char* hubConnectionString ="[your hub's connection string]"; // modify

static void createDevice(IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle, const char* deviceId)
{
    IOTHUB_REGISTRY_DEVICE_CREATE_EX deviceCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;
    
    (void)memset(&deviceCreateInfo, 0, sizeof(deviceCreateInfo));
    deviceCreateInfo.version = 1;
    deviceCreateInfo.deviceId = deviceId;
    deviceCreateInfo.primaryKey = "";
    deviceCreateInfo.secondaryKey = "";
    deviceCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_DEVICE_EX deviceInfoEx;
    memset(&deviceInfoEx, 0, sizeof(deviceInfoEx));
    deviceInfoEx.version = 1;
    
    // Create device
    result = IoTHubRegistryManager_CreateDevice_Ex(iotHubRegistryManagerHandle, &deviceCreateInfo, &deviceInfoEx);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device has been created successfully: deviceId=%s, primaryKey=%s\n", deviceInfoEx.deviceId, deviceInfoEx.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice failed\n");
    }
    // You will need to Free the returned device information after it was created
    IoTHubRegistryManager_FreeDeviceExMembers(&deviceInfoEx);
}

static void createModule(IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle, const char* deviceId, const char* moduleId)
{
    IOTHUB_REGISTRY_MODULE_CREATE moduleCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;    
    
    (void)memset(&moduleCreateInfo, 0, sizeof(moduleCreateInfo));
    moduleCreateInfo.version = 1;
    moduleCreateInfo.deviceId = deviceId;
    moduleCreateInfo.moduleId = moduleId;
    moduleCreateInfo.primaryKey = "";
    moduleCreateInfo.secondaryKey = "";
    moduleCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_MODULE moduleInfo;
    memset(&moduleInfo, 0, sizeof(moduleInfo));
    moduleInfo.version = 1;
    
    // Create module
    result = IoTHubRegistryManager_CreateModule(iotHubRegistryManagerHandle, &moduleCreateInfo, &moduleInfo);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module has been created successfully: deviceId=%s, moduleId=%s, primaryKey=%s\n", moduleInfo.deviceId, moduleInfo.moduleId, moduleInfo.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateModule failed\n");
    }
    // You will need to Free the returned module information after it was created
    IoTHubRegistryManager_FreeModuleMembers(&moduleInfo);
}

int main(void)
{
    (void)platform_init();

    const char* deviceId = "myFirstDevice";
    const char* moduleId = "myFirstModule";
    IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
    IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle = NULL;

    if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(hubConnectionString)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else if ((iotHubRegistryManagerHandle = IoTHubRegistryManager_Create(iotHubServiceClientHandle)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else
    {
        createDevice(iotHubRegistryManagerHandle, deviceId);
        createModule(iotHubRegistryManagerHandle, deviceId, moduleId);
    }

    if (iotHubRegistryManagerHandle != NULL)
    {
        (void)printf("Calling IoTHubRegistryManager_Destroy...\n");
        IoTHubRegistryManager_Destroy(iotHubRegistryManagerHandle);
    }

    if (iotHubServiceClientHandle != NULL)
    {
        (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
        IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
    }
    
    platform_deinit();
    return 0;
}
```

Ta aplikacja tworzy tożsamość urządzenia o identyfikatorze **myFirstDevice** i tożsamością modułu o identyfikatorze **myFirstModule** obszarze urządzenie **myFirstDevice**. (Jeśli ten identyfikator modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera informacje o istniejącym module). Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Tego klucza używa się w symulowanej aplikacji modułu, aby nawiązać połączenie z centrum IoT.

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia i modułu tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera).


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-c-device-sdk"></a>Zaktualizuj dwie modułu przy użyciu urządzenia C zestawu SDK

W tej sekcji zostanie utworzona aplikacja C w Twojej symulowane urządzenie, które aktualizuje dwie modułu zgłaszane właściwości.

1. **Pobierz parametry połączenia modułu** — teraz zaloguj się do witryny [Azure Portal][lnk-portal]. Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź tożsamość myFirstDevice i otwórz ją. Zobaczysz, że tożsamość myFirstModule została pomyślnie utworzona. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

    ![Szczegóły modułu w witrynie Azure Portal][15]

2. **Tworzenie aplikacji UpdateModuleTwinReportedProperties** Dodaj następujące `using` instrukcje w górnej części **Program.cs** pliku:

    ```C
    #include <stdio.h>
    #include <stdlib.h>

    #include "azure_c_shared_utility/crt_abstractions.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"

    #include "iothub_service_client_auth.h"
    #include "iothub_devicetwin.h"

    const char* deviceId = "bugbash-test-2";
    const char* moduleId = "module-id-1";
    static const char* hubConnectionString ="[your hub's connection string]"; // modify
    const char* testJson = "{\"properties\":{\"desired\":{\"integer_property\": b-1234, \"string_property\": \"abcd\"}}}";

    int main(void)
    {
        (void)platform_init();

        IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
        IOTHUB_SERVICE_CLIENT_DEVICE_TWIN_HANDLE iothubDeviceTwinHandle = NULL;

        if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(moduleConnectionString)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else if ((iothubDeviceTwinHandle = IoTHubDeviceTwin_Create(iotHubServiceClientHandle)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else
        {
            char *result = IoTHubDeviceTwin_UpdateModuleTwin(iothubDeviceTwinHandle, deviceId, moduleId, testJson);
            printf("IoTHubDeviceTwin_UpdateModuleTwin returned %s\n", result);
        }

        if (iothubDeviceTwinHandle != NULL)
        {
            (void)printf("Calling IoTHubDeviceTwin_Destroy...\n");
            IoTHubDeviceTwin_Destroy(iothubDeviceTwinHandle);
        }

        if (iotHubServiceClientHandle != NULL)
        {
            (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
            IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
        }
        
        platform_deinit();
        return 0;
    }
    ```

Ten przykładowy kod przedstawia sposób pobierania dwie modułu i zaktualizować właściwości zgłoszone. 

## <a name="get-updates-on-the-device-side"></a>Pobierz aktualizacje na stronie urządzenia.
Oprócz powyższych kodu, można dodać poniżej blok kodu do zaktualizowania dwie komunikat na urządzeniu.

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/macro_utils.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "iothub_module_client_ll.h"
#include "iothub_client_options.h"
#include "iothub_message.h"

// The protocol you wish to use should be uncommented
//
//#define SAMPLE_MQTT
//#define SAMPLE_MQTT_OVER_WEBSOCKETS
#define SAMPLE_AMQP
//#define SAMPLE_AMQP_OVER_WEBSOCKETS
//#define SAMPLE_HTTP

#ifdef SAMPLE_MQTT
    #include "iothubtransportmqtt.h"
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    #include "iothubtransportmqtt_websockets.h"
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    #include "iothubtransportamqp.h"
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    #include "iothubtransportamqp_websockets.h"
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    #include "iothubtransporthttp.h"
#endif // SAMPLE_HTTP

/* Paste in the your iothub connection string  */
static const char* connectionString = "[Fill in connection string]";

static bool g_continueRunning;
#define DOWORK_LOOP_NUM     3

static void deviceTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
{
    (void)userContextCallback;

    printf("Device Twin update received (state=%s, size=%zu): %s\r\n", 
        ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
}

static void reportedStateCallback(int status_code, void* userContextCallback)
{
    (void)userContextCallback;
    printf("Device Twin reported properties update completed with result: %d\r\n", status_code);

    g_continueRunning = false;
}


void iothub_module_client_sample_device_twin_run(void)
{
    IOTHUB_CLIENT_TRANSPORT_PROVIDER protocol;
    IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle;
    g_continueRunning = true;

    // Select the Protocol to use with the connection
#ifdef SAMPLE_MQTT
    protocol = MQTT_Protocol;
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    protocol = MQTT_WebSocket_Protocol;
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    protocol = AMQP_Protocol;
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    protocol = AMQP_Protocol_over_WebSocketsTls;
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    protocol = HTTP_Protocol;
#endif // SAMPLE_HTTP

    if (platform_init() != 0)
    {
        (void)printf("Failed to initialize the platform.\r\n");
    }
    else
    {
        if ((iotHubModuleClientHandle = IoTHubModuleClient_LL_CreateFromConnectionString(connectionString, protocol)) == NULL)
        {
            (void)printf("ERROR: iotHubModuleClientHandle is NULL!\r\n");
        }
        else
        {
            bool traceOn = true;
            const char* reportedState = "{ 'device_property': 'new_value'}";
            size_t reportedStateSize = strlen(reportedState);

            (void)IoTHubModuleClient_LL_SetOption(iotHubModuleClientHandle, OPTION_LOG_TRACE, &traceOn);

            // Check the return of all API calls when developing your solution. Return checks ommited for sample simplification.

            (void)IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, deviceTwinCallback, iotHubModuleClientHandle);
            (void)IoTHubModuleClient_LL_SendReportedState(iotHubModuleClientHandle, (const unsigned char*)reportedState, reportedStateSize, reportedStateCallback, iotHubModuleClientHandle);

            do
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            } while (g_continueRunning);

            for (size_t index = 0; index < DOWORK_LOOP_NUM; index++)
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            }

            IoTHubModuleClient_LL_Destroy(iotHubModuleClientHandle);
        }
        platform_deinit();
    }
}

int main(void)
{
    iothub_module_client_sample_device_twin_run();
    return 0;
}
```


## <a name="next-steps"></a>Kolejne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami][lnk-device-management]
* [Getting started with IoT Edge][lnk-iot-edge] (Wprowadzenie do usługi IoT Edge)


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/