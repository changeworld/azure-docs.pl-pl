---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 03ed1b28b4cabc054301e11c2b4d0f9e632abe02
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839062"
---
Możesz użyć polecenia [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) , aby wyświetlić kolejki magazynu na koncie, jak w poniższym przykładzie:

```azurecli-interactive
az storage queue list --output tsv
```

Dane wyjściowe tego polecenia obejmują kolejkę o nazwie `outqueue`, która jest kolejką utworzoną podczas uruchamiania funkcji.

Następnie użyj [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) polecenia, aby wyświetlić komunikaty w tej kolejce, jak w tym przykładzie:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Zwracany ciąg powinien być taki sam jak komunikat wysłany do przetestowania funkcji.

> [!NOTE]  
> Poprzedni przykład dekoduje zwracany ciąg z base64. Wynika to z faktu, że powiązania magazynu kolejek zapisują i odczytują dane z usługi Azure Storage jako [ciągi Base64](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).