---
title: Rejestrowanie rozszerzeń powiązań usługi Azure Functions
description: Dowiedz się, aby zarejestrować rozszerzenie powiązania usługi Azure Functions na podstawie środowiska.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277520"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązań usługi Azure Functions

W usłudze Azure Functions w wersji 2.x [powiązania](./functions-triggers-bindings.md) są dostępne jako oddzielne pakiety ze środowiska wykonawczego funkcji. Podczas gdy funkcje .NET uzyskują dostęp do powiązań za pośrednictwem pakietów NuGet, pakiety rozszerzeń umożliwiają innym funkcjom dostęp do wszystkich powiązań za pomocą ustawienia konfiguracji.

Należy wziąć pod uwagę następujące elementy związane z rozszerzeniami powiązania:

- Rozszerzenia powiązania nie są jawnie zarejestrowane w funkcji 1.x, z wyjątkiem podczas [tworzenia biblioteki klas C# przy użyciu programu Visual Studio.](#local-csharp)

- Wyzwalacze HTTP i timer są domyślnie obsługiwane i nie wymagają rozszerzenia.

W poniższej tabeli wskazano, kiedy i jak zarejestrować powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w funkcjach 1.x  |Rejestracja<br/> w funkcjach 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal Azure|Automatyczny|Automatyczny|
|Non-.NET języków lub lokalnego tworzenia narzędzi azure core|Automatyczny|[Korzystanie z podstawowych narzędzi i pakietów rozszerzeń usług Azure Functions](#extension-bundles)|
|Biblioteka klas języka C# przy użyciu programu Visual Studio|[Korzystanie z narzędzi NuGet](#vs)|[Korzystanie z narzędzi NuGet](#vs)|
|Biblioteka klas języka C# przy użyciu kodu programu Visual Studio|Nie dotyczy|[Użyj interfejsu wiersza polecenia .NET Core](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Pakiety rozszerzeń dla rozwoju lokalnego

Pakiety rozszerzeń to technologia wdrażania, która umożliwia dodawanie zgodnego zestawu rozszerzeń powiązania funkcji do aplikacji funkcji. Wstępnie zdefiniowany zestaw rozszerzeń są dodawane podczas tworzenia aplikacji. Pakiety rozszerzeń zdefiniowane w pakiecie są ze sobą zgodne, co pomaga uniknąć konfliktów między pakietami. Możesz włączyć pakiety rozszerzeń w pliku host.json aplikacji.  

Pakiety rozszerzeń można używać w wersji 2.x i nowszych wersjach środowiska wykonawczego funkcji. Podczas tworzenia lokalnie upewnij się, że używasz najnowszej wersji [narzędzia Azure Functions Core Tools](functions-run-local.md#v2).

Użyj pakietów rozszerzeń do lokalnego rozwoju przy użyciu narzędzi podstawowych usług Azure Functions, Visual Studio Code i podczas tworzenia zdalnego.

Jeśli nie używasz pakietów rozszerzeń, przed zainstalowaniem jakichkolwiek rozszerzeń powiązania należy zainstalować zestaw .NET Core 2.x SDK na komputerze lokalnym. Pakiety rozszerzeń usuwa to wymaganie dla rozwoju lokalnego. 

Aby użyć pakietów rozszerzeń, zaktualizuj `extensionBundle`plik *host.json,* aby uwzględnić następujący wpis dla:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>Biblioteka klas C\# z programem Visual Studio

W **programie Visual Studio**można instalować pakiety z konsoli Menedżera pakietów za pomocą polecenia [Install-Package,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) jak pokazano w poniższym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Nazwa pakietu używanego dla danego powiązania znajduje się w artykule referencyjnym dla tego powiązania. Na przykład zobacz [sekcję Pakiety](functions-bindings-service-bus.md#functions-1x)w artykule odwołania do powiązania usługi Service Bus .

Zastąp `<TARGET_VERSION>` w przykładzie określoną wersją pakietu, taką jak `3.0.0-beta5`. Prawidłowe wersje są wyświetlane na poszczególnych stronach pakietu w [NuGet.org](https://nuget.org). Główne wersje, które odpowiadają funkcji środowiska wykonawczego 1.x lub 2.x są określone w artykule referencyjnym dla powiązania.

Jeśli używasz `Install-Package` do odwoływania się do powiązania, nie musisz używać [pakietów rozszerzeń](#extension-bundles). Takie podejście jest specyficzne dla bibliotek klas wbudowanych w programie Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Biblioteka klas języka C# z kodem programu Visual Studio

W **programie Visual Studio Code**należy zainstalować pakiety dla projektu biblioteki klas języka C# z wiersza polecenia za pomocą polecenia [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) w wierszu polecenia .NET Core CLI. W poniższym przykładzie pokazano, jak dodać powiązanie:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Interfejsu wiersza polecenia .NET Core można używać tylko dla rozwoju usługi Azure Functions 2.x.

Zamień `<BINDING_TYPE_NAME>` na nazwę pakietu, który zawiera powiązanie, które jest potrzebne. Żądany artykuł referencyjny wiązania można znaleźć na [liście obsługiwanych powiązań](./functions-triggers-bindings.md#supported-bindings).

Zastąp `<TARGET_VERSION>` w przykładzie określoną wersją pakietu, taką jak `3.0.0-beta5`. Prawidłowe wersje są wyświetlane na poszczególnych stronach pakietu w [NuGet.org](https://nuget.org). Główne wersje, które odpowiadają funkcji środowiska wykonawczego 1.x lub 2.x są określone w artykule referencyjnym dla powiązania.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Przykład wyzwalania i powiązania funkcji platformy Azure](./functions-bindings-example.md)
