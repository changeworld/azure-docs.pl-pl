---
title: Lokalne opracowywanie i uruchamianie funkcji platformy Azure
description: Dowiedz się, jak kodować i testować funkcje platformy Azure na komputerze lokalnym przed uruchomieniem ich w usłudze Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230646"
---
# <a name="code-and-test-azure-functions-locally"></a>Kodowanie i testowanie usługi Azure Functions lokalnie

Chociaż możesz tworzyć i testować usługi Azure Functions w [witrynie Azure portal,]wielu deweloperów preferuje lokalne środowisko programistyczne. Funkcje ułatwiają korzystanie z ulubionego edytora kodu i narzędzi programistycznych do tworzenia i testowania funkcji na komputerze lokalnym. Funkcje lokalne można połączyć się z usługami platformy Azure na żywo i można debugować je na komputerze lokalnym przy użyciu pełnego środowiska wykonawczego funkcji.

## <a name="local-development-environments"></a>Lokalne środowiska programistyczne

Sposób tworzenia funkcji na komputerze lokalnym zależy od [języka](supported-languages.md) i preferencji narzędzi. Środowiska w poniższej tabeli obsługują rozwój lokalny:

|Środowisko                              |Języki         |Opis|
|-----------------------------------------|------------|---|
|[Kod programu Visual Studio](functions-develop-vs-code.md)| [C# (biblioteka klas)](functions-dotnet-class-library.md), [Skrypt C# (csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | [Rozszerzenie usługi Azure Functions for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dodaje obsługę funkcji do kodu VS. Wymaga podstawowych narzędzi. Obsługuje programowanie w systemach Linux, MacOS i Windows podczas korzystania z wersji 2.x narzędzi podstawowych. Aby dowiedzieć się więcej, zobacz [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Wiersz polecenia lub terminal](functions-run-local.md) | [C# (biblioteka klas)](functions-dotnet-class-library.md), [Skrypt C# (csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Podstawowe narzędzia azure functions zapewnia] podstawowy środowiska uruchomieniowego i szablony do tworzenia funkcji, które umożliwiają rozwój lokalny. Wersja 2.x obsługuje rozwój w systemach Linux, MacOS i Windows. Wszystkie środowiska opierają się na narzędziach podstawowych dla środowiska wykonawczego funkcji lokalnych. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (biblioteka klas)](functions-dotnet-class-library.md) | Narzędzia usługi Azure Functions są uwzględniane w obciążeniu **deweloperów platformy Azure** [programu Visual Studio 2019](https://www.visualstudio.com/vs/) i nowszych wersjach. Umożliwia kompilowanie funkcji w bibliotece klas i publikowanie biblioteki dll na platformie Azure. Zawiera podstawowe narzędzia do testowania lokalnego. Aby dowiedzieć się więcej, zobacz [Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (różne) | [Java](functions-reference-java.md) | Integruje się z Narzędziami Podstawowymi, aby umożliwić rozwój funkcji Java. Wersja 2.x obsługuje rozwój w systemach Linux, MacOS i Windows. Aby dowiedzieć się więcej, zobacz [Tworzenie pierwszej funkcji za pomocą aplikacji Java i Maven](functions-create-first-java-maven.md). Wspiera również rozwój za pomocą [Eclipse](functions-create-maven-eclipse.md) i [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Każde z tych lokalnych środowisk programistycznych umożliwia tworzenie projektów aplikacji funkcji i używanie wstępnie zdefiniowanych szablonów funkcji do tworzenia nowych funkcji. Każdy używa podstawowych narzędzi, dzięki czemu można przetestować i debugować swoje funkcje względem rzeczywistego środowiska uruchomieniowego funkcji na własnym komputerze, tak jak każda inna aplikacja. Można również opublikować projekt aplikacji funkcji z dowolnego z tych środowisk na platformie Azure.  

## <a name="next-steps"></a>Następne kroki

+ Aby dowiedzieć się więcej na temat lokalnego rozwoju skompilowanych funkcji języka C# przy użyciu programu Visual Studio 2019, zobacz [Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio.](functions-develop-vs.md)
+ Aby dowiedzieć się więcej na temat lokalnego rozwoju funkcji przy użyciu programu VS Code na komputerze Mac, Linux lub Windows, zobacz [Wdrażanie funkcji platformy Azure z programu VS Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Aby dowiedzieć się więcej na temat tworzenia funkcji z wiersza polecenia lub terminala, zobacz [Praca z podstawowymi narzędziami azure functions](functions-run-local.md).

<!-- LINKS -->

[Podstawowe narzędzia funkcji platformy Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
