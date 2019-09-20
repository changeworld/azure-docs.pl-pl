---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f771b6b0416c5777c1ebde7e2cf2c4ffc6f375ff
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155296"
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
- Zaimplementuj zasady ponawiania (w razie potrzeby)

### <a name="use-structured-error-handling"></a>Użyj strukturalnej obsługi błędów

Błędy przechwytywania i publikowania mają kluczowe znaczenie dla monitorowania kondycji aplikacji. Najwyższy poziom dowolnego kodu funkcji powinien zawierać blok try/catch. W bloku catch można przechwytywać i publikować błędy.

### <a name="retry-support"></a>Ponów próbę obsługi

Następujące wyzwalacze obsługują wbudowaną ponowną próbę:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Usługa Azure queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Kolejka/temat)](../articles/azure-functions/functions-bindings-service-bus.md)

Domyślnie te wyzwalacze ponawiają żądania do pięciu razy. Po piątej ponownej próbie oba wyzwalacze zapisują komunikat do [kolejki trującej](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Należy ręcznie zaimplementować zasady ponawiania dla innych typów wyzwalaczy lub powiązań. Implementacje ręczne mogą obejmować zapisywanie informacji o błędach w [kolejce trujących komunikatów](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Pisząc do kolejki trującej, można ponowić próbę wykonania operacji w późniejszym czasie. To podejście jest takie samo, jak używane przez wyzwalacz magazynu obiektów BLOB.
