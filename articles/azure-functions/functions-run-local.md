---
title: Tworzenie i uruchom usługę Azure functions lokalnie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kod i przetestować usługę Azure functions na komputerze lokalnym, przed uruchomieniem funkcji platformy Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 1fe07790bd534cbe18c25cb5fb1e0634f54ac9e2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Kod testu Azure funkcji i lokalnie

Gdy [portalu Azure] zapewnia pełny zestaw narzędzi do tworzenia i testowania usługi Azure Functions, wielu deweloperów preferowane środowisko rozwoju lokalnego. Środowisko Azure Functions ułatwia opracowanie i przetestowanie funkcji na komputerze lokalnym przy użyciu edytora kodu ulubionych i narzędzia deweloperskie lokalnego. Funkcji mogą wyzwalać zdarzeń na platformie Azure, a Twoje C# i funkcji JavaScript można debugować na komputerze lokalnym. 

Jeśli program Visual Studio C# dewelopera usługi Azure Functions także są [integruje się z programem Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> Nie można mieszać lokalne działania projektowe z portalu Programowanie w tej samej aplikacji funkcji. Podczas tworzenia i publikowania funkcji z lokalnym projektu nie należy zachować lub zmodyfikować kod projektu w portalu.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Azure funkcje podstawowe narzędzia] jest lokalna wersja środowiska uruchomieniowego usługi Azure Functions, który można uruchomić na komputerze deweloperskim lokalnego. Nie jest emulatorem ani symulatorem. Jest tym samym środowisko uruchomieniowe, które uprawnienia działa na platformie Azure. Istnieją dwie wersje Azure funkcje podstawowe narzędzia, jeden dla wersji 1.x środowiska uruchomieniowego i jeden dla wersji 2.x. Obie wersje są przekazywane jako [pakietu npm](https://docs.npmjs.com/getting-started/what-is-npm).

>[!NOTE]  
> Przed zainstalowaniem wersji albo trzeba [zainstalować NodeJS](https://docs.npmjs.com/getting-started/installing-node), która obejmuje npm. Dla wersji 2.x narzędzia tylko Node.js 8.5 i nowsze wersje są obsługiwane. 

### <a name="version-2x-runtime"></a>Wersja 2.x w czasie wykonywania

Wersja środowiska uruchomieniowego usługi Azure Functions używa 2.x narzędzi 2.x, który jest wbudowany w program .NET Core. Ta wersja jest obsługiwana na wszystkich platformach .NET Core obsługuje 2.x. Dla aplikacji dla wielu platform będą używać tej wersji, gdy środowisko uruchomieniowe Functions 2.x jest wymagana. 

>[!IMPORTANT]   
> Przed zainstalowaniem narzędzi podstawowych funkcji Azure, [Zainstaluj program .NET Core 2.0](https://www.microsoft.com/net/core).  
>
> Środowisko uruchomieniowe Functions Azure 2.0 jest w wersji zapoznawczej i obecnie nie wszystkie funkcje usługi Azure Functions są obsługiwane. Aby uzyskać więcej informacji, zobacz [usługi Azure Functions runtime 2.0 znane problemy](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) 

 Aby zainstalować narzędzia w wersji 2.0, użyj następującego polecenia:

```bash
npm install -g azure-functions-core-tools@core
```

Podczas instalacji przy użyciu Ubuntu `sudo`w następujący sposób:

```bash
sudo npm install -g azure-functions-core-tools@core
```

Podczas instalowania na macOS i Linux, konieczne może być obejmują `unsafe-perm` Flaga w następujący sposób:

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

### <a name="version-1x-runtime"></a>Wersja 1.x w czasie wykonywania

Z oryginalną wersją narzędzi używa środowiska wykonawczego 1.x funkcji. Ta wersja korzysta z programu .NET Framework i jest obsługiwany tylko na komputerach z systemem Windows. Aby zainstalować wersję narzędzia 1.x, użyj następującego polecenia:

```bash
npm install -g azure-functions-core-tools
```

## <a name="run-azure-functions-core-tools"></a>Uruchom usługę Azure Functions podstawowe narzędzia
 
Azure funkcje podstawowe narzędzia dodaje następujące aliasy poleceń:
* **func**
* **azfun**
* **azurefunctions**

Żadnego z tych aliasów może służyć gdzie `func` przedstawiono w przykładach.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnej

Podczas uruchamiania lokalnego, projekt funkcji jest katalog zawierający pliki [host.json](functions-host-json.md) i [local.settings.json](#local-settings-file). Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat struktury folderów usługi Azure Functions, zobacz [przewodnik dla deweloperów usługi Azure Functions](functions-reference.md#folder-structure).

W oknie terminalu lub z wiersza polecenia Uruchom następujące polecenie, aby utworzyć projekt i lokalne repozytorium Git:

```
func init MyFunctionProj
```

Dane wyjściowe wygląda następująco:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Aby utworzyć projekt bez lokalnego repozytorium Git, użyj `--no-source-control [-n]` opcji.

## <a name="register-extensions"></a>Rejestrowanie rozszerzeń

W wersji 2.x środowiska uruchomieniowego usługi Azure Functions, należy jawnie zarejestrować [powiązanie rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) używaną w funkcji aplikacji. 

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Aby uzyskać więcej informacji, zobacz [usługi Azure Functions wyzwalaczy i powiązań pojęcia](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Plik ustawień lokalnych

Local.settings.json pliku przechowuje ustawienia Azure funkcje podstawowe narzędzia, parametry połączenia i ustawień aplikacji. Składa się z następującej struktury:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Ustawienie      | Opis                            |
| ------------ | -------------------------------------- |
| **isEncrypted** | Jeśli wartość **true**, wszystkie wartości są szyfrowane za pomocą klucza komputera lokalnego. Używane z `func settings` poleceń. Wartość domyślna to **false**. |
| **Wartości** | Kolekcja ustawień aplikacji, używane podczas uruchamiania lokalnego. **AzureWebJobsStorage** i **AzureWebJobsDashboard** przedstawiono; Aby uzyskać pełną listę, zobacz [informacje dotyczące ustawień aplikacji](functions-app-settings.md).  |
| **Host** | Ustawienia w tej sekcji dostosować funkcje procesu hosta podczas uruchamiania lokalnego. | 
| **LocalHttpPort** | Ustawia domyślny port używany podczas uruchamiania lokalnego hosta funkcji (`func host start` i `func run`). `--port` Opcji wiersza polecenia mają pierwszeństwo przed tej wartości. |
| **CORS** | Definiuje źródeł dozwolony dla [współużytkowanie zasobów między źródłami (CORS) do udostępniania](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Źródła są określane jako listę rozdzielaną przecinkami, nie może zawierać spacji. Wartość symbolu wieloznacznego (\*) jest obsługiwana, która zezwala na żądania pochodzące z dowolnego źródła. |
| **ConnectionStrings** | Zawiera parametry połączenia bazy danych dla funkcji. Parametry połączenia w tym obiekcie są dodawane do środowiska z typem dostawcy **System.Data.SqlClient**.  | 

Większość wyzwalaczy i powiązań ma **połączenia** właściwości, który jest mapowany na nazwę zmiennej lub aplikacji ustawienia środowiska. Dla każdej właściwości połączenia musi być zdefiniowana w pliku local.settings.json ustawienia aplikacji. 

Te ustawienia mogą być odczytywane w kodzie jako zmienne środowiskowe. W języku C#, użyj [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) lub [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). W języku JavaScript, użyj `process.env`. Ustawienia określone jako zmienna środowiskowa mają pierwszeństwo przed wartości w pliku local.settings.json. 

Ustawienia w pliku local.settings.json są używane tylko przez narzędzia funkcji podczas uruchamiania lokalnego. Domyślnie te ustawienia nie są migrowane automatycznie po opublikowaniu projektu na platformie Azure. Użyj `--publish-local-settings` przełącznika [po opublikowaniu](#publish) się upewnić, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure.

Jeśli nie parametry połączenia magazynu prawidłowy ma wartość dla **AzureWebJobsStorage**, jest wyświetlany następujący komunikat o błędzie:  

>Brak wartości dla AzureWebJobsStorage w local.settings.json. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Można uruchomić "func azure functionapp pobierania aplikacji ustawień <functionAppName>" lub określić parametry połączenia w local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji

Aby ustawić wartości dla parametrów połączenia, wykonaj jedną z następujących opcji:
* Wprowadź parametry połączenia z [Eksploratora usługi Storage Azure](http://storageexplorer.com/).
* Użyj jednej z następujących poleceń:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Oba polecenia wymagane do pierwszego logowania do platformy Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Tworzenie funkcji

Aby utworzyć funkcję, uruchom następujące polecenie:

```
func new
``` 
`func new` obsługuje następujące argumenty opcjonalne:

| Argument     | Opis                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Szablon język programowania, na przykład C#, F # lub języka JavaScript. |
| **`--template -t`** | Nazwa szablonu. |
| **`--name -n`** | Nazwa funkcji. |

Na przykład można utworzyć wyzwalacza JavaScript HTTP, uruchom polecenie:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Aby utworzyć funkcja wyzwalana kolejki, uruchom polecenie:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Lokalnie uruchamiać funkcje

Aby uruchomić projekt funkcji, należy uruchomić hosta funkcji. Host umożliwia Wyzwalacze dla wszystkich funkcji w projekcie:

```
func host start
```

`func host start` obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Port lokalny do nasłuchiwania. Wartość domyślna: 7071. |
| **`--debug <type>`** | Dostępne są następujące opcje `VSCode` i `VS`. |
| **`--cors`** | Rozdzielana przecinkami lista źródeł CORS, nie może zawierać spacji. |
| **`--nodeDebugPort -n`** | Numer portu debugera węzła do użycia. Wartość domyślna: Wartość z launch.json lub 5858. |
| **`--debugLevel -d`** | Poziom śledzenia konsoli (wyłączony, pełne, info, warning lub error). Domyślne: informacji.|
| **`--timeout -t`** | Limit czasu dla funkcji hosta można uruchomić w sekundach. Wartość domyślna: 20 sekund.|
| **`--useHttps`** | Powiązać https://localhost:{port} , a nie do http://localhost:{port}. Domyślnie ta opcja tworzy zaufanego certyfikatu na tym komputerze.|
| **`--pause-on-error`** | Wstrzymaj na dodatkowe dane wejściowe przed zakończeniem procesu. Przydatne przy uruchamianiu narzędzia podstawowych funkcji platformy Azure z zintegrowane środowisko programistyczne (IDE).|

Po uruchomieniu funkcji hosta danych wyjściowych funkcji wyzwalanych przez URL HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Debugowanie w kodzie VS lub Visual Studio

Aby dołączyć debuger, należy przekazać `--debug` argumentu. Aby debugować funkcji JavaScript, użyj programu Visual Studio Code. C# funkcji należy użyć programu Visual Studio.

Aby debugować funkcje C#, należy użyć `--debug vs`. Można również użyć [Azure funkcje programu Visual Studio 2017 narzędzia](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Aby uruchomić hosta i skonfigurować debugowanie JavaScript, uruchom polecenie:

```
func host start --debug vscode
```

> [!IMPORTANT]
> Debugowanie tylko Node.js 8.x jest obsługiwana. Node.js 9.x nie jest obsługiwane. 

Następnie w programie Visual Studio Code, w **debugowania** widok, wybierz opcję **dołączanie do usługi Azure Functions**. Umożliwia dołączanie punktów przerwania, Sprawdź zmienne i wykonywać krokowo kodu.

![Debugowanie JavaScript z kodem Visual Studio](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Przekazywanie danych testowych do funkcji

Aby przetestować funkcje lokalnie, możesz [uruchomieniu hosta funkcji](#start) i Wywołaj punktów końcowych na serwerze lokalnym za pomocą żądania HTTP. Punkt końcowy, który można wywołać zależy od typu funkcji. 

>[!NOTE]  
> Przykłady w tym temacie narzędzie cURL wysyłanie żądania HTTP z terminala lub wiersza polecenia. Można użyć dowolnego narzędzia do wysyłania żądań HTTP na serwerze lokalnym. Narzędzie cURL jest dostępna w systemach opartych na systemie Linux. W systemie Windows, należy najpierw pobrać i zainstalować [narzędzie cURL](https://curl.haxx.se/).

Aby uzyskać więcej ogólnych informacji na temat testowania funkcji, zobacz [strategii do testowania kodu w usługi Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkcje wyzwalane, HTTP i elementu webhook

Wywołaj następujące punktu końcowego do uruchomienia lokalnie, HTTP i elementu webhook wyzwalane funkcje:

    http://localhost:{port}/api/{function_name}

Upewnij się korzystać z tej samej nazwy serwera i portu nasłuchiwania na hoście funkcji. Zobacz to w danych wyjściowych wygenerowanych przy uruchamianiu hosta funkcji. Można wywołać tego adresu URL przy użyciu dowolnej metody HTTP obsługiwane przez wyzwalacz. 

Wyzwalacze polecenia cURL następujące `MyHttpTrigger` funkcji szybkiego startu żądanie GET z _nazwa_ przekazany parametr ciągu zapytania. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Poniższy przykład jest taką samą funkcję wywołana w żądaniu POST przekazywanie _nazwa_ w treści żądania:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Należy pamiętać, które użytkownik może wprowadzić pobrać żądań z przeglądarki, przekazywanie danych w ciągu zapytania. Dla wszystkich innych metod HTTP musisz użyć cURL, Fiddler, Postman lub podobnego narzędzia testowania HTTP.  

#### <a name="non-http-triggered-functions"></a>Funkcje wyzwalanych bez HTTP
Dla wszystkich rodzajów funkcji innych niż HTTP wyzwalaczy i elementów webhook można przetestować funkcji lokalnie, wywołując punkt końcowy administracji. Wywoływanie ten punkt końcowy z żądaniem HTTP POST na lokalnym serwerze wyzwala funkcji. Dane testowe można przekazać opcjonalnie do wykonania w treści żądania POST. Ta funkcja jest podobny do **testu** kartę w portalu Azure.  

Należy wywołać następujący punkt końcowy administratora, aby wyzwolić funkcje protokołu HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Aby przekazać dane testowe do punktu końcowego administratora funkcji, należy podać dane w treści komunikatu żądania POST. Treść komunikatu musi mieć następujący format JSON:

```JSON
{
    "input": "<trigger_input>"
}
```` 
`<trigger_input>` Wartość zawiera dane w formacie oczekiwany przez funkcję. W poniższym przykładzie cURL jest POST `QueueTriggerJS` funkcji. W takim przypadku danych wejściowych jest ciągiem, który jest odpowiednikiem wiadomości powinien znajdować się w kolejce.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Przy użyciu `func run` w wersji 1.x

>[!IMPORTANT]  
> `func run` Polecenie nie jest obsługiwane w wersji 2.x narzędzi. Aby uzyskać więcej informacji, zobacz temat [jak korzystać z wersji środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

Można także wywoływać bezpośrednio za pomocą funkcji `func run <FunctionName>` i podaj dane wejściowe dla funkcji. To polecenie jest podobny do uruchamiania przy użyciu funkcji **testu** kartę w portalu Azure. 

`func run` obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Zawartość śródwierszową. |
| **`--debug -d`** | Dołącz debuger do procesu hosta, przed uruchomieniem funkcji.|
| **`--timeout -t`** | Czas oczekiwania (w sekundach), do czasu lokalnego hosta funkcji jest gotowy.|
| **`--file -f`** | Nazwa pliku, który ma być używana jako zawartości.|
| **`--no-interactive`** | Nie jest wyświetlany monit o wprowadzenie danych. Przydatne w scenariuszach automatyzacji.|

Na przykład do wywołania funkcji wyzwalanych przez protokół HTTP i przekaż zawartość treści, uruchom następujące polecenie:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Wyświetlanie plików dziennika lokalnie

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publikowanie na platformie Azure

Aby opublikować projekt funkcje aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```
func azure functionapp publish <FunctionAppName>
```

Można użyć następujących opcji:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Ustawienia publikowania w local.settings.json na platformie Azure, monitowanie Zastąp, jeśli ustawienie już istnieje.|
| **`--overwrite-settings -y`** | Musi być używany z `-i`. Zastępuje AppSettings na platformie Azure wartości lokalnej, jeśli jest inny. Domyślnie jest monitu.|

To polecenie publikuje do istniejącej aplikacji funkcji na platformie Azure. Błąd występuje, gdy `<FunctionAppName>` nie istnieje w subskrypcji. Informacje na temat tworzenia aplikacji funkcji z wiersza polecenia lub okno terminalu przy użyciu wiersza polecenia platformy Azure, zobacz [tworzenia aplikacji funkcji wykonywania niekorzystającą](./scripts/functions-cli-create-serverless.md).

`publish` Polecenia przekazuje zawartość katalogu projektu funkcji. Po usunięciu plików lokalnie, `publish` nie powoduje usunięcia ich z platformy Azure. Możesz usunąć pliki na platformie Azure przy użyciu [narzędzie Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) w [portalu Azure].  

>[!IMPORTANT]  
> Podczas tworzenia aplikacji funkcji na platformie Azure, używa wersji 1.x funkcji środowiska uruchomieniowego domyślnie. Aby funkcja aplikacji używana wersja 2.x środowiska uruchomieniowego, Dodaj ustawienie aplikacji `FUNCTIONS_EXTENSION_VERSION=beta`.  
Aby dodać ustawienie aplikacji funkcji, należy użyć poniższego kodu wiersza polecenia platformy Azure: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Kolejne kroki

Azure funkcje podstawowe narzędzia jest [otworzyć źródła i w usłudze GitHub](https://github.com/azure/azure-functions-cli).  
W pliku żądanie usterki lub funkcji [Otwórz problem GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Azure funkcje podstawowe narzędzia]: https://www.npmjs.com/package/azure-functions-core-tools
[portalu Azure]: https://portal.azure.com 
