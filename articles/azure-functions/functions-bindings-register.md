---
title: Rejestrowanie rozszerzeń powiązania usługi Azure Functions
description: Dowiedz się zarejestrować rozszerzenie usługi Azure Functions powiązania zależności od używanego środowiska.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 5969c3e0d270b45347f8132b2d655ba2e56cb2c0
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625893"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązania usługi Azure Functions

W usłudze Azure Functions w wersji 2.x, [powiązania](./functions-triggers-bindings.md) są dostępne jako oddzielne pakiety z środowisko uruchomieniowe usługi functions. Gdy funkcje .NET dostęp powiązania przy użyciu pakietów NuGet, pakietów rozszerzenia dostęp do innych funkcji wszystkie powiązania za pomocą ustawienia konfiguracji.

Należy wziąć pod uwagę następujące elementy związane z powiązań rozszerzenia:

- Rozszerzeń powiązania nie są jawnie zarejestrowane w funkcji 1.x, chyba że [tworzenia C# bibliotekę klasy przy użyciu programu Visual Studio](#local-csharp).

- Wyzwalacze HTTP oraz czasomierzem są obsługiwane domyślnie i nie wymagają rozszerzenia.

Poniższa tabela wskazuje, kiedy i jak należy zarejestrować powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w przypadku funkcji 1.x  |Rejestracja<br/> w przypadku funkcji 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatyczne|Automatyczne|
|Języki inne niż .NET lub lokalny rozwój podstawowych narzędzi usługi Azure|Automatyczne|[Podstawowe narzędzia usługi Azure Functions i pakietów rozszerzeń](#extension-bundles)|
|C#Biblioteka klas przy użyciu programu Visual Studio|[Użyj narzędzia NuGet](#vs)|[Użyj narzędzia NuGet](#vs)|
|Biblioteki klas C# za pomocą programu Visual Studio Code|ND|[Korzystanie z platformy .NET Core interfejsu wiersza polecenia](#vs-code)|

## <a name="extension-bundles"></a>Pakiety rozszerzeń dla wdrożenia lokalnego

Pakiety rozszerzeń jest technologią rozwoju lokalnego środowisko uruchomieniowe 2.x wersji, które umożliwia dodanie zgodne zbiór funkcji do powiązania rozszerzenia do projektu aplikacji funkcji. Te pakiety rozszerzeń są włączone w pakiecie wdrożeniowym podczas wdrażania na platformie Azure. Pakiety sprawia, że wszystkie powiązania opublikowane przez firmę Microsoft jest dostępna za pośrednictwem ustawienia *host.json* pliku. Pakiety rozszerzeń zdefiniowanych w pakiecie są zgodne ze sobą, co pozwala uniknąć konfliktów między pakietami. Podczas tworzenia lokalnie, upewnij się, że używasz najnowszej wersji [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#v2).

Użyj pakietów rozszerzeń dla wszystkich wdrożenia lokalnego przy użyciu podstawowych narzędzi usługi Azure Functions lub Visual Studio Code.

Jeśli nie używasz pakietów rozszerzeń, należy zainstalować platformy .NET Core SDK 2.x na komputerze lokalnym, przed zainstalowaniem jakichkolwiek rozszerzeń powiązania. Pakiety usuwa wymaganie rozwoju lokalnego. 

Aby użyć pakietów rozszerzeń, należy zaktualizować *host.json* pliku, aby uwzględnić następujący wpis dotyczący `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Następujące właściwości są dostępne w `extensionBundle`:

| Właściwość | Opis |
| -------- | ----------- |
| **`id`** | Przestrzeń nazw pakietów rozszerzeń usługi Microsoft Azure Functions. |
| **`version`** | Wersja pakietu do zainstalowania. Środowisko uruchomieniowe usługi Functions zawsze wybiera maksymalna wersja dopuszczalna definiowane przez zakres wersji lub przy określonym interwale. Wartość wersji powyżej zezwala na wszystkie wersje pakietu z 1.0.0 maksymalnie z wyjątkiem 2.0.0. Aby uzyskać więcej informacji, zobacz [notacji interwału do określania zakresów wersji](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Inkrementacja wersje pakietu jako pakiety w zmiany pakietu. Wersja główna zmiany wystąpić, gdy pakiety w pakiecie zwiększenia wersję główną zwykle pokrywa się ze zmianą w wersji głównej środowisko uruchomieniowe usługi Functions.  

Bieżący zestaw zainstalowanych przez domyślny pakiet rozszerzeń są wyliczane w tym [pliku extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="vs"></a> C\# biblioteki klas w programie Visual Studio

W **programu Visual Studio**, możesz zainstalować pakiety z konsoli Menedżera pakietów przy użyciu [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) polecenia, jak pokazano w poniższym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Nazwa pakietu używanego do wiązania danej znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<TARGET_VERSION>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

Jeśli używasz `Install-Package` k odkazu powiązanie, nie trzeba używać [pakiety rozszerzeń](#extension-bundles). To podejście jest specyficzny dla bibliotek klas, utworzone w programie Visual Studio.

## <a name="vs-code"></a> Biblioteki klas C# za pomocą programu Visual Studio Code

> [!NOTE]
> Firma Microsoft zaleca używanie [pakiety rozszerzeń](#extension-bundles) zapewnienie funkcji automatycznej instalacji zgodnego zestawu powiązania pakiety rozszerzeń.

W **programu Visual Studio Code**, zainstalowanie pakietów dla C# projekt biblioteki klas z wiersza polecenia przy użyciu [dotnet Dodaj pakiet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) polecenia w interfejsie wiersza polecenia platformy .NET Core. W poniższym przykładzie pokazano, jak dodać powiązanie:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core interfejsu wiersza polecenia należy używać tylko dla usługi Azure Functions 2.x rozwoju.

Zastąp `<BINDING_TYPE_NAME>` nazwą pakietu, dostępne w artykule dotyczącym struktury dla żądanego wiązania. Można znaleźć żądanego powiązania odwołanie do artykułu w [listę obsługiwanych powiązania](./functions-triggers-bindings.md#supported-bindings).

Zastąp `<TARGET_VERSION>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Usługa Azure przykład wyzwalacz i powiązania funkcji](./functions-bindings-example.md)
