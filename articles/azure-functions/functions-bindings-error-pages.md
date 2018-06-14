---
title: Obsługa wskazówki Azure błędów funkcji | Dokumentacja firmy Microsoft
description: Zawiera ogólne wskazówki dotyczące obsługi błędów występujących w podczas wykonywania funkcji oraz łącza do tematów błędy specyficzne dla powiązania.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: cfowler
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118489"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów funkcji platformy Azure

Ten temat zawiera ogólne wskazówki dotyczące obsługi błędów występujących podczas wykonywania funkcji. Umożliwia także linki do tematów opisujących błędy specyficzne dla powiązania, które mogą wystąpić. 

## <a name="handing-errors-in-functions"></a>Przekazywanie błędów w funkcjach
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kody błędów powiązań

Podczas integracji z usługami Azure, może być zgłaszane błędy, pochodzących z interfejsów API usług podstawowej. Łącza do błąd kodu dokumentacji dla tych usług można znaleźć w **wyjątki i kody powrotne** części tematy dokumentacji powiązania i następujących wyzwalaczy:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
