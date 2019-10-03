---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839164"
---
Otwórz plik Local. Settings. JSON i skopiuj wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Ustaw zmienną środowiskową `AZURE_STORAGE_CONNECTION_STRING` na parametry połączenia za pomocą tego polecenia bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu parametrów połączenia w zmiennej środowiskowej `AZURE_STORAGE_CONNECTION_STRING` można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelnienia za każdym razem.