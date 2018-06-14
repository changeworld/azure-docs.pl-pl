---
title: Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie
description: Środowisko Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, różnice między nimi i sposobu wybierz jedną, które jest odpowiednie dla Ciebie.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919359"
---
# <a name="azure-functions-runtime-versions-overview"></a>Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie

 Istnieją dwa główne wersje środowiska uruchomieniowego usługi Azure Functions.: 1.x i 2.x. W tym artykule wyjaśniono, jak wybrać wersję, która głównych do użycia.

> [!IMPORTANT] 
> Środowisko uruchomieniowe 1.x jest tylko wersja zatwierdzone do użycia w środowisku produkcyjnym.

| Środowisko uruchomieniowe | Stan |
|---------|---------|
|1.x|Ogólnie dostępna (GA)|
|2.x|Wersja zapoznawcza|

Aby uzyskać informacje o sposobie konfigurowania aplikacji funkcji lub środowiska programowania dla określonej wersji, zobacz [jak korzystać z wersji środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md) i [kodu oraz testów usługi Azure Functions lokalnie](functions-run-local.md).

## <a name="cross-platform-development"></a>Wiele platform

Środowisko uruchomieniowe 1.x obsługuje programowanie i obsługują tylko w portalu lub w systemie Windows. Uruchamia 2.x środowiska uruchomieniowego .NET Core, co oznacza, że można uruchomić na wszystkich platformach obsługiwanych przez oprogramowanie .NET Core, w tym system macOS i Linux. Dzięki temu wiele platform i scenariusze hostingu, które nie są możliwe za pomocą 1.x.

## <a name="languages"></a>Języki

Środowisko uruchomieniowe 2.x używa nowego modelu rozszerzalności języka. Początkowo JavaScript i Java są korzystanie z tego nowego modelu. Azure języków eksperymentalne 1.x funkcje nie zostały zaktualizowane do używania nowego modelu, więc nie są obsługiwane w 2.x. W poniższej tabeli przedstawiono, które języki programowania są obsługiwane w każdej wersji środowiska wykonawczego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Aby uzyskać więcej informacji, zobacz [obsługiwanych języków](supported-languages.md).

## <a name="bindings"></a>Powiązania 

Środowisko uruchomieniowe nowej używa 2.x [powiązanie modelu rozszerzalności](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) który ma następujące zalety:

* Obsługę rozszerzeń innych firm powiązania.
* Oddzielenie od siebie środowiska uruchomieniowego i powiązania. Dzięki temu rozszerzenia powiązania za kontrolą wersji i wydania niezależnie. Można na przykład zdecydować się na uaktualnienie do wersji rozszerzenia, które korzysta z nowszej wersji podstawowego zestawu SDK.
* Jaśniejszy środowiska wykonania, gdy tylko powiązania używane są znane i załadowane w czasie wykonywania.

Wszystkie powiązania usługi Azure Functions wbudowanych wdrożyły tego modelu, a nie jest już znajdują się domyślnie, z wyjątkiem wyzwalacz czasomierza i wyzwalacza HTTP. Te rozszerzenia są instalowane automatycznie podczas tworzenia funkcji za pomocą takich narzędzi jak Visual Studio lub za pośrednictwem portalu.

W poniższej tabeli przedstawiono powiązania, które są obsługiwane w każdej wersji środowiska wykonawczego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Znane problemy w 2.x

Aby uzyskać więcej informacji dotyczących powiązań pomocy technicznej i inne przerwy funkcjonalności w 2.x, zobacz [Runtime 2.0 znane problemy](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Do wykonywania 2.0 w środowisku projektowania lokalnego](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobacz informacje o wersji dla wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)