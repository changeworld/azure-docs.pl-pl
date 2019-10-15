---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329566"
---
### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz plik Local. Settings. JSON i skopiuj wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Ustaw zmienną środowiskową `AZURE_STORAGE_CONNECTION_STRING` na parametry połączenia za pomocą tego polecenia bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu parametrów połączenia w zmiennej środowiskowej `AZURE_STORAGE_CONNECTION_STRING` można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelnienia za każdym razem.