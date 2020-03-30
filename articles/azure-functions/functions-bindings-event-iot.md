---
title: Powiązania usługi Azure IoT Hub dla usług Azure Functions
description: Dowiedz się, jak używać wyzwalacza i powiązania usługi IoT Hub w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586137"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Powiązania usługi Azure IoT Hub dla usług Azure Functions

Ten zestaw artykułów wyjaśnia, jak pracować z powiązaniami usługi Azure Functions dla usługi IoT Hub. Obsługa usługi IoT Hub jest oparta na powiązaniu [usługi Azure Event Hubs.](functions-bindings-event-hubs.md)

> [!IMPORTANT]
> Podczas gdy następujące przykłady kodu używają interfejsu API Centrum zdarzeń, dana składnia ma zastosowanie do funkcji Usługi IoT Hub.

| Akcja | Typ |
|--------|------|
| Odpowiadanie na zdarzenia wysyłane do strumienia zdarzeń usługi IoT Hub. | [Wyzwalacz](./functions-bindings-event-iot-trigger.md) |
| Zapisywanie zdarzeń w strumieniu zdarzeń IoT | [Oprawa wyjściowa](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Następne kroki

- [Odpowiadanie na zdarzenia wysyłane do strumienia zdarzeń centrum zdarzeń (wyzwalacz)](./functions-bindings-event-iot-trigger.md)
- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-iot-output.md)
