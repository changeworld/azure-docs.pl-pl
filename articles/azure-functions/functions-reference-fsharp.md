---
title: Dokumentacja F# dla deweloperów Azure Functions
description: Dowiedz się, jak opracowywać F# Azure Functions przy użyciu skryptu.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975010"
---
# <a name="azure-functions-f-developer-reference"></a>Dokumentacja F# dla deweloperów Azure Functions

F#w przypadku Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu lub "Functions" w chmurze. Przepływy danych do F# funkcji za pośrednictwem argumentów funkcji. Nazwy argumentów są określone w `function.json`i istnieją wstępnie zdefiniowane nazwy do uzyskiwania dostępu do takich elementów jak Rejestrator funkcji i tokeny anulowania. 

>[!IMPORTANT]
>F#skrypt (. FSX) jest obsługiwany tylko w [wersji 1. x](functions-versions.md#creating-1x-apps) środowiska uruchomieniowego Azure Functions. Jeśli chcesz użyć F# programu z wersją 2. x i nowszymi wersjami środowiska uruchomieniowego, musisz użyć prekompilowanego F# projektu biblioteki klas (. FS). Tworzysz, zarządzasz i publikujesz projekt F# biblioteki klas przy użyciu programu Visual Studio, jak w przypadku [ C# projektu biblioteki klas](functions-dotnet-class-library.md). Więcej informacji o wersjach funkcji znajduje się w temacie [Azure Functions wersje środowiska uruchomieniowego — Omówienie](functions-versions.md).

W tym artykule przyjęto założenie, że już odczytano [informacje dotyczące deweloperów Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak działa FSX
Plik `.fsx` jest F# skryptem. Można go traktować jako F# projekt, który znajduje się w pojedynczym pliku. Plik zawiera kod dla programu (w tym przypadku funkcję platformy Azure) i dyrektywy dotyczące zarządzania zależnościami.

Jeśli używasz `.fsx` dla funkcji platformy Azure, często wymagane zestawy są automatycznie dołączane, co pozwala skupić się na funkcji, a nie w kodzie "standardowy".

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu F# skryptu wygląda następująco:

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

Istnieje udostępniony plik [host. JSON](functions-host-json.md) , który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma własny plik kodu (. FSX) i plik konfiguracji powiązania (Function. JSON).

Rozszerzenia powiązań wymagane w [wersji 2. x i nowszych wersjach](functions-versions.md) środowiska uruchomieniowego Functions są zdefiniowane w pliku `extensions.csproj`, z rzeczywistymi plikami biblioteki w folderze `bin`. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami
Każde powiązanie obsługuje jakiś zestaw argumentów, zgodnie z opisem w temacie [Azure Functions wyzwalacze i powiązania deweloperów](functions-triggers-bindings.md). Na przykład jeden z powiązań argumentów obsługiwanych przez wyzwalacz obiektu BLOB to POCO, który można wyrazić przy użyciu F# rekordu. Na przykład:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Funkcja F# platformy Azure będzie mieć jeden lub więcej argumentów. Gdy będziemy mówić o Azure Functions argumentach, odwołująmy się do argumentów *wejściowych* i argumentów *danych wyjściowych* . Argument wejściowy jest dokładnie to, co brzmi: dane wejściowe funkcji F# platformy Azure. Argument *wyjściowy* jest modyfikowalnymi danymi lub `byref<>` argumentem, który służy jako sposób przekazywania *danych z funkcji* .

W powyższym przykładzie `blob` jest argumentem wejściowym, a `output` jest argumentem wyjściowym. Zwróć uwagę, że użyto `byref<>` dla `output` (nie ma potrzeby dodawania adnotacji `[<Out>]`). Użycie typu `byref<>` umożliwia funkcji zmianę rekordu lub obiektu, do którego odwołuje się argument.

Gdy F# rekord jest używany jako typ danych wejściowych, definicja rekordu musi być oznaczona przy użyciu `[<CLIMutable>]`, aby umożliwić Azure Functionsj platformie Ustawianie pól odpowiednio przed przekazaniem rekordu do funkcji. W obszarze okapu `[<CLIMutable>]` generuje metody ustawiające dla właściwości rekordu. Na przykład:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# Klasy można również używać dla argumentów in i out. W przypadku klasy właściwości zazwyczaj będą wymagały metod pobierających i setter. Na przykład:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Rejestrowanie
Aby zalogować dane wyjściowe do [dzienników przesyłania strumieniowego](../app-service/troubleshoot-diagnostic-logs.md) w programie F#, funkcja powinna przyjmować argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). W celu zapewnienia spójności zaleca się, aby ten argument miał nazwę `log`. Na przykład:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Można użyć przepływu pracy `async`, ale wynik musi zwrócić `Task`. Można to zrobić za pomocą `Async.StartAsTask`, na przykład:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token anulowania
Jeśli funkcja musi bezpiecznie obsłużyć zamykanie, można nadać jej [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. Można to połączyć z `async`, na przykład:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importowanie przestrzeni nazw
Przestrzenie nazw można otwierać w zwykły sposób:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące obszary nazw są automatycznie otwierane:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odwołujące się do zestawów zewnętrznych
Podobnie odwołania do zestawów struktury można dodać do dyrektywy `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingu Azure Functions:

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

Ponadto następujące zestawy są specjalne i mogą być przywoływane przez simplename (np. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Jeśli musisz odwołać się do zestawu prywatnego, możesz przekazać plik zestawu do folderu `bin` względem funkcji i odwołać się do niego przy użyciu nazwy pliku (np.  `#r "MyAssembly.dll"`). Informacje o sposobach przekazywania plików do folderu funkcji znajdują się w poniższej sekcji dotyczącej zarządzania pakietami.

## <a name="editor-prelude"></a>Preludium edytora
Edytor obsługujący F# usługi kompilatora nie ma informacji o obszarach nazw i zestawach, które Azure Functions automatycznie dołączane. W związku z tym może być przydatne do uwzględnienia Preludium, który ułatwia edytorowi znalezienie zestawów, z których korzystasz, oraz do jawnie otwartych przestrzeni nazw. Na przykład:

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

Gdy Azure Functions wykonuje swój kod, przetwarza źródło o zdefiniowanej `COMPILED`, więc Preludium edytora zostanie zignorowane.

<a name="package"></a>

## <a name="package-management"></a>Zarządzanie pakietami
Aby używać pakietów NuGet w F# funkcji, należy dodać plik `project.json` do folderu funkcji w systemie plików aplikacji funkcji. Oto przykładowy plik `project.json`, który dodaje odwołanie do pakietu NuGet do `Microsoft.ProjectOxford.Face` wersji 1.1.0:

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

Obsługiwane są tylko .NET Framework 4,6, dlatego upewnij się, że plik `project.json` określa `net46`, jak pokazano poniżej.

Po przekazaniu pliku `project.json` środowisko uruchomieniowe pobiera pakiety i automatycznie dodaje odwołania do zestawów pakietów. Nie musisz dodawać dyrektyw `#r "AssemblyName"`. Po prostu Dodaj wymagane instrukcje `open` do pliku `.fsx`.

Możesz chcieć umieścić automatycznie odwołujące się do zestawów w edytorze Preludium, aby ulepszyć interakcję edytora F# z usługami kompilowania.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Jak dodać plik `project.json` do funkcji platformy Azure
1. Zacznij od upewnienia się, że aplikacja funkcji działa, którą można wykonać, otwierając funkcję w Azure Portal. Zapewnia to również dostęp do dzienników przesyłania strumieniowego, w których będą wyświetlane dane wyjściowe instalacji pakietu.
2. Aby przekazać plik `project.json`, użyj jednej z metod opisanych w artykule [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). Jeśli używasz [ciągłego wdrażania dla Azure Functions](functions-continuous-deployment.md), możesz dodać plik `project.json` do gałęzi tymczasowej, aby eksperymentować z nim przed dodaniem go do rozgałęzienia wdrożenia.
3. Po dodaniu `project.json` pliku będą widoczne dane wyjściowe podobne do następującego przykładu w dzienniku przesyłania strumieniowego funkcji:

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
Aby uzyskać zmienną środowiskową lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable`, na przykład:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Używanie kodu. FSX
Możesz użyć kodu z innych plików `.fsx` za pomocą dyrektywy `#load`. Na przykład:

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

Ścieżki zapewniają `#load` dyrektywie odnoszą się do lokalizacji pliku `.fsx`.

* `#load "logger.fsx"` ładuje plik znajdujący się w folderze funkcji.
* `#load "package\logger.fsx"` ładuje plik znajdujący się w folderze `package` w folderze funkcji.
* `#load "..\shared\mylogger.fsx"` ładuje plik znajdujący się w folderze `shared` na tym samym poziomie co folder funkcji, czyli bezpośrednio w `wwwroot`.

Dyrektywa `#load` działa tylko z plikami `.fsx` (F# skrypt), a nie z plikami `.fs`.

## <a name="next-steps"></a>Następne kroki
Więcej informacji zawierają następujące zasoby:

* [Podręcznik języka F#](/dotnet/articles/fsharp/index)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Testowanie Azure Functions](functions-test-a-function.md)
* [Skalowanie Azure Functions](functions-scale.md)

