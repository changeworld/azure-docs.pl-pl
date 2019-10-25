---
title: Obsługiwane języki w Azure Functions
description: Dowiedz się, które języki są obsługiwane (GA) i które są eksperymentalne lub dostępne w wersji zapoznawczej.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: eac67c43915edb0aed273df40e7b08ca846062bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802454"
---
# <a name="supported-languages-in-azure-functions"></a>Obsługiwane języki w Azure Functions

W tym artykule opisano poziomy pomocy technicznej oferowane w przypadku języków, których można używać z programem Azure Functions.

## <a name="levels-of-support"></a>Poziomy pomocy technicznej

Istnieją trzy poziomy pomocy technicznej:

* **Ogólnie dostępna (ga)** — w pełni obsługiwana i zatwierdzona do użycia w środowisku produkcyjnym.
* **Wersja zapoznawcza** — nie jest jeszcze obsługiwana, ale oczekuje się, że w przyszłości zostanie osiągnięty stan ga.
* **Eksperymentalny** — nieobsługiwany i może zostać porzucony w przyszłości; Brak gwarancji dotyczącej wersji zapoznawczej lub stanu GA.

## <a name="languages-by-runtime-version"></a>Języki według wersji środowiska uruchomieniowego 

Dostępne są [dwie wersje środowiska uruchomieniowego Azure Functions](functions-versions.md) . W poniższej tabeli przedstawiono języki obsługiwane w poszczególnych wersjach środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Języki eksperymentalne

Języki eksperymentalne w wersji 1. x nie są dobrze skalowane i nie obsługują wszystkich powiązań.

Nie używaj eksperymentalnych funkcji dla wszystkich elementów, na których polegasz, ponieważ nie ma oficjalnego wsparcia dla nich. Nie należy otwierać przypadków pomocy technicznej w przypadku problemów z językiem eksperymentalnym. 

Środowisko uruchomieniowe w wersji 2. x nie obsługuje języków eksperymentalnych. Obsługa nowych języków jest dodawana tylko wtedy, gdy język może być obsługiwany w środowisku produkcyjnym. 

### <a name="language-extensibility"></a>Rozszerzalność języka

Począwszy od wersji 2. x, środowisko uruchomieniowe zostało zaprojektowane, aby oferować [rozszerzalność języka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Języki JavaScript i Java w środowisku uruchomieniowym 2. x zostały skompilowane przy użyciu tej rozszerzalności.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach tworzenia funkcji w obsługiwanych językach, zobacz następujące zasoby:

+ [C#Dokumentacja dla deweloperów biblioteki klas](functions-dotnet-class-library.md)
+ [C#Dokumentacja dewelopera skryptów](functions-reference-csharp.md)
+ [Dokumentacja dewelopera języka Java](functions-reference-java.md)
+ [Dokumentacja dewelopera języka JavaScript](functions-reference-node.md)
+ [Dokumentacja dewelopera programu PowerShell](functions-reference-powershell.md)
+ [Dokumentacja dla deweloperów języka Python](functions-reference-python.md)
+ [Dokumentacja dewelopera języka TypeScript](functions-reference-node.md#typescript)
