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
ms.date: 08/14/2018
ms.author: glenga
ms.openlocfilehash: 08686c46cbba1d7e51f4d73a6c2d0010d767d0bd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039331"
---
# <a name="work-with-azure-functions-core-tools"></a>Praca z usługą Azure Functions podstawowych narzędzi

Podstawowe narzędzia usługi Azure Functions umożliwia opracowywanie i testowanie funkcji na komputerze lokalnym z wiersza polecenia lub terminalu. Funkcji lokalnych można nawiązać połączenie na żywo usług platformy Azure, a następnie można debugować funkcje na komputerze lokalnym przy użyciu pełne środowisko uruchomieniowe usługi Functions. Możesz nawet wdrożyć aplikację funkcji do subskrypcji platformy Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Podstawowe narzędzia wersji

Istnieją dwie wersje podstawowych narzędzi usługi Azure Functions. Lokalne Środowisko deweloperskie, zależy od używanej wersji [wyboru języka](supported-languages.md)i jest wymagana obsługa na poziomie:

+ [W wersji 1.x](#v1): obsługuje wersję 1.x środowiska uruchomieniowego. Ta wersja narzędzia jest obsługiwana tylko na komputerach Windows i została zainstalowana ze [pakietu npm](https://docs.npmjs.com/getting-started/what-is-npm). Za pomocą tej wersji można tworzyć funkcje w języków eksperymentalnych, które nie są oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [obsługiwanych języków w usłudze Azure Functions](supported-languages.md)

+ [W wersji 2.x](#v2): obsługuje [wersji 2.x środowiska uruchomieniowego](functions-versions.md). Ta wersja obsługuje [Windows](#windows-npm), [macOS](#brew), i [Linux](#linux). Używa menedżerów pakietów określonych platform lub npm dla instalacji.

Jeśli nie określono inaczej, przykłady w niniejszym artykule dotyczą wersji 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Podstawowe narzędzia usługi Azure Functions] obejmuje wersję tego samego środowiska uruchomieniowego, zapewniająca środowisko uruchomieniowe usługi Azure Functions, którą można uruchamiać na lokalnym komputerze deweloperskim. Umożliwia także polecenia, aby tworzyć funkcje, łączenie z platformą Azure i wdrażać projekty funkcji.

### <a name="v1"></a>W wersji 1.x

Środowisko uruchomieniowe 1.x funkcji korzysta z oryginalną wersję narzędzia. Ta wersja programu .NET Framework (4.7) i jest obsługiwana tylko na komputerach z Windows. Przed zainstalowaniem wersji 1.x narzędzia, należy najpierw [zainstalować oprogramowanie NodeJS](https://docs.npmjs.com/getting-started/installing-node), która obejmuje npm.

Aby zainstalować narzędzia 1.x do wersji, użyj następującego polecenia:

```bash
npm install -g azure-functions-core-tools@v1
```

### <a name="v2"></a>W wersji 2.x

Wersja środowiska uruchomieniowego usługi Azure Functions korzysta z 2.x narzędzia 2.x, która jest oparta na module .NET Core. Ta wersja jest obsługiwana na wszystkich platformach .NET Core 2.x obsługuje, w tym [Windows](#windows-npm), [macOS](#brew), i [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Następujące kroki umożliwiają instalowanie podstawowych narzędzi na Windows npm. Można również użyć [Chocolatey](https://chocolatey.org/). Aby uzyskać więcej informacji, zobacz [readme podstawowe narzędzia](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Zainstaluj [.NET Core 2.1 dla Windows](https://www.microsoft.com/net/download/windows).

2. Zainstaluj [Node.js], która obejmuje npm. W wersji 2.x narzędzia tylko 8.5 środowiska Node.js i nowsze wersje są obsługiwane.

3. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    npm install -g azure-functions-core-tools
    ```

#### <a name="brew"></a>System MacOS przy użyciu Homebrew

Poniższe kroki umożliwiają instalowanie podstawowych narzędzi w systemie macOS Homebrew.

1. Zainstaluj [platformy .NET Core 2.1 dla systemu macOS](https://www.microsoft.com/net/download/macos).

2. Zainstaluj [Homebrew](https://brew.sh/), jeśli nie jest jeszcze zainstalowana.

3. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) przy użyciu APT

Następujące kroki użycia [APT](https://wiki.debian.org/Apt) do zainstalowania podstawowych narzędzi w Twojej dystrybucji systemu Ubuntu/Debian Linux. Inne dystrybucje systemu Linux, zobacz [readme podstawowe narzędzia](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Zainstaluj [platformy .NET Core 2.1 dla systemu Linux](https://www.microsoft.com/net/download/linux).

2. Zarejestruj klucz produktu firmy Microsoft jako zaufaną:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Sprawdź, czy serwer Ubuntu z jednym z odpowiedniej wersji z poniższej tabeli. Aby dodać źródła apt, uruchom następujące polecenie:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Dystrybucja systemu Linux | Wersja |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux trudniej zdobyć 18    | `xenial`  |

4. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

### <a name="v1"></a>W wersji 1.x

Środowisko uruchomieniowe 1.x funkcji korzysta z oryginalną wersję narzędzia. Ta wersja programu .NET Framework (4.7.1) i jest obsługiwana tylko na komputerach z Windows. Przed zainstalowaniem wersji 1.x narzędzia, należy najpierw [zainstalować oprogramowanie NodeJS](https://docs.npmjs.com/getting-started/installing-node), która obejmuje npm.

Aby zainstalować narzędzia 1.x do wersji, użyj następującego polecenia:

```bash
npm install -g azure-functions-core-tools@v1
```

## <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnej

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
| **`--worker-runtime`** | Ustawia w środowisku uruchomieniowym języka dla projektu. Obsługiwane wartości to `dotnet`, `node` (JavaScript) i `java`. Gdy nie są ustawione, monit wybierz swoje środowiska uruchomieniowego podczas inicjowania. |

> [!IMPORTANT]
> Domyślnie w wersji 2.x podstawowe narzędzia tworzy funkcję projektów aplikacji dla środowiska uruchomieniowego .NET jako [klasy projektów języka C#](functions-dotnet-class-library.md) (.csproj). Te projekty C#, które mogą być używane z programu Visual Studio lub Visual Studio Code, są kompilowane podczas testowania i publikowania na platformie Azure. Jeśli chcesz zamiast tego do tworzenia i pracy z tym samym skrypt języka C# (csx) pliki utworzone w wersji 1.x i w portalu może zawierać `--csx` parametru podczas tworzenia i wdrażania funkcji.

## <a name="register-extensions"></a>Rejestrowanie rozszerzeń

W wersji 2.x środowisko uruchomieniowe usługi Azure Functions, należy jawnie zarejestrować rozszerzenia powiązanie (typ powiązania) używanych w aplikacji funkcji.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Aby uzyskać więcej informacji, zobacz [pojęcia powiązania i Wyzwalacze usługi Azure Functions](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Plik ustawień lokalnych

Local.settings.json pliku przechowuje ustawienia aplikacji, parametry połączenia i ustawień dla podstawowych narzędzi usługi Azure Functions. Ma następującą strukturę:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS\_WORKER\_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Ustawienie      | Opis                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Po ustawieniu **true**, wszystkie wartości są szyfrowane za pomocą klucza komputera lokalnego. Używane z `func settings` poleceń. Wartość domyślna to **false**. |
| **Wartości** | Kolekcja ustawień aplikacji i parametry połączenia używane podczas uruchamiania lokalnego. Te wartości odpowiadają ustawienia aplikacji w aplikacji funkcji na platformie Azure, takich jak **AzureWebJobsStorage** i **AzureWebJobsDashboard**. Wiele wyzwalaczy i powiązań ma właściwość, która odwołuje się do aplikacji ustawienie parametrów połączenia, takich jak **połączenia** dla [wyzwalacz usługi Blob storage](functions-bindings-storage-blob.md#trigger---configuration). W przypadku takich właściwości potrzebne ustawienie aplikacji zdefiniowane w **wartości** tablicy. <br/>**AzureWebJobsStorage** ustawienie wymaganą aplikację dla wyzwalaczy innych niż HTTP. Jeśli masz [emulatora usługi Azure storage](../storage/common/storage-use-emulator.md) zainstalowane lokalnie, możesz ustawić **AzureWebJobsStorage** do `UseDevelopmentStorage=true` i podstawowe narzędzia używa emulatora. Jest to przydatne podczas tworzenia aplikacji, ale należy przetestować za pomocą połączenia rzeczywisty magazyn, przed przystąpieniem do wdrożenia. |
| **Host** | Ustawienia w tej sekcji dostosować proces hosta funkcji podczas uruchamiania lokalnego. |
| **LocalHttpPort** | Ustawia domyślny port używany podczas uruchamiania lokalnego hosta funkcji (`func host start` i `func run`). `--port` Opcji wiersza polecenia mają pierwszeństwo przed tę wartość. |
| **CORS** | Określa pochodzenia, które mogą uzyskać [współużytkowanie zasobów między źródłami (cors)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Źródła są dostarczane jako listę rozdzielaną przecinkami, bez spacji. Wartość symbolu wieloznacznego (\*) jest obsługiwany, co pozwala żądań z dowolnego źródła. |
| **ConnectionStrings** | Nie używaj tej kolekcji parametry połączenia używane przez usługi powiązania funkcji. Ta kolekcja jest używana tylko przez struktur, które zazwyczaj pobierają parametry połączenia z **ConnectionStrings** pliku sekcji konfiguracji, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Parametry połączenia, w tym obiekcie są dodawane do środowiska z typem dostawcy [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Elementy w tej kolekcji nie są publikowane na platformie Azure z innymi ustawieniami aplikacji. Należy jawnie dodać tych wartości, aby **parametry połączenia** zbiór ustawień aplikacji funkcji. Jeśli tworzysz [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji należy przechowywać wartość parametrów połączenia w **ustawienia aplikacji** przy użyciu innych połączeń. |

Wartości ustawień aplikacji funkcji mogą być odczytywane w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji zobacz sekcję zmiennych środowiska te tematy referencyjne języka:

+ [Wstępnie skompilowany C#](functions-dotnet-class-library.md#environment-variables)
+ [Skryptu C# (csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Ustawienia w pliku local.settings.json są używane tylko przez funkcje narzędzia, podczas uruchamiania lokalnego. Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt zostanie opublikowany na platformie Azure. Użyj `--publish-local-settings` Przełącz [po opublikowaniu](#publish) się upewnić, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure. Wartości w **ConnectionStrings** nigdy nie są publikowane.

Jeśli ustawiono nie prawidłowych parametrów połączenia magazynu dla **AzureWebJobsStorage** i emulatora nie jest używana, jest wyświetlany następujący komunikat o błędzie:  

> Brak wartości dla AzureWebJobsStorage w local.settings.json. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Można uruchomić "azure functionapp func pobierania aplikacji settings <functionAppName>" lub Podaj parametry połączenia w local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Pobierz parametry połączenia magazynu

Nawet w przypadku korzystania z emulatora magazynu do tworzenia aplikacji, można przetestować za pomocą połączenia rzeczywisty magazyn. Przy założeniu, że już [utworzono konto magazynu](../storage/common/storage-create-storage-account.md), prawidłowych parametrów połączenia magazynu można uzyskać w jednym z następujących sposobów:

+ Z [Azure Portal]. Przejdź do swojego konta magazynu, wybierz opcję **klucze dostępu** w **ustawienia**, a następnie skopiuj jeden z **parametry połączenia** wartości.

  ![Skopiuj parametry połączenia z witryny Azure portal](./media/functions-run-local/copy-storage-connection-portal.png)

+ Użyj [Eksploratora usługi Azure Storage](http://storageexplorer.com/) nawiązać połączenia z kontem platformy Azure. W **Explorer**, rozwinąć swoją subskrypcję, wybierz konto magazynu i skopiuj parametry połączenia podstawowej lub dodatkowej. 

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
| **`--language -l`**| Szablon, język programowania, takich jak C#, F # lub JavaScript. Ta opcja jest wymagana w wersji 1.x. W wersji 2.x, użyj tej opcji nie lub wybierz język, który pasuje do środowiska wykonawczego procesu roboczego. |
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
| **`--build`** | Kompiluj bieżący projekt przed uruchomieniem. W wersji 2.x i C# tylko dla projektów. |
| **`--cert`** | Ścieżka do pliku PFX, który zawiera klucz prywatny. Używana tylko z `--useHttps`. W wersji 2.x tylko. |
| **`--cors`** | Rozdzielana przecinkami lista źródeł CORS, bez spacji. |
| **`--debug`** | Uruchamia hosta z portu debugowania Otwórz tak, aby można dołączyć do **func.exe** procesu z [programu Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) lub [programu Visual Studio 2017](functions-dotnet-class-library.md). Prawidłowe wartości to `VSCode` i `VS`.  |
| **`--language-worker`** | Argumenty do konfigurowania procesu roboczego języka. W wersji 2.x tylko. |
| **`--nodeDebugPort -n`** | Port do użycia w debugerze węzła. Wartość domyślna: Wartość z pliku launch.json lub 5858. W wersji 1.x tylko. |
| **`--password`** | Hasło lub plik, który zawiera hasło do pliku .pfx. Używana tylko z `--cert`. W wersji 2.x tylko. |
| **`--port -p`** | Lokalny port do nasłuchiwania. Wartość domyślna: 7071. |
| **`--timeout -t`** | Limit czasu dla hosta funkcji, które można uruchomić w ciągu kilku sekund. Wartość domyślna: 20 sekund.|
| **`--useHttps`** | Powiąż z `https://localhost:{port}` , a nie do `http://localhost:{port}`. Domyślnie ta opcja tworzy zaufanego certyfikatu na komputerze.|

Dla języka C# projekt biblioteki klas (.csproj), należy wprowadzić `--build` opcję, aby wygenerować biblioteki .dll.

Podczas uruchamiania hosta funkcje, dane wyjściowe funkcji wyzwalanych przez adres URL protokołu HTTP:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

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
````

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

### <a name="viewing-log-files-locally"></a>Wyświetlanie plików dziennika lokalnie

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publikowanie na platformie Azure

Podstawowe narzędzia obsługuje dwa typy wdrożenia, wdrażania funkcji pliki projektu bezpośrednio do aplikacji funkcji i wdrażanie niestandardowych kontenera systemu Linux, który jest obsługiwany tylko w wersji 2.x.

W wersji 2.x, konieczne jest posiadanie [zarejestrowanych rozszerzeń](#register-extensions) w projekcie, przed opublikowaniem. Projekty, które wymagają kompilacji powinny zostać skompilowane, tak aby pliki binarne, które mogą być wdrażane.

### <a name="project-file-deployment"></a>Wdrażanie pliku projektu  

Najbardziej typowa metoda wdrażania obejmuje przy użyciu podstawowych narzędzi pakietu projektu aplikacji funkcji i wdrożyć pakiet do aplikacji funkcji. Można opcjonalnie [uruchamiać swoje funkcje bezpośrednio z pakietu wdrożeniowego](run-functions-from-deployment-package.md).

Aby opublikować projekt funkcje do aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```bash
func azure functionapp publish <FunctionAppName>
```

To polecenie publikuje do istniejącej aplikacji funkcji na platformie Azure. Błąd występuje, gdy `<FunctionAppName>` nie istnieje w Twojej subskrypcji. Aby dowiedzieć się, jak utworzyć aplikację funkcji z wiersza polecenia lub okno terminalu przy użyciu wiersza polecenia platformy Azure, zobacz [tworzenie aplikacji funkcji do wykonywania bezserwerowego](./scripts/functions-cli-create-serverless.md).

`publish` Polecenie przesyła zawartość katalogu projektu funkcji. W przypadku usunięcia plików lokalnie, `publish` polecenie nie powoduje usunięcia ich z platformy Azure. Możesz usunąć pliki na platformie Azure przy użyciu [narzędzia Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) w [Azure Portal].  

>[!IMPORTANT]  
> Po utworzeniu aplikacji funkcji na platformie Azure używa wersji 2.x środowisko uruchomieniowe funkcji domyślnie. Aby funkcja aplikacji użyj wersji 1.x środowiska uruchomieniowego, Dodaj ustawienie aplikacji `FUNCTIONS_EXTENSION_VERSION=~1`.  
Użyj poniższego kodu wiersza polecenia platformy Azure, aby dodać to ustawienie do aplikacji funkcji:

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup --settings FUNCTIONS_EXTENSION_VERSION=~1
```

Można użyć następujących opcji publikowania, które mają zastosowanie do wersji, wersji 1.x i 2.x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Ustawienia publikowania w local.settings.json na platformie Azure, monitowanie o zastąpienie, jeżeli ustawienie już istnieje. Jeśli używasz emulatora magazynu, zmień ustawienie aplikacji, aby [połączenia rzeczywisty magazyn](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Pomija monit o zastąpienie ustawień aplikacji podczas `--publish-local-settings -i` jest używany.|

Następujące opcje publikowania są obsługiwane tylko w wersji 2.x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Tylko ustawienia publikowania i pominąć zawartości. Wartość domyślna to monitu. |
|**`--list-ignored-files`** | Wyświetla listę plików, które są ignorowane podczas publikowania, który jest oparty na pliku .funcignore. |
| **`--list-included-files`** | Wyświetla listę plików, które są publikowane, który jest oparty na pliku .funcignore. |
| **`--zip`** | Publikowanie w pakiecie przebieg z pliku Zip. Wymaga aplikacji ma ustawienie AzureWebJobsStorage zdefiniowane. |
| **`--force`** | Ignoruj wstępnie publikowania weryfikacji w niektórych scenariuszach. |
| **`--csx`** | Opublikuj projekt (csx) skrypt języka C#. |
| **`--no-build`** | Pomiń kompilowanie funkcje dotnet. |
| **`--dotnet-cli-params`** | Podczas publikowania skompilowany funkcji języka C# (.csproj), podstawowe narzędzia wywołuje "dotnet build--bin/publikowanie danych wyjściowych". Parametry przekazywane do tego zostanie dołączony do wiersza polecenia. |

### <a name="custom-container-deployment"></a>Wdrażanie kontenerów niestandardowych

Funkcje umożliwia wdrażanie projektu funkcji w kontenerze systemu Linux niestandardowych. Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md). W wersji 2.x podstawowych narzędzi obsługuje wdrażanie niestandardowego kontenera. Kontenerów niestandardowych musi mieć w pliku Dockerfile. Użyj opcji--plik dockerfile na `func init`.

```bash
func deploy
```

Dostępne są następujące opcje wdrażania niestandardowego kontenera: 

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Nazwa rejestru platformy Docker bieżący użytkownik zalogowany. |
| **`--platform`** | Platforma hostingu aplikacji funkcji. Prawidłowe opcje to `kubernetes` |
| **`--name`** | Nazwa aplikacji funkcji. |
| **`--max`**  | Opcjonalnie Ustawia maksymalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--min`**  | Opcjonalnie Ustawia minimalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--config`** | Ustawia plik konfiguracji wdrożenia opcjonalne. |

## <a name="next-steps"></a>Kolejne kroki

Podstawowe narzędzia usługi Azure Functions jest [oprogramowanie typu open source oraz hostowane w serwisie GitHub](https://github.com/azure/azure-functions-cli).  
Aby zgłosić żądanie usterki lub funkcji [Otwórz problem w usłudze GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Podstawowe narzędzia usługi Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
