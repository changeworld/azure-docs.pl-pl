---
title: Odwołanie do dewelopera usług Azure Functions F#
description: Dowiedz się, jak tworzyć usługi Azure Functions przy użyciu skryptu F#.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276766"
---
# <a name="azure-functions-f-developer-reference"></a>Odwołanie dewelopera usług Azure Functions F#

F# dla usługi Azure Functions to rozwiązanie do łatwego uruchamiania małych fragmentów kodu lub "funkcji" w chmurze. Dane przepływa do funkcji F# za pomocą argumentów funkcji. Nazwy argumentów `function.json`są określone w programie , a istnieją wstępnie zdefiniowane nazwy dostępu do rzeczy, takich jak rejestrator funkcji i tokeny anulowania. 

>[!IMPORTANT]
>Skrypt F# (.fsx) jest obsługiwany tylko przez [wersję 1.x](functions-versions.md#creating-1x-apps) środowiska wykonawczego usługi Azure Functions. Jeśli chcesz używać języka F# w wersji 2.x i nowszych wersjach środowiska wykonawczego, należy użyć wstępnie skompilowanego projektu biblioteki klas F# (.fs). Tworzenie, zarządzanie i publikowanie projektu biblioteki klas języka F# przy użyciu programu Visual Studio w taki sposób, w jakim [jest to projekt biblioteki klas języka C#.](functions-dotnet-class-library.md) Aby uzyskać więcej informacji na temat wersji funkcji, zobacz [omówienie wersji środowiska wykonawczego usługi Azure Functions](functions-versions.md).

W tym artykule przyjęto założenie, że zostało już odczytane [odwołanie do dewelopera usług Azure Functions.](functions-reference.md)

## <a name="how-fsx-works"></a>Jak działa plik .fsx
Plik `.fsx` jest skryptem języka F#. Można go traktować jako projekt Języka F#, który jest zawarty w jednym pliku. Plik zawiera zarówno kod dla programu (w tym przypadku funkcji platformy Azure) i dyrektyw do zarządzania zależnościami.

Podczas korzystania `.fsx` z funkcji platformy Azure, często wymagane zestawy są automatycznie uwzględniane dla Ciebie, co pozwala skupić się na funkcji, a nie "standardowy" kod.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu skryptu Języka F# wygląda następująco:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Istnieje udostępniony plik [host.json,](functions-host-json.md) który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu (.fsx) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązania wymagane w [wersji 2.x i nowszych wersjach](functions-versions.md) `extensions.csproj` środowiska wykonawczego Functions są zdefiniowane w pliku, z rzeczywistymi plikami biblioteki w folderze. `bin` Podczas tworzenia lokalnie należy [zarejestrować rozszerzenia powiązania](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, ta rejestracja jest wykonywana dla Ciebie.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami
Każde powiązanie obsługuje niektóre zestaw argumentów, jak szczegółowo w [wyzwalaczach usługi Azure Functions i powiązania odwołania dewelopera.](functions-triggers-bindings.md) Na przykład jednym z powiązań argument wyzwalacza obiektu blob jest POCO, które mogą być wyrażone przy użyciu rekordu F#. Przykład:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Funkcja platformy Azure języka F# będzie przyjmować jeden lub więcej argumentów. Gdy mówimy o argumenty usługi Azure Functions, odwołujemy się do argumentów *wejściowych* i argumentów *wyjściowych.* Argument wejściowy jest dokładnie to, co brzmi jak: dane wejściowe do funkcji platformy Azure F#. Argument *danych wyjściowych* jest `byref<>` modyfikowalne dane lub argument, który służy jako sposób *przekazywania* danych z powrotem z funkcji.

W powyższym `blob` przykładzie jest argumentem wejściowym i `output` jest argumentem wyjściowym. Zauważ, że `byref<>` `output` używane dla (nie ma `[<Out>]` potrzeby, aby dodać adnotacji). Za `byref<>` pomocą typu umożliwia funkcji, aby zmienić, który rekord lub obiekt argument odwołuje się do.

Gdy rekord Języka F# jest używany jako typ wejściowy, definicja `[<CLIMutable>]` rekordu musi być oznaczona, aby umożliwić platformie Azure Functions odpowiednie ustawienie pól przed przekazaniem rekordu do funkcji. Pod maską `[<CLIMutable>]` generuje ustawiacz właściwości rekordu. Przykład:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Klasa F# może być również używana dla argumentów in i out. Dla klasy właściwości zwykle potrzebują getters i ustawiaczów. Przykład:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Rejestrowanie
Aby zalogować dane wyjściowe do [dzienników przesyłania strumieniowego](../app-service/troubleshoot-diagnostic-logs.md) w języku F#, funkcja powinna przyjmować argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Aby uzyskać spójność, zalecamy, aby ten argument został nazwany `log`. Przykład:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Można `async` użyć przepływu pracy, ale wynik musi `Task`zwrócić plik . Można to zrobić `Async.StartAsTask`na przykład za pomocą:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token anulowania
Jeśli funkcja musi obsługiwać zamknięcia bezpiecznie, można [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) nadać mu argument. Można to połączyć na przykład z: `async`

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importowanie obszarów nazw
Przestrzenie nazw można otwierać w zwykły sposób:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące obszary nazw są otwierane automatycznie:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odwoływanie się do zestawów zewnętrznych
Podobnie odwołania do zestawu framework można `#r "AssemblyName"` dodać z dyrektywą.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingowe usługi Azure Functions:

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

Ponadto następujące zespoły są specjalnie zalążene i mogą być odwoływane `#r "AssemblyName"`za pomocą nazwy prostej (np. ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Jeśli chcesz odwołać się do prywatnego zestawu, możesz `bin` przekazać plik złożenia do folderu względem funkcji i odwołać się do niego za pomocą nazwy pliku (np.  `#r "MyAssembly.dll"`). Aby uzyskać informacje na temat przekazywania plików do folderu funkcji, zobacz następującą sekcję dotyczącą zarządzania pakietami.

## <a name="editor-prelude"></a>Edytor Preludium
Edytor obsługujący usługi kompilatora języka F# nie będzie świadomy obszarów nazw i zestawów, które usługa Azure Functions automatycznie zawiera. W związku z tym może być przydatne do uwzględnienia preludium, który pomaga edytorowi znaleźć zestawy, których używasz, i jawnie otworzyć przestrzenie nazw. Przykład:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Gdy usługa Azure Functions wykonuje kod, `COMPILED` przetwarza źródło z zdefiniowanym, więc preludium edytora zostanie zignorowane.

<a name="package"></a>

## <a name="package-management"></a>Zarządzanie pakietami
Aby użyć pakietów NuGet w funkcji `project.json` Języka F#, dodaj plik do folderu funkcji w systemie plików aplikacji funkcyjnej. Oto przykładowy `project.json` plik, który dodaje odwołanie `Microsoft.ProjectOxford.Face` do pakietu NuGet do wersji 1.1.0:

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

Obsługiwane są tylko programy .NET Framework 4.6, `project.json` więc `net46` upewnij się, że plik określa się w sposób pokazany poniżej.

Po przekazaniu `project.json` pliku środowisko wykonawcze pobiera pakiety i automatycznie dodaje odwołania do zestawów pakietu. Nie trzeba dodawać `#r "AssemblyName"` dyrektyw. Wystarczy dodać wymagane `open` instrukcje `.fsx` do pliku.

Można umieścić automatycznie odwołania zestawy w preludium edytora, aby poprawić interakcję edytora z F# Compile Services.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Jak dodać `project.json` plik do funkcji platformy Azure
1. Rozpocznij od upewnienia się, że aplikacja funkcji jest uruchomiona, co można zrobić, otwierając funkcję w witrynie Azure portal. Daje to również dostęp do dzienników przesyłania strumieniowego, w których będą wyświetlane dane wyjściowe instalacji pakietu.
2. Aby przekazać `project.json` plik, użyj jednej z metod opisanych w [sposobie aktualizowania plików aplikacji funkcji](functions-reference.md#fileupdate). Jeśli używasz [ciągłego wdrażania dla usług Azure Functions,](functions-continuous-deployment.md)można dodać `project.json` plik do gałęzi przemieszczania, aby eksperymentować z nim przed dodaniem go do gałęzi wdrażania.
3. Po `project.json` dodaniu pliku zobaczysz dane wyjściowe podobne do następującego przykładu w dzienniku przesyłania strumieniowego funkcji:

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
Aby uzyskać zmienną środowiskową lub `System.Environment.GetEnvironmentVariable`wartość ustawienia aplikacji, użyj, na przykład:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Ponowne odtwarzanie kodu .fsx
Można użyć kodu `.fsx` z innych `#load` plików przy użyciu dyrektywy. Przykład:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Ścieżki zapewnia do `#load` dyrektywy są względem lokalizacji `.fsx` pliku.

* `#load "logger.fsx"`ładuje plik znajdujący się w folderze funkcji.
* `#load "package\logger.fsx"`ładuje plik znajdujący `package` się w folderze w folderze funkcji.
* `#load "..\shared\mylogger.fsx"`ładuje plik znajdujący `shared` się w folderze na tym samym poziomie `wwwroot`co folder funkcji, czyli bezpośrednio pod programem .

Dyrektywa `#load` działa tylko `.fsx` z plikami (skrypt F#), a nie z `.fs` plikami.

## <a name="next-steps"></a>Następne kroki
Więcej informacji zawierają następujące zasoby:

* [F# Przewodnik](/dotnet/articles/fsharp/index)
* [Najważniejsze wskazówki dotyczące funkcji platformy Azure](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)
* [Testowanie funkcji platformy Azure](functions-test-a-function.md)
* [Skalowanie funkcji platformy Azure](functions-scale.md)

