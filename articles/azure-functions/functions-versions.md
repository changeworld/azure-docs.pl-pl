---
title: Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie
description: Usługa Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, różnice między nimi i sposobu wybierz ten, który jest odpowiedni dla Ciebie.
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
ms.openlocfilehash: 8ba12f21d76d8deded047f40489c46657c9380b8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38602121"
---
# <a name="azure-functions-runtime-versions-overview"></a>Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie

 Istnieją dwie wersje główne środowiska uruchomieniowego usługi Azure Functions: wersji 1.x i 2.x. Tylko 1.x zostanie zatwierdzona do użycia w środowisku produkcyjnym. W tym artykule wyjaśniono, what's new in 2.x, która jest dostępna w wersji zapoznawczej.

| Środowisko uruchomieniowe | Stan |
|---------|---------|
|1.x|Ogólnie dostępna (GA)|
|2.x|Wersja zapoznawcza|

> [!NOTE] 
> Ten artykuł odnosi się do usługi w chmurze usługi Azure Functions. Aby uzyskać informacji na temat produktu, który pozwala na uruchamianie usługi Azure Functions w środowisku lokalnym, zobacz [Przegląd środowiska uruchomieniowego usługi Azure Functions](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Programowanie dla wielu platform

Środowisko uruchomieniowe 1.x obsługuje programowanie i hosting tylko w portalu lub Windows. Środowisko uruchomieniowe 2.x działa na platformie .NET Core, co oznacza, że można uruchomić na wszystkich platformach obsługiwanych przez platformę .NET Core, w tym w systemach macOS i Linux. Dzięki temu programowanie dla wielu platform i scenariuszy hostowania, które nie są możliwe dzięki 1.x.

## <a name="languages"></a>Języki

Środowisko uruchomieniowe 2.x używa nowego modelu rozszerzalności języka. Początkowo JavaScript i Java jest korzystanie z tego nowego modelu. Usługa Azure języków eksperymentalnych 1.x funkcje nie zostały zaktualizowane do użycia nowego modelu, dzięki czemu nie są obsługiwane w 2.x. Poniższa tabela wskazuje, które języki programowania są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Aby uzyskać więcej informacji, zobacz [obsługiwane języki](supported-languages.md).

## <a name="bindings"></a>Powiązania 

Środowisko uruchomieniowe 2.x używa nowego [powiązanie modelu rozszerzalności](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , oferuje następujące korzyści:

* Obsługa rozszerzeń powiązania innych firm.
* Rozdzielenie środowiska uruchomieniowego i powiązania. Dzięki temu rozszerzeń powiązania jako numerów wersji i wydania niezależnie. Można na przykład zdecydować się na uaktualnienie do wersji rozszerzenia, która opiera się na nowszą wersję zestawu SDK bazowego.
* Jaśniejszy środowiska wykonania, gdy tylko powiązania używane są znane i ładowane w czasie wykonywania.

Wszystkie wbudowane powiązania usługi Azure Functions zdecydowali się tego modelu, a nie jest już znajdują się domyślnie, z wyjątkiem wyzwalacza czasomierza i wyzwalacza HTTP. Te rozszerzenia są instalowane automatycznie podczas tworzenia funkcji za pomocą narzędzi, takich jak Visual Studio lub za pośrednictwem portalu.

Poniższa tabela wskazuje, które powiązania są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Znane problemy w 2.x

Aby uzyskać więcej informacji o obsłudze powiązania i inne luki funkcjonalne w 2.x, zobacz [znane problemy w wersji 2.0 środowisko uruchomieniowe](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
* [Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md)
* [Informacje o wersji](https://github.com/Azure/azure-functions-host/releases)