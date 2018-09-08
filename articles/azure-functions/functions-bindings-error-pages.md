---
title: Usługa Azure Functions dodanymi komentarzami. wskazówki dotyczące | Dokumentacja firmy Microsoft
description: Zawiera ogólne wskazówki dotyczące obsługi błędów występujących w przypadku wykonywania funkcji oraz łącza do tematów, błędy specyficzne dla powiązania.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 5a8dae73c164b319b4c291685deff402f9798364
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091843"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów w usłudze Azure Functions

Ten temat zawiera ogólne wskazówki dotyczące obsługi błędów występujących podczas wykonywania funkcji. Umożliwia także łącza do tematów, które opisują powiązania określonych błędów, które mogą wystąpić. 

## <a name="handing-errors-in-functions"></a>Przekazywanie błędów w funkcjach
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kody błędów powiązania

Podczas integracji z usługami platformy Azure, może być zgłaszane błędy, pochodzących z interfejsów API usług podstawowych. Łącza do błędu kodu dokumentacji dla tych usług można znaleźć w **wyjątków i kody powrotne** części następujący wyzwalacz i powiązania tematy referencyjne:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
