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
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949970"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure Możesz użyć [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) podstawowego narzędzia do wdrożenia kodu projektu na platformie Azure. W tych przykładach Zamień `<APP_NAME>` na nazwę aplikacji z poprzedniego kroku.

### <a name="c--javascript"></a>C\# /JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
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