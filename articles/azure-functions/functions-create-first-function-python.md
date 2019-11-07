---
title: Tworzenie wyzwalanej przez protokół HTTP funkcji języka Python na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję języka Python na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 791348088d909785b36934c3b9a2ae00fc0acbb7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622048"
---
# <a name="create-an-http-triggered-python-function-in-azure"></a>Tworzenie wyzwalanej przez protokół HTTP funkcji języka Python na platformie Azure

W tym artykule pokazano, jak za pomocą narzędzi wiersza polecenia utworzyć projekt w języku Python, który działa w Azure Functions. Utworzysz również funkcję, która jest wyzwalana przez żądanie HTTP. Na koniec opublikujesz projekt do uruchamiania jako [Funkcja bezserwerowa](functions-scale.md#consumption-plan) na platformie Azure.

Ten artykuł jest pierwszym z dwóch przewodników szybki start dla Azure Functions. Po ukończeniu tego przewodnika Szybki Start możesz [dodać powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md) do funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

+ Zainstaluj środowisko [Python 3.6.8](https://www.python.org/downloads/). Ta wersja języka Python jest weryfikowana przy użyciu funkcji. 3,7 i nowsze wersje nie są jeszcze obsługiwane.

+ Zainstaluj program [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.7.1575 lub nowszej.

+ Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2. x lub nowszej.

+ Mieć aktywną subskrypcję platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Tworzenie i aktywowanie środowiska wirtualnego (opcjonalnie)

Do lokalnego tworzenia funkcji języka Python należy używać środowiska Python 3.6. x. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

> [!NOTE]
> Jeśli język Python nie zainstalował venv w dystrybucji systemu Linux, można go zainstalować za pomocą następującego polecenia:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Program PowerShell lub wiersz polecenia systemu Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Po aktywowaniu środowiska wirtualnego uruchom pozostałe polecenia w nim. Aby wypróbować środowisko wirtualne, uruchom `deactivate`.

## <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

Projekt funkcji jest odpowiednikiem aplikacji funkcji na platformie Azure. Może mieć wiele funkcji, które współużytkują te same konfiguracje lokalne i hostingowe.

1. W środowisku wirtualnym Uruchom następujące polecenie:

    ```console
    func init MyFunctionProj
    ```

1. Wybierz język **Python** jako środowisko uruchomieniowe procesu roboczego.

    Polecenie tworzy folder _MyFunctionProj_ . Zawiera te trzy pliki:

    * *Local. Settings. JSON*: służy do przechowywania ustawień aplikacji i parametrów połączenia podczas lokalnego uruchamiania. Ten plik nie jest publikowany na platformie Azure.
    * *Requirements. txt*: zawiera listę pakietów, które system zainstaluje podczas publikowania na platformie Azure.
    * plik *host. JSON*: zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje w aplikacji funkcji. Ten plik jest publikowany na platformie Azure.

1. Przejdź do nowego folderu *MyFunctionProj* :

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Tworzenie funkcji

Dodaj funkcję do nowego projektu.

1. Aby dodać funkcję do projektu, uruchom następujące polecenie:

    ```console
    func new
    ```

1. Użyj strzałki w dół, aby wybrać szablon **wyzwalacza http** .

1. Po wyświetleniu monitu o nazwę funkcji wpisz *HttpTrigger* , a następnie naciśnij klawisz ENTER.

Te polecenia tworzą podfolder o nazwie _HttpTrigger_. Zawiera następujące pliki:

* *Function. JSON*: plik konfiguracji, który definiuje funkcję, wyzwalacz i inne powiązania. Zwróć uwagę, że w tym pliku wartość `scriptFile` wskazuje plik zawierający funkcję, a tablica `bindings` definiuje wyzwalacz wywołania i powiązania.

    Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) i dane wyjściowe powiązania typu [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_. PR*: plik skryptu, który jest funkcją wyzwalaną przez protokół http. Zauważ, że ten skrypt ma domyślne `main()`. Dane HTTP z wyzwalacza są przekazywane do funkcji przy użyciu `req` o nazwie `binding parameter`. `req`, który jest zdefiniowany w funkcji Function. JSON, jest wystąpieniem [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Obiekt zwracany, zdefiniowany jako `$return` w *funkcji Function. JSON*, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Funkcja jest uruchamiana lokalnie przy użyciu środowiska uruchomieniowego Azure Functions.

1. To polecenie uruchamia aplikację funkcji:

    ```console
    func host start
    ```

    Po uruchomieniu hosta Azure Functions zapisuje coś podobne do poniższych. Jest ono obcinane w tym miejscu, aby można było je lepiej przeczytać:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki.

1. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Poniższy zrzut ekranu przedstawia odpowiedź na żądanie GET, którą funkcja lokalna zwraca do przeglądarki:

    ![Sprawdź lokalnie w przeglądarce](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Wybierz kombinację klawiszy CTRL + C, aby zamknąć aplikację funkcji.

Teraz, gdy funkcja została uruchomiona lokalnie, możesz utworzyć aplikację funkcji i inne wymagane zasoby na platformie Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Tworzenie aplikacji funkcji na platformie Azure

Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi.

Uruchom następujące polecenie. Zastąp `<APP_NAME>` unikatową nazwą aplikacji funkcji. Zastąp `<STORAGE_NAME>` nazwą konta magazynu. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure.

> [!NOTE]
> Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `myResourceGroup` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Powyższe polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów. To wystąpienie służy do monitorowania aplikacji funkcji i wyświetlania dzienników.

Teraz możesz przystąpić do publikowania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure Możesz użyć polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) Core Tools, aby wdrożyć kod projektu na platformie Azure. W tym przykładzie Zastąp `<APP_NAME>` nazwą aplikacji.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Opcja `--build remote`a kompiluje projekt języka Python zdalnie na platformie Azure z plików w pakiecie wdrożeniowym. 

Zobaczysz dane wyjściowe podobne do poniższego komunikatu. Jest ono obcinane w tym miejscu, aby można było je lepiej przeczytać:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Możesz skopiować wartość `Invoke url` dla `HttpTrigger` i użyć jej do zweryfikowania funkcji na platformie Azure. Adres URL zawiera `code` wartość ciągu zapytania, która jest kluczem funkcji, co utrudnia innym wywoływanie punktu końcowego wyzwalacza HTTP na platformie Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Aby wyświetlić dzienniki niemal w czasie rzeczywistym dla opublikowanej aplikacji języka Python, użyj [Live Metrics Stream Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Python z funkcją wyzwalaną przez protokół HTTP, uruchom go na komputerze lokalnym i wdrożony na platformie Azure. Teraz możesz rozłożyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md)
