---
title: Praca z usługą Azure Functions podstawowe narzędzia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kodowanie i testowanie usługi Azure functions z wiersza polecenia lub terminalu na komputerze lokalnym, przed uruchomieniem w usłudze Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: dd6259173792585a83effd42c75ff9a7a7d572e4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448384"
---
# <a name="work-with-azure-functions-core-tools"></a>Praca z usługą Azure Functions podstawowych narzędzi

Podstawowe narzędzia usługi Azure Functions umożliwia opracowywanie i testowanie funkcji na komputerze lokalnym z wiersza polecenia lub terminalu. Funkcji lokalnych można nawiązać połączenie na żywo usług platformy Azure, a następnie można debugować funkcje na komputerze lokalnym przy użyciu pełne środowisko uruchomieniowe usługi Functions. Możesz nawet wdrożyć aplikację funkcji do subskrypcji platformy Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Tworzenie funkcji na komputerze lokalnym i publikowania ich na platformie Azure przy użyciu podstawowych narzędzi obejmuje następujące podstawowe kroki:

> [!div class="checklist"]
> * [Instalowanie podstawowych narzędzi i zależności.](#v2)
> * [Tworzenie projektu aplikacji funkcji z szablonu specyficzny dla języka.](#create-a-local-functions-project)
> * [Rejestrowanie rozszerzeń wyzwalacz i powiązania.](#register-extensions)
> * [Zdefiniuj magazynu i innych połączeń.](#local-settings-file)
> * [Tworzenie funkcji wyzwalacza i szablonów specyficzne dla języka.](#create-func)
> * [Lokalne uruchamianie funkcji](#start)
> * [Publikowanie projektu na platformie Azure](#publish)

## <a name="core-tools-versions"></a>Podstawowe narzędzia wersji

Istnieją dwie wersje podstawowych narzędzi usługi Azure Functions. Lokalne Środowisko deweloperskie, zależy od używanej wersji [wyboru języka](supported-languages.md)i jest wymagana obsługa na poziomie:

+ W wersji 1.x: obsługuje wersję 1.x środowiska uruchomieniowego. Ta wersja narzędzia jest obsługiwana tylko na komputerach Windows i została zainstalowana ze [pakietu npm](https://docs.npmjs.com/getting-started/what-is-npm). Za pomocą tej wersji można tworzyć funkcje w języków eksperymentalnych, które nie są oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [obsługiwanych języków w usłudze Azure Functions](supported-languages.md)

+ [W wersji 2.x](#v2): obsługuje [wersji 2.x środowiska uruchomieniowego](functions-versions.md). Ta wersja obsługuje [Windows](#windows-npm), [macOS](#brew), i [Linux](#linux). Używa menedżerów pakietów określonych platform lub npm dla instalacji.

Jeśli nie określono inaczej, przykłady w niniejszym artykule dotyczą wersji 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Podstawowe narzędzia usługi Azure Functions] obejmuje wersję tego samego środowiska uruchomieniowego, zapewniająca środowisko uruchomieniowe usługi Azure Functions, którą można uruchamiać na lokalnym komputerze deweloperskim. Umożliwia także polecenia, aby tworzyć funkcje, łączenie z platformą Azure i wdrażać projekty funkcji.

### <a name="v2"></a>W wersji 2.x

Wersja środowiska uruchomieniowego usługi Azure Functions korzysta z 2.x narzędzia 2.x, która jest oparta na module .NET Core. Ta wersja jest obsługiwana na wszystkich platformach .NET Core 2.x obsługuje, w tym [Windows](#windows-npm), [macOS](#brew), i [Linux](#linux). 

> [!IMPORTANT]
> Można pominąć wymaganie dotyczące instalowania platformy .NET Core SDK 2.x przy użyciu [pakiety rozszerzeń].

#### <a name="windows-npm"></a>Windows

Następujące kroki umożliwiają instalowanie podstawowych narzędzi na Windows npm. Można również użyć [Chocolatey](https://chocolatey.org/). Aby uzyskać więcej informacji, zobacz [readme podstawowe narzędzia](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Zainstaluj [Node.js], która obejmuje npm. W wersji 2.x narzędzia tylko 8.5 środowiska Node.js i nowsze wersje są obsługiwane.

1. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    npm install -g azure-functions-core-tools
    ```

   Może upłynąć kilka minut, zanim npm pobrać i zainstalować pakiet narzędzi podstawowych.

1. Jeśli nie planujesz używania [pakiety rozszerzeń], zainstaluj [platformy .NET Core SDK 2.x dla Windows](https://www.microsoft.com/net/download/windows).

#### <a name="brew"></a>System MacOS przy użyciu Homebrew

Poniższe kroki umożliwiają instalowanie podstawowych narzędzi w systemie macOS Homebrew.

1. Zainstaluj [Homebrew](https://brew.sh/), jeśli nie jest jeszcze zainstalowana.

1. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

1. Jeśli nie planujesz używania [pakiety rozszerzeń], zainstaluj [platformy .NET Core SDK 2.x dla systemu macOS](https://www.microsoft.com/net/download/macos).


#### <a name="linux"></a> Linux (Ubuntu/Debian) przy użyciu APT

Następujące kroki użycia [APT](https://wiki.debian.org/Apt) do zainstalowania podstawowych narzędzi w Twojej dystrybucji systemu Ubuntu/Debian Linux. Inne dystrybucje systemu Linux, zobacz [readme podstawowe narzędzia](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Zarejestruj klucz produktu firmy Microsoft jako zaufaną:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Sprawdź, czy serwer Ubuntu z jednym z odpowiedniej wersji z poniższej tabeli. Aby dodać źródła apt, uruchom następujące polecenie:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Dystrybucja systemu Linux | Version |
    | --------------- | ----------- |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux trudniej zdobyć 18    | `xenial`  |

1. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Jeśli nie planujesz używania [pakiety rozszerzeń], zainstaluj [platformy .NET Core SDK dla systemu Linux 2.x](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Katalog projektu funkcji zawiera pliki [host.json](functions-host-json.md) i [local.settings.json](#local-settings-file), wraz z podfolderów, które zawierają kod dla poszczególnych funkcji. Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat funkcji strukturę folderów, zobacz [przewodnik dla deweloperów usługi Azure Functions](functions-reference.md#folder-structure).

W wersji 2.x, musisz wybrać język domyślny dla projektu, gdy zostanie zainicjowany, a wszystkie funkcje dodane Użyj domyślnego języka szablonów. W wersji 1.x, należy także określić język, w przypadku tworzenia funkcji.

W oknie terminalu lub wierszu polecenia Uruchom następujące polecenie, aby utworzyć projekt i lokalnego repozytorium Git:

```bash
func init MyFunctionProj
```

Jeśli podasz nazwę projektu, nowy folder o tej nazwie jest utworzony i zainicjowany. W przeciwnym razie jest inicjowany bieżącego folderu.  
W wersji 2.x, po uruchomieniu polecenia musisz wybrać środowisko uruchomieniowe dla Twojego projektu. Jeśli zamierzasz utworzyć funkcje języka JavaScript, wybierz opcję **węzła**:

```output
Select a worker runtime:
dotnet
node
```

Górę/dół klawiszy strzałek, aby wybrać język, naciśnij klawisz Enter. Dane wyjściowe wyglądają jak w poniższym przykładzie dla projektu w języku JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` obsługuje następujące opcje, które są w wersji tylko do 2.x, chyba że określono inaczej:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Inicjuje projekt C# (csx) skrypt. Należy określić `--csx` w kolejnych poleceniach. |
| **`--docker`** | Tworzenie pliku Dockerfile dla kontenera przy użyciu obrazu podstawowego, który jest oparty na wybranej `--worker-runtime`. Użyj tej opcji, gdy planujesz publikować niestandardowego kontenera systemu Linux. |
| **`--force`** | Inicjowanie projektu, nawet wtedy, gdy istnieją istniejące pliki w projekcie. Ustawienie to zastępuje istniejące pliki o takiej samej nazwie. Nie ma wpływu na inne pliki w folderze projektu. |
| **`--no-source-control -n`** | Uniemożliwia tworzenie domyślnej w repozytorium Git w wersji 1.x. W wersji 2.x, domyślnie nie jest tworzone repozytorium git. |
| **`--source-control`** | Określa, czy ma być tworzona repozytorium git. Domyślnie nie jest tworzony repozytorium. Gdy `true`, repozytorium jest tworzony. |
| **`--worker-runtime`** | Ustawia w środowisku uruchomieniowym języka dla projektu. Obsługiwane wartości to `dotnet`, `node` (JavaScript) `java`, i `python`. Gdy nie są ustawione, monit wybierz swoje środowiska uruchomieniowego podczas inicjowania. |

> [!IMPORTANT]
> Domyślnie w wersji 2.x podstawowe narzędzia tworzy funkcję projektów aplikacji dla środowiska uruchomieniowego .NET jako [klasy projektów języka C#](functions-dotnet-class-library.md) (.csproj). Te projekty C#, które mogą być używane z programu Visual Studio lub Visual Studio Code, są kompilowane podczas testowania i publikowania na platformie Azure. Jeśli chcesz zamiast tego do tworzenia i pracy z tym samym skrypt języka C# (csx) pliki utworzone w wersji 1.x i w portalu może zawierać `--csx` parametru podczas tworzenia i wdrażania funkcji.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt zostanie opublikowany na platformie Azure. Użyj `--publish-local-settings` Przełącz [po opublikowaniu](#publish) się upewnić, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure. Należy zauważyć, że wartości w **ConnectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji mogą być odczytywane w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji zobacz sekcję zmiennych środowiska te tematy referencyjne języka:

* [C# precompiled](functions-dotnet-class-library.md#environment-variables)
* [Skryptu C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Jeśli ustawiono nie prawidłowych parametrów połączenia magazynu dla [ `AzureWebJobsStorage` ] i emulatora nie jest używana, jest wyświetlany następujący komunikat o błędzie:

> Brak wartości dla AzureWebJobsStorage w local.settings.json. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Można uruchomić "azure functionapp func pobierania aplikacji settings \<functionAppName\>" lub Podaj parametry połączenia w local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Pobierz parametry połączenia magazynu

Nawet w przypadku korzystania z emulatora magazynu do tworzenia aplikacji, można przetestować za pomocą połączenia rzeczywisty magazyn. Przy założeniu, że już [utworzono konto magazynu](../storage/common/storage-create-storage-account.md), prawidłowych parametrów połączenia magazynu można uzyskać w jednym z następujących sposobów:

+ Z [Azure Portal]. Przejdź do swojego konta magazynu, wybierz opcję **klucze dostępu** w **ustawienia**, a następnie skopiuj jeden z **parametry połączenia** wartości.

  ![Skopiuj parametry połączenia z witryny Azure portal](./media/functions-run-local/copy-storage-connection-portal.png)

+ Użyj [Eksploratora usługi Azure Storage](https://storageexplorer.com/) nawiązać połączenia z kontem platformy Azure. W **Explorer**, rozwinąć swoją subskrypcję, wybierz konto magazynu i skopiuj parametry połączenia podstawowej lub dodatkowej.

  ![Skopiuj parametry połączenia z Eksploratora usługi Storage](./media/functions-run-local/storage-explorer.png)

+ Użyj podstawowych narzędzi, aby pobrać parametry połączenia z platformy Azure przy użyciu jednego z następujących poleceń:

  + Pobierz wszystkie ustawienia z istniejącej aplikacji funkcji:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Pobieranie parametrów połączenia dla konta określonego magazynu:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Po użytkownik są nie już zalogowany na platformie Azure, czy zostanie wyświetlony monit, aby to zrobić.

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

Kod funkcji jest generowany w podfolderze o nazwie podanej funkcji, jak pokazano w następujących danych wyjściowych wyzwalacza kolejki:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Te opcje można również określić w poleceniu za pomocą następujących argumentów:

| Argument     | Opis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Wersja 2.x) Generuje tego samego języka C# (csx) Szablony skryptów używane w wersji 1.x i w portalu. |
| **`--language -l`**| Szablon, język programowania, takich jak C#, F#, czy języka JavaScript. Ta opcja jest wymagana w wersji 1.x. W wersji 2.x, użyj tej opcji nie lub wybierz język, który pasuje do środowiska wykonawczego procesu roboczego. |
| **`--name -n`** | Nazwa funkcji. |
| **`--template -t`** | Użyj `func templates list` polecenie, aby wyświetlić pełną listę dostępnych szablonów dla każdego z obsługiwanych języków.   |

Na przykład aby utworzyć wyzwalacz JavaScript HTTP za pomocą jednego polecenia, uruchom polecenie:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Tworzenie funkcji wyzwalanej przez kolejkę za pomocą jednego polecenia, uruchom polecenie:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Uruchom funkcje lokalnie

Aby uruchomić projektu usługi Functions, uruchom hosta funkcji. Host włącza wyzwalaczy dla wszystkich funkcji w projekcie:

```bash
func host start
```

`host` Polecenia jest wymagana tylko w wersji 1.x.

`func host start` obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Do bieżącego projektu kompilacji przed uruchomieniem. Polecenia dotnet tylko dla projektów. Domyślnym ustawieniem jest wartość false. W wersji 2.x tylko. |
| **`--cert`** | Ścieżka do pliku PFX, który zawiera klucz prywatny. Używana tylko z `--useHttps`. W wersji 2.x tylko. |
| **`--cors-credentials`** | Zezwalaj na uwierzytelnionych żądań cross-origin (np. pliki cookie i Nagłówek uwierzytelnienia) w wersji 2.x tylko. |
| **`--cors`** | Rozdzielana przecinkami lista źródeł CORS, bez spacji. |
| **`--language-worker`** | Argumenty do konfigurowania procesu roboczego języka. W wersji 2.x tylko. |
| **`--nodeDebugPort -n`** | Port do użycia w debugerze węzła. Wartość domyślna: Wartość z pliku launch.json lub 5858. W wersji 1.x tylko. |
| **`--password`** | Hasło lub plik, który zawiera hasło do pliku .pfx. Używana tylko z `--cert`. W wersji 2.x tylko. |
| **`--port -p`** | Lokalny port do nasłuchiwania. Wartość domyślna: 7071. |
| **`--pause-on-error`** | Wstrzymaj na dodatkowe dane wejściowe, zanim proces zostanie zakończony. Używana tylko wtedy, gdy uruchamianie podstawowych narzędzi w zintegrowanym środowisku programistycznym (IDE).|
| **`--script-root --prefix`** | Można określić ścieżkę do katalogu głównego aplikacji funkcji, który ma zostać uruchomić lub wdrożyć. Służy to do skompilowanych projektach, generujących pliki projektu do podfolderu. Na przykład podczas kompilowania biblioteki klas C# plików projektu, host.json, local.settings.json i function.json są generowane w *głównego* podfolder ze ścieżką, takich jak `MyProject/bin/Debug/netstandard2.0`. W tym przypadku Ustaw prefiks jako `--script-root MyProject/bin/Debug/netstandard2.0`. Jest to katalog główny aplikacji funkcji, podczas uruchamiania na platformie Azure. |
| **`--timeout -t`** | Limit czasu dla hosta funkcji, które można uruchomić w ciągu kilku sekund. Wartość domyślna: 20 sekund.|
| **`--useHttps`** | Powiąż z `https://localhost:{port}` , a nie do `http://localhost:{port}`. Domyślnie ta opcja tworzy zaufanego certyfikatu na komputerze.|

Dla języka C# projekt biblioteki klas (.csproj), należy wprowadzić `--build` opcję, aby wygenerować biblioteki .dll.

Podczas uruchamiania hosta funkcje, dane wyjściowe funkcji wyzwalanych przez adres URL protokołu HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Podczas uruchamiania lokalnego, uwierzytelnianie nie jest wymuszane dla punktów końcowych HTTP. Oznacza to, że wszystkie lokalne żądania HTTP są obsługiwane jako `authLevel = "anonymous"`. Aby uzyskać więcej informacji, zobacz [artykułu powiązanie HTTP](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Przekazywanie danych testowych do funkcji

Aby przetestować funkcje lokalnie, możesz [start hosta funkcji](#start) i wywoływanie punktów końcowych na serwerze lokalnym za pomocą żądania HTTP. Punkt końcowy, który można wywołać zależy od typu funkcji.

>[!NOTE]
> Przykłady w tym temacie narzędzie cURL do wysłania żądania HTTP z poziomu terminalu lub wierszu polecenia. Można użyć dowolnego narzędzia do wysyłania żądań HTTP na serwerze lokalnym. Narzędzie cURL jest dostępna domyślnie na komputerach z systemem Linux. W Windows, musisz najpierw pobrać i zainstalować [narzędzie cURL](https://curl.haxx.se/).

Aby uzyskać więcej ogólnych informacji na temat testowania funkcji, zobacz [strategii testowania kodu w usłudze Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP i elementu webhook funkcji wyzwalanych

Wywołaj następujących funkcji wyzwalanych przez punkt końcowy, aby lokalnie uruchomić HTTP i elementy webhook:

    http://localhost:{port}/api/{function_name}

Upewnij się korzystać z tej samej nazwy serwera i portu nasłuchiwania na hoście funkcji. Zobacz to w danych wyjściowych wygenerowanych podczas uruchamiania hosta funkcji. Możesz wywołać ten adres URL przy użyciu dowolnej metody HTTP obsługiwane przez wyzwalacz.

Następujące cURL wyzwalaczy polecenia `MyHttpTrigger` funkcji szybkiego startu z żądania GET _nazwa_ przekazany przez parametr ciągu zapytania.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Poniższy przykład jest w tej samej funkcji, które są wywoływane z żądaniem POST, przekazując _nazwa_ w treści żądania:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Można wprowadzać żądania GET z przeglądarki, przekazując dane w ciągu zapytania. W przypadku innych metod HTTP należy użyć programu cURL, narzędzie Fiddler, Postman lub podobnego narzędzia testowania HTTP.

#### <a name="non-http-triggered-functions"></a>Funkcji wyzwalanych bez HTTP

Dla wszystkich rodzajów funkcji innych niż HTTP wyzwalaczami i elementami webhook można przetestować funkcje lokalnie, wywołując punkt końcowy administracji. Wywołanie tego punktu końcowego za pomocą żądania HTTP POST na lokalnym serwerze wywołuje funkcję. Można przekazać dane testowe do wykonania w treści żądania POST. Ta funkcja jest podobne do **testu** kartę w witrynie Azure portal.

Możesz wywołać następujący punkt końcowy administratora, aby wyzwolić funkcje protokołu HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Aby przekazać dane testowe do punktu końcowego administratora funkcji, musisz podać dane w treści komunikatu żądania POST. Treść wiadomości musi mieć następujący format JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` Wartość zawiera dane w formacie oczekiwanego przez funkcję. W poniższym przykładzie narzędzie cURL jest POST `QueueTriggerJS` funkcji. W tym przypadku dane wejściowe to ciąg, który jest odpowiednikiem to wiadomość oczekiwana znajdują się w kolejce.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Za pomocą `func run` polecenia w wersji 1.x

>[!IMPORTANT]
> `func run` Polecenie nie jest obsługiwane w wersji 2.x narzędzia. Aby uzyskać więcej informacji, zobacz temat [sposobu kierowania wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

Można także wywoływać bezpośrednio przy użyciu funkcji `func run <FunctionName>` i podaj dane wejściowe dla funkcji. To polecenie jest podobne do uruchamiania przy użyciu funkcji **testu** kartę w witrynie Azure portal.

`func run` obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Zawartości śródwierszowej. |
| **`--debug -d`** | Dołącz debuger do procesu hosta przed uruchomieniem funkcji.|
| **`--timeout -t`** | Czas oczekiwania (w sekundach), aż lokalnego hosta funkcji będzie gotowa.|
| **`--file -f`** | Nazwa pliku do użycia jako zawartości.|
| **`--no-interactive`** | Nie monituje o wprowadzenie danych. Przydatne w scenariuszach automatyzacji.|

Na przykład wywołanie funkcji wyzwalanej przez HTTP i przekazywanie zawartość, uruchom następujące polecenie:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publikowanie na platformie Azure

Podstawowe narzędzia usługi Azure Functions obsługuje dwa typy wdrożenia: Wdrażanie plików projektu funkcji bezpośrednio do aplikacji funkcji za pośrednictwem [Zip wdrażanie](functions-deployment-technologies.md#zip-deploy) i [wdrażania niestandardowego kontenera Docker](functions-deployment-technologies.md#docker-container). Konieczne jest posiadanie już [utworzyliśmy aplikację funkcji w ramach subskrypcji Azure](functions-cli-samples.md#create), do którego zostanie przedstawiony sposób wdrażania kodu. Projekty, które wymagają kompilacji powinny zostać skompilowane, tak aby pliki binarne, które mogą być wdrażane.

### <a name="project-file-deployment"></a>Wdrożenia (pliki projektu)

Aby opublikować lokalnego kodu do aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```bash
func azure functionapp publish <FunctionAppName>
```

To polecenie publikuje do istniejącej aplikacji funkcji na platformie Azure. Jeśli spróbujesz opublikować, zostanie wyświetlony błąd `<FunctionAppName>` która nie istnieje w Twojej subskrypcji. Aby dowiedzieć się, jak utworzyć aplikację funkcji z wiersza polecenia lub okno terminalu przy użyciu wiersza polecenia platformy Azure, zobacz [tworzenie aplikacji funkcji do wykonywania bezserwerowego](./scripts/functions-cli-create-serverless.md). Domyślnie to polecenie spowoduje włączenie aplikacji do uruchamiania w [uruchomienia z pakietu](run-functions-from-deployment-package.md) trybu.

>[!IMPORTANT]
> Po utworzeniu aplikacji funkcji w witrynie Azure portal, używa wersji 2.x środowisko uruchomieniowe funkcji domyślnie. Aby funkcja aplikacji użyj wersji 1.x środowiska uruchomieniowego, postępuj zgodnie z instrukcjami [działać w wersji 1.x](functions-versions.md#creating-1x-apps).
> Nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która ma istniejących funkcji.

Poniższe opcje publikowania są stosowane dla wersji, wersji 1.x i 2.x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Ustawienia publikowania w local.settings.json na platformie Azure, monitowanie o zastąpienie, jeżeli ustawienie już istnieje. Jeśli używasz emulatora magazynu, najpierw zmień ustawienia aplikacji [połączenia rzeczywisty magazyn](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Pomija monit o zastąpienie ustawień aplikacji podczas `--publish-local-settings -i` jest używany.|

Następujące opcje publikowania są obsługiwane tylko w wersji 2.x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Tylko ustawienia publikowania i pominąć zawartości. Wartość domyślna to monitu. |
|**`--list-ignored-files`** | Wyświetla listę plików, które są ignorowane podczas publikowania, który jest oparty na pliku .funcignore. |
| **`--list-included-files`** | Wyświetla listę plików, które są publikowane, który jest oparty na pliku .funcignore. |
| **`--nozip`** | Wyłącza domyślne `Run-From-Package` wyłączony tryb. |
| **`--build-native-deps`** | Aplikacje funkcji umożliwia pominięcie generowania .wheels folderu podczas publikowania języka python. |
| **`--additional-packages`** | Lista pakietów do zainstalowania podczas tworzenia zależności natywnych. Na przykład: `python3-dev libevent-dev`. |
| **`--force`** | Ignoruj wstępnie publikowania weryfikacji w niektórych scenariuszach. |
| **`--csx`** | Opublikuj projekt (csx) skrypt języka C#. |
| **`--no-build`** | Pomiń kompilowanie funkcje dotnet. |
| **`--dotnet-cli-params`** | Podczas publikowania skompilowany funkcji języka C# (.csproj), podstawowe narzędzia wywołuje "dotnet build--bin/publikowanie danych wyjściowych". Parametry przekazywane do tego zostanie dołączony do wiersza polecenia. |

### <a name="deployment-custom-container"></a>Wdrożenia (kontener niestandardowy)

Usługa Azure Functions umożliwia wdrażanie projektu funkcji w [niestandardowego kontenera Docker](functions-deployment-technologies.md#docker-container). Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md). Kontenerów niestandardowych musi mieć w pliku Dockerfile. Tworzenie aplikacji za pomocą pliku Dockerfile, użyj opcji--plik dockerfile na `func init`.

```bash
func deploy
```

Dostępne są następujące opcje wdrażania niestandardowego kontenera:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Nazwa rejestru platformy Docker bieżący użytkownik zalogował się. |
| **`--platform`** | Platforma hostingu aplikacji funkcji. Prawidłowe opcje to `kubernetes` |
| **`--name`** | Nazwa aplikacji funkcji. |
| **`--max`**  | Opcjonalnie Ustawia maksymalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--min`**  | Opcjonalnie Ustawia minimalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--config`** | Ustawia plik konfiguracji wdrożenia opcjonalne. |

## <a name="monitoring-functions"></a>Funkcje monitorowania

Jest to zalecany sposób monitorowania wykonywania funkcji dzięki integracji z usługą Azure Application Insights. Po utworzeniu aplikacji funkcji w witrynie Azure portal, ta Integracja jest wykonywane domyślnie. Jednak po utworzeniu aplikacji funkcji przy użyciu wiersza polecenia platformy Azure, integracji w Twojej aplikacji funkcji na platformie Azure nie jest wykonywane.

Aby włączyć usługę Application Insights dla aplikacji funkcji:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [monitora usługi Azure Functions](functions-monitoring.md).
## <a name="next-steps"></a>Kolejne kroki

Podstawowe narzędzia usługi Azure Functions jest [oprogramowanie typu open source oraz hostowane w serwisie GitHub](https://github.com/azure/azure-functions-cli).  
Aby zgłosić żądanie usterki lub funkcji [Otwórz problem w usłudze GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Podstawowe narzędzia usługi Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[pakiety rozszerzeń]: functions-bindings-register.md#extension-bundles
