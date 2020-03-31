---
title: Tworzenie funkcji platformy Azure przy użyciu kodu programu Visual Studio
description: Dowiedz się, jak tworzyć i testować usługi Azure Functions przy użyciu rozszerzenia usługi Azure Functions dla programu Visual Studio Code.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277169"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Tworzenie funkcji platformy Azure przy użyciu kodu programu Visual Studio

[Rozszerzenie usługi Azure Functions for Visual Studio Code] umożliwia lokalne tworzenie funkcji i wdrażanie ich na platformie Azure. Jeśli to środowisko jest twoje pierwsze z usługami Azure Functions, możesz dowiedzieć się więcej na [wprowadzenie do usługi Azure Functions.](functions-overview.md)

Rozszerzenie usługi Azure Functions zapewnia następujące korzyści:

* Edytuj, skompiluj i uruchom funkcje na lokalnym komputerze deweloperskim.
* Opublikuj swój projekt usługi Azure Functions bezpośrednio na platformie Azure.
* Zapisz swoje funkcje w różnych językach, korzystając z zalet programu Visual Studio Code.

Rozszerzenie może być używane z następującymi językami, które są obsługiwane przez środowisko uruchomieniowe usługi Azure Functions, począwszy od wersji 2.x:

* [C# skompilowany](functions-dotnet-class-library.md)
* [Skrypt języka C#](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Wymaga [ustawienia skryptu C# jako domyślnego języka projektu](#c-script-projects).

W tym artykule przykłady są obecnie dostępne tylko dla funkcji biblioteki klas JavaScript (Node.js) i C#.  

Ten artykuł zawiera szczegółowe informacje na temat używania rozszerzenia usługi Azure Functions do tworzenia funkcji i publikowania ich na platformie Azure. Przed przeczytaniem tego artykułu należy [utworzyć pierwszą funkcję przy użyciu programu Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Nie mieszaj lokalnego rozwoju i tworzenia portalu dla aplikacji z jedną funkcją. Podczas publikowania z projektu lokalnego do aplikacji funkcji, proces wdrażania zastępuje wszystkie funkcje, które zostały opracowane w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem i uruchomieniem [rozszerzenia usługi Azure Functions usługi Azure]dla programu Visual Studio[Code]należy spełnić następujące wymagania:

* [Visual Studio Code](https://code.visualstudio.com/) zainstalowany na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Inne zasoby, które są potrzebne, takie jak konto magazynu platformy Azure, są tworzone w ramach subskrypcji podczas [publikowania przy użyciu programu Visual Studio Code.](#publish-to-azure)

> [!IMPORTANT]
> Można tworzyć funkcje lokalnie i publikować je na platformie Azure bez konieczności uruchamiania i uruchamiania ich lokalnie. Aby uruchomić funkcje lokalnie, musisz spełnić pewne dodatkowe wymagania, w tym automatyczne pobieranie narzędzi Azure Functions Core Tools. Aby dowiedzieć się więcej, zobacz [Dodatkowe wymagania dotyczące lokalnego uruchamiania projektu.](#additional-requirements-for-running-a-project-locally)

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

Rozszerzenie Functions umożliwia utworzenie projektu aplikacji funkcji wraz z pierwszą funkcją. Poniższe kroki pokazują, jak utworzyć funkcję wyzwalaną przez PROTOKÓŁ HTTP w nowym projekcie funkcji. [Wyzwalacz HTTP](functions-bindings-http-webhook.md) jest najprostszym szablonem wyzwalacza funkcji do wykazania.

1. Z **platformy Azure: Functions**, wybierz ikonę **Utwórz funkcję:**

    ![Tworzenie funkcji](./media/functions-develop-vs-code/create-function.png)

1. Wybierz folder dla projektu aplikacji funkcji, a następnie **wybierz język dla projektu funkcji**.

1. Jeśli narzędzia podstawowe nie zostały jeszcze zainstalowane, zostaniesz poproszony o **wybranie wersji** narzędzi podstawowych do zainstalowania. Wybierz wersję 2.x lub nowszą. 

1. Wybierz szablon funkcji **wyzwalacza HTTP** lub możesz teraz wybrać **opcję Pomiń,** aby utworzyć projekt bez funkcji. Zawsze można [dodać funkcję do projektu](#add-a-function-to-your-project) później.

    ![Wybieranie szablonu wyzwalacza HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Wpisz **httpexample** dla nazwy funkcji i wybierz enter, a następnie wybierz **pozycję Autoryzacja funkcji.** Ten poziom autoryzacji wymaga podania [klucza funkcyjnego](functions-bindings-http-webhook-trigger.md#authorization-keys) podczas wywoływania punktu końcowego funkcji.

    ![Wybierz autoryzację funkcji](./media/functions-develop-vs-code/create-function-auth.png)

    Funkcja jest tworzona w wybranym języku i w szablonie funkcji wyzwalanej przez protokół HTTP.

    ![Szablon funkcji wyzwalany przez protokół HTTP w programie Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Wygenerowane pliki projektu

Szablon projektu tworzy projekt w wybranym języku i instaluje wymagane zależności. Dla każdego języka nowy projekt ma następujące pliki:

* **host.json**: Umożliwia skonfigurowanie hosta funkcji. Te ustawienia mają zastosowanie, gdy uruchamiasz funkcje lokalnie i gdy je uruchamiasz na platformie Azure. Aby uzyskać więcej informacji, zobacz [host.json reference](functions-host-json.md).

* **local.settings.json**: Zachowuje ustawienia używane podczas uruchamiania funkcji lokalnie. Te ustawienia są używane tylko wtedy, gdy używasz funkcji lokalnie. Aby uzyskać więcej informacji, zobacz [Plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ local.settings.json plik może zawierać wpisy tajne, należy wykluczyć go z kontroli źródła projektu.

W zależności od języka tworzone są te inne pliki:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs plik biblioteki klas,](functions-dotnet-class-library.md#functions-class-library-project) który implementuje funkcję.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* Plik package.json w folderze głównym.

* Folder HttpExample zawierający [plik definicji function.json](functions-reference-node.md#folder-structure) i [plik index.js](functions-reference-node.md#exporting-a-function), plik Node.js zawierający kod funkcji.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

W tym momencie można dodać powiązania wejściowe i wyjściowe do funkcji, [modyfikując plik function.json](#add-a-function-to-your-project) lub [dodając parametr do funkcji biblioteki klas C#.](#add-a-function-to-your-project)

Można również [dodać nową funkcję do projektu](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalowanie rozszerzeń powiązania

Z wyjątkiem wyzwalaczy HTTP i timer, powiązania są implementowane w pakietach rozszerzeń. Należy zainstalować pakiety rozszerzeń dla wyzwalaczy i powiązań, które ich potrzebują. Proces instalowania rozszerzeń powiązania zależy od języka projektu.

# <a name="c"></a>[C\#](#tab/csharp)

Uruchom polecenie [dotnet add package](/dotnet/core/tools/dotnet-add-package) w oknie terminala, aby zainstalować pakiety rozszerzeń, które są potrzebne w projekcie. Następujące polecenie instaluje rozszerzenie usługi Azure Storage, które implementuje powiązania dla obiektów Blob, Queue i magazynu tabel.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

Nową funkcję można dodać do istniejącego projektu przy użyciu jednego ze wstępnie zdefiniowanych szablonów wyzwalaczy funkcji. Aby dodać nowy wyzwalacz funkcji, wybierz klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **Azure Functions: Create Function**. Postępuj zgodnie z instrukcjami, aby wybrać typ wyzwalacza i zdefiniować wymagane atrybuty wyzwalacza. Jeśli wyzwalacz wymaga klucza dostępu lub parametry połączenia, aby połączyć się z usługą, przygotuj go przed utworzeniem wyzwalacza funkcji.

Wyniki tej akcji zależą od języka projektu:

# <a name="c"></a>[C\#](#tab/csharp)

Nowy plik biblioteki klas języka C# (cs) jest dodawany do projektu.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Nowy folder jest tworzony w projekcie. Folder zawiera nowy plik function.json i nowy plik kodu JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Dodawanie powiązań wejściowych i wyjściowych

Funkcję można rozwinąć, dodając powiązania wejściowe i wyjściowe. Proces dodawania powiązań zależy od języka projektu. Aby dowiedzieć się więcej o powiązaniach, zobacz [Koncepcje wyzwalaczy i powiązań usługi Azure Functions.](functions-triggers-bindings.md)

Poniższe przykłady łączą się `outqueue`z kolejką magazynu o nazwie , `MyStorageConnection` gdzie parametry połączenia dla konta magazynu są ustawiane w ustawieniu aplikacji w local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Zaktualizuj metodę funkcji, `Run` aby dodać następujący parametr do definicji metody:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ten kod wymaga dodania `using` następującej instrukcji:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Parametr `msg` jest `ICollector<T>` typem, który reprezentuje zbiór komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu funkcji. Dodaj jedną lub więcej wiadomości do kolekcji. Te komunikaty są wysyłane do kolejki po zakończeniu funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację [powiązania danych wyjściowych magazynu kolejki.](functions-bindings-storage-queue-output.md)

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Visual Studio Code umożliwia dodawanie powiązań do pliku function.json, wykonując wygodny zestaw monitów. Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl+click on macOS) plik **function.json** w folderze funkcji i wybierz pozycję **Dodaj powiązanie:**

![Dodawanie powiązania do istniejącej funkcji JavaScript ](media/functions-develop-vs-code/function-add-binding.png)

Poniżej przedstawiono przykładowe monity o zdefiniowanie nowego powiązania danych wyjściowych magazynu:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek wiązania** | `out` | Powiązanie jest powiązanie danych wyjściowych. |
| **Wybierz powiązanie z kierunkiem** | `Azure Queue Storage` | Powiązanie jest powiązaniem kolejki usługi Azure Storage. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `msg` | Nazwa identyfikująca parametr wiązania, do którego odwołuje się kod. |
| **Kolejka, do której zostanie wysłana wiadomość** | `outqueue` | Nazwa kolejki, do których pisze powiązanie. Gdy *queueName* nie istnieje, powiązanie tworzy go przy pierwszym użyciu. |
| **Wybierz ustawienie z "local.settings.json"** | `MyStorageConnection` | Nazwa ustawienia aplikacji zawierającego parametry połączenia dla konta magazynu. Ustawienie `AzureWebJobsStorage` zawiera parametry połączenia dla konta magazynu utworzonego za pomocą aplikacji funkcji. |

W tym przykładzie następujące powiązanie `bindings` jest dodawane do tablicy w pliku function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Można również dodać tę samą definicję powiązania bezpośrednio do function.json.

W kodzie funkcji `msg` powiązanie jest `context`dostępne z , jak w tym przykładzie:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Aby dowiedzieć się więcej, zobacz odwołanie do [powiązania danych wyjściowych magazynu kolejki.](functions-bindings-storage-queue-output.md)

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Visual Studio Code umożliwia publikowanie projektu functions bezpośrednio na platformie Azure. W ramach tego procesu tworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure. Aplikacja funkcji zapewnia kontekst wykonywania dla Twoich funkcji. Projekt jest pakowany i wdrażany do nowej aplikacji funkcji w ramach subskrypcji platformy Azure.

Podczas publikowania z programu Visual Studio Code do nowej aplikacji funkcji na platformie Azure, są oferowane zarówno szybkie działanie aplikacji tworzenia ścieżki i ścieżki zaawansowanej. 

Podczas publikowania z programu Visual Studio Code, można skorzystać z technologii [wdrażania zip.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Tworzenie aplikacji szybkiej funkcji

Po wybraniu **+ Tworzenie nowej aplikacji funkcji na platformie Azure...** rozszerzenie automatycznie generuje wartości zasobów platformy Azure wymaganych przez aplikację funkcji. Te wartości są oparte na nazwie aplikacji funkcji, którą wybierzesz. Na przykład przy użyciu ustawień domyślnych do publikowania projektu do nowej aplikacji funkcji na platformie Azure, zobacz [Visual Studio Code Szybki start artykułu](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Jeśli chcesz podać jawne nazwy utworzonych zasobów, musisz wybrać ścieżkę tworzenia zaawansowanego.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publikowanie projektu w nowej aplikacji funkcji na platformie Azure przy użyciu opcji zaawansowanych

Następujące kroki publikowania projektu w nowej aplikacji funkcji utworzonej za pomocą zaawansowanych opcji tworzenia:

1. W obszarze **Azure: Functions** wybierz ikonę **Wdrażanie w aplikacji funkcji.**

    ![Ustawienia aplikacji funkcji](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Jeśli nie jesteś zalogowany, zostanie wyświetlony monit o **zalogowanie się na platformie Azure**. Można również **utworzyć bezpłatne konto platformy Azure**. Po zalogowaniu się z przeglądarki wróć do programu Visual Studio Code.

1. Jeśli masz wiele subskrypcji, **wybierz subskrypcję** dla aplikacji funkcji, a następnie wybierz **+ Utwórz nową aplikację funkcji na platformie Azure... _Zaawansowane_**. Ta opcja _zaawansowana_ zapewnia większą kontrolę nad zasobami utworzonymi na platformie Azure. 

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz aplikację funkcji na platformie Azure | Tworzenie nowej aplikacji funkcji na platformie Azure | W następnym wierszu polecenia wpisz globalnie unikatową nazwę identyfikującą nową aplikację funkcji, a następnie wybierz pozycję Enter. Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`. |
    | Wybieranie systemu operacyjnego | Windows | Aplikacja funkcji działa w systemie Windows. |
    | Wybieranie planu hostingowego | Plan Zużycie | Używany jest [hosting planu zużycia](functions-scale.md#consumption-plan) bez użycia serwera. |
    | Wybieranie środowiska wykonawczego dla nowej aplikacji | Twój język projektu | Środowisko wykonawcze musi być zgodne z projektem, który publikujesz. |
    | Wybieranie grupy zasobów dla nowych zasobów | Tworzenie nowej grupy zasobów | W następnym wierszu polecenia wpisz nazwę `myResourceGroup`grupy zasobów, na przykład , a następnie wybierz pozycję Enter. Można również wybrać istniejącą grupę zasobów. |
    | Wybieranie konta magazynu | Tworzenie nowego konta magazynu | W następnym wierszu polecenia wpisz globalnie unikatową nazwę dla nowego konta magazynu używanego przez aplikację funkcji, a następnie wybierz pozycję Enter. Nazwy kont magazynu muszą mieć od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Można również wybrać istniejące konto. |
    | Wybieranie lokalizacji dla nowych zasobów | region | Wybierz lokalizację w [regionie](https://azure.microsoft.com/regions/) w pobliżu lub w pobliżu innych usług, do których mają dostęp funkcje. |

    Powiadomienie pojawia się po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrażania. Wybierz **opcję Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure.

## <a name="republish-project-files"></a>Ponowne publikowanie plików projektu

Po skonfigurowaniu [ciągłego wdrażania](functions-continuous-deployment.md)aplikacja funkcji na platformie Azure jest aktualizowana za każdym razem, gdy pliki źródłowe są aktualizowane w lokalizacji źródła połączonego. Zaleca się ciągłe wdrażanie, ale można również ponownie opublikować aktualizacje pliku projektu z programu Visual Studio Code.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.

1. W programie Visual Studio Code wybierz pozycję F1, aby otworzyć paletę poleceń. W palecie poleceń wyszukaj i wybierz **pozycję Azure Functions: Deploy to function app**.

1. Jeśli nie jesteś zalogowany, zostanie wyświetlony monit o **zalogowanie się na platformie Azure**. Po zalogowaniu się w przeglądarce wróć do programu Visual Studio Code. Jeśli masz wiele subskrypcji, **wybierz subskrypcję,** która zawiera aplikację funkcji.

1. Wybierz istniejącą aplikację funkcji na platformie Azure. Gdy zostaniesz ostrzeżony o zastępowanie wszystkich plików w aplikacji funkcji, wybierz pozycję **Wdrażanie,** aby potwierdzić ostrzeżenie i kontynuować.

Projekt jest przebudowywany, przepakowany i przekazany na platformę Azure. Istniejący projekt zostanie zastąpiony przez nowy pakiet, a aplikacja funkcji zostanie ponownie uruchomiona.

## <a name="get-the-url-of-the-deployed-function"></a>Pobierz adres URL wdrożonej funkcji

Aby wywołać funkcję wyzwalaną przez protokół HTTP, potrzebujesz adresu URL tej funkcji, gdy jest wdrażana w aplikacji funkcji. Ten adres URL zawiera wszystkie wymagane [klawisze funkcyjne](functions-bindings-http-webhook-trigger.md#authorization-keys). Za pomocą rozszerzenia można uzyskać te adresy URL dla wdrożonych funkcji.

1. Wybierz klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **Azure Functions: Copy Function URL**.

1. Postępuj zgodnie z instrukcjami, aby wybrać aplikację funkcji na platformie Azure, a następnie określony wyzwalacz HTTP, który chcesz wywołać.

Adres URL funkcji jest kopiowany do schowka wraz `code` z wszelkimi wymaganymi kluczami przekazanymi przez parametr zapytania. Użyj narzędzia HTTP do przesyłania żądań POST lub przeglądarki dla żądań GET do funkcji zdalnej.  

## <a name="run-functions-locally"></a>Uruchamianie funkcji lokalnie

Rozszerzenie Usługi Azure Functions umożliwia uruchamianie projektu functions na lokalnym komputerze deweloperskim. Lokalny środowisko wykonawcze jest tym samym środowisko uruchomieniowe, które obsługuje aplikację funkcji na platformie Azure. Ustawienia lokalne są odczytywane z [pliku local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Dodatkowe wymagania dotyczące lokalnego uruchamiania projektu

Aby uruchomić projekt funkcji lokalnie, należy spełnić te dodatkowe wymagania:

* Zainstaluj wersję 2.x lub nowszą [narzędzia Podstawowe usługi Azure Functions](functions-run-local.md#v2). Pakiet Narzędzia podstawowe jest pobierany i instalowany automatycznie po uruchomieniu projektu lokalnie. Narzędzia podstawowe obejmują całe środowisko uruchomieniowe usługi Azure Functions, więc pobieranie i instalacja może zająć trochę czasu.

* Zainstaluj określone wymagania dla wybranego języka:

    | Język | Wymaganie |
    | -------- | --------- |
    | **C #** | [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[Narzędzia interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debuger dla rozszerzenia Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 lub nowsze](https://maven.apache.org/) |
    | **Javascript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Rozszerzenie Pythona](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) zalecane|

    <sup>*</sup>Aktywne wersje LTS i konserwacji LTS (zalecane 8.11.1 i 10.14.1).

### <a name="configure-the-project-to-run-locally"></a>Konfigurowanie projektu do lokalnego

Środowisko wykonawcze Functions używa konta usługi Azure Storage wewnętrznie dla wszystkich typów wyzwalaczy innych niż HTTP i webhooks. Dlatego należy ustawić klucz **Values.AzureWebJobsStorage** na prawidłowy ciąg połączenia konta usługi Azure Storage.

W tej sekcji użyto [rozszerzenia usługi Azure Storage dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) z [Eksploratorem usługi Azure Storage,](https://storageexplorer.com/) aby połączyć się z ciągiem połączenia magazynu i pobrać go.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio otwórz **Cloud Explorer**, rozwiń węzeł **Konto** > magazynu**Konto magazynu**, a następnie wybierz **pozycję Właściwości** i skopiuj wartość **podstawowego ciągu połączenia.**

2. W projekcie otwórz plik local.settings.json i ustaw wartość klucza **AzureWebJobsStorage** na skopiowany ciąg połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do **tablicy Wartości** dla innych połączeń wymaganych przez funkcje.

Aby uzyskać więcej informacji, zobacz [Plik ustawień lokalnych](#local-settings-file).

### <a name="debugging-functions-locally"></a>Funkcje debugowania lokalnie  

Aby debugować funkcje, wybierz F5. Jeśli nie pobrano jeszcze [podstawowych narzędzi][azure functions core tools,]zostanie wyświetlony monit o zrobienie tego. Gdy narzędzia core jest zainstalowany i uruchomiony, dane wyjściowe są wyświetlane w terminalu. Jest to takie samo `func host start` jak uruchamianie polecenia Narzędzia podstawowe z terminala, ale z dodatkowymi zadaniami kompilacji i dołączonym debugerem.  

Po uruchomieniu projektu można wyzwolić funkcje, tak jak podczas wdrażania projektu na platformie Azure. Gdy projekt jest uruchomiony w trybie debugowania, punkty przerwania są trafione w programie Visual Studio Code, zgodnie z oczekiwaniami.

Adres URL żądania dla wyzwalaczy HTTP jest wyświetlany w danych wyjściowych w terminalu. Klucze funkcyjne dla wyzwalaczy HTTP nie są używane, gdy projekt jest uruchomiony lokalnie. Aby uzyskać więcej informacji, zobacz [Strategie testowania kodu w usłudze Azure Functions.](functions-test-a-function.md)  

Aby dowiedzieć się więcej, zobacz [Praca z podstawowymi narzędziami azure functions]azure tools[.]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt jest publikowany na platformie Azure. Po zakończeniu publikowania masz możliwość publikowania ustawień z local.settings.json do aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej, zobacz [Publikowanie ustawień aplikacji](#publish-application-settings).

Wartości w **ConnectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można również odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz sekcje Zmienne środowiskowe tych artykułów referencyjnych specyficznych dla języka:

* [Wstępnie skompilowany c#](functions-dotnet-class-library.md#environment-variables)
* [Skrypt języka C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Ustawienia aplikacji na platformie Azure

Ustawienia w pliku local.settings.json w projekcie powinny być takie same jak ustawienia aplikacji w aplikacji funkcji na platformie Azure. Wszystkie ustawienia dodane do local.settings.json również muszą zostać dodane do aplikacji funkcji na platformie Azure. Te ustawienia nie są przekazywane automatycznie podczas publikowania projektu. Podobnie wszystkie ustawienia utworzone w aplikacji funkcji [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) muszą zostać pobrane do projektu lokalnego.

### <a name="publish-application-settings"></a>Publikowanie ustawień aplikacji

Najprostszym sposobem opublikowania wymaganych ustawień w aplikacji funkcji na platformie Azure jest użycie łącza **Przekaż ustawienia,** które pojawia się po opublikowaniu projektu:

![Prześlij ustawienia aplikacji](./media/functions-develop-vs-code/upload-app-settings.png)

Można również opublikować ustawienia za pomocą **polecenia Usługi Azure Functions: Przekaż ustawienie lokalne** na palecie poleceń. Można dodać poszczególne ustawienia do ustawień aplikacji na platformie Azure za pomocą **polecenia Usługi Azure: Dodaj nowe ustawienie.**

> [!TIP]
> Przed opublikowaniem pliku local.settings.json należy zapisać plik local.settings.json.

Jeśli plik lokalny jest zaszyfrowany, jest ponownie odszyfrowywany, publikowany i szyfrowany. Jeśli istnieją ustawienia, które mają sprzeczne wartości w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie sposobu postępowania.

Wyświetlanie istniejących ustawień aplikacji w obszarze **Azure: Functions,** rozszerzając subskrypcję, aplikację funkcji i **ustawienia aplikacji**.

![Wyświetlanie ustawień aplikacji funkcji w programie Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Pobieranie ustawień z platformy Azure

Jeśli utworzono ustawienia aplikacji na platformie Azure, można je pobrać do pliku local.settings.json za pomocą polecenia **Usługi Azure Functions: Download Remote Settings.**

Podobnie jak w przypadku przekazywania, jeśli plik lokalny jest zaszyfrowany, jest ponownie odszyfrowywane, aktualizowane i szyfrowane. Jeśli istnieją ustawienia, które mają sprzeczne wartości w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie sposobu postępowania.

## <a name="monitoring-functions"></a>Funkcje monitorowania

Po [uruchomieniu funkcji lokalnie](#run-functions-locally)dane dziennika są przesyłane strumieniowo do konsoli terminala. Można również uzyskać dane dziennika, gdy projekt functions jest uruchomiony w aplikacji funkcji na platformie Azure. Możesz połączyć się z dziennikami przesyłania strumieniowego na platformie Azure, aby wyświetlić dane dziennika w czasie zbliżonym do rzeczywistego, lub włączyć usługę Application Insights, aby lepiej zrozumieć, jak działa aplikacja funkcji.

### <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji, często warto zobaczyć rejestrowanie informacji w czasie zbliżonym do rzeczywistego. Można wyświetlić strumień plików dziennika generowanych przez funkcje. To dane wyjściowe są przykładem dzienników przesyłania strumieniowego dla żądania do funkcji wyzwalanej przez HTTP:

![Dane wyjściowe dzienników przesyłania strumieniowego dla wyzwalacza HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Aby dowiedzieć się więcej, zobacz [Dzienniki przesyłania strumieniowego](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Dzienniki przesyłania strumieniowego obsługują tylko jedno wystąpienie hosta functions. Gdy funkcja jest skalowana do wielu wystąpień, dane z innych wystąpień nie są wyświetlane w strumieniu dziennika. [Strumień metryk na żywo](../azure-monitor/app/live-stream.md) w usłudze Application Insights obsługuje wiele wystąpień. Podczas gdy również w czasie zbliżonym do rzeczywistego, analiza strumieniowa opiera się na [próbkowane dane.](functions-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

Zaleca się monitorowanie wykonywania funkcji przez integrację aplikacji funkcji z usługi Application Insights. Podczas tworzenia aplikacji funkcji w witrynie Azure portal, ta integracja występuje domyślnie. Podczas tworzenia aplikacji funkcji podczas publikowania programu Visual Studio należy samodzielnie zintegrować aplikację Application Insights.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>Projekty\# skryptów C

Domyślnie wszystkie projekty języka C# są tworzone jako [projekty biblioteki skompilowanych klas w języku C#.](functions-dotnet-class-library.md) Jeśli wolisz pracować z projektami skryptów języka C#, musisz wybrać skrypt C# jako domyślny język w ustawieniach rozszerzenia usługi Azure Functions:

1. Wybierz **pozycję** > **Ustawienia****preferencji** > plików .

1. Przejdź do sekcji**Rozszerzenia** >  **ustawień** > użytkownika**Usługi Azure Functions**.

1. Wybierz **C#Script** z **funkcji platformy Azure: Język projektu**.

Po wykonaniu tych kroków wywołania do podstawowych narzędzi podstawowych zawierają `--csx` opcję, która generuje i publikuje pliki projektu skryptu C#(csx). Po określeniu tego domyślnego języka, wszystkie projekty, które tworzysz domyślnie do projektów skryptów języka C#. Nie zostanie wyświetlony monit o wybranie języka projektu, gdy ustawiono wartość domyślną. Aby utworzyć projekty w innych językach, należy zmienić to ustawienie lub usunąć je z pliku user settings.json. Po usunięciu tego ustawienia podczas tworzenia projektu zostanie wyświetlony monit o wybranie języka.

## <a name="command-palette-reference"></a>Odwołanie do palety poleceń

Rozszerzenie usługi Azure Functions zapewnia przydatny interfejs graficzny w obszarze do interakcji z aplikacjami funkcji na platformie Azure. Ta sama funkcja jest również dostępna jako polecenia w palecie poleceń (F1). Dostępne są następujące polecenia usługi Azure Functions:

|Usługa Azure Functions, polecenie  | Opis  |
|---------|---------|
|**Dodawanie nowych ustawień**  |  Tworzy nowe ustawienie aplikacji na platformie Azure. Aby dowiedzieć się więcej, zobacz [Publikowanie ustawień aplikacji](#publish-application-settings). Może być również konieczne [pobranie tego ustawienia do ustawień lokalnych](#download-settings-from-azure). |
| **Konfigurowanie źródła wdrażania** | Łączy aplikację funkcji na platformie Azure z lokalnym repozytorium Git. Aby dowiedzieć się więcej, zobacz [Ciągłe wdrażanie usług Azure Functions](functions-continuous-deployment.md). |
| **Łączenie się z repozytorium GitHub** | Łączy aplikację funkcji z repozytorium GitHub. |
| **Kopiuj adres URL funkcji** | Pobiera zdalny adres URL funkcji wyzwalanej przez http, która jest uruchomiona na platformie Azure. Aby dowiedzieć się więcej, zobacz [Uzyskaj adres URL wdrożonej funkcji](#get-the-url-of-the-deployed-function). |
| **Tworzenie aplikacji funkcji na platformie Azure** | Tworzy nową aplikację funkcji w subskrypcji na platformie Azure. Aby dowiedzieć się więcej, zobacz sekcję dotyczącą publikowania w nowej aplikacji funkcji na [platformie Azure](#publish-to-azure).        |
| **Odszyfruj ustawienia** | Odszyfrowuje [ustawienia lokalne,](#local-settings-file) które zostały zaszyfrowane przez **usługę Azure Functions: Encrypt Settings**.  |
| **Usuń aplikację funkcji** | Usuwa aplikację funkcji z subskrypcji na platformie Azure. Jeśli w planie usługi App Service nie ma żadnych innych aplikacji, możesz ją również usunąć. Inne zasoby, takie jak konta magazynu i grupy zasobów, nie są usuwane. Aby usunąć wszystkie zasoby, należy [usunąć grupę zasobów](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Nie ma to wpływu na projekt lokalny. |
|**Funkcja usuwania**  | Usuwa istniejącą funkcję z aplikacji funkcji na platformie Azure. Ponieważ to usunięcie nie ma wpływu na projekt lokalny, zamiast tego należy rozważyć usunięcie funkcji lokalnie, a następnie [ponowne opublikowanie projektu.](#republish-project-files) |
| **Usuń serwer proxy** | Usuwa serwer proxy usługi Azure Functions z aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej o serwerach proxy, zobacz [Praca z serwerami proxy usługi Azure Functions](functions-proxies.md). |
| **Ustawienie usuwania** | Usuwa ustawienie aplikacji funkcji na platformie Azure. To usunięcie nie ma wpływu na ustawienia w pliku local.settings.json. |
| **Odłączanie się od repo**  | Usuwa [połączenie ciągłego wdrażania](functions-continuous-deployment.md) między aplikacją funkcji na platformie Azure a repozytorium kontroli źródła. |
| **Pobieranie ustawień zdalnych** | Pobiera ustawienia z wybranej aplikacji funkcji na platformie Azure do pliku local.settings.json. Jeśli plik lokalny jest zaszyfrowany, jest ponownie odszyfrowywane, aktualizowane i szyfrowane. Jeśli istnieją ustawienia, które mają sprzeczne wartości w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie sposobu postępowania. Przed uruchomieniem tego polecenia należy zapisać zmiany w pliku local.settings.json. |
| **Edytuj ustawienia** | Zmienia wartość istniejącego ustawienia aplikacji funkcji na platformie Azure. To polecenie nie ma wpływu na ustawienia pliku local.settings.json.  |
| **Szyfruj ustawienia** | Szyfruje poszczególne `Values` elementy w tablicy w [ustawieniach lokalnych](#local-settings-file). W tym `IsEncrypted` pliku jest `true`również ustawiona na , który określa, że lokalne środowisko wykonawcze odszyfruje ustawienia przed ich użyciem. Szyfruj ustawienia lokalne, aby zmniejszyć ryzyko wycieku cennych informacji. Na platformie Azure ustawienia aplikacji są zawsze przechowywane zaszyfrowane. |
| **Wykonaj funkcję teraz** | Ręcznie uruchamia [funkcję wyzwalaną przez czasomierza](functions-bindings-timer.md) na platformie Azure. To polecenie jest używane do testowania. Aby dowiedzieć się więcej na temat wyzwalania funkcji innych niż HTTP na platformie Azure, zobacz [Ręczne uruchamianie funkcji wyzwalanej przez protokół HTTP](functions-manually-run-non-http.md). |
| **Inicjowanie projektu do użycia za pomocą kodu VS** | Dodaje wymagane pliki projektu programu Visual Studio Code do istniejącego projektu funkcji. To polecenie służy do pracy z projektem utworzonym przy użyciu narzędzi podstawowych. |
| **Instalowanie lub aktualizowanie podstawowych narzędzi usług Azure Functions** | Instaluje lub aktualizuje [podstawowe narzędzia azure functions], który jest używany do uruchamiania funkcji lokalnie. |
| **Ponowne wdrożenie**  | Umożliwia ponowne wdrażanie plików projektu z podłączonego repozytorium Git do określonego wdrożenia na platformie Azure. Aby ponownie opublikować aktualizacje lokalne z programu Visual Studio Code, [ponownie opublikuj projekt](#republish-project-files). |
| **Zmień nazwę ustawień** | Zmienia nazwę klucza istniejącego ustawienia aplikacji funkcji na platformie Azure. To polecenie nie ma wpływu na ustawienia pliku local.settings.json. Po zmianie nazwy ustawień na platformie Azure należy [pobrać te zmiany do projektu lokalnego](#download-settings-from-azure). |
| **Ponownie uruchomić** | Uruchamia ponownie aplikację funkcji na platformie Azure. Wdrażanie aktualizacji również uruchamia ponownie aplikację funkcji. |
| **Ustawianie usługi AzureWebJobsStorage**| Ustawia wartość ustawienia `AzureWebJobsStorage` aplikacji. To ustawienie jest wymagane przez usługę Azure Functions. Jest ustawiona, gdy aplikacja funkcji jest tworzona na platformie Azure. |
| **Początek** | Uruchamia aplikację funkcji zatrzymanej na platformie Azure. |
| **Uruchamianie dzienników przesyłania strumieniowego** | Uruchamia dzienniki przesyłania strumieniowego dla aplikacji funkcji na platformie Azure. Użyj dzienników przesyłania strumieniowego podczas zdalnego rozwiązywania problemów na platformie Azure, jeśli chcesz zobaczyć informacje o rejestrowaniu w czasie zbliżonym do rzeczywistego. Aby dowiedzieć się więcej, zobacz [Dzienniki przesyłania strumieniowego](#streaming-logs). |
| **Zatrzymaj** | Zatrzymuje aplikację funkcji, która jest uruchomiona na platformie Azure. |
| **Zatrzymaj dzienniki przesyłania strumieniowego** | Zatrzymuje dzienniki przesyłania strumieniowego dla aplikacji funkcji na platformie Azure. |
| **Przełączanie jako ustawienie gniazda** | Po włączeniu zapewnia, że ustawienie aplikacji będzie się powtarzać dla danego gniazda wdrożenia. |
| **Odinstalowywanie podstawowych narzędzi funkcji platformy Azure** | Usuwa narzędzia podstawowe funkcji platformy Azure, które są wymagane przez rozszerzenie. |
| **Przekazywanie ustawień lokalnych** | Przekazuje ustawienia z pliku local.settings.json do wybranej aplikacji funkcji na platformie Azure. Jeśli plik lokalny jest zaszyfrowany, jest ponownie odszyfrowywane, przekazywane i szyfrowane. Jeśli istnieją ustawienia, które mają sprzeczne wartości w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie sposobu postępowania. Przed uruchomieniem tego polecenia należy zapisać zmiany w pliku local.settings.json. |
| **Wyświetlanie zatwierdzania w usłudze GitHub** | Pokazuje najnowsze zatwierdzenie w określonym wdrożeniu, gdy aplikacja funkcji jest połączona z repozytorium. |
| **Wyświetlanie dzienników wdrażania** | Pokazuje dzienniki dla określonego wdrożenia do aplikacji funkcji na platformie Azure. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o podstawowych narzędziach usługi Azure Functions, zobacz [Praca z podstawowymi narzędziami usługi Azure Functions](functions-run-local.md).

Aby dowiedzieć się więcej na temat tworzenia funkcji jako bibliotek klas .NET, zobacz [odwołanie dewelopera usługi Azure Functions C#.](functions-dotnet-class-library.md) W tym artykule zawiera również łącza do przykładów, jak używać atrybutów do deklarowania różnych typów powiązań obsługiwanych przez usługi Azure Functions.

[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Podstawowe narzędzia funkcji platformy Azure]: functions-run-local.md
