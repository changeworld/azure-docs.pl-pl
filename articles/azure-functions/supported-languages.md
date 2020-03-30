---
title: Obsługiwane języki w usłudze Azure Functions
description: Dowiedz się, które języki są obsługiwane (GA), a które eksperymentalne lub w wersji zapoznawczej.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942260"
---
# <a name="supported-languages-in-azure-functions"></a>Obsługiwane języki w usłudze Azure Functions

W tym artykule opisano poziomy pomocy technicznej oferowane dla języków, które można używać z usługą Azure Functions.

## <a name="levels-of-support"></a>Poziomy wsparcia

Istnieją trzy poziomy wsparcia:

* **Ogólnie dostępne (GA)** - w pełni obsługiwane i zatwierdzone do użytku produkcyjnego.
* **Wersja zapoznawcza** — nie jest jeszcze obsługiwana, ale oczekuje się, że osiągnie stan GA w przyszłości.
* **Eksperymentalne** — nie jest obsługiwane i może zostać porzucone w przyszłości; brak gwarancji ewentualnego stanu podglądu lub ga.

## <a name="languages-by-runtime-version"></a>Języki według wersji środowiska wykonawczego 

Dostępne są [trzy wersje środowiska wykonawczego usługi Azure Functions.](functions-versions.md) W poniższej tabeli przedstawiono, które języki są obsługiwane w każdej wersji środowiska wykonawczego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Języki eksperymentalne

Języki eksperymentalne w wersji 1.x nie są dobrze skalowane i nie obsługują wszystkich powiązań.

Nie używaj funkcji eksperymentalnych do niczego, na czym polegasz, ponieważ nie ma dla nich oficjalnego wsparcia. Nie należy otwierać przypadków pomocy technicznej w przypadku problemów z językami eksperymentalnymi. 

Nowsze wersje środowiska wykonawczego nie obsługują języków eksperymentalnych. Obsługa nowych języków jest dodawana tylko wtedy, gdy język może być obsługiwany w produkcji. 

### <a name="language-extensibility"></a>Rozszerzalność języka

Począwszy od wersji 2.x, środowisko wykonawcze jest zaprojektowane tak, aby oferować [rozszerzalność języka.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) Języki JavaScript i Java w czasie wykonywania 2.x są tworzone przy tej rozszerzalności.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak tworzyć funkcje w obsługiwanych językach, zobacz następujące zasoby:

+ [Odwołanie do dewelopera biblioteki klas języka C#](functions-dotnet-class-library.md)
+ [Odwołanie do dewelopera skryptu języka C#](functions-reference-csharp.md)
+ [Odwołanie do dewelopera języka Java](functions-reference-java.md)
+ [Odwołanie do programu JavaScript](functions-reference-node.md)
+ [Odwołanie do dewelopera programu PowerShell](functions-reference-powershell.md)
+ [Odwołanie do dewelopera języka Python](functions-reference-python.md)
+ [Odwołanie do programu TypeScript dla deweloperów](functions-reference-node.md#typescript)
