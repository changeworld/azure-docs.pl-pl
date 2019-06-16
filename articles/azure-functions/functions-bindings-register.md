---
title: Rejestrowanie rozszerzeń powiązania usługi Azure Functions
description: Dowiedz się zarejestrować rozszerzenie usługi Azure Functions powiązania zależności od używanego środowiska.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65864543"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązania usługi Azure Functions

W usłudze Azure Functions w wersji 2.x, [powiązania](./functions-triggers-bindings.md) są dostępne jako oddzielne pakiety z środowisko uruchomieniowe usługi functions. Gdy funkcje .NET dostęp powiązania przy użyciu pakietów NuGet, pakietów rozszerzenia dostęp do innych funkcji wszystkie powiązania za pomocą ustawienia konfiguracji.

Należy wziąć pod uwagę następujące elementy związane z powiązań rozszerzenia:

- Rozszerzeń powiązania nie są jawnie zarejestrowane w funkcji 1.x, chyba że [tworzenia C# biblioteki klas przy użyciu programu Visual Studio 2019](#local-csharp).

- Wyzwalacze HTTP oraz czasomierzem są obsługiwane domyślnie i nie wymagają rozszerzenia.

Poniższa tabela wskazuje, kiedy i jak należy zarejestrować powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w przypadku funkcji 1.x  |Rejestracja<br/> w przypadku funkcji 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatyczne|Automatyczne|
|Języki inne niż .NET lub lokalny rozwój podstawowych narzędzi usługi Azure|Automatyczne|[Podstawowe narzędzia usługi Azure Functions i pakietów rozszerzeń](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#Biblioteka klas przy użyciu programu Visual Studio 2019 r.|[Użyj narzędzia NuGet](#c-class-library-with-visual-studio-2019)|[Użyj narzędzia NuGet](#c-class-library-with-visual-studio-2019)|
|Biblioteki klas C# za pomocą programu Visual Studio Code|ND|[Korzystanie z platformy .NET Core interfejsu wiersza polecenia](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Lokalne Programowanie przy użyciu podstawowych narzędzi usługi Azure Functions i pakietów rozszerzeń

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#Biblioteka klas przy użyciu programu Visual Studio 2019 r.

W **Visual Studio 2019**, możesz zainstalować pakiety z konsoli Menedżera pakietów przy użyciu [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) polecenia, jak pokazano w poniższym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Nazwa pakietu używanego do wiązania danej znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<TARGET_VERSION>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteki klas C# za pomocą programu Visual Studio Code

W **programu Visual Studio Code**, możesz zainstalować pakiety z wiersza polecenia przy użyciu [dotnet Dodaj pakiet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) polecenia w .NET Core interfejsu wiersza polecenia, jak pokazano w poniższym przykładzie:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core interfejsu wiersza polecenia należy używać tylko dla usługi Azure Functions 2.x rozwoju.

Nazwa pakietu do użycia dla danego powiązania znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<TARGET_VERSION>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Usługa Azure przykład wyzwalacz i powiązania funkcji](./functions-bindings-example.md)

