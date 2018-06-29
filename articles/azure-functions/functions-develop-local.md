---
title: Tworzenie i uruchom usługę Azure functions lokalnie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kod i przetestować usługę Azure functions na komputerze lokalnym, przed uruchomieniem funkcji platformy Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/27/2018
ms.author: glenga
ms.openlocfilehash: 89f94be4cf624914183480362ae23c62c363622f
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088636"
---
# <a name="code-and-test-azure-functions-locally"></a>Kod testu Azure funkcji i lokalnie

Gdy jesteś w stanie opracowanie i przetestowanie usługi Azure Functions w [Azure Portal], wielu deweloperów preferowane środowisko rozwoju lokalnego. Funkcje ułatwia użyj narzędzi edytora i rozwoju ulubionych kodu do tworzenia i testowania funkcji na komputerze lokalnym. Funkcji lokalnego może nawiązać połączenie na żywo usług Azure i można je debugowania na komputerze lokalnym przy użyciu pełnej obsługi funkcji.

## <a name="local-development-environments"></a>Środowisk deweloperskich lokalnego

Sposób rozwijać funkcji na komputerze lokalnym zależy od użytkownika [języka](supported-languages.md) i narzędzi Preferencje. Środowisk w poniższej tabeli obsługuje lokalne działania projektowe:

|Środowisko                              |Języki         |Opis|
|-----------------------------------------|------------|---|
| [Wiersz polecenia lub terminalu](functions-run-local.md) | [Skryptu C# (csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Środowisko Azure Functions podstawowe narzędzia] zapewnia podstawowego środowiska wykonawczego i szablony do tworzenia funkcje, dzięki czemu rozwoju lokalnego. Wersja 2.x programowanie pomocy technicznej w systemie Linux, MacOS i systemu Windows. Wszystkie środowiska polegają na podstawowe narzędzia lokalnego środowiska uruchomieniowego funkcji.|
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [Skryptu C# (csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Rozszerzenia usługi Azure Functions dla kodu VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dodaje funkcji obsługi kodu programu VS. Wymaga podstawowych narzędzi. Obsługuje programowanie w systemie Linux, MacOS i systemu Windows, jeśli korzysta z wersji 2.x podstawowych narzędzi. Aby dowiedzieć się więcej, zobacz [wdrażanie na platformie Azure przy użyciu usługi Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (Biblioteka klas)](functions-dotnet-class-library.md) | Narzędzia usługi Azure Functions znajdują się w **Azure programowanie** obciążenie [programu Visual Studio 2017 wersji 15,5 cala](https://www.visualstudio.com/vs/) i nowszych wersjach. Umożliwia skompilowania funkcji w bibliotece klas i publikowanie biblioteki dll na platformie Azure. Zawiera podstawowe narzędzia do testowania lokalnego. Aby dowiedzieć się więcej, zobacz [Tworzenie usługi Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Integruje się z podstawowe narzędzia umożliwiające projektowanie funkcji języka Java. Wersja 2.x obsługuje programowanie w systemie Linux, MacOS i systemu Windows. Aby dowiedzieć się więcej, zobacz [tworzenie pierwszej funkcji przy użyciu języka Java i Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Każdy z tych środowisk deweloperskich lokalnego umożliwia tworzenie funkcji projekty aplikacji i utworzyć nowe funkcje za pomocą wstępnie zdefiniowanych szablonów funkcji. Każda używa podstawowych narzędzi, dzięki czemu można testowanie i debugowanie funkcji przed rzeczywistym środowisko uruchomieniowe Functions na własnym komputerze, tak jak każda inna aplikacja. Można również opublikować możesz funkcja projektu aplikacji za pomocą dowolnego z tych środowisk Azure.  

## <a name="next-steps"></a>Kolejne kroki

+ Aby dowiedzieć się więcej na temat lokalne działania projektowe skompilowanych funkcji C#, za pomocą programu Visual Studio 2017, zobacz [Tworzenie usługi Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md).
+ Aby dowiedzieć się więcej na temat lokalne działania projektowe funkcji za pomocą VS kodu na komputerze Mac, Linux lub Windows, temacie [kodzie VS dokumentacji usługi Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Aby dowiedzieć się więcej o wdrażaniu funkcji z wiersza polecenia lub terminalu, zobacz [pracować z Azure funkcje podstawowe narzędzia](functions-run-local.md).

<!-- LINKS -->

[Środowisko Azure Functions podstawowe narzędzia]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
