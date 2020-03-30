---
title: Jak przenieść ładunek między urządzeniem a usługą inicjowania obsługi urządzeń platformy Azure
description: W tym dokumencie opisano sposób przenoszenia ładunku między urządzeniem a usługą inicjowania obsługi administracyjnej urządzeń (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246691"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Jak przenieść ładunek między urządzeniem a DPS
Czasami DPS potrzebuje więcej danych z urządzeń, aby prawidłowo aprowizować je do odpowiedniego Centrum IoT i że dane muszą być dostarczane przez urządzenie. Na odwrót DPS może zwracać dane do urządzenia, aby ułatwić logikę po stronie klienta. 

## <a name="when-to-use-it"></a>Zastosowanie
Ta funkcja może służyć jako ulepszenie [alokacji niestandardowej](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Na przykład chcesz przydzielić urządzenia na podstawie modelu urządzenia bez interwencji człowieka. W takim przypadku użyjesz [alokacji niestandardowej](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Urządzenie można skonfigurować tak, aby zgłaszało informacje o modelu w ramach [wywołania urządzenia rejestrującego](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS przekaże ładunek urządzenia do niestandardowego elementu webhook alokacji. Funkcja może zdecydować, do którego centrum IoT Hub przejdzie po otrzymaniu informacji o modelu urządzenia. Podobnie jeśli element webhook chce zwrócić niektóre dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Urządzenie wysyła ładunek danych do DPS
Gdy urządzenie wysyła [wywołanie urządzenia rejestru](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) do DPS, wywołanie rejestru można udoskonalić, aby wziąć inne pola w treści. Ciało wygląda następująco: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS zwraca dane do urządzenia
Jeśli element webhook zasad alokacji niestandardowej chce zwrócić niektóre dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook. Zmiana znajduje się w sekcji ładunku poniżej. 
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
Ta funkcja jest dostępna w [modułach SDK klienta](https://docs.microsoft.com/azure/iot-dps/)C, C#, JAVA i Node.js.  

## <a name="next-steps"></a>Następne kroki
* Tworzenie przy użyciu zestawu [SDK usługi Azure IoT]( https://github.com/Azure/azure-iot-sdks) dla usługi Azure IoT Hub i usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
