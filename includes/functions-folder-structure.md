---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811561"
---
Kod dla wszystkich funkcji w aplikacji określonych funkcji znajduje się w folderze głównym (`wwwroot`) zawiera plik konfiguracji hosta i co najmniej jeden podfolder. Każdy podfolder zawiera kod dla oddzielnych funkcji, jak w poniższym przykładzie:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

Pliku host.json zawiera pewne konfiguracje specyficzne dla środowiska uruchomieniowego i znajduje się w folderze głównym aplikacji funkcji. Aby uzyskać informacje dotyczące ustawień, które są dostępne, zobacz [dokumentacja pliku host.JSON](../articles/azure-functions/functions-host-json.md).

Każda funkcja ma folder, który zawiera jeden lub więcej plików kodu, konfiguracji function.json i inne zależności. Dla języka C# projekt biblioteki klas, wdrożono plik biblioteki (.dll) skompilowanych klasy `bin` podfolderu.

