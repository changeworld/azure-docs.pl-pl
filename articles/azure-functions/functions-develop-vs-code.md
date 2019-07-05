---
title: Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio Code | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i testować usługi Azure Functions przy użyciu rozszerzenia usługi Azure Functions dla programu Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452698"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio Code

[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code] można lokalnie tworzyć i wdrażać funkcje na platformie Azure. To środowisko w przypadku pierwszej za pomocą usługi Azure Functions, można dowiedzieć się więcej o [wprowadzenie do usługi Azure Functions](functions-overview.md).

Rozszerzenie usługi Azure Functions zapewnia następujące korzyści: 

* Edytowanie, tworzenie i uruchamianie functions na lokalnym komputerze deweloperskim. 
* Publikowanie projektu usługi Azure Functions bezpośrednio na platformie Azure. 
* Pisanie funkcji w różnych językach, mając wszystkie korzyści z programu Visual Studio Code. 

Rozszerzenia mogą być używane z następujące języki obsługiwane przez środowisko uruchomieniowe usługi Azure Functions w wersji 2.x: 

* [C#skompilowany](functions-dotnet-class-library.md) 
* [Skrypt języka C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Program PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Wymaga, [ustaw C# skryptu jako język domyślny projekt](#c-script-projects).

Ten artykuł zawiera przykłady są obecnie dostępne tylko dla języka JavaScript (Node.js) i C# klasy funkcji biblioteki.  

Ten artykuł zawiera szczegółowe informacje na temat tworzenia funkcji i opublikuj je na platformie Azure za pomocą rozszerzenia usługi Azure Functions. Przed zapoznaniem się w tym artykule, wykonaj następujące czynności [tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Nie można mieszać lokalnego opracowywania aplikacji za pomocą portalu w tej samej aplikacji funkcji. Podczas publikowania z poziomu lokalnego projektu aplikacji funkcji w procesie wdrażania zastępuje wszystkie funkcje, które opracowane w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować i uruchomić [rozszerzenia usługi Azure Functions][rozszerzenie usługi azure functions dla programu visual studio code], musi spełniać następujące wymagania:

* [Visual Studio Code](https://code.visualstudio.com/) zainstalowanej na jednym z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Inne zasoby, których potrzebujesz, takie jak konto usługi Azure Storage są tworzone w Twojej subskrypcji po użytkownik [opublikować za pomocą programu Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Możesz opracowywać funkcji lokalnie i publikowanie na platformie Azure bez konieczności start i uruchamiasz je lokalnie. Istnieją dodatkowe wymagania dotyczące uruchamiania usługi funkcjonalności lokalnie, w tym automatyczne pobieranie podstawowych narzędzi usługi Azure Functions. Aby dowiedzieć się więcej, zobacz [dodatkowe wymagania do uruchomienia lokalnie](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

Rozszerzenie funkcji umożliwia utworzenie projektu aplikacji funkcji, wraz z pierwszą funkcję. Poniższe kroki pokazują jak utworzyć funkcję wyzwalaną przez protokół HTTP w nowego projektu usługi functions. [Wyzwalacz HTTP](functions-bindings-http-webhook.md) jest najprostszym szablonu wyzwalacza funkcji, aby zademonstrować.

1. Z obszaru **Azure: Functions** wybierz ikonę tworzenia funkcji.

    ![Tworzenie funkcji](./media/functions-develop-vs-code/create-function.png)

1. Wybierz folder dla projektu aplikacji funkcji, a następnie **wybierz język dla projektu funkcji**. 

1. Wybierz **wyzwalacza HTTP** szablonu funkcji, lub możesz wybrać **na razie Pomiń** Tworzenie projektu bez funkcji. Zawsze możesz [Dodawanie funkcji do projektu](#add-a-function-to-your-project) w późniejszym czasie. 

    ![Wybieranie szablonu wyzwalacza HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Typ `HTTPTrigger` dla nazwy funkcji i naciśnij klawisz Enter, następnie wybierz pozycję **funkcja** autoryzacji. Ten poziom autoryzacji wymaga podania [funkcyjne](functions-bindings-http-webhook.md#authorization-keys) podczas wywoływania punktu końcowego funkcji.

    ![Wybierz uwierzytelnianie — funkcja](./media/functions-develop-vs-code/create-function-auth.png)

    Funkcja zostanie utworzona w wybranym języku i przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP.

    ![Szablon funkcji wyzwalanej przez protokół HTTP w programie Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

Szablon projektu tworzy projekt w wybranym języku, instaluje wymagane zależności. W dowolnym języku nowy projekt ma następujące pliki:

* **host.json**: Umożliwia konfigurowanie hostów funkcji. Te ustawienia mają zastosowanie zarówno, gdy uruchomiona lokalnie i na platformie Azure. Aby uzyskać więcej informacji, zobacz [dokumentacja pliku host.JSON](functions-host-json.md).

* **local.settings.json**: Przechowuje ustawienia używane podczas uruchamiania funkcji lokalnie. Te ustawienia są używane tylko w sytuacji, gdy uruchamiane lokalnie. Aby uzyskać więcej informacji, zobacz [pliku ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Pliku local.settings.json może zawierać klucze tajne, musi on spod kontroli źródła z projektu.

W tym momencie, Dodaj dane wejściowe i wyjściowe powiązania do funkcji przez [modyfikując plik function.json](#javascript-2), lub [dodanie parametru do C# klasy funkcji biblioteki](#c-class-library-2).

Możesz również [Dodawanie nowych funkcji do projektu](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalowanie rozszerzeń powiązania

Z wyjątkiem wyzwalaczy HTTP oraz czasomierzem powiązania są implementowane w pakiety rozszerzeń. Należy zainstalować pakiety rozszerzeń dla wyzwalaczy i powiązań, które tego wymagają. Sposób instalowania rozszerzeń powiązania zależy od języka projektu.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# biblioteki klas

Uruchom [dotnet Dodaj pakiet](/dotnet/core/tools/dotnet-add-package) polecenia w oknie terminalu, aby zainstalować pakiety rozszerzeń, należy w projekcie. Poniższy przykład instaluje rozszerzenie usługi Azure Storage, która implementuje powiązania dla magazynu obiektów Blob, kolejek i tabel.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

Przy użyciu jednej z wstępnie zdefiniowanych szablonów wyzwalacza funkcji, można dodać nowej funkcji do istniejącego projektu. Aby dodać nowy wyzwalacz funkcji, naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **usługi Azure Functions: Utwórz funkcję...** . Postępuj zgodnie z monitami, aby wybierz typ wyzwalacza i zdefiniuj wymaganych atrybutów wyzwalacza. Jeśli wyzwalacz wymaga dostępu do klucza lub parametrów połączenia do łączenia się z usługą, przygotuj go przed utworzeniem wyzwalacza funkcji. 

Wynikiem tej operacji są zależne od języka projektu:

### <a name="javascript"></a>JavaScript

W projekcie, który zawiera nowy plik function.json i nowy plik kodu JavaScript, tworzony jest nowy folder.

### <a name="c-class-library"></a>C\# biblioteki klas

Nowy C# klasy biblioteki (CS) plik zostanie dodany do projektu.

## <a name="add-input-and-output-bindings"></a>Dodaj dane wejściowe i wyjściowe powiązania

Funkcję można rozszerzyć, dodając powiązania danych wejściowych i wyjściowych. Sposób to zrobić zależy od języka projektu. Aby dowiedzieć się więcej na temat powiązań, zobacz [pojęcia powiązania i Wyzwalacze usługi Azure Functions](functions-triggers-bindings.md). 

Poniższe przykłady nawiązać połączenie z kolejką magazynu o nazwie `outqueue`, w którym ciąg połączenia dla konta magazynu jest ustawiana w `MyStorageConnection` ustawienia aplikacji w local.settings.json. 

### <a name="javascript"></a>JavaScript

Visual Studio Code umożliwia dodawanie powiązań do pliku function.json wykonując wygodne zestawu monitów. Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl + kliknięcie w systemie macOS) `function.json` plików w folderze funkcji, a następnie wybierz **Dodaj powiązanie...** . 

![Dodawanie powiązań do istniejącej funkcji języka JavaScript ](media/functions-develop-vs-code/function-add-binding.png)

Poniżej przedstawiono przykład monitami, aby zdefiniować nowe powiązanie danych wyjściowych magazynu:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie to powiązanie danych wyjściowych. |
| **Wybierz powiązanie kierunku...** | `Azure Queue Storage` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **Nazwa służąca do identyfikacji tego powiązania w kodzie** | `msg` | Nazwa identyfikująca parametr wiązania, do którego odwołuje się kod. |
| **Kolejka, do którego będą wysyłane wiadomości** | `outqueue` | Nazwa kolejki, która zapisuje powiązania. Gdy *queueName* nie istnieje, tworzy go przy pierwszym użyciu powiązania. |
| **Wybierz ustawienie z "local.setting.json"** | `MyStorageConnection` | Nazwa ustawienia aplikacji zawierającego parametry połączenia dla konta magazynu. `AzureWebJobsStorage` Zawiera parametry połączenia dla konta magazynu, które utworzono za pomocą aplikacji funkcji. |

W tym przykładzie następujące powiązania jest dodawany do `bindings` tablicy w pliku function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Można również dodać tę samą definicję powiązania bezpośrednio do swojej function.json.

W kodzie funkcji `msg` powiązania jest dostępny z `context`, jak w poniższym przykładzie:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Aby dowiedzieć się więcej, zobacz [powiązania danych wyjściowych usługi Queue storage](functions-bindings-storage-queue.md#output---javascript-example) odwołania.

### <a name="c-class-library"></a>C\# biblioteki klas

Zaktualizuj metodę funkcja następujący parametr, aby dodać `Run` definicję metody:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ten kod, należy dodać następujący kod, `using` instrukcji:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` Parametr `ICollector<T>` typ, który reprezentuje kolekcję wiadomości, które są zapisywane do pliku wyjściowego powiązania, kiedy funkcja kończy. Co najmniej jeden komunikat Dodaj do kolekcji, wysłanych do kolejki po zakończeniu funkcji.

Aby dowiedzieć się więcej, zobacz [powiązania danych wyjściowych usługi Queue storage](functions-bindings-storage-queue.md#output---c-example) odwołania.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Program Visual Studio Code umożliwia publikowanie projektu usługi Functions bezpośrednio na platformie Azure. W ramach tego procesu tworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure. Aplikacja funkcji zapewnia kontekst wykonywania dla Twoich funkcji. Projekt jest pakowany i wdrażany do nowej aplikacji funkcji w ramach subskrypcji platformy Azure.

Podczas publikowania z programu Visual Studio Code, używane są jedną z dwóch metod wdrażania:

* [ZIP wdrażania z włączoną uruchomienia z pakietu](functions-deployment-technologies.md#zip-deploy): używana w przypadku większości wdrożeń usługi Azure Functions.
* [Adres URL pakietu zewnętrznego](functions-deployment-technologies.md#external-package-url): używane do wdrożenia aplikacji systemu Linux na [planu zużycie](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Funkcja szybkiego tworzenia aplikacji

Domyślnie program Visual Studio Code automatycznie generuje wartości dla zasobów platformy Azure wymaganych przez aplikację funkcji. Wartości te są oparte na nazwę aplikacji funkcji, którą wybierzesz. Na przykład z użyciem wartości domyślnych do publikowania projektu w nowej aplikacji funkcji na platformie Azure, zobacz [programu Visual Studio Code z artykułem Szybki Start](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Jeśli chcesz podać jawne nazwy utworzonych zasobów, należy włączyć publikowanie przy użyciu opcji zaawansowanych.

### <a name="enabled-publishing-with-advanced-create-options"></a>Opcje tworzenia publikowanie włączone za pomocą zaawansowanych

Zapewnia kontrolę nad ustawieniami skojarzona z tworzenia usługi Azure Functions aplikacje, zaktualizuj rozszerzenie usługi Azure Functions, aby włączyć ustawienia zaawansowane.

1. Kliknij przycisk **Plik > Preferencje > Ustawienia**

1. Nawigowanie po **ustawienia użytkownika > Rozszerzenia > Usługa Azure Functions**

1. Zaznacz pole wyboru dla **funkcji platformy Azure: Advanced Creation**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Publikowanie w nowej aplikacji funkcji na platformie Azure przy użyciu zaawansowanego tworzenia

Poniższe kroki opublikować projekt, aby utworzyć nową aplikację funkcji przy użyciu zaawansowane opcje tworzenia.

1. W **platformy Azure: Funkcje** obszaru, wybierz Wdróż na ikonie aplikacji funkcji.

    ![Ustawienia aplikacji — funkcja](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Jeśli nie wylogowuj, monit o **logowanie do platformy Azure**. Możesz również **Utwórz bezpłatne konto platformy Azure**. Po pomyślnym zalogowaniu się za pomocą przeglądarki wróć do programu Visual Studio Code.

1. Jeśli masz wiele subskrypcji, **Wybierz subskrypcję,** dla aplikacji funkcji kliknij **+ Utwórz nową aplikację funkcji na platformie Azure**.

1. Postępuj zgodnie z monitami Podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz aplikację funkcji na platformie Azure | + Utwórz nową aplikację funkcji na platformie Azure | W następnym wierszu wpisz unikatową w skali globalnej nazwę, która identyfikuje nową aplikację funkcji, a następnie naciśnij klawisz Enter. Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`. |
    | Wybierz system operacyjny | Windows | Funkcja aplikacja działa w systemie Windows |
    | Wybierz plan hostingu | Plan Zużycie | Bez użycia serwera [użycie planu hostingu](functions-scale.md#consumption-plan) jest używany. |
    | Wybierz środowisko uruchomieniowe dla nowej aplikacji | Język projektu | Środowisko wykonawcze musi odpowiadać projektu, który w przypadku publikowania. |
    | Wybierz grupę zasobów dla nowych zasobów | Utwórz nową grupę zasobów | W następnym wierszu, wpisz nazwę grupy zasobów, takie jak `myResourceGroup`, a następnie naciśnij klawisz enter. Można także wybrać istniejącą grupę zasobów. |
    | Wybierz konto magazynu | Tworzenie nowego konta magazynu | W następnym wierszu Typ globalnie unikatową nazwę nowego konta magazynu używany przez aplikację funkcji i naciśnij klawisz Enter. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również istniejącego konta. |
    | Wybierz lokalizację dla nowych zasobów | region | Wybierz lokalizację w [regionie](https://azure.microsoft.com/regions/) znajdującym się w pobliżu lub blisko innych usług, do których Twoje funkcje uzyskują dostęp. |

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz **wyświetlanie danych wyjściowych** w tym zgłoszeniu do wyświetlania, tworzenia i wynikami wdrożenia, łącznie z zasobów platformy Azure, które zostały utworzone.

## <a name="republish-project-files"></a>Ponownie opublikować pliki projektu

Po skonfigurowaniu [ciągłe wdrażanie](functions-continuous-deployment.md), aplikację funkcji na platformie Azure jest aktualizowany zawsze wtedy, gdy pliki źródłowe są aktualizowane w lokalizacji źródłowej połączonej. Gdy firma Microsoft zaleca tej praktyką programowania, można także ponownie opublikować aktualizacje plików projektu z programu Visual Studio Code. 

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń, wyszukiwanie i wybieranie `Azure Functions: Deploy to function app...`.

1. Jeśli nie wylogowuj, monit o **logowanie do platformy Azure**. Po pomyślnym zalogowaniu się za pomocą przeglądarki wróć do programu Visual Studio Code. Jeśli masz wiele subskrypcji, **Wybierz subskrypcję,** zawierający aplikację funkcji.

1. Wybierz istniejącą aplikację funkcji na platformie Azure. Gdy ostrzegani zastąpienia wszystkich plików w aplikacji funkcji, wybierz pozycję **Wdróż** potwierdzić ostrzeżenie i kontynuować. 

Projekt jest ponownie skompilowany, to i przekazany na platformę Azure. Istniejący projekt jest zastępowana przez nowy pakiet i liczby ponownych uruchomień aplikacji funkcji.

## <a name="get-deployed-function-url"></a>Pobierz adres URL wdrożonej funkcji

Aby można było wywołać funkcję wyzwalaną przez protokół HTTP, należy adres URL funkcji podczas wdrażania aplikacji funkcji. Ten adres URL zawiera wszystkie wymagane [klawisze funkcyjne](functions-bindings-http-webhook.md#authorization-keys). Rozszerzenie umożliwia uzyskiwanie tych adresów URL dla wdrożonej funkcji.

1. Naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **usługi Azure Functions: Skopiuj adres URL funkcji**.

1. Postępuj zgodnie z monitami, aby wybrać aplikację funkcji na platformie Azure i następnie określonych wyzwalacza HTTP, który chcesz wywołać. 

Funkcja adres URL zostanie skopiowany do Schowka, oraz wszelkie wymagane klucze przekazywanych przy użyciu `code` parametr zapytania. Narzędzie HTTP można przesłać żądania POST lub przeglądarki dla żądań GET wysyłanych na funkcję zdalną.  

## <a name="run-functions-locally"></a>Uruchom funkcje lokalnie

Rozszerzenie usługi Azure Functions umożliwiają uruchamianie projektu usługi functions na lokalnym komputerze deweloperskim. Lokalne środowisko uruchomieniowe jest tego samego środowiska uruchomieniowego, który hostuje aplikację funkcji na platformie Azure. Ustawienia lokalne są odczytywane z [pliku local.settings.json](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Dodatkowe wymagania do uruchomienia lokalnie

Aby można było uruchomić projektu usługi Functions lokalnie, użytkownik musi spełniać te dodatkowe wymagania:

* Instalowanie wersji 2.x [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#v2). Pakiet narzędzi Core jest pobierane i instalowane dla Twojego automatycznie podczas uruchamiania projektu lokalnie. Podstawowe narzędzia obejmują całe środowisko uruchomieniowe usługi Azure Functions, więc pobierania i instalacji może zająć trochę czasu.

* Zainstaluj określone wymagania dla wybranego języka:

    | Język | Wymaganie |
    | -------- | --------- |
    | **C#** | [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Narzędzia interfejsu wiersza polecenia platformy .NET core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debuger dla rozszerzenia języka Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6 +](https://www.python.org/downloads/)|

    <sup>*</sup>Aktywne LTS i konserwacji LTS wersje (8.11.1 i 10.14.1 zalecane).

### <a name="configure-the-project-to-run-locally"></a>Konfigurowanie projektu do uruchomienia lokalnie

Środowisko uruchomieniowe usługi Functions wewnętrznie używa konta usługi Azure Storage dla wszystkich typów wyzwalacza innego niż HTTP i elementy webhook. Oznacza to, że należy ustawić **Values.AzureWebJobsStorage** klucza prawidłowe parametry połączenia konta usługi Azure Storage.

Ta sekcja używa [rozszerzenia Azure Storage dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) z [Microsoft Azure Storage Explorer](https://storageexplorer.com/) nawiązać połączenie i pobieranie parametrów połączenia magazynu.   

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio, otwórz **programu Cloud Explorer**, rozwiń węzeł **konta magazynu** > **konta magazynu**, a następnie wybierz **właściwości**i skopiuj **podstawowe parametry połączenia** wartość.

2. W swoim projekcie Otwórz pliku local.settings.json, a następnie ustaw wartość **AzureWebJobsStorage** skopiowany klucz do parametrów połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do **wartości** tablicy dla innych połączeń wymaganych funkcji.

Aby uzyskać więcej informacji, zobacz [pliku ustawień lokalnych](#local-settings-file).

### <a name="debugging-functions-locally"></a>Debugowanie funkcji lokalnie  

Aby debugować funkcji, naciśnij klawisz F5. Jeśli nie pobrano jeszcze [podstawowe narzędzia][podstawowe narzędzia usługi azure functions], pojawi się odpowiedni monit. Kiedy podstawowych narzędzi jest zainstalowana i uruchomiona, naciśnij w terminalu są wyświetlane dane wyjściowe. Jest to równoważne uruchomieniu `func host start` polecenia podstawowe narzędzia w terminalu, ale z dodatkowymi tworzyć zadania i dołączony debuger.  

Z projektem, uruchamianie możesz wyzwolić funkcji, tak jak w przypadku wdrożona na platformie Azure. Podczas pracy w trybie debugowania, punkty przerwania są osiągane w programie Visual Studio Code, zgodnie z oczekiwaniami.

Adres URL żądania HTTP wyzwalaczy jest wyświetlany w danych wyjściowych w terminalu. Klawiszy funkcyjnych wyzwalaczy HTTP nie są używane podczas uruchamiania lokalnego. Aby uzyskać więcej informacji, zobacz [strategii testowania kodu w usłudze Azure Functions](functions-test-a-function.md).  

Aby dowiedzieć się więcej, zobacz [pracy przy użyciu podstawowych narzędzi usługi Azure Functions][podstawowe narzędzia usługi azure functions].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt zostanie opublikowany na platformie Azure. Po zakończeniu publikowania, możesz skorzystać z opcji publikowania ustawienia z local.settings.json aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej, zobacz [aplikacji ustawień publikowania](#publish-application-settings).

Wartości w **ConnectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji mogą być odczytywane w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji zobacz sekcję zmiennych środowiska tych artykułów odwołanie językowe:

* [C# precompiled](functions-dotnet-class-library.md#environment-variables)
* [Skryptu C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Ustawienia aplikacji na platformie Azure

Ustawienia w pliku local.settings.json w projekcie powinna być taka sama jak ustawienia aplikacji w aplikacji funkcji na platformie Azure. Ustawień, które można dodać do pliku local.settings.json należy również dodać do aplikacji funkcji na platformie Azure. Te ustawienia nie są ładowane automatycznie podczas publikowania projektu. Podobnie, wszystkie ustawienia, które tworzysz w aplikacji funkcji [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) musi zostać pobrane do lokalnego projektu.

### <a name="publish-application-settings"></a>Aplikacja ustawienia publikowania

Najprostszym sposobem na publikowanie wymaganych ustawień aplikacji funkcji na platformie Azure jest użycie **ustawienia przekazywania** łącze, które jest wyświetlane, gdy opublikujesz projekt.

![Zakończono wdrożenie przekazywania ustawień aplikacji](./media/functions-develop-vs-code/upload-app-settings.png)

Ustawienia można również opublikować za pomocą `Azure Functions: Upload Local Setting` polecenia w palecie poleceń. Poszczególne ustawienia są dodawane do ustawień aplikacji na platformie Azure przy użyciu `Azure Functions: Add New Setting...` polecenia. 

> [!TIP]
> Pamiętaj zapisać pliku local.settings.json, przed jego opublikowaniem.

Jeśli plik lokalny jest zaszyfrowany, odszyfrowane, opublikowanych i ponownie szyfrowane. Jeśli istnieją ustawienia z różnymi wartościami w obu lokalizacjach, zostanie wyświetlony monit wybierz sposób kontynuować.

Wyświetlanie istniejących ustawień aplikacji w **platformy Azure: Funkcje** obszaru, rozwijając subskrypcji aplikacji funkcji i **ustawienia aplikacji**.

![Ustawienie aplikacji funkcji widoku w programie Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Ustawienia pobierania z platformy Azure

Jeśli utworzono ustawienia aplikacji na platformie Azure, możesz je pobrać do pliku local.settings.json. za pomocą `Azure Functions: Download Remote Settings...` polecenia. 

Podobnie jak w przypadku przekazywania, jeśli plik lokalny jest zaszyfrowany, odszyfrowane, zaktualizować i ponownie szyfrowane. Jeśli istnieją ustawienia z różnymi wartościami w obu lokalizacjach, zostanie wyświetlony monit wybierz sposób kontynuować.

## <a name="monitoring-functions"></a>Funkcje monitorowania

Po użytkownik [uruchamiane lokalnie](#run-functions-locally), danych dziennika jest przesyłany strumieniowo do konsoli usługi terminalowe. Możesz także uzyskać dane dziennika, podczas projektu usługi functions jest uruchamiany w aplikacji funkcji na platformie Azure. Albo nawiązaniu połączenia z przesyłania strumieniowego dzienników na platformie Azure, aby wyświetlić dane dziennika niemal w czasie rzeczywistym lub można włączyć usługi Application Insights i uzyskać pełniejsze informacje dotyczące zachowuje się jak aplikacja funkcji.

### <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji, jest często warto wyświetlać informacje rejestrowania w czasie niemal rzeczywistym. Można wyświetlić strumienia plików dzienników generowanych przez funkcje. Funkcja wyzwalana przez przykład przesyłania strumieniowego dzienników żądania HTTP znajduje się następujące dane wyjściowe:

![Przesyłanie strumieniowe dzienników danych wyjściowych wyzwalacza HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Aby dowiedzieć się więcej, zobacz [dzienniki przesyłania strumieniowego](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Dzienniki przesyłania strumieniowego obsługuje tylko jedno wystąpienie hosta funkcji. W przypadku funkcji skalowania do wielu wystąpień, dane z innych wystąpień nie są wyświetlane w strumień dziennika. [Live Stream metryki](../azure-monitor/app/live-stream.md) w usłudze Application Insights obsługuje wiele wystąpień. A także w niemal w czasie rzeczywistym analizy przesyłania strumieniowego są również na podstawie [próbce danych](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Zalecanym sposobem monitorowania wykonywania funkcji jest integrowanie aplikacji funkcji z usługi Azure Application Insights. Po utworzeniu aplikacji funkcji w witrynie Azure portal, ta Integracja jest wykonywane domyślnie. Jednak po utworzeniu aplikacji funkcji podczas publikowania w programie Visual Studio nie jest wykonywane integracji w Twojej aplikacji funkcji na platformie Azure.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [monitora usługi Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# skryptu projektów

Domyślnie wszystkie C# projekty są tworzone jako [ C# skompilowany projekty bibliotek klas](functions-dotnet-class-library.md). Jeśli zamiast tego wolisz pracować z C# projektów skryptu należy wybrać C# skryptu jako domyślny język w ustawieniach rozszerzenia usługi Azure Functions.

1. Kliknij przycisk **Plik > Preferencje > Ustawienia**.

1. Nawigowanie po **ustawienia użytkownika > Rozszerzenia > Usługa Azure Functions**.

1. Wybierz **C #Script** z **funkcji platformy Azure: Projekt języka**.

W tym momencie obejmują kierowanych do podstawowego podstawowe narzędzia `--csx` opcja, która generuje i publikuje C# skryptu pliki projektu (csx). Domyślny język określony, wszystkie tworzony domyślnie projekty C# skryptu projektów. Nie jest monitowany o wybierz język, projekt, gdy ma wartość domyślną. Aby utworzyć inne projekty języka, możesz zmienić to ustawienie, lub usuń go z pliku settings.json użytkownika. Po usunięciu tego ustawienia należy ponownie monit wybierz język, podczas tworzenia projektu.

## <a name="command-palette-reference"></a>Dokumentacja paletę poleceń

Rozszerzenie usługi Azure Functions udostępnia przydatne interfejs graficzny, w obszarze platformy Azure do interakcji z aplikacji funkcji na platformie Azure. Taką samą funkcjonalność jest również dostępny jako polecenia w palecie poleceń (F1). Dostępne są następujące polecenia platformy Azure specyficzne funkcje:

|Usługa Azure Functions polecenia  | Opis  |
|---------|---------|
|**Dodaj nowe ustawienia...**  |  Tworzy nowe ustawienie aplikacji na platformie Azure. Aby dowiedzieć się więcej, zobacz [aplikacji ustawień publikowania](#publish-application-settings). Może być również konieczne [Pobierz to ustawienie w ustawieniach lokalnych](#download-settings-from-azure). |
| **Konfigurowanie źródła wdrożenia...** | Połącz swoją aplikację funkcji, na platformie Azure do lokalnego repozytorium Git. Aby dowiedzieć się więcej, zobacz [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md). |
| **Połącz z repozytorium GitHub...** | Połącz swoją aplikację funkcji z repozytorium GitHub. |
| **Skopiuj adres URL funkcji** | Pobiera funkcji działającej na platformie Azure wyzwalanej przez zdalny adres URL protokołu HTTP. Aby dowiedzieć się więcej, zobacz temat jak [Pobierz adres URL wdrożonej funkcji](#get-deployed-function-url). |
| **Tworzenie aplikacji funkcji na platformie Azure...** | Tworzy nową aplikację funkcji w ramach subskrypcji na platformie Azure. Aby dowiedzieć się więcej, zobacz temat jak [publikowania nowej aplikacji funkcji na platformie Azure](#publish-to-azure).        |
| **Odszyfrowywanie ustawienia** | Użyj w celu odszyfrowania [ustawienia lokalne](#local-settings-file) zaszyfrowanych za pomocą `Azure Functions: Encrypt Settings`.  |
| **Usuwanie aplikacji funkcji...** | Usuwa istniejącą aplikację funkcji z subskrypcji na platformie Azure. Jeśli nie ma żadnych innych aplikacji w ramach planu usługi App Service, otrzymuje opcji natychmiastowego usunięcia, zbyt. Inne zasoby, takie jak konta magazynu i grupy zasobów nie są usuwane. Aby usunąć wszystkie zasoby, należy zamiast tego [Usuń grupę zasobów](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Nie ma wpływu na projekt lokalnego. |
|**Usuwanie funkcji...**  | Usuwa istniejącą funkcję z aplikacji funkcji na platformie Azure. Ponieważ tego usunięcia nie ma wpływu na lokalny projekt, zamiast tego Rozważ usunięcie funkcji lokalnie i następnie [ponowne publikowanie projektu](#republish-project-files). |
| **Usuń serwer Proxy...** | Usuwa serwer proxy usługi Azure Functions z aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat serwerów proxy, zobacz [pracy za pomocą usługi Azure Functions Proxies](functions-proxies.md). |
| **Usuń ustawienie...** | Usuwa ustawienie aplikacji funkcji na platformie Azure. Nie ma wpływu na ustawienia w pliku local.settings.json. |
| **Odłącz od repozytorium...**  | Usuń [ciągłe wdrażanie](functions-continuous-deployment.md) połączenia między aplikacją funkcji na platformie Azure i repozytorium kontroli źródła. |
| **Pobieranie ustawień zdalnych...** | Pobiera ustawienia z aplikacji wybranej funkcji na platformie Azure do pliku local.settings.json. Jeśli plik lokalny jest zaszyfrowany, odszyfrowane, zaktualizowany i ponownie szyfrowane. Jeśli istnieją ustawienia z różnymi wartościami w obu lokalizacjach, zostanie wyświetlony monit wybierz sposób kontynuować. Upewnij się, że zmiany zostały zapisane do pliku local.settings.json przed uruchomieniem tego polecenia. |
| **Edytuj ustawienia...** | Zmienia wartość z istniejącymi ustawieniami aplikacji funkcji na platformie Azure. Nie ma wpływu na ustawienia w pliku local.settings.json.  |
| **Ustawienia szyfrowania** | Szyfruje poszczególnych elementów w `Values` tablicy w [ustawienia lokalne](#local-settings-file). W tym pliku `IsEncrypted` jest również ustawiona na `true`, która informuje lokalne środowisko uruchomieniowe do odszyfrowania ustawienia przed ich użyciem. Szyfrowanie ustawienia lokalne, aby zmniejszyć ryzyko przeciek cenne informacje. Na platformie Azure aplikacji, które zawsze są przechowywane ustawienia szyfrowane. |
| **Wykonaj teraz — funkcja** | Uruchamia [funkcji wyzwalanej przez czasomierz](functions-bindings-timer.md) na platformie Azure ręcznie do celów testowych. Aby dowiedzieć się więcej na temat wyzwalanie funkcji innych niż HTTP na platformie Azure, zobacz [ręcznie uruchomić bez funkcji wyzwalanej przez HTTP](functions-manually-run-non-http.md). |
| **Inicjowanie projektu do użytku z programem VS Code...** | Dodaje wymagane pliki projektu programu Visual Studio Code do istniejącego projektu usługi Functions. Użyj tego polecenia, aby pracować z projektem, który został utworzony przy użyciu podstawowych narzędzi. |
| **Instalacja aktualizacji usługi Azure Functions podstawowych narzędzi** | Instaluje lub aktualizuje [Podstawowe narzędzia usługi Azure Functions] służące do uruchomienia lokalnie. |
| **Ponowne wdrażanie**  | Umożliwia ponowne wdrożenie plików projektu z repozytorium Git połączone z konkretnym wdrożeniem na platformie Azure. Aby ponownie opublikować lokalne aktualizacje w programie Visual Studio Code [ponownie opublikować projekt](#republish-project-files). |
| **Zmień nazwę ustawień...** | Zmienia nazwę klucza z istniejącymi ustawieniami aplikacji funkcji na platformie Azure. Nie ma wpływu na ustawienia w pliku local.settings.json. Po zmianie nazwy ustawień na platformie Azure, wykonaj następujące czynności [pobierania tych zmian do lokalnego projektu](#download-settings-from-azure). |
| **Ponowne uruchomienie** | Ponowne uruchomienie aplikacji funkcji na platformie Azure. Wdrażanie aktualizacji powoduje także ponowne uruchomienie aplikacji funkcji. |
| **Ustaw AzureWebJobStorage...**| Ustawia wartość `AzureWebJobStorage` ustawienia aplikacji. To ustawienie jest wymagane przez usługę Azure functions i zostaje ustawiona po utworzeniu aplikacji funkcji na platformie Azure. |
| **Start** | Uruchamia aplikację funkcji zatrzymania na platformie Azure. | 
| **Rozpocząć przesyłanie strumieniowe dzienników** | Uruchamia strumieniowe przesyłanie dzienników dla aplikacji funkcji na platformie Azure. Dzienniki przesyłania strumieniowego w czasie korzystają zdalnego rozwiązywania problemów w systemie Azure, jeśli zachodzi potrzeba wyświetlenia tych informacji w czasie niemal rzeczywistym. Aby dowiedzieć się więcej, zobacz [dzienniki przesyłania strumieniowego](#streaming-logs). |
| **Stop** | Zamknięciu rozwijanego aplikacji funkcji działających na platformie Azure. |
| **Zatrzymać przesyłanie strumieniowe dzienników** | Zatrzymuje strumieniowe przesyłanie dzienników dla aplikacji funkcji na platformie Azure. |
| **Przełącz jako ustawienie miejsca** | Po włączeniu gwarantuje, że ustawienie aplikacji będzie nadal występować po dane miejsce wdrożenia. |
| **Odinstaluj podstawowych narzędzi usługi Azure Functions** | Usuwa podstawowe narzędzia usługi Azure Functions, co jest wymagane przez rozszerzenie. |
| **Przekaż ustawień lokalnych...** | Przekazuje ustawienia z pliku local.settings.json aplikacji wybranej funkcji na platformie Azure. Jeśli plik lokalny jest zaszyfrowany, odszyfrowane, przekazane i ponownie szyfrowane. Jeśli istnieją ustawienia z różnymi wartościami w obu lokalizacjach, zostanie wyświetlony monit wybierz sposób kontynuować. Upewnij się, że zmiany zostały zapisane do pliku local.settings.json przed uruchomieniem tego polecenia. |
| **Wyświetlanie zatwierdzenia w usłudze GitHub** | Pokazuje najnowszego zatwierdzenia w określonym wdrożeniu, gdy aplikacja funkcji jest podłączony do repozytorium. |
| **Wyświetl dzienniki wdrożenia** | Pokazuje dzienniki dla określonego wdrożenia do aplikacji funkcji na platformie Azure. |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat podstawowych narzędzi usługi Azure Functions, zobacz [kodu i testowanie usługi Azure functions lokalnie](functions-run-local.md).

Aby dowiedzieć się więcej o projektowaniu funkcji jako bibliotek klasy .NET, zobacz [dokumentacja dla deweloperów usługi Azure Functions C#](functions-dotnet-class-library.md). Ten artykuł zawiera również linki przykłady dotyczące używania atrybutów do deklarowania różne rodzaje powiązań, obsługiwane przez usługi Azure Functions.    

[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Podstawowe narzędzia usługi Azure Functions]: functions-run-local.md