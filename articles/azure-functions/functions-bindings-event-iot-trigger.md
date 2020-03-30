---
title: Powiązania usługi Azure IoT Hub dla usług Azure Functions
description: Dowiedz się, jak reagować na zdarzenia wysyłane do strumienia zdarzeń usługi IoT hub w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589729"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure IoT Hub dla funkcji platformy Azure

W tym artykule wyjaśniono, jak pracować z powiązaniami usługi Azure Functions dla usługi IoT Hub. Obsługa usługi IoT Hub jest oparta na powiązaniu [usługi Azure Event Hubs.](functions-bindings-event-hubs.md)

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Podczas gdy następujące przykłady kodu używają interfejsu API Centrum zdarzeń, dana składnia ma zastosowanie do funkcji Usługi IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Następne kroki

- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-iot-output.md)
