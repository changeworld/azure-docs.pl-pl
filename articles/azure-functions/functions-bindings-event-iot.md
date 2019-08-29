---
title: Powiązania IoT Hub platformy Azure dla Azure Functions
description: Dowiedz się, jak używać powiązań IoT Hub w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: d8b749a294da379e99e61072ff7a3415c508d2ac
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097649"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Powiązania IoT Hub platformy Azure dla Azure Functions

W tym artykule opisano sposób pracy z Azure Functions powiązaniami IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

W przypadku Azure Functions w wersji 1. x powiązania IoT Hub są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) w wersji 2. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

W przypadku funkcji 2. x Użyj pakietu [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) w wersji 3. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
