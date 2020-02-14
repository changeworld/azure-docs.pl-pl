---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8573a915c572885b53437843b114aa987be4c66b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198460"
---
Błędy wywoływane w Azure Functions mogą pochodzić z jednego z następujących źródeł:

- Korzystanie z wbudowanych [wyzwalaczy Azure Functions i powiązań](..\articles\azure-functions\functions-triggers-bindings.md)
- Wywołania interfejsów API podstawowych usług platformy Azure
- Wywołania punktów końcowych REST
- Wywołania bibliotek klienckich, pakietów lub interfejsów API innych firm

Po zastosowaniu stałych praktyk obsługi błędów ważne jest, aby uniknąć utraty danych lub brakujących komunikatów. Zalecane praktyki obsługi błędów obejmują następujące akcje:

- [Włącz Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Użyj strukturalnej obsługi błędów](#use-structured-error-handling)
- [Projektuj dla idempotentności](../articles/azure-functions/functions-idempotent.md)
- [Zaimplementuj zasady ponawiania](../articles/azure-functions/functions-reliable-event-processing.md) (w razie potrzeby)

### <a name="use-structured-error-handling"></a>Użyj strukturalnej obsługi błędów

Błędy przechwytywania i publikowania mają kluczowe znaczenie dla monitorowania kondycji aplikacji. Najwyższy poziom dowolnego kodu funkcji powinien zawierać blok try/catch. W bloku catch można przechwytywać i publikować błędy.

### <a name="retry-support"></a>Ponów próbę obsługi

Następujące wyzwalacze obsługują wbudowaną ponowną próbę:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Usługa Azure queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Kolejka/temat)](../articles/azure-functions/functions-bindings-service-bus.md)

Domyślnie te wyzwalacze ponawiają żądania do pięciu razy. Po piątej ponowieniu próby zarówno usługa Azure queue storage, jak i Azure Service Bus wyzwalają zapisanie komunikatu w [kolejce trującej](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Należy ręcznie zaimplementować zasady ponawiania dla innych typów wyzwalaczy lub powiązań. Implementacje ręczne mogą obejmować zapisywanie informacji o błędach w [kolejce trujących komunikatów](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Pisząc do kolejki trującej, można ponowić próbę wykonania operacji w późniejszym czasie. To podejście jest takie samo, jak używane przez wyzwalacz magazynu obiektów BLOB.
