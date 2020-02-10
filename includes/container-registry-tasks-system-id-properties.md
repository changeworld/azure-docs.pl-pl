---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 700dbfde3be2f24eb57acbdeb9d2841ef2bdfe44
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112281"
---
W danych wyjściowych polecenia sekcja `identity` pokazuje tożsamość typu `SystemAssigned` jest ustawiona w zadaniu. `principalId` jest IDENTYFIKATORem podmiotu zabezpieczeń dla tożsamości zadania:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Użyj polecenia [AZ ACR Task show][az-acr-task-show] , aby zapisać principalId w zmiennej, aby użyć ich w późniejszych poleceniach. Zastąp nazwę zadania i rejestr w następującym poleceniu:

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
