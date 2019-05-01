---
title: Dokumentacja dla deweloperów programu PowerShell dla usługi Azure Functions
description: Dowiedz się, jak tworzenie funkcji przy użyciu programu PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 71ac525e2af7473ca9ce0a8f60268e76eccd1a9a
ms.sourcegitcommit: 111a7b3e19d5515ce7036287cea00a7204ca8b56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64530386"
---
# <a name="azure-functions-powershell-developer-guide"></a>Przewodnik dewelopera usługi Azure PowerShell funkcji

Ten artykuł zawiera szczegółowe informacje o tym, jak wpisać usługi Azure Functions przy użyciu programu PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Funkcja programu PowerShell jest przedstawiana jako skrypt programu PowerShell, który jest wykonywany po ich wyzwoleniu. Każdy skrypt funkcji ma powiązane function.json, który definiuje sposób działania funkcji, takich jak jak jest wyzwalane i parametry wejściowe i wyjściowe. Aby dowiedzieć się więcej, zobacz [wyzwalacze i powiązania artykułu](functions-triggers-bindings.md). 

Podobnie jak inne rodzaje funkcji skrypt programu PowerShell przyjmuje parametry, które pasują do nazw powiązania danych wejściowych zdefiniowane w function.json. A `TriggerMetadata` parametr jest przekazywany również, który zawiera dodatkowe informacje na temat wyzwalacza, który uruchomił funkcji.

W tym artykule założono, że już znasz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md). Należy również ukończyć [szybkiego startu usługi Functions dla programu PowerShell](functions-create-first-function-powershell.md) tworzenie pierwszej funkcji programu PowerShell.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów wymagane dla projektu programu PowerShell wygląda podobnie do poniższego. Można zmienić to ustawienie domyślne. Aby uzyskać więcej informacji, zobacz [plik_skryptu](#configure-function-scriptfile) poniższej sekcji.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

W folderze głównym projektu jest wspólny [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma folder z pliku z kodem (ps1) i plik konfiguracji powiązania (function.json). Nazwa `function.json`w katalogu nadrzędnym jest zawsze nazwę funkcji.

Niektóre powiązania wymagają obecności `extensions.csproj`. Powiązanie rozszerzenia, wymagane w [wersji 2.x](functions-versions.md) funkcje środowiska uruchomieniowego, są definiowane w `extensions.csproj` pliku z plikami rzeczywistej biblioteki w `bin` folderu. Podczas tworzenia lokalnie, należy najpierw [zarejestrować rozszerzeń powiązania](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, rejestracja odbywa się za Ciebie.

W aplikacjach funkcji programu PowerShell, mogą opcjonalnie mieć `profile.ps1` które są uruchamiane podczas uruchamiania aplikacji funkcji (znanych jako  *[zimnego](#cold-start)*. Aby uzyskać więcej informacji, zobacz [profil PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiowanie skryptu programu PowerShell w funkcji

Domyślnie środowisko uruchomieniowe usługi Functions szuka funkcji w `run.ps1`, gdzie `run.ps1` udziałów tego samego katalogu nadrzędnego odpowiadającymi mu dostawcami `function.json`.

Wykonanie skryptu są przekazywane liczbę argumentów. Aby obsługiwać te parametry, należy dodać `param` bloku na początku skryptu w taki sposób, jak w poniższym przykładzie:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametr TriggerMetadata

`TriggerMetadata` Parametr jest używany, aby podać dodatkowe informacje na temat wyzwalacza. Dodatkowe metadane różni się powiązania powiązania, ale wszystkie one zawierać `sys` właściwość, która zawiera następujące dane:

```powershell
$TriggerMetadata.sys
```

| Właściwość   | Opis                                     | Type     |
|------------|-------------------------------------------------|----------|
| utcNow     | Gdy w formacie UTC, funkcja została wyzwolona        | DateTime |
| MethodName | Nazwa funkcji, która została wyzwolona     | string   |
| RandGuid   | Unikatowy identyfikator guid do wykonywania tej funkcji | string   |

Każdy typ wyzwalacza ma inny zestaw metadanych. Na przykład `$TriggerMetadata` dla `QueueTrigger` zawiera `InsertionTime`, `Id`, `DequeueCount`, między innymi. Aby uzyskać więcej informacji na temat metadanych wyzwalacz kolejki, przejdź do [dokumentacji oficjalnego Usługa wyzwalaczy kolejkowania](functions-bindings-storage-queue.md#trigger---message-metadata). Zapoznaj się z dokumentacją na [wyzwalaczy](functions-triggers-bindings.md) pracujesz z, aby zobaczyć, co teraz wewnątrz metadanych wyzwalacza.

## <a name="bindings"></a>Powiązania

W programie PowerShell [powiązania](functions-triggers-bindings.md) są konfigurowane i zdefiniowane w funkcji function.json. Funkcje wchodzić w interakcje z powiązaniami na wiele sposobów.

### <a name="reading-trigger-and-input-data"></a>Dane wejściowe i odczytywania wyzwalaczy

Wyzwalacz i powiązania danych wejściowych są odczytywane jako parametry przekazywane do funkcji. Powiązania danych wejściowych mają `direction` równa `in` w function.json. `name` Właściwości zdefiniowanych w `function.json` trwa nazwę parametru, `param` bloku. Od czasu programu PowerShell używa nazwanych parametrów na potrzeby powiązania, kolejność parametrów nie ma znaczenia. Jednak jest najlepszym rozwiązaniem zgodna z kolejnością powiązań zdefiniowanych w `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zapisywanie danych wyjściowych

W przypadku funkcji zawiera powiązania danych wyjściowych `direction` równa `out` w function.json. Możesz zapisywać powiązania danych wyjściowych za pomocą `Push-OutputBinding` polecenia cmdlet, które jest dostępne środowisko uruchomieniowe usługi Functions. We wszystkich przypadkach `name` właściwości powiązania, zgodnie z definicją w `function.json` odpowiada `Name` parametru `Push-OutputBinding` polecenia cmdlet.

Poniżej pokazano, jak wywoływać `Push-OutputBinding` w skrypcie funkcji:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Można również przekazać wartość dla określonego powiązania przy użyciu potoku.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` zachowuje się inaczej w zależności od wartości określonej dla `-Name`:

* Gdy nie można rozpoznać określonej nazwy do powiązania danych wyjściowych prawidłowe, zostanie zgłoszony błąd.

* W przypadku powiązania danych wyjściowych akceptuje zbiór wartości, można wywołać `Push-OutputBinding` wielokrotnie, aby wypchnąć wielu wartości.

* Kiedy powiązania danych wyjściowych akceptuje tylko wartości pojedynczego wystąpienia, wywołanie `Push-OutputBinding` zgłasza błąd, po raz drugi.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Składnia

Poniżej przedstawiono prawidłowe parametry w celu wywoływania `Push-OutputBinding`:

| Name (Nazwa) | Type | Pozycja | Opis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Nazwa powiązania danych wyjściowych chcesz ustawić. |
| **`-Value`** | Object | 2 | Wartość powiązania danych wyjściowych należy ustawić, który jest akceptowany z potoku ByValue. |
| **`-Clobber`** | SwitchParameter | o nazwie | (Opcjonalnie) Jeśli zostanie określony, wymusza wartość do ustawienia dla powiązania określonym produktem wyjściowym. | 

Obsługiwane są również następujące parametry wspólne: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Aby uzyskać więcej informacji, zobacz [o CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Przykład wypychania OutputBinding: Odpowiedzi HTTP

Wyzwalacz HTTP zwraca odpowiedź przy użyciu powiązania danych wyjściowych o nazwie `response`. W poniższym przykładzie powiązania danych wyjściowych z `response` ma wartość "Wyjście #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Ponieważ dane wyjściowe do protokołu HTTP, który akceptuje tylko wartość singleton, błąd jest generowany, gdy `Push-OutputBinding` jest wywoływana po raz drugi.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

W przypadku danych wyjściowych, które akceptują tylko pojedyncze wartości, można użyć `-Clobber` parametr do zastąpienia starej wartości, zamiast próby dodania do kolekcji. W poniższym przykładzie założono, że dodano już wartość. Za pomocą `-Clobber`, odpowiedź w poniższym przykładzie zastępuje istniejącą wartość w celu zwrócenia wartości "Wyjście #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Przykład wypychania OutputBinding: Powiązania danych wyjściowych kolejki

`Push-OutputBinding` Służy do wysyłania danych do powiązania danych wyjściowych, takich jak [powiązania danych wyjściowych usługi Azure Queue storage](functions-bindings-storage-queue.md#output). W poniższym przykładzie komunikatów zapisywanych w kolejce ma wartość "Wyjście #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Powiązania danych wyjściowych kolejki magazynu akceptuje wiele wartości danych wyjściowych. W takim przypadku wywołanie w poniższym przykładzie, po pierwszym zapisuje do kolejki, listy przy użyciu dwóch elementów: "dane wyjściowe #1" i "Wyjście #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Poniższy przykład, gdy zostanie wywołana po dwie poprzednie, dodaje dwa więcej wartości do kolekcji danych wyjściowych:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Podczas zapisywania do kolejki, komunikat zawiera tych czterech wartości: "Wyjście #1", "Wyjście #2", "Wyjście #3" i "Wyjście #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Polecenia cmdlet

Możesz użyć `Get-OutputBinding` polecenie cmdlet do pobierania wartości ustawione dla wiązania danych wyjściowych. To polecenie cmdlet pobiera tablicę skrótów, która zawiera nazwiska powiązania danych wyjściowych przy użyciu odpowiadających im wartości. 

Oto przykład użycia `Get-OutputBinding` do zwracania bieżącej wartości wiązania:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` zawiera również parametr o nazwie `-Name`, który może służyć do filtrowania zwrócone powiązanie, jak w poniższym przykładzie:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Symbole wieloznaczne (*) są obsługiwane w `Get-OutputBinding`.

## <a name="logging"></a>Rejestrowanie

Rejestrowanie w funkcjach programu PowerShell działa jak regularne rejestrowania programu PowerShell. Można użyć polecenia cmdlet funkcji rejestrowania można zapisać do każdego strumienia wyjściowego. Każde polecenie cmdlet jest mapowany na poziom dziennika, przez funkcje.

| Funkcje poziom rejestrowania | Polecenia cmdlet rejestrowania |
| ------------- | -------------- |
| Błąd | **`Write-Error`** |
| Ostrzeżenie | **`Write-Warning`**  | 
| Informacje | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informacje | Zapisuje _informacji_ poziomu rejestrowania. |
| Debugowanie | **`Write-Debug`** |
| Ślad | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oprócz tych poleceń cmdlet nic zapisywane do potoku zostanie przekierowana do `Information` dziennika poziomu i wyświetlane w formacie domyślnym programu PowerShell.

> [!IMPORTANT]
> Za pomocą `Write-Verbose` lub `Write-Debug` poleceń cmdlet nie jest wystarczająco, aby zobaczyć pełne i rejestrowanie na poziomie debugowania. Należy także skonfigurować próg poziomu dziennika, który deklaruje poziom dzienniki faktycznie interesujące Cię. Aby dowiedzieć się więcej, zobacz [skonfigurować poziom dziennika aplikacji funkcji](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfiguruje poziom dziennika aplikacji — funkcja

Functions umożliwia definiowanie poziomu próg ułatwia do sterowania sposobem funkcji zapisuje w dziennikach. Aby ustawić próg wszystkie ślady wyświetlony w konsoli, należy użyć `logging.logLevel.default` właściwość [ `host.json` pliku][dokumentacja pliku host.JSON]. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji.

Poniższy przykład ustawia próg na wartość, aby włączyć pełne rejestrowanie dla wszystkich funkcji, ale ustawiające próg na wartość, aby włączyć rejestrowanie debugowania dla funkcji o nazwie `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Aby uzyskać więcej informacji, zobacz [dokumentacja pliku host.JSON].

### <a name="viewing-the-logs"></a>Wyświetlanie dzienników

Jeśli aplikacja funkcji jest uruchomiona na platformie Azure, można użyć usługi Application Insights do monitorowania go. Odczyt [monitorowania usługi Azure Functions](functions-monitoring.md) Aby dowiedzieć się więcej na temat przeglądania i zapytania o dzienniki funkcji.

Jeśli korzystasz z aplikacji funkcji lokalnie na potrzeby programowania, rejestruje domyślny system plików. Aby wyświetlić dzienniki w konsoli, należy ustawić `AZURE_FUNCTIONS_ENVIRONMENT` zmiennej środowiskowej, aby `Development` przed uruchomieniem aplikacji funkcji.

## <a name="triggers-and-bindings-types"></a>Wyzwalacze i powiązania typów

Liczba wyzwalaczy i powiązań są dostępne za pomocą aplikacji funkcji. Pełną listę wyzwalaczy i powiązań [można znaleźć tutaj](functions-triggers-bindings.md#supported-bindings).

Wszystkich wyzwalaczy i powiązań są reprezentowane jako kilka typów rzeczywiste dane w kodzie:

* Tablica skrótów
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Pierwszych pięć typów na tej liście są typy .NET standard. Ostatnie dwa są używane tylko przez [wyzwalacza HttpTrigger](#http-triggers-and-bindings).

Każdy parametr wiązania w funkcji musi być jednym z tych typów.

### <a name="http-triggers-and-bindings"></a>HTTP wyzwalaczy i powiązań

HTTP i wyzwalaczy elementu webhook protokołu HTTP wyjściowe i powiązania reprezentują wiadomości HTTP za pomocą obiektów żądań i odpowiedzi.

#### <a name="request-object"></a>Obiekt "Request"

Obiekt odpowiedzi, który jest przekazywany do skryptu jest typu `HttpRequestContext`, który ma następujące właściwości:

| Właściwość  | Opis                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Obiekt, który zawiera treść żądania. `Body` jest serializowany w najlepszy typ na podstawie danych. Na przykład jeśli dane JSON, jest przekazywany w tablicy skrótów. Jeśli danych jest ciągiem, jest przekazywany w postaci ciągu. | obiekt |
| **`Headers`** | Słownik zawierający nagłówki żądania.                | Dictionary < string, string ><sup>*</sup> |
| **`Method`** | Metoda HTTP żądania.                                | string                    |
| **`Params`**  | Obiekt zawierający parametry routingu żądania. | Dictionary < string, string ><sup>*</sup> |
| **`Query`** | Obiekt zawierający parametry zapytania.                  | Dictionary < string, string ><sup>*</sup> |
| **`Url`** | Adres URL żądania.                                        | string                    |

<sup>*</sup> Wszystkie `Dictionary<string,string>` kluczy jest rozróżniana wielkość liter.

#### <a name="response-object"></a>Obiekt odpowiedzi

Obiekt odpowiedzi, który należy wysłać ponownie jest typu `HttpResponseContext`, który ma następujące właściwości:

| Właściwość      | Opis                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Obiekt, który zawiera treści odpowiedzi.           | obiekt                    |
| **`ContentType`** | Krótkie dostępne ustawienia Typ zawartości odpowiedzi. | string                    |
| **`Headers`** | Obiekt, który zawiera nagłówki odpowiedzi.               | Słownik lub tablicy skrótów   |
| **`StatusCode`**  | Kod stanu HTTP odpowiedzi.                       | ciąg lub int             |

#### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi

Podczas pracy z wyzwalaczami HTTP są dostępne żądania HTTP w taki sam sposób jak inne powiązania danych wejściowych. Jest on `param` bloku.

Użyj `HttpResponseContext` obiekt do zwrotu odpowiedzi, jak pokazano w następującym:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Wynik wywołania tej funkcji będą:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Rzutowanie typów wyzwalaczy i powiązań

W przypadku niektórych powiązania, takich jak powiązania obiektu blob, możesz określić typ parametru.

Na przykład, aby dane z magazynu obiektów Blob, podana jako ciąg znaków, należy dodać następujący typ rzutowany na Moje `param` bloku:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil programu PowerShell

W programie PowerShell istnieje pojęcie profilu programu PowerShell. Jeśli nie znasz przy użyciu profilów programu PowerShell, zobacz [o profilach](/powershell/module/microsoft.powershell.core/about/about_profiles).

W funkcjach programu PowerShell skrypt profilu jest wykonywany podczas uruchamiania aplikacji funkcji. Funkcja aplikacje mają swój początek podczas pierwszego wdrożenia, a po jest bezczynny ([zimnego](#cold-start)).

Po utworzeniu aplikacji funkcji przy użyciu narzędzi, takich jak Visual Studio Code i funkcji podstawowych narzędzi usługi Azure, domyślny `profile.ps1` zostanie utworzony. Profil domyślny jest utrzymywany [w repozytorium GitHub narzędzia Core](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) i zawiera:

* Automatyczne uwierzytelnianie tożsamości usługi Zarządzanej na platformie Azure.
* Możliwości, aby włączyć funkcję programu Azure PowerShell `AzureRM` aliasy środowiska PowerShell, jeśli chcesz.

## <a name="powershell-version"></a>Wersja programu PowerShell

W poniższej tabeli przedstawiono wersja programu PowerShell, używane przez każda główna wersja środowiska uruchomieniowego funkcji:

| Funkcje wersji | Wersja programu PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (zablokowane przez środowisko uruchomieniowe) |
| 2.x               | PowerShell Core 6                              |

Możesz wyświetlić bieżącą wersję, drukowanie `$PSVersionTable` z żadnej funkcji.

## <a name="dependency-management"></a>Zarządzanie zależnościami

Funkcje programu PowerShell obsługują zarządzanie moduły platformy Azure przez usługę. Modyfikowanie host.json i ustawienie właściwości managedDependency włączone na wartość true, plik requirements.psd1 zostanie przetworzony. Najnowszych modułów platformy Azure zostanie automatycznie pobrana i udostępnione do funkcji.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Korzystanie z własnych niestandardowych modułów lub modułów z [galerii programu PowerShell](https://powershellgallery.com) różni się nieco od jak go normalnie.

Po zainstalowaniu modułu na komputerze lokalnym, usługa zostanie wprowadzona w jednym z folderów globalnie dostępną w Twojej `$env:PSModulePath`. Ponieważ funkcja działa na platformie Azure, nie mają dostępu do modułów, zainstalowana na tym komputerze. Takie rozwiązanie wymaga `$env:PSModulePath` dla funkcji programu PowerShell aplikacji różni się od `$env:PSModulePath` w regularnych skrypt programu PowerShell.

W przypadku funkcji `PSModulePath` zawiera dwie ścieżki:

* A `Modules` folder, który istnieje w katalogu głównym aplikacji funkcji.
* Ścieżka do `Modules` folderu, który znajduje się wewnątrz procesu roboczego języka programu PowerShell.

### <a name="function-app-level-modules-folder"></a>Aplikacja funkcji na poziomie `Modules` folderu

Aby użyć moduły niestandardowe lub moduły programu PowerShell z galerii programu PowerShell, można umieścić moduły od których zależą swoje funkcje, w `Modules` folderu. Z tego folderu moduły są automatycznie dostępne dla środowisko uruchomieniowe usługi functions. Dowolną funkcję w aplikacji funkcji można użyć tych modułów.

Aby móc korzystać z tej funkcji, należy utworzyć `Modules` folder w katalogu głównym aplikacji funkcji. Zapisz moduły, w których chcesz użyć w funkcji w tej lokalizacji.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Użyj `Save-Module` Zapisz wszystkie moduły, użyj funkcji lub skopiować niestandardowe moduły do `Modules` folderu. Z folderem, moduły aplikacja funkcji mają następującą strukturę folderów:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Po uruchomieniu aplikacji funkcji, proces roboczy języka programu PowerShell dodaje `Modules` folder `$env:PSModulePath` , dzięki czemu możesz polegać na moduł autoloading tak samo, jak w regularnym skrypt programu PowerShell.

### <a name="language-worker-level-modules-folder"></a>Poziom procesu roboczego języka `Modules` folderu

Kilka modułów są często używane przez proces roboczy języka programu PowerShell. Te moduły są zdefiniowane w ostatniej pozycji `PSModulePath`. 

Bieżącą listę modułów jest następująca:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): moduł używany do pracy z archiwami, takie jak `.zip`, `.nupkg`i innym osobom.
* **ThreadJob**: Implementacja oparta zadania programu PowerShell, interfejsów API.

Najbardziej aktualną wersję tych modułów jest używany przez funkcje. Aby użyć określonej wersji tych modułów, możesz umieścić w określonej wersji `Modules` folderu aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W przypadku funkcji [ustawienia aplikacji](functions-app-settings.md), takie jak połączenia z usługą ciągów, są widoczne jako zmiennych środowiskowych podczas wykonywania. Możesz uzyskać dostęp te ustawienia przy użyciu `$env:NAME_OF_ENV_VAR`, jak pokazano w poniższym przykładzie:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Podczas uruchamiania lokalnego, ustawienia aplikacji są odczytywane z [local.settings.json](functions-run-local.md#local-settings-file) pliku projektu.

## <a name="concurrency"></a>Współbieżność

Domyślnie środowisko uruchomieniowe funkcji PowerShell może przetwarzać tylko jedno wywołanie funkcji w danym momencie. Jednak ten poziom współbieżności może nie być wystarczające w następujących sytuacjach:

* Gdy próbujesz obsługiwać dużą liczbę wywołań w tym samym czasie.
* Jeśli używasz funkcji, które wywołują innych funkcji w tej samej aplikacji funkcji.

To zachowanie można zmienić, ustawiając następującą zmienną środowiskową na wartość całkowitą:

```
PSWorkerInProcConcurrencyUpperBound
```

Ta zmienna środowiskowa jest ustawiony [ustawienia aplikacji](functions-app-settings.md) aplikacji funkcji.

### <a name="considerations-for-using-concurrency"></a>Zagadnienia dotyczące korzystania ze współbieżności

Program PowerShell jest _jednowątkowej_ języku skryptowym domyślnie. Jednak można dodać współbieżności przy użyciu wielu obszarach działania programu PowerShell w tym samym procesie. Ta funkcja jest o tym, jak działa środowisko uruchomieniowe usługi Azure Functions w programie PowerShell.

Istnieją pewne wady w przypadku tej metody.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Współbieżność tylko jest dobrą maszyny, na którym jest uruchomiona na

Jeśli aplikacja funkcji jest uruchomiony na [planu usługi App Service](functions-scale.md#app-service-plan) obsługuje tylko pojedynczego rdzenia, a następnie współbieżności nie pomoże, większość. To, ponieważ nie ma żadnych więcej rdzeni, aby ułatwić Równoważenie obciążenia. W tym przypadku wydajność może się zmieniać, gdy jeden rdzeń przełączenie kontekstu między obszary działania.

[Planu zużycie](functions-scale.md#consumption-plan) jest wykonywane przy użyciu tylko jednego rdzenia, więc nie mogą wykorzystać współbieżności. Jeśli chcesz wykorzystać współbieżności, zamiast tego wdrażania funkcji działanie aplikacji funkcji na dedykowanego planu usługi App Service za pomocą rdzeni.

#### <a name="azure-powershell-state"></a>Stan programu PowerShell platformy Azure

Program Azure PowerShell używa niektórych _poziom procesu_ konteksty i stanie, aby pomóc w zmniejszeniu możesz wpisać nadmiarowe. Jednak jeśli włączyć współbieżności w aplikacji funkcji i wykonywanie działań takich jak zmiany stanu, może wystąpić z wyścigu. Te warunki wyścigu są trudne do debugowania, ponieważ jedno wywołanie zależy od pewnego stanu, a inne wywołania zmieniła stan.

Współbieżności przy użyciu programu Azure PowerShell jest wiele wartości, ponieważ niektóre operacje może zająć znaczną ilość czasu. Jednak należy kontynuować z rozwagą. Jeśli podejrzewasz, że występują warunki sytuacji wyścigu, ustaw współbieżność do `1` i ponów próbę żądania.

## <a name="configure-function-scriptfile"></a>Konfigurowanie funkcji `scriptFile`

Domyślnie funkcja programu PowerShell jest wykonywany z `run.ps1`, plik, który współużytkuje tego samego katalogu nadrzędnego odpowiadającymi mu dostawcami `function.json`.

`scriptFile` Właściwość `function.json` pozwala uzyskać strukturę folderów, która wygląda podobnie jak w poniższym przykładzie:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

W tym przypadku `function.json` dla `myFunction` obejmuje `scriptFile` właściwości odwołującej się do pliku za pomocą funkcji eksportowanych do uruchomienia.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Użycie modułów programu PowerShell, konfigurując punkt wejścia

Ten artykuł pokazuje funkcje programu PowerShell w domyślnym `run.ps1` pliku skryptu, wygenerowane za pomocą szablonów.
Można jednak również uwzględnić funkcje w modułach programu PowerShell. Można odwoływać się za pomocą kodu określonych funkcji w module `scriptFile` i `entryPoint` pól w function.json "pliku konfiguracji.

W tym przypadku `entryPoint` jest nazwą funkcji lub polecenia cmdlet w module programu PowerShell, do którego odwołuje się `scriptFile`.

Należy rozważyć następujące struktury folderów:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Gdzie `PSFunction.psm1` zawiera:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

W tym przykładzie konfiguracja `myFunction` obejmuje `scriptFile` właściwość, która odwołuje się do `PSFunction.psm1`, który jest moduł programu PowerShell w innym folderze.  `entryPoint` Odwołania do właściwości `Invoke-PSTestFunc` funkcji, która jest punktem wejścia w module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

W przypadku tej konfiguracji `Invoke-PSTestFunc` pobiera wykonywane dokładnie jako `run.ps1` będzie.

## <a name="considerations-for-powershell-functions"></a>Zagadnienia dotyczące funkcji programu PowerShell

Podczas pracy z funkcjami programu PowerShell, należy pamiętać o kwestiach w poniższych sekcjach.

### <a name="cold-start"></a>Zimny Start

Podczas tworzenia funkcji platformy Azure w [bez użycia serwera modelu hostingu](functions-scale.md#consumption-plan), zimny start są mogą stać się rzeczywistością. *Zimnego* odwołuje się do czas jego przyjmuje na aplikację funkcji, aby rozpocząć Uruchamianie przetwarzania żądania. Zimnego występuje więcej często zużycia planu, ponieważ pobiera zamknięcia aplikacji funkcji w czasie nieaktywności.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduły pakietu zamiast `Install-Module`

Skrypt jest uruchamiany na każdego wywołania. Unikaj używania `Install-Module` w skrypcie. Zamiast tego użyć `Save-Module` przed opublikowaniem, dzięki czemu nie trzeba marnowania czasu pobierania modułu funkcji. Jeśli zimnych startów mających wpływ na funkcje, zaleca się wdrożenie aplikacji funkcji do [planu usługi App Service](functions-scale.md#app-service-plan) równa *zawsze włączone* lub [plan w warstwie Premium](functions-scale.md#premium-plan-public-preview).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)

[Dokumentacja pliku host.JSON]: functions-host-json.md
