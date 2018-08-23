---
title: Twórz i uruchamiaj lokalnie usługi Azure functions | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kodowanie i testowanie usługi Azure functions na lokalnym komputerze, przed uruchomieniem w usłudze Azure Functions.
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
ms.openlocfilehash: 4425c0594f4a3520f780b723d2ffbd41fc4b62c7
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056529"
---
# <a name="code-and-test-azure-functions-locally"></a>Kodowanie i testowanie usługi Azure Functions lokalnie

Gdy jesteś w stanie na opracowywanie i testowanie usługi Azure Functions w [Azure Portal], wielu programistów wolą środowisko rozwoju lokalnego. Funkcje ułatwia tworzenie i testowanie funkcji na komputerze lokalnym przy użyciu Twojego ulubionego kodu edytora i narzędzi programistycznych. Lokalnych funkcji nawiązać połączenie na żywo usług platformy Azure oraz Debuguj je na komputerze lokalnym przy użyciu pełne środowisko uruchomieniowe usługi Functions.

## <a name="local-development-environments"></a>Lokalne wdrażanie środowisk

Sposób tworzenia funkcji na komputerze lokalnym jest zależna od usługi [języka](supported-languages.md) i preferencje narzędzi. Środowisk w poniższej tabeli obsługują rozwoju lokalnego:

|Środowisko                              |Języki         |Opis|
|-----------------------------------------|------------|---|
| [Wiersz polecenia lub terminalu](functions-run-local.md) | [C# (Biblioteka klas)](functions-dotnet-class-library.md), [skrypt języka C# (csx)](functions-reference-csharp.md), [języka JavaScript](functions-reference-node.md) | [Podstawowe narzędzia usługi Azure Functions] udostępnia podstawowe środowisko wykonawcze i szablony do tworzenia funkcji, który umożliwia tworzenie aplikacji lokalnych. W wersji 2.x obsługuje programowanie w systemie Linux, MacOS i Windows. Wszystkie środowiska zależy od podstawowych narzędzi dla lokalne środowisko uruchomieniowe usługi Functions. |
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [Skrypt języka C# (csx)](functions-reference-csharp.md), [języka JavaScript](functions-reference-node.md) | [Rozszerzenie usługi Azure Functions dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dodaje funkcji obsługi do programu VS Code. Wymaga podstawowych narzędzi. Obsługuje programowanie w systemie Linux, MacOS i Windows, w przypadku korzystania z wersji 2.x podstawowych narzędzi. Aby dowiedzieć się więcej, zobacz [Wdróż na platformie Azure przy użyciu usługi Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (Biblioteka klas)](functions-dotnet-class-library.md) | Narzędzia usługi Azure Functions są objęte **programowanie na platformie Azure** obciążenie [programu Visual Studio 2017 w wersji 15.5](https://www.visualstudio.com/vs/) i nowszych wersjach. Umożliwia kompilowanie funkcji w bibliotece klas i opublikować plik .dll na platformie Azure. Zawiera podstawowe narzędzia na potrzeby testowania lokalnego. Aby dowiedzieć się więcej, zobacz [Tworzenie usługi Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Integruje się z podstawowe narzędzia umożliwiające tworzenie aplikacji funkcji języka Java. W wersji 2.x obsługuje programowanie w systemie Linux, MacOS i Windows. Aby dowiedzieć się więcej, zobacz [tworzenie pierwszej funkcji przy użyciu języka Java i Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Każda z tych środowisk programowania lokalnego umożliwia tworzenie projektów aplikacji funkcji i utworzyć nowe funkcje za pomocą wstępnie zdefiniowanych szablonów funkcji. Podstawowe narzędzia każdej używane tak, aby można testowanie i debugowanie funkcji względem rzeczywiste środowisko uruchomieniowe usługi Functions na własnym komputerze, tak samo jak każdą inną aplikację. Można również opublikować możesz projektu aplikacji funkcji za pomocą dowolnego z tych środowisk na platformie Azure.  

## <a name="next-steps"></a>Kolejne kroki

+ Aby dowiedzieć się więcej o lokalnym programowaniu skompilowanych funkcji języka C#, za pomocą programu Visual Studio 2017, zobacz [Tworzenie usługi Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md).
+ Aby dowiedzieć się więcej o lokalnym programowaniu funkcji na komputerze Mac, Linux lub Windows za pomocą programu VS Code, zobacz [dokumentacji programu VS Code dla usługi Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Aby dowiedzieć się więcej na temat tworzenia funkcji z wiersza polecenia lub terminalu, zobacz [pracy przy użyciu podstawowych narzędzi usługi Azure Functions](functions-run-local.md).

<!-- LINKS -->

[Podstawowe narzędzia usługi Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
