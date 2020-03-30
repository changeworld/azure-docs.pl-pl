---
title: Powiązanie danych wyjściowych usługi Azure IoT Hub dla usług Azure Functions
description: Dowiedz się pisać wiadomości do strumieni usługi Azure IoT Hubs przy użyciu usługi Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277429"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych usługi Azure IoT Hub dla usług Azure Functions

W tym artykule wyjaśniono, jak pracować z powiązaniami danych wyjściowych usługi Azure Functions dla usługi IoT Hub. Obsługa usługi IoT Hub jest oparta na powiązaniu [usługi Azure Event Hubs.](functions-bindings-event-hubs.md)

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Podczas gdy następujące przykłady kodu używają interfejsu API Centrum zdarzeń, dana składnia ma zastosowanie do funkcji Usługi IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Następne kroki

- [Odpowiadanie na zdarzenia wysyłane do strumienia zdarzeń centrum zdarzeń (wyzwalacz)](./functions-bindings-event-iot-trigger.md)
