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
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726891"
---
Podczas opracowywania funkcji lokalnie, można zainstalować rozszerzenia, które należy za pomocą narzędzi Azure podstawowe funkcje z terminala lub z wiersza polecenia. 

Po zaktualizowaniu Twojej *function.json* pliku, aby uwzględnić wszystkie powiązania musi systemem funkcji `func extensions install` polecenia w folderze projektu. Polecenie odczytuje *function.json* pliku, aby zobaczyć, które pakiety muszą, a następnie instaluje je.

Jeśli chcesz zainstalować pakiet w określonej wersji lub chcesz zainstalować pakiety przed rozpoczęciem edycji *function.json* plików, użyj `func extensions install` polecenia o nazwie pakietu, jak pokazano w poniższym przykładzie:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Zastąp `<target_version>` z określoną wersją pakietu. Prawidłowe wersje są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org).
