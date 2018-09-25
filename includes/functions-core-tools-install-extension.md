---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044513"
---
Podczas tworzenia funkcji lokalnie, można zainstalować rozszerzenia, które należy przy użyciu podstawowych narzędzi usługi Azure Functions z z poziomu terminalu lub wierszu polecenia.

Po zaktualizowaniu usługi *function.json* pliku, aby uwzględnić wszystkie powiązania, które wymaga funkcji, uruchom następujące polecenie w folderze projektu.

```bash
func extensions install
```

Polecenie odczytuje *function.json* plik, aby wyświetlić pakiety, których potrzebujesz, instaluje je i ponownie kompiluje projekt rozszerzenia. Dodanie nowych powiązań w bieżącej wersji, ale nie aktualizuje istniejące powiązania. Użyj `--force` opcję, aby zaktualizować istniejące powiązania do najnowszej wersji, podczas instalowania nowych.

Jeśli chcesz zainstalować określoną wersję pakietu lub chcesz zainstalować pakiety przed rozpoczęciem edycji *function.json* pliku, użyj `func extensions install` polecenia nazwą pakietu, jak pokazano w poniższym przykładzie:

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Zastąp `<target_version>` z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org).
