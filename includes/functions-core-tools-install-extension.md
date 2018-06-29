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
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063752"
---
Podczas opracowywania funkcji lokalnie, można zainstalować rozszerzenia, które należy za pomocą narzędzi Azure podstawowe funkcje z terminala lub z wiersza polecenia. 

Po zaktualizowaniu Twojej *function.json* pliku, aby uwzględnić wszystkie powiązania musi systemem funkcji `func extensions install` polecenia w folderze projektu. Polecenie odczytuje *function.json* pliku, aby zobaczyć, które pakiety muszą, a następnie instaluje je.

Jeśli chcesz zainstalować pakiet w określonej wersji lub chcesz zainstalować pakiety przed rozpoczęciem edycji *function.json* plików, użyj `func extensions install` polecenia o nazwie pakietu, jak pokazano w poniższym przykładzie:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Zastąp `<target_version>` z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowe wersje są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org).
