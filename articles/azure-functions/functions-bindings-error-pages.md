---
title: Wskazówki dotyczące obsługi błędów Azure Functions
description: Dowiedz się, jak obsłużyć błędy w Azure Functions z łączami do określonych błędów powiązań.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155288"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów Azure Functions

Obsługa błędów w Azure Functions ma na celu uniknięcie utraty danych, nieodebranych zdarzeń i monitorowania kondycji aplikacji.

W tym artykule opisano ogólne strategie obsługi błędów oraz linki do błędów specyficznych dla powiązania.

## <a name="handling-errors"></a>Obsługa błędów

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kody błędów powiązania

W przypadku integracji z usługami platformy Azure błędy mogą pochodzić z interfejsów API podstawowych usług. Informacje dotyczące błędów związanych z powiązaniem są dostępne w sekcji **wyjątki i kody powrotu** w następujących artykułach:

+ [Usługi Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
