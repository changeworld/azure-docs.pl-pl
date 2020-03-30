---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564702"
---
## <a name="register-extensions"></a>Zarejestruj rozszerzenia

Z wyjątkiem wyzwalaczy HTTP i timer, powiązania functions w wersji wykonawczej 2.x i nowszej są implementowane jako pakiety rozszerzeń. W wersji 2.x i poza środowiska wykonawczego usługi Azure Functions, należy jawnie zarejestrować rozszerzenia dla typów powiązań używanych w funkcjach. Wyjątkami od tego są powiązania HTTP i wyzwalacze czasomierza, które nie wymagają rozszerzeń.

Rozszerzenia powiązania można zainstalować indywidualnie lub dodać odwołanie do pakietu rozszerzenia do pliku projektu host.json. Pakiety rozszerzeń usuwa możliwość wystąpienia problemów ze zgodnością pakietu podczas korzystania z wielu typów powiązań. Jest to zalecane podejście do rejestrowania rozszerzeń wiązania. Pakiety rozszerzeń również usuwa wymóg instalowania .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Pakiety rozszerzeń

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Aby dowiedzieć się więcej, zobacz [Rejestrowanie rozszerzeń powiązań usługi Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Przed dodaniem powiązań do pliku function.json należy dodać pakiety rozszerzeń do host.json.

### <a name="register-individual-extensions"></a>Rejestrowanie poszczególnych rozszerzeń

Jeśli musisz zainstalować rozszerzenia, które nie są w pakiecie, można ręcznie zarejestrować poszczególne pakiety rozszerzenia dla określonych powiązań. 

> [!NOTE]
> Aby ręcznie zarejestrować rozszerzenia `func extensions install`za pomocą programu , musisz mieć zainstalowany plik .NET Core 2.x SDK.

Po zaktualizowaniu pliku *function.json* w celu uwzględnienia wszystkich powiązań, których potrzebuje funkcja, uruchom następujące polecenie w folderze projektu.

```bash
func extensions install
```

Polecenie odczytuje plik *function.json,* aby zobaczyć, które pakiety są potrzebne, instaluje je i odbudowuje projekt rozszerzeń. Dodaje żadnych nowych powiązań w bieżącej wersji, ale nie aktualizuje istniejących powiązań. Użyj `--force` opcji, aby zaktualizować istniejące powiązania do najnowszej wersji podczas instalowania nowych.
