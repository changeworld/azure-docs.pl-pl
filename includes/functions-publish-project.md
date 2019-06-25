---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183016"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure, możesz użyć [ `func azure functionapp publish` ](../articles/azure-functions/functions-run-local.md#project-file-deployment) polecenie podstawowych narzędzi, aby wdrożyć kod projektu na platformie Azure. W poniższym poleceniu zastąp `<APP_NAME>` nazwą aplikacji w poprzednim kroku.

```bash
func azure functionapp publish <APP_NAME>
```

Widoczne będą dane wyjściowe podobne do poniższych składników, które zostały obcięte dla czytelności.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Skopiuj wartość wywołania adresu URL na potrzeby usługi HttpTrigger, które teraz umożliwia testowanie funkcji na platformie Azure. Adres URL zawiera `code` wartość ciągu zapytania jest klucz funkcji. Ten klucz utrudnia innym osobom do wywołania wyzwalacza HTTP punktu końcowego na platformie Azure.