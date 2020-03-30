---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474191"
---
Błędy wywoływane w usłudze Azure Functions mogą pochodzić z dowolnego z następujących źródeł:

- Korzystanie z wbudowanych [wyzwalaczy i powiązań](..\articles\azure-functions\functions-triggers-bindings.md) usług Azure Functions
- Wywołania interfejsów API podstawowych usług platformy Azure
- Wywołania punktów końcowych REST
- Wywołania bibliotek, pakietów lub interfejsów API innych firm

Przestrzeganie solidnych praktyk obsługi błędów jest ważne, aby uniknąć utraty danych lub nieodebranych wiadomości. Zalecane praktyki obsługi błędów obejmują następujące akcje:

- [Włączanie usługi Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Korzystanie ze strukturalnej obsługi błędów](#use-structured-error-handling)
- [Projekt dla idempotency](../articles/azure-functions/functions-idempotent.md)
- [Wdrażanie zasad ponawiania prób](../articles/azure-functions/functions-reliable-event-processing.md) (w stosownych przypadkach)

### <a name="use-structured-error-handling"></a>Korzystanie ze strukturalnej obsługi błędów

Przechwytywanie i publikowanie błędów ma kluczowe znaczenie dla monitorowania kondycji aplikacji. Najwyższy poziom dowolnego kodu funkcji powinien zawierać blok try/catch. W bloku catch można przechwytywać i publikować błędy.

### <a name="retry-support"></a>Pomoc techniczna ponawianie próby

Następujące wyzwalacze mają wbudowaną obsługę ponawiania prób:

* [Magazyn obiektów Blob platformy Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Magazyn kolejki platformy Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Usługa Azure Service Bus (kolejka/temat)](../articles/azure-functions/functions-bindings-service-bus.md)

Domyślnie te wyzwalacze ponawiają żądania do pięciu razy. Po piątej próbie zarówno magazyn kolejki azure, jak i wyzwalacze usługi Azure Service Bus zapisują wiadomość do [kolejki trucić.](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)

Należy ręcznie zaimplementować zasady ponawiania prób dla innych wyzwalaczy lub typów powiązań. Implementacje ręczne mogą obejmować zapisywanie informacji o błędzie w [kolejce trujących komunikatów](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Zapisując do kolejki trucizny, masz możliwość ponowić próbę operacji w późniejszym czasie. Takie podejście jest takie samo, używane przez wyzwalacz magazynu obiektów Blob.
