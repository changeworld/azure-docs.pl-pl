---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38942731"
---
Podczas tworzenia funkcji lokalnie, można zainstalować rozszerzenia, które należy przy użyciu podstawowych narzędzi usługi Azure Functions z z poziomu terminalu lub wierszu polecenia. 

Po zaktualizowaniu usługi *function.json* pliku, aby uwzględnić wszystkie powiązania, które należy uruchomić funkcję `func extensions install` polecenia w folderze projektu. Polecenie odczytuje *function.json* plik, aby zobaczyć, które pakiety muszą, a następnie jego zainstalowanie.

Jeśli chcesz zainstalować określoną wersję pakietu lub chcesz zainstalować pakiety przed rozpoczęciem edycji *function.json* pliku, użyj `func extensions install` polecenia nazwą pakietu, jak pokazano w poniższym przykładzie:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Zastąp `<target_version>` z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org).
