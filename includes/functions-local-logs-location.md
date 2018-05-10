---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
Gdy host funkcji działa lokalnie, zapisuje dzienniki na następującej ścieżce:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

W systemie Windows `<DefaultTempDirectory>` jest pierwszą wartość znaleziono USERPROFILE TMP, TEMP, zmienne środowiskowe lub katalogu systemu Windows.
System MacOS lub Linux `<DefaultTempDirectory>` jest zmienną środowiskową TMPDIR.

> [!NOTE]
> Po uruchomieniu hosta funkcji spowoduje zastąpienie istniejącej struktury plików w katalogu.