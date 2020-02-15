---
title: Jak przenieść ładunek między urządzeniami a usługą Azure Device Provisioning
description: W tym dokumencie opisano sposób transferu ładunku między urządzeniami a usługą Device Provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3eec39e975b1e782eafe16205623c625f462a865
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209413"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Jak przenieść ładunek między urządzeniami i usługą DPS
Czasami usługa DPS potrzebuje więcej danych z urządzeń, aby prawidłowo udostępnić je właściwym IoT Hub i że dane muszą być dostarczone przez urządzenie. Na odwrót usługa DPS może zwrócić dane do urządzenia w celu ułatwienia logiki po stronie klienta. 

## <a name="when-to-use-it"></a>Kiedy stosować
Tej funkcji można użyć jako rozszerzenia [alokacji niestandardowej](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Na przykład, chcesz przydzielić urządzenia w oparciu o model urządzenia bez interwencji człowieka. W takim przypadku zostanie użyta [alokacja niestandardowa](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Można skonfigurować urządzenie do zgłaszania informacji o modelu w ramach [wywołania rejestracji urządzenia](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). Usługa DPS przekaże ładunek urządzenia do elementu webhook niestandardowej alokacji. A funkcja może zdecydować, które IoT Hub urządzenie będzie przejdzie, gdy otrzyma informacje o modelu urządzenia. Podobnie, jeśli element webhook chce zwrócić pewne dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Urządzenie wysyła ładunek danych do programu DPS
Gdy urządzenie wysyła [wywołanie rejestracji urządzenia](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) do punktu dystrybucji, wywołanie Register można rozszerzyć, aby przyjąć inne pola w treści. Treść wygląda następująco: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>Usługa DPS zwraca dane do urządzenia
Jeśli element webhook niestandardowych zasad alokacji chce zwrócić pewne dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook. Ta zmiana znajduje się w sekcji ładunek poniżej. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Obsługa zestawu SDK
Ta funkcja jest dostępna w zestawach C# [SDK klienta](https://docs.microsoft.com/azure/iot-dps/)języka C,, Java i Node. js.  

## <a name="next-steps"></a>Następne kroki
* Opracowywanie zawartości przy użyciu [zestawu Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) dla platformy Azure IoT Hub i platformy Azure IoT Hub Device Provisioning Service
