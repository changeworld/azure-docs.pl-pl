---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255826"
---
## <a name="robots933456-in-logs"></a>roboty933456 w dziennikach

W dziennikach kontenerów może zostać wyświetlony następujący komunikat:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Możesz bezpiecznie zignorować ten komunikat. `/robots933456.txt`jest fikcyjną ścieżką adresu URL używaną przez usługę App Service do sprawdzania, czy kontener jest w stanie obsługiwać żądania. Odpowiedź 404 po prostu wskazuje, że ścieżka nie istnieje, ale informuje app service wiedzieć, że kontener jest w dobrej kondycji i gotowy do odpowiedzi na żądania.

