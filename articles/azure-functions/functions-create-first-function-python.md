---
title: Tworzenie wyzwalanej przez protokół HTTP funkcji języka Python na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję języka Python na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 3de8c42c59455cc326fa909bc520a94daac68706
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769340"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Szybki Start: Tworzenie wyzwalanej przez protokół HTTP funkcji języka Python na platformie Azure

W tym artykule pokazano, jak za pomocą narzędzi wiersza polecenia utworzyć projekt w języku Python, który działa w Azure Functions. Utworzysz również funkcję, która jest wyzwalana przez żądanie HTTP. Po uruchomieniu lokalnie należy opublikować projekt do uruchamiania jako [Funkcja bezserwerowa](functions-scale.md#consumption-plan) na platformie Azure. 

Ten artykuł jest pierwszym z dwóch przewodników szybki start dla Azure Functions. Po ukończeniu tego przewodnika Szybki Start możesz [dodać powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md) do funkcji.

Istnieje również oparta na [Visual Studio Code wersja](/azure/python/tutorial-vs-code-serverless-python-01) tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

+ Zainstaluj środowisko [Python 3.7.4](https://www.python.org/downloads/). Ta wersja języka Python jest weryfikowana przy użyciu funkcji. Środowisko Python 3,8 i jego nowsze wersje nie są jeszcze obsługiwane.

+ Zainstaluj program [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej.

+ Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.76 lub nowszej.

+ Mieć aktywną subskrypcję platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego

Należy używać środowiska Python 3,7 do lokalnego tworzenia funkcji języka Python. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

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

Projekt funkcji może mieć wiele funkcji, które współużytkują te same konfiguracje lokalne i hostingowe.

W środowisku wirtualnym Uruchom następujące polecenia:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

`func init` polecenie tworzy folder _MyFunctionProj_ . Projekt języka Python w tym folderze nie zawiera jeszcze żadnych funkcji. Dodasz je dalej.

## <a name="create-a-function"></a>Tworzenie funkcji

Aby dodać funkcję do projektu, uruchom następujące polecenie:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

To polecenie tworzy podfolder o nazwie _HttpTrigger_, który zawiera następujące pliki:

* *Function. JSON*: plik konfiguracji, który definiuje funkcję, wyzwalacz i inne powiązania. Zwróć uwagę, że w tym pliku wartość `scriptFile` wskazuje plik zawierający funkcję, a tablica `bindings` definiuje wyzwalacz wywołania i powiązania.

    Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) i dane wyjściowe powiązania typu [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_. PR*: plik skryptu, który jest funkcją wyzwalaną przez protokół http. Zauważ, że ten skrypt ma domyślne `main()`. Dane HTTP z wyzwalacza są przekazywane do funkcji przy użyciu `req` o nazwie `binding parameter`. `req`, który jest zdefiniowany w funkcji Function. JSON, jest wystąpieniem [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Obiekt zwracany, zdefiniowany jako `$return` w *funkcji Function. JSON*, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](functions-bindings-http-webhook.md).

Teraz można uruchomić nową funkcję na komputerze lokalnym.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

To polecenie uruchamia aplikację funkcji przy użyciu środowiska uruchomieniowego Azure Functions (Func. exe):

```console
func host start
```

Powinny zostać wyświetlone następujące informacje zapisywane w danych wyjściowych:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Skopiuj adres URL funkcji `HttpTrigger` z tego danych wyjściowych i wklej go na pasku adresu przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Poniższy zrzut ekranu przedstawia odpowiedź na żądanie GET, którą funkcja lokalna zwraca do przeglądarki:

![Sprawdź lokalnie w przeglądarce](./media/functions-create-first-function-python/function-test-local-browser.png)

Użyj klawiszy CTRL + C, aby zamknąć wykonywanie aplikacji funkcji.

Po uruchomieniu funkcji lokalnie można wdrożyć kod funkcji na platformie Azure.  
Przed wdrożeniem aplikacji musisz utworzyć pewne zasoby platformy Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Tworzenie aplikacji funkcji na platformie Azure

Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

Uruchom następujące polecenie. Zastąp `<APP_NAME>` unikatową nazwą aplikacji funkcji. Zastąp `<STORAGE_NAME>` nazwą konta magazynu. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure.

> [!NOTE]
> Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `myResourceGroup` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Poprzednie polecenie tworzy aplikację funkcji z uruchomioną 3.7.4 języka Python. Program udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów. To wystąpienie służy do monitorowania aplikacji funkcji i wyświetlania dzienników. 

Teraz możesz przystąpić do publikowania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure Możesz użyć polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) Core Tools, aby wdrożyć kod projektu na platformie Azure. W tym przykładzie Zastąp `<APP_NAME>` nazwą aplikacji.

```console
func azure functionapp publish <APP_NAME>
```

Projekt języka Python został skompilowany zdalnie na platformie Azure z plików w pakiecie wdrożeniowym. 

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
