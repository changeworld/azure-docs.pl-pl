---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78262372"
---
### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz plik local.settings.json i skopiuj `AzureWebJobsStorage`wartość , która jest ciągiem połączenia konta magazynu. Ustaw `AZURE_STORAGE_CONNECTION_STRING` zmienną środowiskową na ciąg połączenia za pomocą tego polecenia Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu ciągu połączenia `AZURE_STORAGE_CONNECTION_STRING` w zmiennej środowiskowej, można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelniania za każdym razem.