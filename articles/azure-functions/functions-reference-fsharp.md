---
title: 'Azure dokumentacja dla deweloperów funkcje F # | Dokumentacja firmy Microsoft'
description: 'Zrozumienie sposobu tworzenia usługi Azure Functions przy użyciu języka F #.'
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: ''
tags: ''
keywords: 'Azure funkcji, funkcji, zdarzenia przetwarzania elementów webhook, dynamiczne obliczeń, niekorzystającą architektury, F #'
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 2c84de3f38a49bc97fda04a7a4eb449a1f7d14bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31515598"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions dokumentacja dla deweloperów języka F #

F # dla usługi Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu lub "funkcji" w chmurze. Przepływy danych w funkcji F # za pomocą argumentów funkcji. Argument nazwy zostały określone w `function.json`, i jest wstępnie zdefiniowanych nazw do uzyskiwania dostępu do elementów, jak funkcja tokenów rejestratora i anulowania.

W tym artykule przyjęto założenie, że został już przeczytany [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak działa fsx
`.fsx` Plik jest skryptu języka F #. Go można traktować jako projekt F #, który jest zawarty w jednym pliku. Plik zawiera kod programu (w tym przypadku funkcji Azure) i wytyczne dotyczące zarządzania zależności.

Jeśli używasz `.fsx` dla funkcji platformy Azure, zwykle wymagane zestawy są automatycznie dołączane do Ciebie, co pozwala skupić się na kodzie funkcji, a nie "standardowy".

## <a name="binding-to-arguments"></a>Powiązanie z argumentów
Każdego powiązania obsługuje niektóre zestaw argumentów, zgodnie z opisem w [dokumentacja dla deweloperów usługi Azure Functions wyzwalaczy i powiązań](functions-triggers-bindings.md). Na przykład jest jednym z powiązań argument wyzwalacza obiektu blob obsługiwanych przez POCO, które można wyrazić przy użyciu rekordu języka F #. Na przykład:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Funkcja Azure F # potrwa co najmniej jeden argument. Przy omawianiu usługi Azure Functions argumenty zwane *wejściowych* argumentów i *dane wyjściowe* argumentów. Argument wejściowy jest dokładnie wydaje takich jak: dane wejściowe do funkcji Azure F #. *Dane wyjściowe* argument jest modyfikowalna danych lub `byref<>` argumentu, który służy jako sposób przekazywania danych z powrotem *limit* funkcji.

W powyższym przykładzie `blob` jest argument wejściowy i `output` jest argumentem danych wyjściowych. Należy zauważyć, że użyliśmy `byref<>` dla `output` (nie musi, aby dodać `[<Out>]` adnotacji). Przy użyciu `byref<>` typ umożliwia funkcji zmiany które rekordu lub argument odwołuje się do obiektu.

Gdy rekordu języka F # jest używany jako typ danych wejściowych, muszą być oznaczone definicji rekordu `[<CLIMutable>]` w celu umożliwienia framework usługi Azure Functions można ustawić pola odpowiednio przed przekazaniem rekordu do funkcji. Pod maską `[<CLIMutable>]` generuje metody ustawiające właściwości rekordu. Na przykład:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Można także klasy F # dla wejściowe i wyjściowe argumentów. Dla klasy właściwości należy zwykle ustawiających i pobierających. Na przykład:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Rejestrowanie
Aby rejestrować dane wyjściowe do Twojej [Podgląd dzienników przesyłanych strumieniowo](../app-service/web-sites-enable-diagnostic-log.md) w języku F #, funkcja powinno zająć argumentu typu `TraceWriter`. W celu zachowania spójności, firma Microsoft zaleca, nosi nazwę tego argumentu `log`. Na przykład:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asynchroniczne
`async` Przepływu pracy mogą być używane, ale musi zwracać wynik `Task`. Można to zrobić z `Async.StartAsTask`, na przykład:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token anulowania
Jeśli funkcja trzeba bezpiecznie obsłużyć zamknięcia, można nadać [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumentu. Może to być łączone z `async`, na przykład:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importowanie przestrzenie nazw
Przestrzenie nazw mogą być otwierane w zwykły sposób:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Następujących przestrzeni nazw są automatycznie otwierane:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Zewnętrzne zestawy odwołujące
Podobnie można dodać odwołania do zestawów framework z `#r "AssemblyName"` dyrektywy.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Następujące zestawy są automatycznie dodawane przez usługi Azure Functions Środowisko hostingu:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Ponadto następujące zestawy są specjalne z uwzględnieniem wielkości liter i odwołuje simplename (np. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Jeśli chcesz odwołać zestaw prywatny, możesz przekazać plik zestawu do `bin` folderu względem Twojej funkcji i odwołanie go przy użyciu pliku (np. nazwy  `#r "MyAssembly.dll"`). Aby uzyskać informacje na temat przekazywania plików do folderu funkcji zobacz sekcję poniżej pakietu zarządzania.

## <a name="editor-prelude"></a>Edytor Prelude
Edytor, który obsługuje usługi kompilatora F # nie będą świadomi obszary nazw i zestawy, które automatycznie uwzględnia usługi Azure Functions. W efekcie może być przydatne, obejmują prelude, pomocne w edytorze odnaleźć zestawów, którego używasz i otwierać przestrzeni nazw. Na przykład:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Azure Functions wykonuje kodu, przetwarza źródło o `COMPILED` zdefiniowane, więc prelude edytora zostanie zignorowany.

<a name="package"></a>

## <a name="package-management"></a>Pakiet zarządzania
Aby używać pakietów NuGet w funkcji F #, Dodaj `project.json` plik do folderu funkcji w systemie plików aplikacji funkcji. Oto przykład `project.json` pliku, który dodaje odwołanie do pakietu NuGet, aby `Microsoft.ProjectOxford.Face` wersji 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Obsługiwane jest tylko .NET Framework 4.6, upewnij się, że Twoje `project.json` Określa plik `net46` w sposób pokazany poniżej.

Po przekazaniu `project.json` plików, środowisko uruchomieniowe pobiera pakiety i automatycznie dodaje odwołania do zestawów pakietu. Nie trzeba dodać `#r "AssemblyName"` dyrektywy. Po prostu Dodaj wymagane `open` instrukcje do Twojej `.fsx` pliku.

Możesz umieścić automatycznie zestawów odwołań w Twojej prelude edytora, zwiększające w edytorze interakcji z F # kompilacji usługi.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Jak dodać `project.json` plik, aby z funkcji platformy Azure
1. Rozpocznij od upewnić się, że funkcja aplikacji jest uruchomiony, co można zrobić, otwierając funkcji w portalu Azure. To również udostępnia dzienniki przesyłania strumieniowego gdzie zostaną wyświetlone dane wyjściowe instalacji pakietu.
2. Aby przekazać `project.json` plików, użyj jednej z metod opisanych w [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). Jeśli używasz [ciągłego wdrażania usługi Azure Functions](functions-continuous-deployment.md), możesz dodać `project.json` plików do tymczasowej gałąź celu eksperymentować przed dodaniem jej do swojej gałęzi wdrożenia.
3. Po `project.json` zostanie dodany plik, zostanie wyświetlone dane wyjściowe podobne do poniższego przykładu w funkcji do przesyłania strumieniowego dzienników:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Zmienne środowiskowe
Aby uzyskać wartość zmiennej środowiskowej lub wartość ustawienia aplikacji, należy użyć `System.Environment.GetEnvironmentVariable`, na przykład:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Ponowne wykorzystywanie kodu fsx
Można użyć kodu z innych `.fsx` plików za pomocą `#load` dyrektywy. Na przykład:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Zawiera ścieżki do `#load` dyrektywy są powiązane z lokalizacją użytkownika `.fsx` pliku.

* `#load "logger.fsx"` ładuje plik znajduje się w folderze funkcji.
* `#load "package\logger.fsx"` ładuje plik znajduje się w `package` folderu w folderze funkcji.
* `#load "..\shared\mylogger.fsx"` ładuje plik znajduje się w `shared` folderu na tym samym poziomie co folder funkcji, bezpośrednio pod `wwwroot`.

`#load` Dyrektywy działa tylko z `.fsx` plików (F # skrypt), a nie z `.fs` plików.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Przewodnik F #](/dotnet/articles/fsharp/index)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure funkcje wyzwalaczy i powiązań](functions-triggers-bindings.md)
* [Środowisko Azure Functions testowania](functions-test-a-function.md)
* [Środowisko Azure Functions skalowania](functions-scale.md)

