---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183026"
---
Usługa Azure Functions [wyzwalaczy i powiązań](../articles/azure-functions/functions-triggers-bindings.md) komunikować się z różnymi usługami platformy Azure. Podczas integracji z tymi usługami, może być zgłaszane błędy, pochodzących z interfejsów API podstawowych usług platformy Azure. Ponadto mogą wystąpić błędy, podczas próby komunikacji z innymi usługami w kodzie funkcji przy użyciu REST lub bibliotek klienckich. Aby uniknąć utraty danych i zapewnienia dobrej zachowania funkcji, jest ważne, aby obsługiwać błędy z dowolnego źródła.

Następujących wyzwalaczy obsługują użyty wbudowany mechanizm ponawiania:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Usługa Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (queue/topic)](../articles/azure-functions/functions-bindings-service-bus.md)

Domyślnie te wyzwalacze zostaną ponowione maksymalnie pięć razy. Po piątej ponawiania próby, te wyzwalacze Zapisz komunikat specjalny [skażone kolejki](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages).

Inne wyzwalacze funkcji ma nie użyty wbudowany mechanizm ponawiania, gdy występują błędy podczas wykonywania funkcji. Aby zapobiec utracie informacji wyzwalacz powinien wystąpić błąd w funkcji, firma Microsoft zaleca, użyj bloków try-catch w kodzie funkcji w celu przechwytywania błędów. Gdy wystąpi błąd, napisz informacji przekazanego do funkcji przez wyzwalacz w kolejce specjalnego komunikatu "skażone". To podejście jest używane przez [wyzwalacz usługi Blob storage](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs).

W ten sposób można przechwycić wyzwalać zdarzenia, które mogą zostać utracone z powodu błędów i spróbuj je ponownie w późniejszym czasie za pomocą innej funkcji do przetwarzania komunikatów z kolejki skażone, korzystając z informacji przechowywanych.  
