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
ms.openlocfilehash: fbb537c9584c948af37694b3bfc77a7c345e084d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639118"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure Możesz użyć [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) podstawowego narzędzia do wdrożenia kodu projektu na platformie Azure. W poniższym poleceniu Zastąp `<APP_NAME>` wartość nazwą aplikacji z poprzedniego kroku.

```bash
func azure functionapp publish <APP_NAME>
```

Zobaczysz dane wyjściowe podobne do następujących, które zostały obcięte pod kątem czytelności:

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

`Invoke url` Skopiuj wartość `HttpTrigger`dla elementu, którego możesz teraz użyć do testowania funkcji na platformie Azure. Adres URL zawiera `code` wartość ciągu zapytania, który jest kluczem funkcji. Ten klucz utrudnia innym osobom wywoływanie punktu końcowego wyzwalacza HTTP na platformie Azure.