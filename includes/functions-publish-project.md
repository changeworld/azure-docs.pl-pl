---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452959"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure możesz użyć polecenia [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment), aby wdrożyć kod projektu na platformie Azure.

```bash
func azure functionapp publish <FunctionAppName>
```

Zostaną wyświetlone dane wyjściowe podobne do danych poniżej. Zostały one skrócone, aby poprawić czytelność.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Teraz można przetestować funkcje na platformie Azure.