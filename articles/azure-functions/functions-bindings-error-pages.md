---
title: Wskazówki dotyczące obsługi błędów usługi Azure Functions
description: Dowiedz się, jak obsługiwać błędy w usłudze Azure Functions za pomocą łączy z określonymi błędami wiązania.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586193"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów usług Azure Functions

Obsługa błędów w usłudze Azure Functions jest ważne, aby uniknąć utraty danych, nieodebranych zdarzeń i monitorowania kondycji aplikacji.

W tym artykule opisano ogólne strategie obsługi błędów wraz z łączami do błędów specyficznych dla powiązania.

## <a name="handling-errors"></a>Obsługa błędów

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kody błędów wiązania

Podczas integrowania z usługami platformy Azure błędy mogą pochodzić z interfejsów API usług źródłowych. Informacje dotyczące błędów specyficznych dla powiązania są dostępne w sekcji **Wyjątki i kody zwrotów** następujących artykułów:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Centra zdarzeń](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Centra IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
