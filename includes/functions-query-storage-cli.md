---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474282"
---
### <a name="query-the-storage-queue"></a>Zapytanie o kolejkę magazynu

Za pomocą [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia można wyświetlić kolejki magazynu na koncie, jak w poniższym przykładzie:

```azurecli-interactive
az storage queue list --output tsv
```

Dane wyjściowe z tego `outqueue`polecenia zawiera kolejkę o nazwie , która jest kolejką, która została utworzona po uruchomieniu funkcji.

Następnie użyj [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) polecenia, aby wyświetlić wiadomości w tej kolejce, jak w tym przykładzie:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Zwracany ciąg powinien być taki sam jak wiadomość wysłana w celu przetestowania funkcji.

> [!NOTE]  
> W poprzednim przykładzie dekoduje zwracany ciąg z base64. Dzieje się tak, ponieważ powiązania magazynu kolejki zapisują i odczytują z usługi Azure Storage jako [ciągi base64.](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)