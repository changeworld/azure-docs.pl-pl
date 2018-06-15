---
title: Obsługiwane języki w funkcji platformy Azure
description: Dowiedz się, jakie wersje językowe są obsługiwane (GA) i które są eksperymentalne lub w wersji zapoznawczej.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2017
ms.locfileid: "24879440"
---
# <a name="supported-languages-in-azure-functions"></a>Obsługiwane języki w funkcji platformy Azure

W tym artykule opisano oferowane poziomy wsparcia dla języków, korzystając z usługi Azure Functions.

## <a name="levels-of-support"></a>Poziom obsługi

Istnieją trzy poziomy pomocy technicznej:

* **Ogólnie dostępna (GA)** — w pełni obsługiwane i zatwierdzone do użycia w środowisku produkcyjnym.
* **Podgląd** — nie jest jeszcze obsługiwane, ale może osiągnąć stan GA w przyszłości.
* **Eksperymentalne** — obsługiwane i może zostać przerwane w przyszłości; żadnej gwarancji stan GA lub ostatecznego podglądu.

## <a name="languages-in-runtime-1x-and-2x"></a>Języki w środowisku uruchomieniowym 1.x i 2.x

[Dwie wersje środowiska uruchomieniowego usługi Azure Functions](functions-versions.md) są dostępne. Środowisko wykonawcze 1.x jest po Jest tylko środowiska uruchomieniowego, która jest zatwierdzona przez aplikacje produkcyjne. Środowisko uruchomieniowe 2.x jest obecnie w przeglądzie, tak więc obsługiwanych języków w wersji zapoznawczej. W poniższej tabeli przedstawiono, które języki są obsługiwane w każdej wersji środowiska wykonawczego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Eksperymentalne języków

Eksperymentalne języków 1.x nie skalowalne i nie obsługują wszystkie powiązania. Na przykład Python przebiega powoli, ponieważ środowisko uruchomieniowe Functions działa *python.exe* przy każdym wywołaniu funkcji. I Python obsługuje powiązania protokołu HTTP, nie można uzyskać dostępu do obiektu żądania.

Obsługa eksperymentalna programu PowerShell jest ograniczony do wersji 4.0, ponieważ jest zainstalowanych na maszynach wirtualnych działających w funkcji aplikacji. Jeśli chcesz uruchamiać skrypty programu PowerShell, należy wziąć pod uwagę [usługi Automatyzacja Azure](https://azure.microsoft.com/services/automation/).

Środowisko uruchomieniowe 2.x nie obsługuje języki eksperymentalne. W 2.x możemy dodać obsługę języka tylko wtedy, gdy jest on również skaluje i obsługuje zaawansowane wyzwalaczy.

Jeśli chcesz użyć jednego z języków, które są dostępne tylko w 1.x pozostać na 1.x środowiska uruchomieniowego. Ale nie należy używać eksperymentalne języków dla wszystkich elementów, które korzystają z, ponieważ nie ma żadnych oficjalnego wsparcia dla nich. Możesz poprosić o pomoc przy [stwarza problemy GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues), ale przypadków pomocy technicznej nie powinny być otwierane problemy z języków eksperymentalne. 

### <a name="language-extensibility"></a>Rozszerzalność języka

Środowisko uruchomieniowe 2.x zaprojektowano w celu oferują [rozszerzalności języka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Między pierwszym języków opierać się na rozszerzania model jest języka Java, która jest w wersji zapoznawczej w 2.x.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobie używania jeden z języków GA lub preview w usługi Azure Functions, zobacz następujące zasoby:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)