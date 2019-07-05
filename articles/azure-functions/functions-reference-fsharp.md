---
title: Usługa Azure Functions F# dokumentacja dla deweloperów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie funkcji platformy Azure przy użyciu F# skryptu.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: Azure funkcji, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bezserwerowaF#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 23e9ffa5c86674cb34951f29573e033b4a904941
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442230"
---
# <a name="azure-functions-f-developer-reference"></a>Usługa Azure Functions F# dokumentacja dla deweloperów

F#dla usługi Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu lub "funkcji" w chmurze. Dane są przesyłane z F# funkcji za pośrednictwem argumentów funkcji. Argument nazwy zostały określone w `function.json`, wiąże się z wstępnie zdefiniowanych nazw do uzyskiwania dostępu do elementów, takich jak funkcja rejestratora i anulowania tokeny. 

>[!IMPORTANT]
>F#Skrypt (.fsx) jest obsługiwana tylko przez [wersji 1.x](functions-versions.md#creating-1x-apps) środowiska uruchomieniowego usługi Azure Functions. Jeśli chcesz używać F# ze środowiskiem uruchomieniowym w wersji 2.x, należy użyć wstępnie skompilowanych F# projekt biblioteki klas (.fs). Tworzenie, zarządzanie i publikowanie F# projekt biblioteki klas przy użyciu programu Visual Studio, jak w przypadku [ C# projekt biblioteki klas](functions-dotnet-class-library.md). Aby uzyskać więcej informacji na temat funkcji wersji zobacz [Przegląd wersje środowiska uruchomieniowego usługi Azure Functions](functions-versions.md).

W tym artykule założono, że zostały już przeczytane [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak działa fsx
`.fsx` Plik jest F# skryptu. Jego można traktować jako F# projektu, który znajduje się w jednym pliku. Plik zawiera kod dla Twojego programu (w tym przypadku funkcji platformy Azure) oraz dyrektywy do zarządzania zależnościami.

Kiedy używasz `.fsx` dla funkcji platformy Azure, często wymagane zestawy są automatycznie dołączane do Ciebie, co pozwala skupić się na kod funkcji, a nie "standardowy".

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla F# projekt skryptu wygląda podobnie do następującej:

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

Brak udostępnionej [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu (.fsx) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) funkcji środowiska uruchomieniowego są zdefiniowane w `extensions.csproj` pliku z plikami rzeczywistej biblioteki w `bin` folderu. Podczas tworzenia lokalnie, należy najpierw [zarejestrować rozszerzeń powiązania](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, rejestracja odbywa się za Ciebie.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami
Każde powiązanie obsługuje niektóre argumenty, zgodnie z opisem w [dokumentacja dla deweloperów usługi wyzwalaczy i powiązań usługi Azure Functions](functions-triggers-bindings.md). Na przykład, jedno z powiązań argument obsługuje wyzwalacz obiektu blob jest POCO, które mogą być wyrażone za pomocą F# rekordu. Na przykład:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Twoje F# funkcji platformy Azure spowoduje przejście jednego lub więcej argumentów. Gdy mówimy o argumenty usługi Azure Functions, nazywamy *wejściowych* argumentów i *dane wyjściowe* argumentów. Argument wejściowy jest dokładnie, co wydaje się to np.: dane wejściowe do Twojej F# funkcji platformy Azure. *Dane wyjściowe* argument jest modyfikowalną danych lub `byref<>` argument, który służy jako sposób przekazywania danych z powrotem *się* funkcji.

W powyższym przykładzie `blob` jest argument wejściowy i `output` jest argumentem danych wyjściowych. Należy zauważyć, że użyliśmy `byref<>` dla `output` (nie ma potrzeby można dodać `[<Out>]` adnotacji). Za pomocą `byref<>` typ umożliwia funkcji do zmiany, które rekord lub argumentu, który odwołuje się do obiektu.

Gdy F# rekord jest używany jako typ danych wejściowych, muszą być oznaczone definicji rekordu `[<CLIMutable>]` parzysta w ramach usługi Azure Functions odpowiednie ustawienie pola przed przekazaniem rekord do funkcji. Kulisy `[<CLIMutable>]` generuje metod ustawiających właściwości rekordu. Na przykład:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# Klasy można również dla obu in i out argumenty. Klasy właściwości będzie zwykle na potrzeby metod pobierających i ustawiających. Na przykład:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Rejestrowanie
Aby rejestrować dane wyjściowe do usługi [przesyłanie strumieniowe dzienników](../app-service/troubleshoot-diagnostic-logs.md) w F#, funkcji powinno przyjmować argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). W celu zachowania spójności, firma Microsoft zaleca, nosi nazwę tego argumentu `log`. Na przykład:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>asynchroniczne
`async` Przepływ pracy może być używany, ale wynik musi zwracać `Task`. Można to zrobić za pomocą `Async.StartAsTask`, na przykład:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token anulowania
Jeśli funkcja musi bezpiecznie obsłużyć zamykania, możesz nadać mu [ `CancellationToken` ](/dotnet/api/system.threading.cancellationtoken) argumentu. Może to być łączone z `async`, na przykład:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importowanie przestrzeni nazw
Przestrzenie nazw mogą być otwierane w zwykły sposób:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące przestrzenie nazw są automatycznie otwierane:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odwoływanie się do zestawów zewnętrznych
Podobnie, można dodać odwołania do zestawów framework z `#r "AssemblyName"` dyrektywy.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingu usługi Azure Functions:

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

Ponadto następujące zestawy są specyficzne, z uwzględnieniem wielkości liter i mogą być przywoływane przez simplename (np. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Jeśli potrzebujesz odwołać zestaw prywatny, możesz przekazać plik zestawu do `bin` folderu względem Twojej funkcji i odwołania za pomocą pliku nazwa (np.  `#r "MyAssembly.dll"`). Aby uzyskać informacje na temat przekazywania plików do folderu funkcji zobacz następującą sekcję pakietu zarządzania.

## <a name="editor-prelude"></a>Edytor Prelude
Edytor który obsługuje F# usługi kompilatora nie będą korzystają z przestrzeni nazw i zestawów, które automatycznie uwzględnia usługi Azure Functions. W efekcie może być przydatne, obejmują prelude, ułatwiające edytora Znajdź zestawy, które są używane i otwierać przestrzeni nazw. Na przykład:

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

Usługi Azure Functions wykonuje kod, przetwarza źródło o `COMPILED` zdefiniowane, więc prelude edytora zostanie zignorowany.

<a name="package"></a>

## <a name="package-management"></a>Zarządzanie pakietami
Do korzystania z pakietów NuGet w F# działa, Dodaj `project.json` plik do folderu funkcji w systemie plików aplikacji funkcji. Oto przykład `project.json` pliku, który dodaje odwołanie do pakietu NuGet, aby `Microsoft.ProjectOxford.Face` wersji 1.1.0:

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

Tylko .NET Framework 4.6 jest obsługiwane, dlatego upewnij się, że Twoje `project.json` plik Określa `net46` jak pokazano poniżej.

Podczas przekazywania `project.json` plik, środowisko uruchomieniowe pobiera pakiety i automatycznie dodaje odwołania do zestawów pakietu. Nie trzeba dodawać `#r "AssemblyName"` dyrektywy. Wystarczy dodać wymagane `open` instrukcje, aby Twoje `.fsx` pliku.

Możesz też chcieć umieścić automatycznie zestawów odwołań w swojej prelude edytora, aby poprawić swoim edytorze interakcji z F# usługi kompilacji.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Jak dodać `project.json` plików do funkcji platformy Azure
1. Rozpocznij, upewniając się, aplikacja funkcji jest uruchomiona, co można zrobić, otwierając funkcji w witrynie Azure portal. To daje dostęp do dzienników przesyłania strumieniowego gdzie zostaną wyświetlone dane wyjściowe instalacji pakietu.
2. Aby przekazać `project.json` pliku, użyj jednej z metod opisanych w [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). Jeśli używasz [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md), możesz dodać `project.json` pliku gałęzi tymczasowej, aby eksperymentować z tym przed dodaniem go do swojej gałęzi z wdrożenia.
3. Po `project.json` plik zostanie dodany, widoczne będą dane wyjściowe podobne do poniższego przykładu w funkcji użytkownika przesyłania strumieniowego dziennika:

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
Aby uzyskać zmiennej środowiskowej lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable`, na przykład:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Ponowne użycie kodu fsx
Możesz użyć kodu z innych `.fsx` plików za pomocą `#load` dyrektywy. Na przykład:

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

Zapewnia ścieżki `#load` dyrektywy są powiązane z lokalizacją usługi `.fsx` pliku.

* `#load "logger.fsx"` ładuje plik znajdujący się w folderze funkcji.
* `#load "package\logger.fsx"` ładuje plik znajdujący się w `package` folderu w folderze funkcji.
* `#load "..\shared\mylogger.fsx"` ładuje plik znajdujący się w `shared` folderu na tym samym poziomie jak folderze funkcji, znajdującą się pod `wwwroot`.

`#load` Dyrektywa działa tylko z `.fsx` (F# skryptu) plików, a nie z `.fs` plików.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [F#Przewodnik](/dotnet/articles/fsharp/index)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* [Testowanie usługi Azure Functions](functions-test-a-function.md)
* [Skalowanie usługi Azure Functions](functions-scale.md)

