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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739631"
---
Gdy host funkcji działa lokalnie, zapisuje dzienniki w następującej ścieżce:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

W Windows `<DefaultTempDirectory>` jest pierwszą wartość znaleziono USERPROFILE TMP, TEMP, zmienne środowiskowe lub katalog Windows.
W systemie MacOS lub Linux `<DefaultTempDirectory>` jest zmienna środowiskowa TMPDIR.

> [!NOTE]
> Po uruchomieniu hosta funkcji zastępuje istniejącą strukturę pliku w katalogu.