---
title: Wyślij wiadomości zabezpieczeń Centrum zabezpieczeń Azure dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości zabezpieczeń za pomocą Centrum zabezpieczeń Azure dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: be17c5bb4d09e0868af0c6fd9b31f7653b614735
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762773"
---
# <a name="send-security-messages-sdk"></a>Wysyłanie komunikatów zabezpieczeń zestawu SDK

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku wyjaśniono Azure Security Center (ASC) dla możliwości usługi IoT, po wybraniu do zbierania i wysyłania komunikatów zabezpieczeń urządzenia bez użycia usługi ASC agenta IoT i wyjaśniono, jak to zrobić.  

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Wyślij wiadomość zabezpieczeń interfejsu API do użyciaC#
> * Korzystanie security wysyłanie wiadomości interfejsu API dla języka C

## <a name="asc-for-iot-capabilities"></a>ASC możliwości technologii IoT

ASC IoT jest przetwarzanie i analizowanie dowolnych danych związanych z zabezpieczeniami wiadomości, tak długo, jak długo dane wysyłane jest zgodny z [ASC schematu IoT](https://aka.ms/iot-security-schemas) a komunikat jest ustawiana jako komunikat zabezpieczeń.

## <a name="security-message"></a>Komunikat zabezpieczeń

ASC IoT definiuje komunikat zabezpieczeń przy użyciu następujących kryteriów:
- Jeśli komunikat został wysłany za pomocą usługi Azure IoT C /C# zestawu SDK
- Jeśli komunikat jest zgodny z [schematu komunikat zabezpieczeń](https://aka.ms/iot-security-schemas)
- Jeśli wiadomość została ustawiona jako przed wysłaniem komunikat zabezpieczeń

Każdy komunikat zabezpieczeń obejmują metadane nadawcy, takie jak `AgentId`, `AgentVersion`, `MessageSchemaVersion` oraz listę zdarzeń związanych z zabezpieczeniami.
Schemat definiuje ważne i wymagane właściwości wiadomości zabezpieczeń, w tym typy zdarzeń.

[!NOTE]
> Komunikaty wysyłane, które nie są zgodne ze schematem zostaną zignorowane. Upewnij się, że schemat przed zainicjowaniem wysyłająca dane ponieważ ignorowanych komunikaty nie są obecnie przechowywane. 
> Komunikaty wysyłane, które nie zostały ustawione jako komunikat zabezpieczeń przy użyciu usługi Azure IoT C /C# zestaw SDK nie będą kierowane do usługi ASC dla potoku IoT

## <a name="valid-message-example"></a>Przykład prawidłowy komunikat

W poniższym przykładzie obiekt komunikatu podmioty zabezpieczeń. Przykład zawiera metadane komunikatów i jeden `ProcessCreate` zdarzeń zabezpieczeń.

Po ustawieniu jako komunikat zabezpieczeń i wysłane, ten komunikat zostanie przetworzony przez usługę ASC dla IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Wysyłanie komunikatów zabezpieczeń 

Wysyłanie komunikatów zabezpieczeń bez użycia usługi ASC agenta IoT za pomocą [usługi Azure IoT C# zestawu SDK urządzenia](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) lub [zestaw SDK urządzeń Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Aby wysłać dane urządzenia z urządzeń do przetwarzania przez usługę ASC dla IoT, użyj jednej z poniższych interfejsów API, aby oznaczyć wiadomości dla prawidłowy routing z usługą ASC potoku przetwarzania IoT. Komunikaty wysyłane w ten sposób zostanie przetworzone i wyświetlane jako wgląd w zabezpieczenia w ramach usługi ASC dla IoT, w ramach zarówno usługi IoT Hub lub Azure Security Center. 

Wszystkie dane, które są wysyłane, nawet wtedy, gdy oznaczone poprawny nagłówek muszą być zgodne z [ASC schematu komunikatów IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Zabezpieczenia komunikatów z interfejsu API wysyłania

**Wysyłanie komunikatów zabezpieczeń** interfejs API jest obecnie dostępna w języku C i C#.  

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

## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj ASC dla usługi IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Włącz [usługi](quickstart-onboard-iot-hub.md)
- Odczyt [— często zadawane pytania](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- Zrozumienie [zalecenia](concept-recommendations.md)
- Zrozumienie [alertów](concept-security-alerts.md)
