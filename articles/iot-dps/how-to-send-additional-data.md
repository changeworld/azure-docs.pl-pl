---
title: Jak przenieść dodatkowe dane między urządzeniami a usługą Azure Device Provisioning
description: W tym dokumencie opisano sposób przenoszenia dodatkowych danych między usługą Device Provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974857"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Jak przenieść dodatkowe dane między urządzeniem a usługą DPS
Czasami usługa DPS potrzebuje dodatkowych danych z urządzeń, aby poprawnie aprowizować je we właściwym wystąpieniu usługi IoT Hub. Dane te muszą zostać dostarczone przez urządzenie. Na odwrót usługa DPS może zwrócić dane do urządzenia w celu ułatwienia logiki po stronie klienta. 

## <a name="when-to-use-it"></a>Kiedy stosować
Tej funkcji można użyć jako rozszerzenia [alokacji niestandardowej](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Na przykład, chcesz przydzielić urządzenia w oparciu o model urządzenia bez interwencji człowieka. W takim przypadku zostanie użyta [alokacja niestandardowa](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Można skonfigurować urządzenie do zgłaszania informacji o modelu w ramach [wywołania rejestracji urządzenia](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). Usługa DPS przekaże Informacje o urządzeniu do elementu webhook niestandardowej alokacji. A funkcja może zdecydować, które IoT Hub urządzenie będzie przejdzie, gdy otrzyma informacje o modelu urządzenia. Podobnie jeśli element webhook zechce zwrócić pewne dane do urządzenia, przekaże je z powrotem jako ciąg w odpowiedzi elementu webhook.  

## <a name="device-sends-data-to-dps"></a>Urządzenie wysyła dane do programu DPS
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
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>Usługa DPS zwraca dane do urządzenia
Jeśli element webhook niestandardowych zasad alokacji chce zwrócić pewne dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook. Zmiana znajduje się w sekcji returnData poniżej. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Obsługa zestawu SDK
Ta funkcja jest dostępna w zestawach C# [SDK klienta](https://docs.microsoft.com/azure/iot-dps/)języka C,, Java i Node. js.  

## <a name="next-steps"></a>Następne kroki
* Opracowywanie zawartości przy użyciu [zestawu Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) dla platformy Azure IoT Hub i platformy Azure IoT Hub Device Provisioning Service
