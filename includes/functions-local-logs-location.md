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
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "33814632"
---
Gdy host funkcji działa lokalnie, zapisuje dzienniki na następującej ścieżce:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

W systemie Windows `<DefaultTempDirectory>` jest pierwszą wartość znaleziono USERPROFILE TMP, TEMP, zmienne środowiskowe lub katalogu systemu Windows.
System MacOS lub Linux `<DefaultTempDirectory>` jest zmienną środowiskową TMPDIR.

> [!NOTE]
> Po uruchomieniu hosta funkcji spowoduje zastąpienie istniejącej struktury plików w katalogu.