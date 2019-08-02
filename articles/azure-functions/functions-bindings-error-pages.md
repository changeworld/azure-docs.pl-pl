---
title: Wskazówki dotyczące obsługi błędów Azure Functions | Microsoft Docs
description: Zawiera ogólne wskazówki dotyczące obsługi błędów występujących w czasie wykonywania funkcji oraz linki do tematów dotyczących błędów specyficznych dla powiązania.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: bc0ffec1bfe55ee81573feffc58e1adb11421eec
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667444"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów Azure Functions

Ten temat zawiera ogólne wskazówki dotyczące obsługi błędów występujących podczas wykonywania funkcji. Zawiera również linki do tematów opisujących błędy dotyczące powiązań, które mogą wystąpić. 

## <a name="handling-errors-in-functions"></a>Obsługa błędów w funkcjach
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kody błędów powiązania

W przypadku integracji z usługami platformy Azure mogą wystąpić błędy zgłoszone przez interfejsy API podstawowych usług. Linki do dokumentacji kodu błędu dla tych usług można znaleźć w sekcji **wyjątki i kody powrotne** w następujących tematach:

+ [Usługi Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
