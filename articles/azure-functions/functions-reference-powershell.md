---
title: Odwołanie dewelopera programu PowerShell dla usług Azure Functions
description: Dowiedz się, jak tworzyć funkcje przy użyciu programu PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276740"
---
# <a name="azure-functions-powershell-developer-guide"></a>Przewodnik dla deweloperów programu Azure Functions PowerShell

Ten artykuł zawiera szczegółowe informacje na temat pisania usługi Azure Functions przy użyciu programu PowerShell.

Funkcja (funkcja) platformy PowerShell Azure jest reprezentowana jako skrypt programu PowerShell, który jest wykonywany po wyzwoleniu. Każdy skrypt funkcji `function.json` ma powiązany plik, który definiuje zachowanie funkcji, na przykład sposób jej wyzwalania i jego parametry wejściowe i wyjściowe. Aby dowiedzieć się więcej, zobacz [wyzwalacze i powiązanie artykułu](functions-triggers-bindings.md). 

Podobnie jak inne rodzaje funkcji, funkcje skryptu programu PowerShell przyjmują parametry, które pasują do nazw wszystkich powiązań wejściowych zdefiniowanych w `function.json` pliku. Parametr `TriggerMetadata` jest również przekazywany, który zawiera dodatkowe informacje na wyzwalacz, który uruchomił funkcję.

W tym artykule założono, że zostały już odczytane [odwołanie dewelopera usługi Azure Functions.](functions-reference.md) Aby utworzyć pierwszą funkcję programu PowerShell, należy również ukończyć [program Szybki start funkcji programu PowerShell.](functions-create-first-function-powershell.md)

## <a name="folder-structure"></a>Struktura folderów

Wymagana struktura folderów dla projektu programu PowerShell wygląda następująco. Tę wartość domyślną można zmienić. Aby uzyskać więcej informacji, zobacz [scriptFile](#configure-function-scriptfile) sekcji poniżej.

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

W katalogu głównym projektu znajduje się [`host.json`](functions-host-json.md) udostępniony plik, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma folder z własnym plikiem kodu (ps1) i plikiem konfiguracji powiązania (`function.json`). Nazwa katalogu nadrzędnego pliku function.json jest zawsze nazwą funkcji.

Niektóre powiązania wymagają obecności `extensions.csproj` pliku. Rozszerzenia powiązania, wymagane w [wersji 2.x i nowszych wersjach](functions-versions.md) `extensions.csproj` środowiska wykonawczego Functions, są `bin` zdefiniowane w pliku z rzeczywistymi plikami biblioteki w folderze. Podczas tworzenia lokalnie należy [zarejestrować rozszerzenia powiązania](functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, ta rejestracja jest wykonywana dla Ciebie.

W aplikacjach funkcji programu PowerShell `profile.ps1` opcjonalnie możesz mieć, który działa, gdy aplikacja funkcji zaczyna działać (w przeciwnym razie znany jako *[zimny start](#cold-start)*. Aby uzyskać więcej informacji, zobacz [Profil programu PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiowanie skryptu programu PowerShell jako funkcji

Domyślnie środowisko uruchomieniowe Functions wyszukuje funkcję w `run.ps1`trybie ,, gdzie `run.ps1` udostępnia ten sam katalog nadrzędny, co odpowiadający mu `function.json`program .

Skrypt jest przekazywany szereg argumentów podczas wykonywania. Aby obsłużyć `param` te parametry, dodaj blok do górnej części skryptu, jak w poniższym przykładzie:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametr TriggerMetadata

Parametr `TriggerMetadata` jest używany do dostarczania dodatkowych informacji o wyzwalaczu. Dodatkowe metadane różnią się od powiązania do `sys` powiązania, ale wszystkie zawierają właściwość, która zawiera następujące dane:

```powershell
$TriggerMetadata.sys
```

| Właściwość   | Opis                                     | Typ     |
|------------|-------------------------------------------------|----------|
| Utcnow     | Kiedy w utc funkcja została wyzwolona        | DateTime |
| Methodname | Nazwa funkcji, która została wyzwolona     | ciąg   |
| RandGuid (RandGuid)   | unikalnym identyfikatorem do tego wykonania funkcji | ciąg   |

Każdy typ wyzwalacza ma inny zestaw metadanych. `$TriggerMetadata` Na przykład, `QueueTrigger` for `InsertionTime`zawiera `Id` `DequeueCount`, , między innymi. Aby uzyskać więcej informacji na temat metadanych wyzwalacza kolejki, przejdź do [oficjalnej dokumentacji wyzwalaczy kolejki](functions-bindings-storage-queue-trigger.md#message-metadata). Sprawdź dokumentację [wyzwalaczy,](functions-triggers-bindings.md) z którymi pracujesz, aby zobaczyć, co znajduje się w metadanych wyzwalacza.

## <a name="bindings"></a>Powiązania

W programie PowerShell [powiązania](functions-triggers-bindings.md) są konfigurowane i definiowane w funkcji function.json. Funkcje współdziałają z powiązaniami na wiele sposobów.

### <a name="reading-trigger-and-input-data"></a>Odczyt wyzwalacza i danych wejściowych

Powiązania wyzwalacza i wejściowe są odczytywane jako parametry przekazywane do funkcji. Powiązania wejściowe `direction` mają `in` zestaw w function.json. Właściwość `name` zdefiniowana w `function.json` jest nazwą parametru w `param` bloku. Ponieważ program PowerShell używa nazwanych parametrów do wiązania, kolejność parametrów nie ma znaczenia. Jednak najlepszym rozwiązaniem jest przestrzeganie kolejności powiązań zdefiniowanych `function.json`w .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zapisywanie danych wyjściowych

W funkcji powiązania danych `direction` wyjściowych `out` ma zestaw w function.json. Można zapisać do powiązania danych `Push-OutputBinding` wyjściowych przy użyciu polecenia cmdlet, który jest dostępny dla funkcji środowiska wykonawczego. We wszystkich przypadkach `name` właściwość powiązania zdefiniowanego w `function.json` `Name` odpowiada parametrowi `Push-OutputBinding` polecenia cmdlet.

Poniżej przedstawiono `Push-OutputBinding` sposób wywoływania w skrypcie funkcji:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Można również przekazać w wartości dla określonego powiązania za pośrednictwem potoku.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`zachowuje się inaczej w zależności `-Name`od wartości określonej dla:

* Jeśli określona nazwa nie może być rozpoznana do prawidłowego powiązania danych wyjściowych, a następnie zgłaszany jest błąd.

* Gdy powiązanie danych wyjściowych akceptuje kolekcję `Push-OutputBinding` wartości, można wywołać wielokrotnie do wypychania wielu wartości.

* Gdy powiązanie danych wyjściowych akceptuje tylko `Push-OutputBinding` wartość singleton, wywołanie po raz drugi wywołuje błąd.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Składni

Poniżej znajdują się `Push-OutputBinding`prawidłowe parametry wywoływania:

| Nazwa | Typ | Pozycja | Opis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Ciąg | 1 | Nazwa powiązania danych wyjściowych, które chcesz ustawić. |
| **`-Value`** | Obiekt | 2 | Wartość powiązania danych wyjściowych, które chcesz ustawić, który jest akceptowany z potoku ByValue. |
| **`-Clobber`** | SwitchParameter | O nazwie | (Opcjonalnie) Po określeniu wymusza ustawienie wartości dla określonego powiązania wyjściowego. | 

Obsługiwane są również następujące typowe parametry: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Aby uzyskać więcej informacji, zobacz [Informacje o commonparameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Przykład wiązania wypychania: odpowiedzi HTTP

Wyzwalacz HTTP zwraca odpowiedź przy `response`użyciu powiązania wyjściowego o nazwie . W poniższym przykładzie powiązanie danych wyjściowych `response` ma wartość "#1 wyjściowym":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Ponieważ dane wyjściowe są http, który akceptuje tylko wartość singleton, błąd jest generowany, gdy `Push-OutputBinding` jest wywoływana po raz drugi.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Dla wyjść, które akceptują tylko wartości `-Clobber` singleton, można użyć parametru, aby zastąpić starą wartość zamiast próbować dodać do kolekcji. W poniższym przykładzie przyjęto założenie, że wartość została już dodana. Za `-Clobber`pomocą , odpowiedź z poniższego przykładu zastępuje istniejącą wartość, aby zwrócić wartość "#3 wyjściowym":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Przykład wypychania i wiązania danych: powiązanie danych wyjściowych kolejki

`Push-OutputBinding`służy do wysyłania danych do powiązań wyjściowych, takich jak [powiązanie danych wyjściowych magazynu usługi Azure Queue.](functions-bindings-storage-queue-output.md) W poniższym przykładzie komunikat napisany w kolejce ma wartość "data #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Powiązanie danych wyjściowych dla kolejki magazynu akceptuje wiele wartości wyjściowych. W takim przypadku wywołanie następującego przykładu po pierwszym zapisuje do kolejki listę z dwoma elementami: "output #1" i "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Poniższy przykład, gdy wywoływane po poprzednich dwóch, dodaje dwie kolejne wartości do kolekcji danych wyjściowych:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Po zapisaniu w kolejce komunikat zawiera te cztery wartości: "output #1", "output #2", "output #3" i "output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`Polecenie cmdlet

Polecenia `Get-OutputBinding` cmdlet można użyć do pobrania wartości aktualnie ustawionych dla powiązań danych wyjściowych. To polecenie cmdlet pobiera tabelę mieszania, która zawiera nazwy powiązań danych wyjściowych z ich odpowiednich wartości. 

Poniżej przedstawiono przykład `Get-OutputBinding` użycia do zwracania bieżących wartości wiązania:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`zawiera również parametr `-Name`o nazwie , który może służyć do filtrowania zwróconego powiązania, jak w poniższym przykładzie:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Symbole wieloznaczne (*) `Get-OutputBinding`są obsługiwane w pliku .

## <a name="logging"></a>Rejestrowanie

Rejestrowanie w programie PowerShell funkcje działa jak regularne rejestrowanie programu PowerShell. Polecenia cmdlet rejestrowania służy do zapisu do każdego strumienia wyjściowego. Każde polecenie cmdlet jest mapowana na poziom dziennika używany przez funkcje.

| Poziom rejestrowania funkcji | Polecenie cmdlet rejestrowania |
| ------------- | -------------- |
| Błąd | **`Write-Error`** |
| Ostrzeżenie | **`Write-Warning`**  | 
| Informacje | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informacje | Zapisuje do rejestrowania na poziomie _informacji._ |
| Debugowanie | **`Write-Debug`** |
| Ślad | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oprócz tych poleceń cmdlet wszystko zapisane w potoku `Information` jest przekierowywane do poziomu dziennika i wyświetlane z domyślnym formatowaniem programu PowerShell.

> [!IMPORTANT]
> Za `Write-Verbose` pomocą `Write-Debug` lub poleceń cmdlet nie wystarczy, aby zobaczyć pełne i debugowania poziomu rejestrowania. Należy również skonfigurować próg poziomu dziennika, który deklaruje, jaki poziom dzienników rzeczywiście zależy. Aby dowiedzieć się więcej, zobacz [Konfigurowanie poziomu dziennika aplikacji funkcji](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurowanie poziomu dziennika aplikacji funkcji

Usługa Azure Functions umożliwia zdefiniowanie poziomu progowego, aby ułatwić kontrolowanie sposobu, w jaki funkcje zapisuje w dziennikach. Aby ustawić próg dla wszystkich śladów zapisanych `logging.logLevel.default` w konsoli, należy użyć właściwości w [ `host.json` ]pliku[host.json reference]. To ustawienie dotyczy wszystkich funkcji w aplikacji funkcji.

Poniższy przykład ustawia próg, aby włączyć pełne rejestrowanie dla wszystkich funkcji, ale ustawia próg, `MyFunction`aby włączyć rejestrowanie debugowania dla funkcji o nazwie:

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

Aby uzyskać więcej informacji, zobacz [host.json reference].

### <a name="viewing-the-logs"></a>Wyświetlanie dzienników

Jeśli aplikacja function jest uruchomiona na platformie Azure, można użyć usługi Application Insights, aby ją monitorować. Przeczytaj [monitorowanie usługi Azure Functions,](functions-monitoring.md) aby dowiedzieć się więcej na temat przeglądania i wykonywania zapytań dzienników funkcji.

Jeśli używasz aplikacji funkcji lokalnie do programowania, loguje domyślnie system plików. Aby wyświetlić dzienniki w konsoli, ustaw zmienną środowiskową `AZURE_FUNCTIONS_ENVIRONMENT` przed `Development` uruchomieniem aplikacji function.

## <a name="triggers-and-bindings-types"></a>Wyzwalacze i typy powiązań

Istnieje wiele wyzwalaczy i powiązań dostępnych do użycia z aplikacją funkcji. Pełną listę wyzwalaczy i powiązań [można znaleźć tutaj](functions-triggers-bindings.md#supported-bindings).

Wszystkie wyzwalacze i powiązania są reprezentowane w kodzie jako kilka rzeczywistych typów danych:

* Hashtable
* ciąg
* bajt[]
* int
* double
* HttpRequestContext (HttpRequestContext)
* HttpResponseContext

Pierwsze pięć typów na tej liście to standardowe typy .NET. Dwa ostatnie są używane tylko przez [spust HttpTrigger](#http-triggers-and-bindings).

Każdy parametr wiązania w funkcjach musi być jednym z tych typów.

### <a name="http-triggers-and-bindings"></a>Wyzwalacze i powiązania HTTP

Wyzwalacze HTTP i webhook oraz powiązania wyjściowe HTTP używają obiektów żądań i odpowiedzi do reprezentowania wiadomości HTTP.

#### <a name="request-object"></a>Obiekt żądania

Obiekt żądania, który jest przekazywany do `HttpRequestContext`skryptu jest typu , który ma następujące właściwości:

| Właściwość  | Opis                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Obiekt, który zawiera treść żądania. `Body`jest serializowany do najlepszego typu na podstawie danych. Na przykład jeśli dane są JSON, jest przekazywana jako mieszania. Jeśli dane są ciągiem, jest przekazywana jako ciąg. | obiekt |
| **`Headers`** | Słownik zawierający nagłówki żądań.                | Ciąg<słownika,ciąg><sup>*</sup> |
| **`Method`** | Metoda HTTP żądania.                                | ciąg                    |
| **`Params`**  | Obiekt, który zawiera parametry routingu żądania. | Ciąg<słownika,ciąg><sup>*</sup> |
| **`Query`** | Obiekt, który zawiera parametry kwerendy.                  | Ciąg<słownika,ciąg><sup>*</sup> |
| **`Url`** | Adres URL żądania.                                        | ciąg                    |

<sup>*</sup>Wszystkie `Dictionary<string,string>` klucze są niewrażliwe na wielkości liter.

#### <a name="response-object"></a>Obiekt odpowiedzi

Obiekt odpowiedzi, który należy odesłać, `HttpResponseContext`jest typu , który ma następujące właściwości:

| Właściwość      | Opis                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Obiekt, który zawiera treść odpowiedzi.           | obiekt                    |
| **`ContentType`** | Krótka dłoń do ustawiania typu zawartości dla odpowiedzi. | ciąg                    |
| **`Headers`** | Obiekt, który zawiera nagłówki odpowiedzi.               | Słownik lub hashtable   |
| **`StatusCode`**  | Kod stanu HTTP odpowiedzi.                       | ciąg lub int             |

#### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi

Podczas pracy z wyzwalaczami HTTP można uzyskać dostęp do żądania HTTP w taki sam sposób, jak w przypadku innego powiązania wejściowego. Jest w `param` bloku.

Użyj `HttpResponseContext` obiektu, aby zwrócić odpowiedź, jak pokazano w następujący sposób:

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

Wynikiem wywołania tej funkcji będzie:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Odlewanie typu dla wyzwalaczy i powiązań

Dla niektórych powiązań, takich jak powiązanie obiektu blob, można określić typ parametru.

Na przykład, aby dane z magazynu obiektów blob były dostarczane `param` jako ciąg, dodaj następujący typ rzutowania do mojego bloku:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil programu PowerShell

W programie PowerShell istnieje koncepcja profilu programu PowerShell. Jeśli nie znasz profili programu PowerShell, zobacz [Informacje o profilach](/powershell/module/microsoft.powershell.core/about/about_profiles).

W programie PowerShell Functions skrypt profilu jest wykonywany po uruchomieniu aplikacji funkcji. Aplikacje funkcyjne uruchamiają się po pierwszym wdrożeniu i po bezczynności[(zimny start).](#cold-start)

Podczas tworzenia aplikacji funkcji przy użyciu narzędzi, takich jak Visual Studio `profile.ps1` Code i Azure Functions Core Tools, domyślnie jest tworzony dla Ciebie. Domyślny profil jest obsługiwany [w repozytorium Podstawowych narzędzi GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) i zawiera:

* Automatyczne uwierzytelnianie MSI na platformie Azure.
* Możliwość włączenia aliasów programu `AzureRM` Azure PowerShell PowerShell, jeśli chcesz.

## <a name="powershell-version"></a>Wersja programu PowerShell

W poniższej tabeli przedstawiono wersję programu PowerShell używaną przez każdą wersję główną środowiska wykonawczego Functions:

| Wersja funkcji | Wersja programu PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Program Windows PowerShell 5.1 (zablokowany przez środowisko wykonawcze) |
| 2.x               | Program PowerShell Core 6                              |

Bieżącą wersję można wyświetlić, drukując `$PSVersionTable` z dowolnej funkcji.

## <a name="dependency-management"></a>Zarządzanie zależnościami

Funkcje umożliwia korzystanie z [galerii programu PowerShell](https://www.powershellgallery.com) do zarządzania zależnościami. Po włączeniu zarządzania zależnościami plik requirements.psd1 jest używany do automatycznego pobierania wymaganych modułów. To zachowanie można włączyć, ustawiając `managedDependency` właściwość `true` w katalogu głównym pliku [host.json,](functions-host-json.md)jak w poniższym przykładzie:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Podczas tworzenia nowego projektu funkcji programu PowerShell zarządzanie zależnościami jest domyślnie włączone, z dołączonym [ `Az` modułem](/powershell/azure/new-azureps-module-az) platformy Azure. Maksymalna liczba aktualnie obsługiwanych modułów wynosi 10. Obsługiwana składnia jest _`MajorNumber`_ `.*` lub dokładna wersja modułu, jak pokazano w poniższym przykładzie requirements.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Po zaktualizowaniu pliku requirements.psd1 zaktualizowane moduły są instalowane po ponownym uruchomieniu komputera.

> [!NOTE]
> Zależności zarządzane wymaga dostępu do www.powershellgallery.com do pobierania modułów. Podczas uruchamiania lokalnie upewnij się, że środowisko wykonawcze może uzyskać dostęp do tego adresu URL, dodając wszystkie wymagane reguły zapory. 

Następujące ustawienia aplikacji mogą służyć do zmiany sposobu pobierania i instalowania zależności zarządzanych. Uaktualnienie aplikacji rozpoczyna `MDMaxBackgroundUpgradePeriod`się w obrębie , a `MDNewSnapshotCheckPeriod`proces uaktualniania kończy się w przybliżeniu .

| Ustawienie aplikacji funkcji              | Wartość domyślna             | Opis                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 dni)     | Każdy proces roboczy programu PowerShell inicjuje sprawdzanie uaktualnień modułu `MDMaxBackgroundUpgradePeriod` w Galerii programu PowerShell przy uruchamianiu procesu i co po tym. Gdy nowa wersja modułu jest dostępna w galerii programu PowerShell, jest zainstalowana w systemie plików i udostępniona pracownikom programu PowerShell. Zmniejszenie tej wartości umożliwia aplikacji funkcji szybciej uzyskać nowsze wersje modułu, ale również zwiększa użycie zasobów aplikacji (we/wy sieci, procesora CPU, pamięci masowej). Zwiększenie tej wartości zmniejsza użycie zasobów aplikacji, ale może również opóźnić dostarczanie nowych wersji modułu do aplikacji. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 godzina)       | Po zainstalowaniu nowych wersji modułu w systemie plików każdy proces roboczy programu PowerShell musi zostać ponownie uruchomiony. Ponowne uruchamianie pracowników programu PowerShell wpływa na dostępność aplikacji, ponieważ może przerwać wykonywanie bieżącej funkcji. Dopóki wszystkie procesy robocze programu PowerShell nie zostaną ponownie uruchomione, wywołania funkcji mogą używać starych lub nowych wersji modułu. Ponowne uruchomienie wszystkich pracowników programu `MDNewSnapshotCheckPeriod`PowerShell zakończy się w ramach programu . Zwiększenie tej wartości zmniejsza częstotliwość przerw, ale może również wydłużyć okres, w którym wywołania funkcji używają wersji starego lub nowego modułu niedeterministycznie. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 dzień)     | Aby uniknąć nadmiernych uaktualnień modułów podczas częstego ponownego uruchamiania procesu roboczego, sprawdzanie uaktualnień `MDMinBackgroundUpgradePeriod`modułu nie jest wykonywane, gdy którykolwiek pracownik już zainicjował to zaewidencjonowanie w ostatnim . |

Wykorzystanie własnych modułów niestandardowych jest trochę inne niż to, jak można to zrobić normalnie.

Na komputerze lokalnym moduł zostanie zainstalowany w jednym z dostępnych `$env:PSModulePath`na całym świecie folderach w programie . Podczas pracy na platformie Azure nie masz dostępu do modułów zainstalowanych na komputerze. Oznacza to, `$env:PSModulePath` że dla aplikacji funkcji programu `$env:PSModulePath` PowerShell różni się od zwykłego skryptu programu PowerShell.

W funkcji, `PSModulePath` zawiera dwie ścieżki:

* Folder, `Modules` który istnieje w katalogu głównym aplikacji funkcji.
* Ścieżka do `Modules` folderu, który jest kontrolowany przez pracownika języka programu PowerShell.

### <a name="function-app-level-modules-folder"></a>Folder na `Modules` poziomie aplikacji funkcji

Aby użyć modułów niestandardowych, można umieścić moduły, `Modules` od których funkcje zależą w folderze. Z tego folderu moduły są automatycznie dostępne dla środowiska wykonawczego funkcji. Dowolnej funkcji w aplikacji funkcji można użyć tych modułów. 

> [!NOTE]
> Moduły określone w pliku requirements.psd1 są automatycznie pobierane i dołączane do ścieżki, więc nie trzeba ich dołączać do folderu modułów. Są one przechowywane lokalnie w `$env:LOCALAPPDATA/AzureFunctions` `/data/ManagedDependencies` folderze i w folderze po uruchomieniu w chmurze.

Aby skorzystać z funkcji modułu `Modules` niestandardowego, utwórz folder w katalogu głównym aplikacji funkcji. Skopiuj moduły, których chcesz użyć w funkcjach, do tej lokalizacji.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

W `Modules` przypadku folderu aplikacja funkcji powinna mieć następującą strukturę folderów:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Po uruchomieniu aplikacji funkcji, pracownik języka programu `Modules` PowerShell `$env:PSModulePath` dodaje ten folder do, dzięki czemu można polegać na automatyczne ładowanie modułu, tak jak w zwykłym skrypcie programu PowerShell.

### <a name="language-worker-level-modules-folder"></a>Folder na `Modules` poziomie pracownika języka

Kilka modułów są powszechnie używane przez pracownika języka programu PowerShell. Moduły te są zdefiniowane `PSModulePath`w ostatniej pozycji . 

Aktualna lista modułów jest następująca:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): moduł używany do pracy `.zip` `.nupkg`z archiwami, takich jak , i innych.
* **ThreadJob:** implementacja wątków interfejsów API zadania programu PowerShell.

Domyślnie funkcja Functions używa najnowszej wersji tych modułów. Aby użyć określonej wersji modułu, `Modules` umieść tę konkretną wersję w folderze aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W [funkcji, ustawienia aplikacji](functions-app-settings.md), takich jak parametry połączenia usługi, są widoczne jako zmienne środowiskowe podczas wykonywania. Dostęp do tych `$env:NAME_OF_ENV_VAR`ustawień można uzyskać za pomocą , jak pokazano w poniższym przykładzie:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Podczas uruchamiania lokalnie ustawienia aplikacji są odczytywane z pliku projektu [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="concurrency"></a>Współbieżność

Domyślnie środowisko uruchomieniowe programu PowerShell może przetwarzać tylko jedno wywołanie funkcji naraz. Jednak ten poziom współbieżności może nie być wystarczający w następujących sytuacjach:

* Podczas próby obsługi dużej liczby wywołań w tym samym czasie.
* Gdy masz funkcje, które wywołują inne funkcje wewnątrz tej samej aplikacji funkcji.

To zachowanie można zmienić, ustawiając następującą zmienną środowiskową na wartość całkowitą:

```
PSWorkerInProcConcurrencyUpperBound
```

Ta zmienna środowiskowa jest ustawiana w [ustawieniach aplikacji](functions-app-settings.md) aplikacji funkcji.

### <a name="considerations-for-using-concurrency"></a>Zagadnienia dotyczące korzystania z współbieżności

Program PowerShell jest domyślnie pojedynczym _wątkiem_ języka skryptów. Jednak współbieżność można dodać przy użyciu wielu obszarów run programu PowerShell w tym samym procesie. Ilość utworzonych obszarów runspace będzie zgodna z ustawieniem aplikacji PSWorkerInProcConcurrencyUpperBound. Na przepływność będzie mieć wpływ ilość procesora CPU i pamięci dostępnej w wybranym planie.

Program Azure PowerShell używa niektórych kontekstów _na poziomie procesu_ i stanu, aby pomóc zaoszczędzić od nadmiernego pisania. Jednak jeśli włączysz współbieżność w aplikacji funkcji i wywołać akcje, które zmieniają stan, może skończyć się z warunków wyścigu. Te warunki wyścigu są trudne do debugowania, ponieważ jedno wywołanie opiera się na określonym stanie, a inne wywołanie zmienił stan.

Współbieżność usługi Azure PowerShell ma ogromną wartość, ponieważ niektóre operacje mogą zająć dużo czasu. Należy jednak postępować ostrożnie. Jeśli podejrzewasz, że występują warunki wyścigu, ustaw ustawienie aplikacji PSWorkerInConcurrencyUpperBound `1` i zamiast tego użyj izolacji poziomu procesu procesu procesu [języka](functions-app-settings.md#functions_worker_process_count) dla współbieżności.

## <a name="configure-function-scriptfile"></a>Konfigurowanie funkcji`scriptFile`

Domyślnie funkcja programu PowerShell `run.ps1`jest wykonywana z pliku, który współużytkuje ten sam katalog nadrzędny, co odpowiadający mu `function.json`plik .

Właściwość `scriptFile` w `function.json` może służyć do uzyskania struktury folderu, który wygląda jak w poniższym przykładzie:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

W takim przypadku `function.json` `myFunction` for `scriptFile` zawiera właściwość odwołującą się do pliku z wyeksportowane funkcji do uruchomienia.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Używanie modułów programu PowerShell przez konfigurowanie punktu wejścia

W tym artykule przedstawiono funkcje programu PowerShell w domyślnym `run.ps1` pliku skryptu wygenerowanym przez szablony.
Jednak można również dołączyć funkcje w modułach programu PowerShell. Kod określonej funkcji można odwoływać się `scriptFile` `entryPoint` do określonego kodu funkcji w module za pomocą pól i w pliku konfiguracyjnym function.json.

W takim `entryPoint` przypadku jest to nazwa funkcji lub polecenia cmdlet w `scriptFile`module programu PowerShell, do którego odwołuje się program .

Należy wziąć pod uwagę następującą strukturę folderów:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

W `PSFunction.psm1` przypadku gdy zawiera:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

W tym przykładzie `myFunction` konfiguracja `scriptFile` zawiera właściwość, która odwołuje się `PSFunction.psm1`do programu , który jest modułem programu PowerShell w innym folderze.  Właściwość `entryPoint` odwołuje `Invoke-PSTestFunc` się do funkcji, która jest punktem wejścia w module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

W tej konfiguracji `Invoke-PSTestFunc` pobiera wykonywane dokładnie `run.ps1` tak, jak by.

## <a name="considerations-for-powershell-functions"></a>Zagadnienia dotyczące funkcji programu PowerShell

Podczas pracy z funkcjami programu PowerShell, należy pamiętać o zagadnieniach w poniższych sekcjach.

### <a name="cold-start"></a>Zimny początek

Podczas tworzenia usługi Azure Functions w [modelu hostingu bezserwerowego,](functions-scale.md#consumption-plan)zimne starty są rzeczywistością. *Zimny start* odnosi się do okresu czasu, jaki zajmuje aplikacji funkcji, aby rozpocząć działanie do przetwarzania żądania. Zimny start występuje częściej w planie zużycia, ponieważ aplikacja funkcji zostanie zamknięta w okresach braku aktywności.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduły pakietowe zamiast używania`Install-Module`

Skrypt jest uruchamiany przy każdym wywołaniu. Unikaj `Install-Module` używania w skrypcie. Zamiast tego `Save-Module` należy użyć przed opublikowaniem, aby funkcja nie musiała tracić czasu na pobieranie modułu. Jeśli rozruchy na zimno mają wpływ na funkcje, rozważ wdrożenie aplikacji funkcji w [planie usługi App Service](functions-scale.md#app-service-plan) ustawionym na zawsze *włączony* lub do [planu Premium.](functions-scale.md#premium-plan)

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)

[Dokumentacja pliku host.json]: functions-host-json.md
