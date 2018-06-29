---
title: Praca z usługę Azure Functions podstawowe narzędzia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kod i przetestować usługę Azure functions z wiersza polecenia lub terminalu na komputerze lokalnym, przed uruchomieniem funkcji platformy Azure.
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
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 5c582b080ec6f2cff801758fc4bff4f7d07fd7df
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083073"
---
# <a name="work-with-azure-functions-core-tools"></a>Praca z usługę Azure Functions podstawowe narzędzia

Azure funkcje podstawowe narzędzia umożliwia opracowanie i przetestowanie funkcji na komputerze lokalnym z wiersza polecenia lub terminalu. Funkcji lokalnego może nawiązać połączenie na żywo usług Azure, a na komputerze lokalnym przy użyciu pełnego środowisko uruchomieniowe Functions można debugować funkcji. Nawet można wdrożyć aplikacji funkcji do subskrypcji platformy Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Podstawowe narzędzia wersji

Istnieją dwie wersje narzędzi podstawowych funkcji platformy Azure. Wersja używanego zależy od sieci lokalne Środowisko deweloperskie, wybór języka i poziom obsługi wymagane:

+ [Wersja 1.x](#v1): obsługuje wersja 1.x środowiska uruchomieniowego, które jest ogólnie dostępna (GA). Ta wersja narzędzia jest obsługiwany tylko na komputerach z systemem Windows i jest instalowany z [pakietu npm](https://docs.npmjs.com/getting-started/what-is-npm). W tej wersji można utworzyć funkcji w eksperymentalnym języki, które oficjalnie nie są obsługiwane. Aby uzyskać więcej informacji, zobacz [obsługiwanych języków w usługi Azure Functions](supported-languages.md)

+ [Wersja 2.x](#v2): obsługuje wersję 2.x środowiska uruchomieniowego. Ta wersja obsługuje [Windows](#windows-npm), [macOS](#brew), i [Linux](#linux). Używa menedżerów specyficzne dla platformy pakietu lub programu npm dla instalacji. Podobnie jak 2.x środowiska uruchomieniowego ta wersja narzędzia core jest obecnie w przeglądzie.

Jeśli nie podano inaczej, przykłady w tym artykule dotyczą wersji 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Środowisko Azure Functions podstawowe narzędzia] zawiera wersję tego samego środowiska uruchomieniowego obsługującego środowisko uruchomieniowe usługi Azure Functions, które można uruchomić na komputerze deweloperskim lokalnego. Umożliwia także polecenia do tworzenia funkcji, połączenia z platformą Azure i wdrażanie funkcji projektów.

### <a name="v1"></a>Wersja 1.x

Z oryginalną wersją narzędzi używa środowiska wykonawczego 1.x funkcji. Ta wersja programu .NET Framework (4.7.1) używa i jest obsługiwana tylko na komputerach z systemem Windows. Przed zainstalowaniem wersji narzędzia 1.x musi [zainstalować NodeJS](https://docs.npmjs.com/getting-started/installing-node), która obejmuje npm.

Aby zainstalować wersję narzędzia 1.x, użyj następującego polecenia:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Wersja 2.x

>[!NOTE]
> Środowisko uruchomieniowe Functions Azure 2.0 jest w wersji zapoznawczej i obecnie nie wszystkie funkcje usługi Azure Functions są obsługiwane. Aby uzyskać więcej informacji, zobacz [wersje usługi Azure Functions](functions-versions.md) 

Wersja środowiska uruchomieniowego usługi Azure Functions używa 2.x narzędzi 2.x, który jest wbudowany w program .NET Core. Ta wersja jest obsługiwana na wszystkich platformach obsługuje 2.x .NET Core, w tym [Windows](#windows-npm), [macOS](#brew), i [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Następujące kroki umożliwiają npm zainstalować narzędzia Core w systemie Windows. Można również użyć [Chocolatey](https://chocolatey.org/). Aby uzyskać więcej informacji, zobacz [readme podstawowe narzędzia](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Zainstaluj [.NET Core 2.0 w systemie Windows](https://www.microsoft.com/net/download/windows).

2. Zainstaluj [Node.js], która obejmuje npm. Dla wersji 2.x narzędzia tylko Node.js 8.5 i nowsze wersje są obsługiwane.

3. Instalacja pakietu podstawowe narzędzia:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>System MacOS z oprogramowania Homebrew

Poniższe kroki należy zainstalować podstawowe narzędzia na macOS przez oprogramowania Homebrew.

1. Zainstaluj [.NET Core 2.0 dla macOS](https://www.microsoft.com/net/download/macos).

2. Zainstaluj [Homebrew](https://brew.sh/), jeśli to nie jest jeszcze zainstalowana.

3. Instalacja pakietu podstawowe narzędzia:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) z APT

Następujące kroki użyj [APT](https://wiki.debian.org/Apt) zainstalować podstawowe narzędzia na dystrybucji Ubuntu/Debian Linux. Dla innych dystrybucje systemu Linux, zobacz [readme podstawowe narzędzia](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Zainstaluj [.NET Core 2.0 w systemie Linux](https://www.microsoft.com/net/download/linux).

2. Zarejestruj klucz produktu firmy Microsoft jako zaufany:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Sprawdź, czy serwer Ubuntu działa jeden z odpowiedniej wersji z poniższej tabeli. Aby dodać stanie źródła, uruchom polecenie:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Dystrybucja systemu Linux | Wersja |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux trudniej zdobyć 18    | `xenial`  |

4. Instalacja pakietu podstawowe narzędzia:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnej

Funkcje katalogu projektu zawiera pliki [host.json](functions-host-json.md) i [local.settings.json](#local-settings-file), wzdłuż podfolderów, które zawierają kod dla poszczególnych funkcji. Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat struktury folderów funkcji, zobacz [przewodnik dla deweloperów usługi Azure Functions](functions-reference.md#folder-structure).

Wersja 2.x, musisz wybrać domyślny język projektu, gdy jest on zainicjowany, a wszystkie funkcje dodane Użyj domyślnych szablonów języka. W wersji 1.x, należy określić język w przypadku tworzenia funkcji.

W oknie terminalu lub z wiersza polecenia Uruchom następujące polecenie, aby utworzyć projekt i lokalne repozytorium Git:

```bash
func init MyFunctionProj
```

W wersji 2.x, po uruchomieniu polecenia musisz wybrać środowisko uruchomieniowe dla projektu. Jeśli planujesz tworzenie funkcji JavaScript, wybierz **węzła**:

```output
Select a worker runtime:
dotnet
node
```

Górę/dół klawisze strzałek, aby wybrać język, naciśnij klawisz Enter. Dane wyjściowe wyglądają jak w następującym przykładzie dla projektu w języku JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Aby utworzyć projekt bez lokalnego repozytorium Git, użyj `--no-source-control [-n]` opcji.

## <a name="register-extensions"></a>Rejestrowanie rozszerzeń

W wersji 2.x środowiska uruchomieniowego usługi Azure Functions, należy jawnie zarejestrować używanych w aplikacji funkcję rozszerzeń powiązanie (typ powiązania).

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Aby uzyskać więcej informacji, zobacz [usługi Azure Functions wyzwalaczy i powiązań pojęcia](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Plik ustawień lokalnych

Local.settings.json pliku przechowuje ustawienia Azure funkcje podstawowe narzędzia, parametry połączenia i ustawień aplikacji. Składa się z następującej struktury:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
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
| **IsEncrypted** | Jeśli wartość **true**, wszystkie wartości są szyfrowane za pomocą klucza komputera lokalnego. Używane z `func settings` poleceń. Wartość domyślna to **false**. |
| **Wartości** | Kolekcja ustawień aplikacji i parametrów połączeń używanych podczas uruchamiania lokalnego. Te wartości odpowiadają ustawieniom aplikacji w aplikacji funkcji na platformie Azure, takich jak **AzureWebJobsStorage** i **AzureWebJobsDashboard**. Wiele wyzwalaczy i powiązań ma właściwość, która odwołuje się do aplikacji ustawienie parametrów połączenia, takich jak **połączenia** dla [wyzwalacza magazynu obiektów Blob](functions-bindings-storage-blob.md#trigger---configuration). Dla takich właściwości, należy ustawienie aplikacji zdefiniowane w **wartości** tablicy. <br/>**AzureWebJobsStorage** ustawienie — aplikacja wymagana wyzwalaczy innych niż HTTP. Jeśli masz [emulatora magazynu Azure](../storage/common/storage-use-emulator.md) zainstalowane lokalnie, można ustawić **AzureWebJobsStorage** do `UseDevelopmentStorage=true` i podstawowe narzędzia używa emulatora. Jest to przydatne podczas programowania, ale należy przetestować z połączeniem rzeczywistego magazynu przed wdrożeniem. |
| **Host** | Ustawienia w tej sekcji dostosować funkcje procesu hosta podczas uruchamiania lokalnego. |
| **LocalHttpPort** | Ustawia domyślny port używany podczas uruchamiania lokalnego hosta funkcji (`func host start` i `func run`). `--port` Opcji wiersza polecenia mają pierwszeństwo przed tej wartości. |
| **CORS** | Definiuje źródeł dozwolony dla [współużytkowanie zasobów między źródłami (CORS) do udostępniania](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Źródła są określane jako listę rozdzielaną przecinkami, nie może zawierać spacji. Wartość symbolu wieloznacznego (\*) jest obsługiwana, która zezwala na żądania pochodzące z dowolnego źródła. |
| **ConnectionStrings** | Nie używaj tej kolekcji parametry połączenia używane przez powiązania funkcji. Ta kolekcja jest używana tylko przez platformy, które należy pobrać parametry połączenia z **ConnectionStrings** pliku sekcji konfiguracji, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Parametry połączenia w tym obiekcie są dodawane do środowiska z typem dostawcy [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Elementy w tej kolekcji nie są publikowane na platformie Azure z innymi ustawieniami aplikacji. Należy jawnie dodać tych wartości **parametry połączenia** sekcji **ustawienia aplikacji** dla funkcji aplikacji. |

Wartości ustawienia aplikacji funkcji mogą być odczytywane w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji zobacz sekcję zmiennych środowiska te tematy dokumentacji specyficzny dla języka:

+ [Wstępnie skompilowana C#](functions-dotnet-class-library.md#environment-variables)
+ [Skryptu C# (csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Ustawienia w pliku local.settings.json są używane tylko przez narzędzia funkcji podczas uruchamiania lokalnego. Domyślnie te ustawienia nie są migrowane automatycznie po opublikowaniu projektu na platformie Azure. Użyj `--publish-local-settings` przełącznika [po opublikowaniu](#publish) się upewnić, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure. Wartości w **ConnectionStrings** nigdy nie są publikowane.

Jeśli nie parametry połączenia magazynu prawidłowy ma wartość dla **AzureWebJobsStorage** i emulatora nie jest używana, jest wyświetlany następujący komunikat o błędzie:  

>Brak wartości dla AzureWebJobsStorage w local.settings.json. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Można uruchomić "func azure functionapp pobierania aplikacji ustawień <functionAppName>" lub określić parametry połączenia w local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Pobrać parametry połączenia magazynu

Nawet wtedy, gdy do tworzenia aplikacji przy użyciu emulatora magazynu, można przetestować z połączeniem rzeczywistego magazynu. Zakładając, że masz już [utworzono konto magazynu](../storage/common/storage-create-storage-account.md), można uzyskać magazynu prawidłowe parametry połączenia w jednym z następujących sposobów:

+ Z [Azure Portal]. Przejdź do swojego konta magazynu, wybierz opcję **klucze dostępu** w **ustawienia**, następnie skopiować jeden z **ciąg połączenia** wartości.

  ![Skopiuj parametry połączenia z portalu Azure](./media/functions-run-local/copy-storage-connection-portal.png)

+ Użyj [Eksploratora usługi Storage Azure](http://storageexplorer.com/) do łączenia się z kontem platformy Azure. W **Explorer**, rozwiń subskrypcji, wybierz konto magazynu, a następnie skopiuj parametry połączenia podstawowej lub dodatkowej. 

  ![Skopiuj parametry połączenia z Eksploratora usługi Storage](./media/functions-run-local/storage-explorer.png)

+ Użyj podstawowe narzędzia, aby pobrać parametry połączenia z platformy Azure z jednym z następujących poleceń:

    + Pobierz wszystkie ustawienia z istniejącej aplikacji funkcji:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Pobierz ciąg połączenia dla konta określonego magazynu:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Po możesz nie są już podpisane Azure, zostanie wyświetlony monit, aby to zrobić.

## <a name="create-func"></a>Tworzenie funkcji

Aby utworzyć funkcję, uruchom następujące polecenie:

```bash
func new
```

W wersji 2.x, po uruchomieniu `func new` zostanie wyświetlony monit o wybierz szablon domyślny język aplikacji funkcji, a następnie zostanie również wyświetlony monit o wybierz nazwę funkcji. W wersji 1.x, zostanie również wyświetlony monit o wybierz język.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Kod funkcji jest generowany w podfolderze o nazwie określona funkcja, jak widać w następujących danych wyjściowych wyzwalacza kolejki:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Te opcje można również określić w poleceniu przy użyciu następujących argumentów:

| Argument     | Opis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| Szablon język programowania, na przykład C#, F # lub języka JavaScript. Ta opcja jest wymagana w wersji 1.x. W wersji 2.x, użyj tej opcji nie lub wybierz domyślny język projektu. |
| **`--template -t`** | Nazwa szablonu może być jedna z wartości:<br/><ul><li>`Blob trigger`</li><li>`Cosmos DB trigger`</li><li>`Event Grid trigger`</li><li>`HTTP trigger`</li><li>`Queue trigger`</li><li>`SendGrid`</li><li>`Service Bus Queue trigger`</li><li>`Service Bus Topic trigger`</li><li>`Timer trigger`</li></ul> |
| **`--name -n`** | Nazwa funkcji. |

Na przykład można utworzyć wyzwalacza JavaScript HTTP za pomocą jednego polecenia, uruchom polecenie:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Aby utworzyć funkcja wyzwalana kolejki za pomocą jednego polecenia, uruchom polecenie:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Lokalnie uruchamiać funkcje

Aby uruchomić projekt funkcji, należy uruchomić hosta funkcji. Host umożliwia Wyzwalacze dla wszystkich funkcji w projekcie:

```bash
func host start
```

`func host start` obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Port lokalny do nasłuchiwania. Wartość domyślna: 7071. |
| **`--debug <type>`** | Uruchamia hosta z portem debugowania otworzyć tak, aby można dołączyć do **func.exe** procesu z [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) lub [programu Visual Studio 2017](functions-dotnet-class-library.md). *\<Typu\>* są opcje `VSCode` i `VS`.  |
| **`--cors`** | Rozdzielana przecinkami lista źródeł CORS, nie może zawierać spacji. |
| **`--nodeDebugPort -n`** | Numer portu debugera węzła do użycia. Wartość domyślna: Wartość z launch.json lub 5858. |
| **`--debugLevel -d`** | Poziom śledzenia konsoli (wyłączony, pełne, info, warning lub error). Domyślne: informacji.|
| **`--timeout -t`** | Limit czasu dla funkcji hosta można uruchomić w sekundach. Wartość domyślna: 20 sekund.|
| **`--useHttps`** | Powiązać `https://localhost:{port}` , a nie do `http://localhost:{port}`. Domyślnie ta opcja tworzy zaufanego certyfikatu na tym komputerze.|
| **`--pause-on-error`** | Wstrzymaj na dodatkowe dane wejściowe przed zakończeniem procesu. Używane podczas uruchamiania podstawowych narzędzi Visual Studio lub kodzie VS.|

Po uruchomieniu funkcji hosta danych wyjściowych funkcji wyzwalanych przez URL HTTP:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

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

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Poniższy przykład jest taką samą funkcję wywołana w żądaniu POST przekazywanie _nazwa_ w treści żądania:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Możesz wprowadzić pobrać żądań z przeglądarki, przekazywanie danych w ciągu zapytania. Dla wszystkich innych metod HTTP musisz użyć cURL, Fiddler, Postman lub podobnego narzędzia testowania HTTP.  

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

```bash
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

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Wyświetlanie plików dziennika lokalnie

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publikowanie na platformie Azure

Aby opublikować projekt funkcje aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```bash
func azure functionapp publish <FunctionAppName>
```

Można użyć następujących opcji:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Ustawienia publikowania w local.settings.json na platformie Azure, monitowanie Zastąp, jeśli ustawienie już istnieje. Jeśli używasz emulatora magazynu, Zmień ustawienia aplikacji na [połączenia z magazynem rzeczywiste](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Musi być używany z `-i`. Zastępuje AppSettings na platformie Azure wartości lokalnej, jeśli jest inny. Domyślnie jest monitu.|

To polecenie publikuje do istniejącej aplikacji funkcji na platformie Azure. Błąd występuje, gdy `<FunctionAppName>` nie istnieje w subskrypcji. Informacje na temat tworzenia aplikacji funkcji z wiersza polecenia lub okno terminalu przy użyciu wiersza polecenia platformy Azure, zobacz [tworzenia aplikacji funkcji wykonywania niekorzystającą](./scripts/functions-cli-create-serverless.md).

`publish` Polecenia przekazuje zawartość katalogu projektu funkcji. Po usunięciu plików lokalnie, `publish` nie powoduje usunięcia ich z platformy Azure. Możesz usunąć pliki na platformie Azure przy użyciu [narzędzie Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) w [Azure Portal].  

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

[Środowisko Azure Functions podstawowe narzędzia]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
