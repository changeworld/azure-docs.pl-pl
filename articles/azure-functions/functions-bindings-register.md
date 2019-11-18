---
title: Rejestrowanie rozszerzeń powiązań Azure Functions
description: Dowiedz się, jak zarejestrować rozszerzenie powiązania Azure Functions w oparciu o środowisko.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 40dca0797d75597f4728423eb9d6d071a15d81b9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129266"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązań Azure Functions

W Azure Functions w wersji 2. x [powiązania](./functions-triggers-bindings.md) są dostępne jako oddzielne pakiety w środowisku uruchomieniowym funkcji. Chociaż usługa .NET Functions uzyskuje dostęp do powiązań za pomocą pakietów NuGet, zbiory rozszerzeń umożliwiają innym funkcjom dostęp do wszystkich powiązań za pomocą ustawienia konfiguracji.

Należy wziąć pod uwagę następujące elementy związane z rozszerzeniami powiązań:

- Rozszerzenia powiązań nie są jawnie zarejestrowane w funkcjach 1. x z wyjątkiem [tworzenia biblioteki C# klas przy użyciu programu Visual Studio](#local-csharp).

- Wyzwalacze protokołu HTTP i czasomierze są domyślnie obsługiwane i nie wymagają rozszerzenia.

Poniższa tabela zawiera informacje o tym, kiedy i w jaki sposób rejestrujesz powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w funkcjach 1. x  |Rejestracja<br/> w funkcjach 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatyczne|Automatyczne|
|Programowanie w języku Non-.NET lub lokalne podstawowe narzędzia platformy Azure|Automatyczne|[Użyj Azure Functions Core Tools i pakietów rozszerzeń](#extension-bundles)|
|C#Biblioteka klas przy użyciu programu Visual Studio|[Korzystanie z narzędzi NuGet](#vs)|[Korzystanie z narzędzi NuGet](#vs)|
|C#Biblioteka klas przy użyciu Visual Studio Code|Nie dotyczy|[Użyj interfejs wiersza polecenia platformy .NET Core](#vs-code)|

## <a name="extension-bundles"></a>Zestawy rozszerzeń do lokalnego tworzenia

Pakiety rozszerzeń to technologia wdrażania, która umożliwia dodawanie zgodnego zestawu funkcji powiązań dla aplikacji funkcji. Podczas kompilowania aplikacji dodawane są wstępnie zdefiniowane rozszerzenia. Pakiety rozszerzeń zdefiniowane w pakiecie są zgodne ze sobą, co pomaga uniknąć konfliktów między pakietami. Zestawy rozszerzeń można włączyć w pliku host. JSON aplikacji.  

Można używać pakietów rozszerzeń z wersją 2. x i nowszymi wersjami środowiska uruchomieniowego Functions. Podczas programowania lokalnego upewnij się, że używasz najnowszej wersji [Azure Functions Core Tools](functions-run-local.md#v2).

Używaj pakietów rozszerzeń do lokalnego tworzenia przy użyciu Azure Functions Core Tools, Visual Studio Code i podczas zdalnego kompilowania.

Jeśli nie używasz zestawów rozszerzeń, przed zainstalowaniem rozszerzeń powiązań należy zainstalować zestaw SDK programu .NET Core 2. x na komputerze lokalnym. Pakiety rozszerzeń usuwają to wymaganie na potrzeby lokalnego projektowania. 

Aby użyć pakietów rozszerzeń, zaktualizuj plik *host. JSON* w taki sposób, aby zawierał następujący wpis dla `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>Biblioteka klas C\# z programem Visual Studio

W programie **Visual Studio**można zainstalować pakiety z konsoli Menedżera pakietów za pomocą polecenia [install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , jak pokazano w następującym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Nazwa pakietu używana dla danego powiązania znajduje się w artykule referencyjnym dla tego powiązania. Aby zapoznać się z przykładem, zobacz [sekcję Packages artykułu dotyczącego powiązania Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<TARGET_VERSION>` w przykładzie z określoną wersją pakietu, np. `3.0.0-beta5`. Prawidłowe wersje są wymienione na stronach poszczególnych pakietów w witrynie [NuGet.org](https://nuget.org). Wersje główne, które odnoszą się do środowiska uruchomieniowego Functions 1. x lub 2. x, są określone w artykule referencyjnym dla powiązania.

Jeśli używasz `Install-Package` do odwoływania się do powiązania, nie musisz używać [pakietów rozszerzeń](#extension-bundles). To podejście jest specyficzne dla bibliotek klas skompilowanych w programie Visual Studio.

## <a name="vs-code"></a>C# Biblioteka klas z Visual Studio Code

> [!NOTE]
> Zalecamy używanie [pakietów rozszerzeń](#extension-bundles) w celu automatycznego zainstalowania zgodnego zestawu pakietów rozszerzenia powiązania. 

W **Visual Studio Code**Zainstaluj pakiety dla projektu biblioteki C# klas z wiersza polecenia przy użyciu polecenia [dotnet Add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) w interfejs wiersza polecenia platformy .NET Core. Poniższy przykład ilustruje sposób dodawania powiązania:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Interfejs wiersza polecenia platformy .NET Core można używać tylko na potrzeby programowania Azure Functions 2. x.

Zastąp `<BINDING_TYPE_NAME>` nazwą pakietu, który zawiera wymagane powiązanie. Żądany artykuł referencyjny powiązania można znaleźć na [liście obsługiwanych powiązań](./functions-triggers-bindings.md#supported-bindings).

Zastąp `<TARGET_VERSION>` w przykładzie z określoną wersją pakietu, np. `3.0.0-beta5`. Prawidłowe wersje są wymienione na stronach poszczególnych pakietów w witrynie [NuGet.org](https://nuget.org). Wersje główne, które odnoszą się do środowiska uruchomieniowego Functions 1. x lub 2. x, są określone w artykule referencyjnym dla powiązania.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Przykład wyzwalacza i powiązania funkcji platformy Azure](./functions-bindings-example.md)
