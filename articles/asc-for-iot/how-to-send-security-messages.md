---
title: Wysyłanie wiadomości zabezpieczających do usługi Azure Security Center dla usługi IoT| Dokumenty firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości zabezpieczające przy użyciu usługi Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963822"
---
# <a name="send-security-messages-sdk"></a>Wysyłanie sdk wiadomości zabezpieczających

W tym przewodniku opisano funkcje usługi Azure Security Center for IoT, gdy użytkownik zdecyduje się zbierać i wysyłać komunikaty zabezpieczeń urządzenia bez użycia agenta Usługi Azure Security Center dla IoT, a także wyjaśnia, jak to zrobić.  

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Wysyłanie wiadomości zabezpieczających przy użyciu sdk usługi Azure IoT C
> * Wysyłanie wiadomości zabezpieczających przy użyciu zestawu SDK usługi Azure IoT C#
> * Wysyłanie wiadomości zabezpieczających przy użyciu zestawu SDK języka Azure IoT Python
> * Wysyłanie wiadomości zabezpieczających przy użyciu sdk węzła Azure IoT.js
> * Wysyłanie wiadomości zabezpieczających przy użyciu zestawu SDK Java usługi Azure IoT


## <a name="azure-security-center-for-iot-capabilities"></a>Usługi Azure Security Center dla funkcji IoT

Usługa Azure Security Center for IoT może przetwarzać i analizować wszelkiego rodzaju dane komunikatów zabezpieczeń, o ile wysyłane dane są zgodne ze [schematem Usługi Azure Security Center dla IoT,](https://aka.ms/iot-security-schemas) a wiadomość jest ustawiona jako komunikat zabezpieczeń.

## <a name="security-message"></a>Komunikat zabezpieczeń

Usługa Azure Security Center for IoT definiuje komunikat zabezpieczeń przy użyciu następujących kryteriów:
- Jeśli wiadomość została wysłana za pomocą usługi Azure IoT SDK
- Jeśli wiadomość jest zgodna ze [schematem wiadomości zabezpieczeń](https://aka.ms/iot-security-schemas)
- Jeśli wiadomość została ustawiona jako komunikat zabezpieczający przed wysłaniem

Każda wiadomość zabezpieczająca zawiera metadane `AgentId`nadawcy, takie jak , `AgentVersion` `MessageSchemaVersion` i listę zdarzeń zabezpieczeń.
Schemat definiuje prawidłowe i wymagane właściwości komunikatu zabezpieczeń, w tym typy zdarzeń.

>[!Note]
> Wiadomości wysyłane, które nie są zgodne ze schematem są ignorowane. Upewnij się, że schemat przed rozpoczęciem wysyłania danych, ponieważ ignorowane wiadomości nie są obecnie przechowywane. 

>[!Note]
> Wiadomości wysłane, które nie zostały ustawione jako komunikat zabezpieczeń przy użyciu zestawu Azure IoT SDK, nie będą kierowane do potoku Usługi Azure Security Center dla IoT.

## <a name="valid-message-example"></a>Przykład prawidłowej wiadomości

W poniższym przykładzie przedstawiono prawidłowy obiekt wiadomości zabezpieczającej. Przykład zawiera metadane wiadomości `ProcessCreate` i jedno zdarzenie zabezpieczeń.

Po ustawieniu jako komunikat zabezpieczeń i wysłaniu, ta wiadomość zostanie przetworzona przez Usługę Azure Security Center dla IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Wysyłanie wiadomości zabezpieczających 

Wysyłaj wiadomości zabezpieczeń *bez* korzystania z usługi Azure Security Center dla agenta IoT, przy użyciu [sdk urządzenia Usługi Azure IoT C,](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) [zestaw SDK urządzenia Azure IoT C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), , [zestaw SDK węzła Azure IoT Node.js,](https://github.com/Azure/azure-iot-sdk-node) [zestaw SDK języka Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python)lub zestaw java [SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-java).

Aby wysłać dane urządzenia z urządzeń do przetwarzania przez usługę Azure Security Center dla IoT, użyj jednego z następujących interfejsów API, aby oznaczyć wiadomości dla poprawnego routingu do usługi Azure Security Center dla potoku przetwarzania IoT. 

Wszystkie wysyłane dane, nawet jeśli są oznaczone poprawnym nagłówkiem, muszą być również zgodne ze [schematem wiadomości Usługi Azure Security Center for IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Interfejs API wysyłania wiadomości zabezpieczających 

Interfejs API **wysyłania komunikatów zabezpieczających** jest obecnie dostępny w językach C i C#, Python, Node.js i Java.  

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```
#### <a name="c-api"></a>Interfejs API języka C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```
#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Interfejs API języka Python

Aby korzystać z interfejsu API języka Python, należy zainstalować pakiet [azure-iot-device](https://pypi.org/project/azure-iot-device/).

Korzystając z interfejsu API języka Python, można wysłać komunikat zabezpieczający za pośrednictwem modułu lub za pośrednictwem urządzenia przy użyciu unikatowego ciągu połączenia urządzenia lub modułu. Korzystając z następującego przykładu skryptu Języka Python, z urządzeniem, użyj **IoTHubDeviceClient**i z modułem, użyj **IoTHubModuleClient**. 

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Interfejs API języka Java

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```


## <a name="next-steps"></a>Następne kroki
- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często](resources-frequently-asked-questions.md) zadawane pytania
- Dowiedz się, jak uzyskać dostęp do [nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
- Zrozumienie [rekomendacji](concept-recommendations.md)
- Opis [alertów](concept-security-alerts.md)
