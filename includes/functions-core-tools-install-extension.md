---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d62da82b4a4dd35532dd8776a9111689db469201
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448359"
---
## <a name="register-extensions"></a>Rejestrowanie rozszerzeń

Z wyjątkiem HTTP oraz czasomierzem wyzwalacze, funkcje powiązania w wersji środowiska uruchomieniowego 2.x są implementowane jako pakiety rozszerzeń. W wersji 2.x środowisko uruchomieniowe usługi Azure Functions, należy jawnie zarejestrować rozszerzenia dla typów powiązania, używany w funkcji. Wyjątki od tej reguły są powiązania protokołu HTTP i wyzwalaczy czasomierza, które nie wymagają rozszerzeń.

Można zainstalować rozszerzenia powiązania indywidualnie lub można dodać odwołanie do pakietu rozszerzenia pliku host.json projektu. Pakiety rozszerzeń usuwa prawdopodobieństwo wystąpienia problemów ze zgodnością pakietu, korzystając z wieloma typami powiązania. Jest to zalecane podejście do rejestrowania rozszerzeń powiązania. Pakiety rozszerzeń również eliminuje konieczność zainstalowania platformy .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>pakiety rozszerzeń

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Aby dowiedzieć się więcej, zobacz [rejestrowania usługi Azure Functions powiązania rozszerzenia](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Należy dodać rozszerzenie pakiety do host.json przed dodaniem powiązań do pliku w pliku functions.json.

### <a name="register-individual-extensions"></a>Zarejestruj poszczególne rozszerzenia

Jeśli musisz zainstalować rozszerzenia, które nie znajdują się w pakiecie, możesz ręcznie zarejestrować rozszerzenia poszczególnych pakietów dla określonego powiązania. 

> [!NOTE]
> Aby ręcznie zarejestrować rozszerzenia za pomocą `func extensions install`, konieczne jest posiadanie platformy .NET Core 2.x zainstalowany zestaw SDK.

Po zaktualizowaniu usługi *function.json* pliku, aby uwzględnić wszystkie powiązania, które wymaga funkcji, uruchom następujące polecenie w folderze projektu.

```bash
func extensions install
```

Polecenie odczytuje *function.json* plik, aby wyświetlić pakiety, których potrzebujesz, instaluje je i ponownie kompiluje projekt rozszerzenia. Dodanie nowych powiązań w bieżącej wersji, ale nie aktualizuje istniejące powiązania. Użyj `--force` opcję, aby zaktualizować istniejące powiązania do najnowszej wersji, podczas instalowania nowych.