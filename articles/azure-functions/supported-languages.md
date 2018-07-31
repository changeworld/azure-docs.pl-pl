---
title: Obsługiwane języki w usłudze Azure Functions
description: Dowiedz się, jakie języki są obsługiwane (GA) i które są eksperymentalne lub w wersji zapoznawczej.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 00f291e903948bf43bc997816b6072186cf1f889
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343087"
---
# <a name="supported-languages-in-azure-functions"></a>Obsługiwane języki w usłudze Azure Functions

W tym artykule wyjaśniono, że poziom obsługi oferowany dla języków, za pomocą usługi Azure Functions.

## <a name="levels-of-support"></a>Poziomy pomocy technicznej

Istnieją trzy poziomy pomocy technicznej:

* **Ogólnie dostępna (GA)** — w pełni obsługiwana i zatwierdzone do użycia w środowisku produkcyjnym.
* **Podgląd** — nie są jeszcze obsługiwane, ale oczekuje się, że osiągną stan wersji ogólnie dostępnej w przyszłości.
* **Eksperymentalne** — obsługiwane i może zostać porzucony w przyszłości; nie gwarantuje stan GA lub ostatecznej wersji zapoznawczej.

## <a name="languages-in-runtime-1x-and-2x"></a>Języki w środowisku uruchomieniowym w wersji 1.x i 2.x

[Dwie wersje środowiska uruchomieniowego usługi Azure Functions](functions-versions.md) są dostępne. Środowisko uruchomieniowe 1.x jest GA. Jest tylko środowiska uruchomieniowego, która jest zatwierdzona przez aplikacje produkcyjne. Środowisko uruchomieniowe 2.x jest obecnie w wersji zapoznawczej, więc języki, które obsługuje są w wersji zapoznawczej. W poniższej tabeli przedstawiono, które języki są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Języków eksperymentalnych

Języków eksperymentalnych w 1.x nie skalują się dobrze, a nie obsługują wszystkie powiązania. Na przykład języka Python jest powolne, ponieważ środowisko uruchomieniowe usługi Functions działa *python.exe* przy każdym wywołaniu funkcji. I Python obsługuje powiązania protokołu HTTP, nie można uzyskać dostępu do obiektu żądania.

Eksperymentalna Obsługa programu PowerShell jest ograniczona do wersji 4.0, ponieważ zainstalowanych na maszynach wirtualnych korzystających z aplikacji funkcji. Jeśli chcesz uruchamiać skrypty programu PowerShell, należy wziąć pod uwagę [usługi Azure Automation](https://azure.microsoft.com/services/automation/).

Środowisko uruchomieniowe 2.x nie obsługuje języków eksperymentalnych. W 2.x dodamy obsługę języka, tylko wtedy, gdy jej efektywne i obsługuje zaawansowane wyzwalaczy.

Jeśli chcesz użyć jednego z języków, które są dostępne tylko w 1.x Pozostań na środowisko uruchomieniowe 1.x. Ale nie należy używać języków eksperymentalnych dla wszystkich elementów, które działają, ponieważ nie ma żadnych oficjalne wsparcie dla nich. Możesz poprosić o pomoc przy [stwarza problemy usługi GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues), ale nie należy otwierać przypadki pomocy technicznej o problemach z języków eksperymentalnych. 

### <a name="language-extensibility"></a>Rozszerzalność języka

Środowisko uruchomieniowe 2.x jest przeznaczona do zaoferowania [rozszerzalność języka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Między językami pierwszy opierać się na tym rozszerzalności modelu jest Java, która jest w wersji zapoznawczej w 2.x.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o sposobie używania jednego z języków GA lub w wersji zapoznawczej w usłudze Azure Functions, zobacz następujące zasoby:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)