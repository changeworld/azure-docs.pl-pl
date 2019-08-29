---
title: Pracuj z Azure Functions Core Tools | Microsoft Docs
description: Dowiedz się, jak kodować i testować usługi Azure Functions w wierszu polecenia lub terminalu na komputerze lokalnym przed uruchomieniem ich na Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 4bad9db6af6d368c943619bc2105f3fba0b7eb7b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096156"
---
# <a name="work-with-azure-functions-core-tools"></a>Pracuj z Azure Functions Core Tools

Azure Functions Core Tools umożliwia tworzenie i testowanie funkcji na komputerze lokalnym z poziomu wiersza polecenia lub terminalu. Funkcje lokalne mogą łączyć się z aktywnymi usługami platformy Azure, a funkcje można debugować na komputerze lokalnym za pomocą środowiska uruchomieniowego Full Functions. Możesz nawet wdrożyć aplikację funkcji w ramach subskrypcji platformy Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Tworzenie funkcji na komputerze lokalnym i publikowanie ich na platformie Azure przy użyciu narzędzi podstawowych wykonuje następujące podstawowe czynności:

> [!div class="checklist"]
> * [Zainstaluj podstawowe narzędzia i zależności.](#v2)
> * [Utwórz projekt aplikacji funkcji na podstawie szablonu specyficznego dla języka.](#create-a-local-functions-project)
> * [Zarejestruj wyzwalacz i rozszerzenia powiązań.](#register-extensions)
> * [Zdefiniuj magazyn i inne połączenia.](#local-settings-file)
> * [Utwórz funkcję z wyzwalacza i szablonu specyficznego dla języka.](#create-func)
> * [Uruchom funkcję lokalnie](#start)
> * [Publikowanie projektu na platformie Azure](#publish)

## <a name="core-tools-versions"></a>Wersje podstawowych narzędzi

Istnieją dwie wersje Azure Functions Core Tools. Używana wersja zależy od lokalnego środowiska programistycznego, [wyboru języka](supported-languages.md)i wymaganego poziomu pomocy technicznej:

+ Wersja 1. x: obsługuje wersję 1. x środowiska uruchomieniowego. Ta wersja narzędzi jest obsługiwana tylko na komputerach z systemem Windows i jest instalowana z [pakietu npm](https://docs.npmjs.com/getting-started/what-is-npm). W tej wersji można tworzyć funkcje w językach eksperymentalnych, które nie są oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [obsługiwane języki w Azure Functions](supported-languages.md)

+ [Wersja 2. x](#v2): obsługuje [wersję 2. x środowiska uruchomieniowego](functions-versions.md). Ta wersja obsługuje [systemy Windows](#windows-npm), [macOS](#brew)i [Linux](#linux). Używa menedżerów pakietów specyficznych dla platformy lub npm do instalacji.

Jeśli nie określono inaczej, przykłady w tym artykule dotyczą wersji 2. x.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Azure Functions Core Tools] obejmuje wersję tego samego środowiska uruchomieniowego, która umożliwia Azure Functions środowisko uruchomieniowe, które można uruchomić na lokalnym komputerze deweloperskim. Udostępnia również polecenia służące do tworzenia funkcji, łączenia się z platformą Azure i wdrażania projektów funkcji.

### <a name="v2"></a>Wersja 2. x

W wersji 2. x narzędzi jest używane środowisko uruchomieniowe Azure Functions 2. x, które jest oparte na platformie .NET Core. Ta wersja jest obsługiwana na wszystkich platformach .NET Core 2. x obsługuje, w tym [Windows](#windows-npm), [macOS](#brew)i [Linux](#linux). 

> [!IMPORTANT]
> Istnieje możliwość obejścia wymagania dotyczącego instalowania zestawu SDK platformy .NET Core 2. x przy użyciu [Pakiety rozszerzeń].

#### <a name="windows-npm"></a>Windows

Poniższe kroki służą do instalowania podstawowych narzędzi w systemie Windows przy użyciu programu npm. Możesz również użyć [czekolady](https://chocolatey.org/). Aby uzyskać więcej informacji, zobacz [plik Readme podstawowych narzędzi](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Zainstaluj program [Node.js], który obejmuje npm. W przypadku wersji 2. x narzędzi obsługiwane są tylko wersje Node. js 8,5 i nowsze.

1. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    npm install -g azure-functions-core-tools
    ```

   Pobranie i zainstalowanie pakietu podstawowych narzędzi może potrwać kilka minut.

1. Jeśli nie planujesz używania [Pakiety rozszerzeń], zainstaluj [zestaw SDK programu .NET Core 2. x dla systemu Windows](https://www.microsoft.com/net/download/windows).

#### <a name="brew"></a>MacOS z oprogramowania Homebrew

Poniższe kroki używają oprogramowania homebrew, aby zainstalować podstawowe narzędzia na macOS.

1. Zainstaluj program [oprogramowania Homebrew](https://brew.sh/), jeśli nie jest jeszcze zainstalowany.

1. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

1. Jeśli nie planujesz używania [Pakiety rozszerzeń], zainstaluj [zestaw .NET Core 2. x SDK dla macOS](https://www.microsoft.com/net/download/macos).


#### <a name="linux"></a>Linux (Ubuntu/Debian) z APT

Poniższe kroki używają [apt](https://wiki.debian.org/Apt) do instalowania podstawowych narzędzi w dystrybucji systemu Ubuntu/Debian Linux. Aby poznać inne dystrybucje systemu Linux, zobacz [plik Readme podstawowych narzędzi](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Zainstaluj klucz GPG repozytorium pakietów Microsoft, aby zweryfikować integralność pakietu:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Sprawdź, czy na serwerze Ubuntu jest uruchomiona jedna z odpowiednich wersji z poniższej tabeli. Aby dodać Źródło apt, uruchom polecenie:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Dystrybucja systemu Linux | Version |
    | --------------- | ----------- |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux mennic 18    | `xenial`  |

1. Zainstaluj pakiet podstawowych narzędzi:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Jeśli nie planujesz używania [Pakiety rozszerzeń], zainstaluj program [.NET Core 2. x SDK dla systemu Linux](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Katalog projektu usługi Functions zawiera pliki pliku [host. JSON](functions-host-json.md) oraz [Local. Settings. JSON](#local-settings-file), a także podfoldery zawierające kod dla poszczególnych funkcji. Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat struktury folderu Functions, zobacz [Przewodnik po programie Azure Functions Developers](functions-reference.md#folder-structure).

Wersja 2. x wymaga wybrania domyślnego języka dla projektu, gdy zostanie on zainicjowany, a wszystkie funkcje zostały dodane Użyj domyślnych szablonów języka. W wersji 1. x należy określić język za każdym razem, gdy tworzysz funkcję.

W oknie terminalu lub w wierszu polecenia Uruchom następujące polecenie, aby utworzyć projekt i lokalne repozytorium git:

```bash
func init MyFunctionProj
```

Po podaniu nazwy projektu zostanie utworzony i zainicjowany nowy folder o tej nazwie. W przeciwnym razie bieżący folder zostanie zainicjowany.  
W wersji 2. x po uruchomieniu polecenia należy wybrać środowisko uruchomieniowe dla projektu. 

```output
Select a worker runtime:
dotnet
node
python (preview)
powershell (preview)
```

Użyj klawiszy strzałek w górę/w dół, aby wybrać język, a następnie naciśnij klawisz ENTER. Jeśli planujesz programowanie funkcji JavaScript lub TypeScript, wybierz **węzeł**, a następnie wybierz język. Język TypeScript ma [pewne dodatkowe wymagania](functions-reference-node.md#typescript). 

Dane wyjściowe wyglądają podobnie do następującego przykładu dla projektu JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init`Program obsługuje następujące opcje, które są dostępne tylko w wersji 2. x, chyba że wskazano inaczej:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Inicjuje projekt C# skryptu (. CSX). Należy określić `--csx` w kolejnych poleceniach. |
| **`--docker`** | Utwórz pliku dockerfile dla kontenera przy użyciu obrazu podstawowego, który jest oparty na wybranym `--worker-runtime`z nich. Użyj tej opcji, jeśli planujesz publikowanie do niestandardowego kontenera systemu Linux. |
| **`--force`** | Zainicjuj projekt nawet wtedy, gdy istnieją pliki w projekcie. To ustawienie zastępuje istniejące pliki o tej samej nazwie. Nie ma to wpływu na inne pliki w folderze projektu. |
| **`--no-source-control -n`** | Zapobiega domyślnym tworzeniu repozytorium Git w wersji 1. x. W wersji 2. x repozytorium Git nie jest tworzone domyślnie. |
| **`--source-control`** | Określa, czy tworzone jest repozytorium git. Domyślnie repozytorium nie jest tworzone. Gdy `true`jest tworzone repozytorium. |
| **`--worker-runtime`** | Ustawia środowisko uruchomieniowe języka dla projektu. Obsługiwane wartości to `dotnet`, `node` (JavaScript), `java`i `python`. Gdy nie jest ustawiona, zostanie wyświetlony monit o wybranie środowiska uruchomieniowego podczas inicjowania. |

> [!IMPORTANT]
> Domyślnie wersja 2. x podstawowych narzędzi tworzy projekty aplikacji funkcji dla środowiska uruchomieniowego .NET jako [ C# projekty klas](functions-dotnet-class-library.md) (. csproj). Te C# projekty, które mogą być używane z programem Visual Studio lub Visual Studio Code, są kompilowane podczas testowania i podczas publikowania na platformie Azure. Jeśli zamiast tego chcesz utworzyć i korzystać z tych samych C# plików skryptów (. CSX) utworzonych w wersji 1. x i w portalu, musisz uwzględnić `--csx` parametr podczas tworzenia i wdrażania funkcji.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt jest publikowany na platformie Azure. Użyj przełącznika [podczas publikowania](#publish) , aby upewnić się, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure. `--publish-local-settings` Należy pamiętać, że wartości w **connectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można także odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz sekcję zmienne środowiskowe w następujących tematach referencyjnych dotyczących języka:

* [C#prekompilowanego](functions-dotnet-class-library.md#environment-variables)
* [Skryptu C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Gdy nie ustawiono prawidłowych parametrów połączenia magazynu dla [`AzureWebJobsStorage`] i emulator nie jest używany, wyświetlany jest następujący komunikat o błędzie:

> Brak wartości elementu AzureWebJobsStorage w pliku Local. Settings. JSON. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Można uruchomić polecenie "Func Azure functionapp Fetch-App-Settings \<functionAppName\>" lub określić parametry połączenia w pliku Local. Settings. JSON.

### <a name="get-your-storage-connection-strings"></a>Pobieranie parametrów połączenia magazynu

Nawet w przypadku korzystania z emulatora magazynu na potrzeby programowania można testować przy użyciu rzeczywistego połączenia magazynu. Przy założeniu, że [konto magazynu](../storage/common/storage-create-storage-account.md)zostało już utworzone, można uzyskać prawidłowe parametry połączenia magazynu w jeden z następujących sposobów:

+ Z [Azure Portal]. Przejdź do konta magazynu, wybierz pozycję **klucze dostępu** w obszarze **Ustawienia**, a następnie skopiuj jedną z wartości **parametrów połączenia** .

  ![Kopiuj parametry połączenia z Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

+ Użyj [Eksplorator usługi Azure Storage](https://storageexplorer.com/) , aby nawiązać połączenie z kontem platformy Azure. W **Eksploratorze**Rozwiń swoją subskrypcję, wybierz konto magazynu i skopiuj podstawowe lub pomocnicze parametry połączenia.

  ![Kopiuj parametry połączenia z Eksplorator usługi Storage](./media/functions-run-local/storage-explorer.png)

+ Narzędzia podstawowe umożliwiają pobranie parametrów połączenia z platformy Azure przy użyciu jednego z następujących poleceń:

  + Pobierz wszystkie ustawienia z istniejącej aplikacji funkcji:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Pobierz parametry połączenia dla określonego konta magazynu:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Jeśli jeszcze nie zalogowano się na platformie Azure, zostanie wyświetlony monit.

## <a name="create-func"></a>Tworzenie funkcji

Aby utworzyć funkcję, uruchom następujące polecenie:

```bash
func new
```

W wersji 2. x podczas uruchamiania `func new` zostanie wyświetlony monit o wybranie szablonu w języku domyślnym aplikacji funkcji, a następnie zostanie wyświetlony monit o wybranie nazwy funkcji. W wersji 1. x jest również wyświetlany monit o wybranie języka.

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

Kod funkcji jest generowany w podfolderze o podanej nazwie funkcji, jak widać w następujących danych wyjściowych wyzwalacza kolejki:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Możesz również określić te opcje w poleceniu przy użyciu następujących argumentów:

| Argument     | Opis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Wersja 2. x) Generuje te same C# szablony skryptów (. CSX), które są używane w wersji 1. x i w portalu. |
| **`--language -l`**| Język programowania szablonu, taki jak C#, F#lub JavaScript. Ta opcja jest wymagana w wersji 1. x. W wersji 2. x nie używaj tej opcji lub wybierz język pasujący do środowiska wykonawczego procesu roboczego. |
| **`--name -n`** | Nazwa funkcji. |
| **`--template -t`** | `func templates list` Użyj polecenia, aby wyświetlić pełną listę dostępnych szablonów dla każdego obsługiwanego języka.   |

Na przykład aby utworzyć wyzwalacz HTTP JavaScript w pojedynczym poleceniu, uruchom polecenie:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Aby utworzyć funkcję wyzwalaną przez kolejkę w pojedynczym poleceniu, uruchom polecenie:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Uruchamianie funkcji lokalnie

Aby uruchomić projekt funkcji, uruchom hosta funkcji. Host włącza wyzwalacze dla wszystkich funkcji w projekcie. 

### <a name="version-2x"></a>Wersja 2. x

W wersji 2. x środowiska uruchomieniowego polecenie uruchamiania różni się w zależności od języka projektu.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Wersja 1. x

Wersja 1. x środowiska uruchomieniowego funkcji wymaga `host` polecenia, jak w poniższym przykładzie:

```command
func host start
```

`func start`Program obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Nie wykonuj kompilacji bieżącego projektu przed uruchomieniem. Tylko w przypadku projektów dotnet. Wartość domyślna to false. Tylko wersja 2. x. |
| **`--cert`** | Ścieżka do pliku PFX, który zawiera klucz prywatny. Używany tylko z `--useHttps`. Tylko wersja 2. x. |
| **`--cors-credentials`** | Zezwalaj na żądania uwierzytelniane między źródłami (np. pliki cookie i nagłówek uwierzytelniania) tylko w wersji 2. x. |
| **`--cors`** | Rozdzielana przecinkami lista źródeł CORS bez spacji. |
| **`--language-worker`** | Argumenty umożliwiające skonfigurowanie procesu roboczego języka. Tylko wersja 2. x. |
| **`--nodeDebugPort -n`** | Port do użycia przez debuger węzła. Wartooć Wartość z pliku Launch. JSON lub 5858. Tylko wersja 1. x. |
| **`--password`** | Hasło lub plik zawierający hasło dla pliku PFX. Używany tylko z `--cert`. Tylko wersja 2. x. |
| **`--port -p`** | Port lokalny, na którym nasłuchuje. Wartość domyślna: 7071. |
| **`--pause-on-error`** | Wstrzymaj, aby uzyskać dodatkowe dane wejściowe przed wyjściem z procesu. Używane tylko w przypadku uruchamiania podstawowych narzędzi z zintegrowanego środowiska programistycznego (IDE).|
| **`--script-root --prefix`** | Służy do określania ścieżki do katalogu głównego aplikacji funkcji, która ma być uruchamiana lub wdrażana. Służy do kompilowania projektów, które generują pliki projektu w podfolderze. Na przykład podczas kompilowania projektu biblioteki C# klas plik host. JSON, Local. Settings. JSON i Function. JSON jest generowany w podfolderze *głównym* o ścieżce podobnej `MyProject/bin/Debug/netstandard2.0`do. W takim przypadku należy ustawić prefiks jako `--script-root MyProject/bin/Debug/netstandard2.0`. Jest to katalog główny aplikacji funkcji w przypadku uruchamiania na platformie Azure. |
| **`--timeout -t`** | Limit czasu uruchamiania hosta usługi Functions (w sekundach). Wartooć 20 sekund.|
| **`--useHttps`** | Powiąż `https://localhost:{port}` z, a `http://localhost:{port}`nie z. Domyślnie ta opcja tworzy zaufany certyfikat na komputerze.|

Po uruchomieniu hosta funkcji wyświetla adres URL funkcji wyzwalanych przez protokół HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>W przypadku uruchamiania lokalnego uwierzytelnianie nie jest wymuszane dla punktów końcowych HTTP. Oznacza to, że wszystkie lokalne żądania HTTP są obsługiwane `authLevel = "anonymous"`jako. Aby uzyskać więcej informacji, zobacz [artykuł dotyczący powiązań http](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Przekazywanie danych testowych do funkcji

Aby przetestować funkcje lokalnie, należy [uruchomić hosta funkcji](#start) i punkty końcowe wywołań na serwerze lokalnym przy użyciu żądań HTTP. Wywoływany punkt końcowy zależy od typu funkcji.

>[!NOTE]
> Przykłady w tym temacie używają narzędzia zwinięcie do wysyłania żądań HTTP z terminalu lub wiersza polecenia. Możesz użyć wybranego narzędzia, aby wysyłać żądania HTTP do serwera lokalnego. Narzędzie zwinięcie jest dostępne domyślnie w systemach z systemem Linux i Windows 10 Build 17063 i nowszych. W starszych systemach Windows należy najpierw pobrać i zainstalować [Narzędzie zwinięcie](https://curl.haxx.se/).

Aby uzyskać więcej ogólnych informacji na temat funkcji testowania, zapoznaj się z tematem [strategie testowania kodu w Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkcje wyzwalane przez protokół HTTP i element webhook

Przywołująsz następujący punkt końcowy do lokalnego uruchamiania funkcji HTTP i elementu webhook:

    http://localhost:{port}/api/{function_name}

Upewnij się, że używasz tej samej nazwy serwera i portu, na którym nasłuchuje hosty funkcji. Ta wartość jest wyświetlana w danych wyjściowych generowanych podczas uruchamiania hosta funkcji. Możesz wywołać ten adres URL przy użyciu dowolnej metody HTTP obsługiwanej przez wyzwalacz.

Następujące polecenie zwinięcie wyzwala `MyHttpTrigger` funkcję szybkiego startu z żądania GET z parametrem _name_ przekazaną w ciągu zapytania.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Poniższy przykład to ta sama funkcja wywołana z żądania POST w treści żądania :

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Żądania GET z przeglądarki mogą przekazywać dane w ciągu zapytania. W przypadku wszystkich innych metod HTTP należy użyć zwinięcie, programu Fiddler, Poster lub podobnego narzędzia do testowania HTTP.

#### <a name="non-http-triggered-functions"></a>Funkcje wyzwalane inne niż HTTP

W przypadku wszystkich rodzajów funkcji innych niż wyzwalacze HTTP i elementy webhook można testować funkcje lokalnie, wywołując punkt końcowy administracji. Wywołanie tego punktu końcowego przez żądanie HTTP POST na serwerze lokalnym wyzwala funkcję. Opcjonalnie można przekazać dane testowe do wykonania w treści żądania POST. Ta funkcja jest podobna do karty **test** w Azure Portal.

Aby wyzwolić funkcje inne niż HTTP, należy wywołać następujący punkt końcowy administratora:

    http://localhost:{port}/admin/functions/{function_name}

Aby przekazać dane testowe do punktu końcowego administratora funkcji, należy podać dane w treści komunikatu żądania POST. Treść wiadomości musi mieć następujący format JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` Wartość zawiera dane w formacie oczekiwanym przez funkcję. Poniższy przykład zazwinięcia jest wpisem do `QueueTriggerJS` funkcji. W tym przypadku dane wejściowe to ciąg, który jest odpowiednikiem komunikatu, który powinien zostać znaleziony w kolejce.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Za pomocą `func run` polecenia w wersji 1. x

>[!IMPORTANT]
> `func run` Polecenie nie jest obsługiwane w wersji 2. x narzędzi. Aby uzyskać więcej informacji, zobacz temat [jak dowiedzieć się, jak kierować Azure Functions wersji środowiska uruchomieniowego](set-runtime-version.md).

Można również wywołać funkcję bezpośrednio przy użyciu `func run <FunctionName>` i podać dane wejściowe dla funkcji. To polecenie jest podobne do uruchamiania funkcji za pomocą karty **test** w Azure Portal.

`func run`Program obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Zawartość wbudowana. |
| **`--debug -d`** | Dołącz debuger do procesu hosta przed uruchomieniem funkcji.|
| **`--timeout -t`** | Czas oczekiwania (w sekundach), aż do momentu, gdy host funkcji lokalnych jest gotowy.|
| **`--file -f`** | Nazwa pliku do użycia jako zawartość.|
| **`--no-interactive`** | Nie monituje o dane wejściowe. Przydatne w scenariuszach automatyzacji.|

Na przykład aby wywołać funkcję wyzwalaną przez protokół HTTP i przekazać treść zawartości, uruchom następujące polecenie:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publikowanie na platformie Azure

Azure Functions Core Tools obsługuje dwa typy wdrożeń: Wdrażanie plików projektów funkcji bezpośrednio w aplikacji funkcji za pomocą narzędzia [zip Deploy](functions-deployment-technologies.md#zip-deploy) i [wdrażanie niestandardowego kontenera Docker](functions-deployment-technologies.md#docker-container). Użytkownik musi już [utworzyć aplikację funkcji w ramach subskrypcji platformy Azure](functions-cli-samples.md#create), w której zostanie wdrożony swój kod. Projekty wymagające kompilacji powinny zostać skompilowane, aby można było wdrożyć pliki binarne.

### <a name="project-file-deployment"></a>Wdrożenie (pliki projektu)

Aby opublikować kod lokalny w aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```bash
func azure functionapp publish <FunctionAppName>
```

To polecenie publikuje w istniejącej aplikacji funkcji na platformie Azure. Wystąpi błąd podczas próby opublikowania w usłudze `<FunctionAppName>` , która nie istnieje w Twojej subskrypcji. Aby dowiedzieć się, jak utworzyć aplikację funkcji z poziomu wiersza polecenia lub okna terminalu przy użyciu interfejsu CLI platformy Azure, zobacz [tworzenie aplikacja funkcji do wykonywania](./scripts/functions-cli-create-serverless.md)bezserwerowego. Domyślnie to polecenie wdraża aplikację do [uruchamiania z pakietu wdrożeniowego](run-functions-from-deployment-package.md). Aby wyłączyć ten zalecany tryb wdrażania, użyj `--nozip` opcji.

>[!IMPORTANT]
> Podczas tworzenia aplikacji funkcji w Azure Portal jest domyślnie używane środowisko uruchomieniowe funkcji w wersji 2. x. Aby aplikacja funkcji korzystała z wersji 1. x środowiska uruchomieniowego, postępuj zgodnie z instrukcjami w temacie [Run on Version 1. x](functions-versions.md#creating-1x-apps).
> Nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która ma istniejące funkcje.

Poniższe opcje publikowania dotyczą obu wersji, 1. x i 2. x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publikuj ustawienia w pliku Local. Settings. JSON na platformie Azure, monitując o zastąpienie, jeśli to ustawienie już istnieje. Jeśli używasz emulatora magazynu, najpierw Zmień ustawienie aplikacji na [rzeczywiste połączenie magazynu](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Pomiń monit o zastąpienie ustawień aplikacji, gdy `--publish-local-settings -i` jest używany.|

Następujące opcje publikowania są obsługiwane tylko w wersji 2. x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Tylko Publikuj ustawienia i Pomiń zawartość. Wartość domyślna to Prompt. |
|**`--list-ignored-files`** | Wyświetla listę plików, które są ignorowane podczas publikowania, oparte na pliku. funcignore. |
| **`--list-included-files`** | Wyświetla listę opublikowanych plików, która jest oparta na pliku. funcignore. |
| **`--nozip`** | Wyłącza tryb domyślny `Run-From-Package` . |
| **`--build-native-deps`** | Pomija generowanie folderu. kół podczas publikowania aplikacji funkcji języka Python. |
| **`--additional-packages`** | Lista pakietów do zainstalowania podczas kompilowania natywnych zależności. Na przykład: `python3-dev libevent-dev`. |
| **`--force`** | Ignoruj weryfikację przed publikacją w określonych scenariuszach. |
| **`--csx`** | Opublikuj projekt C# skryptu (. CSX). |
| **`--no-build`** | Pomiń tworzenie funkcji dotnet. |
| **`--dotnet-cli-params`** | Podczas publikowania skompilowanych C# funkcji (. csproj) podstawowe narzędzia wywołują polecenie "dotnet Build--Output bin/Publish". Wszystkie parametry przesłane do tego zostaną dołączone do wiersza polecenia. |

### <a name="deployment-custom-container"></a>Wdrożenie (kontener niestandardowy)

Azure Functions umożliwia wdrożenie projektu funkcji w niestandardowym [kontenerze platformy Docker](functions-deployment-technologies.md#docker-container). Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji w systemie Linux przy użyciu obrazu niestandardowego](functions-create-function-linux-custom-image.md). Kontenery niestandardowe muszą mieć pliku dockerfile. Aby utworzyć aplikację za pomocą pliku dockerfile, użyj opcji--pliku dockerfile w systemie `func init`.

```bash
func deploy
```

Dostępne są następujące opcje wdrożenia kontenera niestandardowego:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Nazwa rejestru platformy Docker, do którego jest zalogowany bieżący użytkownik. |
| **`--platform`** | Platforma hostingu dla aplikacji funkcji. Prawidłowe opcje to`kubernetes` |
| **`--name`** | Nazwa aplikacji funkcji. |
| **`--max`**  | Opcjonalnie ustawia maksymalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--min`**  | Opcjonalnie ustawia minimalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--config`** | Ustawia opcjonalny plik konfiguracyjny wdrożenia. |

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest integracja z usługą Azure Application Insights. Dzienniki wykonywania można przesyłać strumieniowo na komputer lokalny. Aby dowiedzieć się więcej, zobacz [Monitor Azure Functions](functions-monitoring.md).

### <a name="enable-application-insights-integration"></a>Włącz integrację Application Insights

Po utworzeniu aplikacji funkcji w Azure Portal integracja Application Insights jest wykonywana domyślnie. Jednak podczas tworzenia aplikacji funkcji przy użyciu interfejsu wiersza polecenia platformy Azure integracja w aplikacji funkcji na platformie Azure nie jest wykonywana.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Włączanie dzienników przesyłania strumieniowego

Można wyświetlić strumień plików dziennika generowanych przez funkcje w sesji wiersza polecenia na komputerze lokalnym. 

#### <a name="native-streaming-logs"></a>Natywne dzienniki przesyłania strumieniowego

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Ten typ dzienników przesyłania strumieniowego wymaga [włączenia Application Insights integracji](#enable-application-insights-integration) dla aplikacji funkcji.   


## <a name="next-steps"></a>Następne kroki

Azure Functions Core Tools to środowisko [Open Source i jest hostowane w witrynie GitHub](https://github.com/azure/azure-functions-cli).  
Aby zgłosić błąd lub żądanie funkcji, [Otwórz problem z usługą GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[Pakiety rozszerzeń]: functions-bindings-register.md#extension-bundles
