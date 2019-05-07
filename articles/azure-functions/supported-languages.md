---
title: Obsługiwane języki w usłudze Azure Functions
description: Dowiedz się, jakie języki są obsługiwane (GA) i które są eksperymentalne lub w wersji zapoznawczej.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148818"
---
# <a name="supported-languages-in-azure-functions"></a>Obsługiwane języki w usłudze Azure Functions

W tym artykule wyjaśniono, że poziom obsługi oferowany dla języków, za pomocą usługi Azure Functions.

## <a name="levels-of-support"></a>Poziomy pomocy technicznej

Istnieją trzy poziomy pomocy technicznej:

* **Ogólnie dostępna (GA)** — w pełni obsługiwana i zatwierdzone do użycia w środowisku produkcyjnym.
* **Podgląd** — nie są jeszcze obsługiwane, ale oczekuje się, że osiągną stan wersji ogólnie dostępnej w przyszłości.
* **Eksperymentalne** — obsługiwane i może zostać porzucony w przyszłości; nie gwarantuje stan GA lub ostatecznej wersji zapoznawczej.

## <a name="languages-in-runtime-1x-and-2x"></a>Języki w środowisku uruchomieniowym w wersji 1.x i 2.x

[Dwie wersje środowiska uruchomieniowego usługi Azure Functions](functions-versions.md) są dostępne. W poniższej tabeli przedstawiono, które języki są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Języków eksperymentalnych

Języków eksperymentalnych wersji 1.x nie jest dobrze skalowalna i nie obsługują wszystkie powiązania.

Nie należy używać funkcji eksperymentalnych dla wszystkich elementów, z którym polegasz, ponieważ nie ma żadnych oficjalne wsparcie dla nich. Przypadki pomocy technicznej nie powinny być otwierane w przypadku problemów z języków eksperymentalnych. 

Środowisko uruchomieniowe 2.x wersja nie obsługuje języków eksperymentalnych. Obsługa nowych języków jest dodawana tylko wtedy, gdy język może być obsługiwany w środowisku produkcyjnym. 

### <a name="language-extensibility"></a>Rozszerzalność języka

Środowisko uruchomieniowe 2.x jest przeznaczona do zaoferowania [rozszerzalność języka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Środowisko uruchomieniowe 2.x języka JavaScript i Java są tworzone za pomocą tego rozszerzalności.

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

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
