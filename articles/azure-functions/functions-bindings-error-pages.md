---
title: Wskazówki dotyczące obsługi błędów Azure Functions
description: Dowiedz się, jak obsłużyć błędy w Azure Functions z łączami do określonych błędów powiązań.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 06dc4c5c1713ee10f263e573a698e9ea36ca2662
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227324"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów Azure Functions

Obsługa błędów w Azure Functions ma na celu uniknięcie utraty danych, nieodebranych zdarzeń i monitorowania kondycji aplikacji.

W tym artykule opisano ogólne strategie obsługi błędów oraz linki do błędów specyficznych dla powiązania.

## <a name="handling-errors"></a>Obsługa błędów

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kody błędów powiązania

W przypadku integracji z usługami platformy Azure błędy mogą pochodzić z interfejsów API podstawowych usług. Informacje dotyczące błędów związanych z powiązaniem są dostępne w sekcji **wyjątki i kody powrotu** w następujących artykułach:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
