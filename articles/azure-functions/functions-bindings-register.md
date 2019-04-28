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
origin.date: 02/18/2019
ms.date: 04/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437858"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązania usługi Azure Functions

Usługa Azure Functions obsługuje HTTP oraz czasomierzem gotowe. Aby pracować z innymi usługami, musisz zainstalować lub *zarejestrować* [powiązania](./functions-triggers-bindings.md) rozszerzenia. Rozszerzenia wiązania są udostępniane za pośrednictwem podstawowych narzędzi usługi Azure lub NuGet pakietów. 

Poniższa tabela wskazuje, kiedy i jak należy zarejestrować powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w przypadku funkcji 1.x  |Rejestracja<br/> w przypadku funkcji 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatyczny|[Automatyczne z wiersza](#azure-portal-development)|
|Języki inne niż .NET lub lokalny rozwój podstawowych narzędzi usługi Azure|Automatyczny|[Użyj poleceń interfejsu wiersza polecenia podstawowych narzędzi](#local-development-azure-functions-core-tools)|
|Biblioteki klas C# za pomocą programu Visual Studio 2017|[Użyj narzędzia NuGet](#c-class-library-with-visual-studio-2017)|[Użyj narzędzia NuGet](#c-class-library-with-visual-studio-2017)|
|Biblioteki klas C# za pomocą programu Visual Studio Code|ND|[Korzystanie z platformy .NET Core interfejsu wiersza polecenia](#c-class-library-with-visual-studio-code)|

Wyjątki, które nie wymagają jawna rejestracja, ponieważ są automatycznie rejestrowane we wszystkich wersjach i środowiska są następujące typy powiązania: HTTP oraz czasomierzem.

> [!IMPORTANT]
> Ta zawartość w pozostałej części tego artykułu ma zastosowanie tylko do funkcji 2.x. Rozszerzeń powiązania nie są jawnie zarejestrowane w funkcji 1.x, chyba że [tworzenia C# bibliotekę klasy przy użyciu programu Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Programowanie na platformie Azure portal

Podczas tworzenia funkcji lub dodać powiązanie, zostanie wyświetlony monit, gdy rozszerzenie dla wyzwalaczem lub powiązaniem wymaga rejestracji. Odpowiadanie do wiersza polecenia, klikając **zainstalować** do zarejestrowania rozszerzenia. Instalacja może trwać do 10 minut, w ramach planu zużycie. 

Każdego rozszerzenia, należy zainstalować jeden raz dla danej funkcji aplikacji. Dla obsługiwanych powiązań, które nie są dostępne w portalu lub zaktualizować zainstalowanego rozszerzenia można także [ręczne instalowanie lub aktualizowanie usługi Azure Functions powiązania rozszerzeń z portalu](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Lokalne programowanie podstawowych narzędzi usługi Azure Functions

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Biblioteki klas C# za pomocą programu Visual Studio 2017

W **programu Visual Studio 2017**, możesz zainstalować pakiety z konsoli Menedżera pakietów przy użyciu [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) polecenia, jak pokazano w poniższym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Nazwa pakietu do użycia dla danego powiązania znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<target_version>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteki klas C# za pomocą programu Visual Studio Code

W **programu Visual Studio Code**, możesz zainstalować pakiety z wiersza polecenia przy użyciu [dotnet Dodaj pakiet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) polecenia w .NET Core interfejsu wiersza polecenia, jak pokazano w poniższym przykładzie:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core interfejsu wiersza polecenia należy używać tylko dla usługi Azure Functions 2.x rozwoju.

Nazwa pakietu do użycia dla danego powiązania znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<target_version>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Usługa Azure przykład wyzwalacz i powiązania funkcji](./functions-bindings-example.md)


