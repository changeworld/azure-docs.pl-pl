---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262372"
---
### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz plik Local. Settings. JSON i skopiuj wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Ustaw zmienną środowiskową `AZURE_STORAGE_CONNECTION_STRING` na parametry połączenia za pomocą tego polecenia bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu parametrów połączenia w zmiennej środowiskowej `AZURE_STORAGE_CONNECTION_STRING` można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelnienia za każdym razem.