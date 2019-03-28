---
title: Wyślij wiadomości zabezpieczeń z usługą ASC dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości zabezpieczeń za pomocą usługi ASC dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3daef0bfb2b9108e2b3c28ca0f6975a6f5768e92
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541589"
---
# <a name="tutorial-send-security-messages-sdk"></a>Samouczek: Wysyłanie komunikatów zabezpieczeń zestawu SDK

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym samouczku wyjaśniono ASC dla możliwości analizy danych IoT w przypadku wybrania zbierać i wysyłać urządzenia zabezpieczeń bez użycia usługi ASC agenta IoT i wyjaśniono, jak to zrobić.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Wyślij wiadomość zabezpieczeń interfejsu API do użyciaC#
> * Korzystanie security wysyłanie wiadomości interfejsu API dla języka C

## <a name="asc-for-iot"></a>ASC dla IoT 

Przetwarzanie i analizowanie dowolnych danych związanych z zabezpieczeniami wiadomości, tak długo, jak długo dane wysyłane jest zgodny z ASC IoT [ASC schematu IoT](https://github.com/Azure/ASC-for-IoT-Schemas). 

## <a name="send-security-messages"></a>Wysyłanie komunikatów zabezpieczeń 

Wysyłanie komunikatów zabezpieczeń bez użycia usługi ASC agenta IoT za pomocą [zestaw SDK urządzeń Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp).

Aby wysłać dane urządzenia z urządzeń do przetwarzania przez usługę ASC dla IoT, użyj jednej z poniższych interfejsów API, aby oznaczyć wiadomości dla prawidłowy routing z usługą ASC potoku przetwarzania IoT. Komunikaty wysyłane w ten sposób zostanie przetworzone i wyświetlane jako wgląd w zabezpieczenia w ramach usługi ASC dla IoT, w ramach zarówno usługi IoT Hub lub Azure Security Center. 

Wszystkie dane, które są wysyłane, nawet wtedy, gdy oznaczone poprawny nagłówek muszą być zgodne z [ASC schematu komunikatów IoT](https://github.com/Azure/ASC-for-IoT-Schemas). 

> [!NOTE]
> Komunikaty wysyłane, które nie są zgodne ze schematem zostaną zignorowane. Upewnij się, że schemat przed zainicjowaniem wysyłająca dane ponieważ ignorowanych komunikaty nie są obecnie przechowywane. 

### <a name="send-security-message-api"></a>Zabezpieczenia komunikatów z interfejsu API wysyłania

**Wysyłanie komunikatów zabezpieczeń** interfejs API jest obecnie dostępna w języku C i C#.  

#### <a name="c-api"></a>Interfejs API języka C#

```cs
private static async Task SendSecurityMessageAsync()
{
    string messageContent = "Security Data";
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