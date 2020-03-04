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
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255826"
---
## <a name="robots933456-in-logs"></a>robots933456 w dziennikach

W dziennikach kontenerów może zostać wyświetlony następujący komunikat:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Możesz bezpiecznie zignorować ten komunikat. `/robots933456.txt` to fikcyjna ścieżka URL, której App Service używa do sprawdzenia, czy kontener może obsługiwać żądania. Odpowiedź na 404 wskazuje na to, że ścieżka nie istnieje, ale pozwala App Service wiedzieć, że kontener jest w dobrej kondycji i jest gotowy do reagowania na żądania.

