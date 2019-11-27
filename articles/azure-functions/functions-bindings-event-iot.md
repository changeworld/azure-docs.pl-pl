---
title: Powiązania IoT Hub platformy Azure dla Azure Functions
description: Dowiedz się, jak używać powiązań IoT Hub w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 93a8dc600e8fadb16df8e82fbe02a1b186191d12
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227252"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Powiązania IoT Hub platformy Azure dla Azure Functions

W tym artykule opisano sposób pracy z Azure Functions powiązaniami IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

W przypadku Azure Functions w wersji 1. x powiązania IoT Hub są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

W przypadku funkcji 2. x Użyj pakietu [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
