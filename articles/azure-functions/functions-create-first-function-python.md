---
title: Tworzenie wyzwalanej przez protokół HTTP funkcji języka Python na platformie Azure
description: Utwórz i Wdróż kod języka Python bezserwerowy w chmurze przy użyciu Azure Functions.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 529a992178fae5566c8e315956388c4cd4b80257
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116219"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Szybki Start: Tworzenie wyzwalanej przez protokół HTTP funkcji języka Python na platformie Azure

W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka Python, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również oparta na [Visual Studio Code wersja](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python) tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.76 lub nowszej. 
- [3.7.4 Python-64 bit](https://www.python.org/downloads/release/python-374/). (3.7.4 języka Python jest weryfikowany przy użyciu Azure Functions; Środowisko Python 3,8 i jego nowsze wersje nie są jeszcze obsługiwane.)

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

1. W terminalu lub oknie poleceń Uruchom `func --version`, aby sprawdzić, czy Azure Functions Core Tools jest w wersji 2.7.1846 lub nowszej.
1. Uruchom `az --version`, aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.76 lub nowszego.
1. Uruchom `az login`, aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.
1. Uruchom `python --version` (Linux/MacOS) lub `py --version` (Windows), aby sprawdzić raporty wersji języka Python 3.7. x.

## <a name="create-and-activate-a-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego

W odpowiednim folderze Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`. Upewnij się, że używasz języka Python 3,7, który jest obsługiwany przez Azure Functions.


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli środowisko Python nie zainstalowało pakietu venv na dystrybucji systemu Linux, uruchom następujące polecenie:

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Wszystkie kolejne polecenia są uruchamiane w tym aktywowanym środowisku wirtualnym. (Aby wyjść z środowiska wirtualnego, uruchom `deactivate`.)

## <a name="create-a-local-function-project"></a>Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

1. W środowisku wirtualnym Uruchom polecenie `func init`, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:

    ```
    func init LocalFunctionProj --python
    ```
    
    Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [Local. Settings. JSON](functions-run-local.md#local-settings-file) i pliku [host. JSON](functions-host-json.md). Ponieważ *Local. Settings. JSON* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

    > [!TIP]
    > Ponieważ projekt funkcji jest powiązany z określonym środowiskiem uruchomieniowym, wszystkie funkcje w projekcie muszą być zapisywane w tym samym języku.

1. Przejdź do folderu projektu:

    ```
    cd LocalFunctionProj
    ```
    
1. Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie argument `--name` jest unikatową nazwą funkcji, a argument `--template` Określa wyzwalacz funkcji. `func new` utworzyć podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *Function. JSON*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>Obowiązkowe Sprawdzanie zawartości pliku

W razie potrzeby możesz pominąć, aby [uruchomić funkcję lokalnie](#run-the-function-locally) i później przejrzeć zawartość pliku.

#### <a name="__init__py"></a>\_\_init\_\_. PR

*\_\_init\_\_. pr* `main()` zawiera funkcję języka Python, która jest wyzwalana zgodnie z konfiguracją w *funkcji Function. JSON*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

W przypadku wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` zgodnie z definicją w *funkcji Function. JSON*. `req` to wystąpienie [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, zdefiniowany jako `$return` w *funkcji Function. JSON*, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](functions-bindings-http-webhook.md).

#### <a name="functionjson"></a>function.json

*Function. JSON* to plik konfiguracji, który definiuje wejściowe i wyjściowe `bindings` dla funkcji, w tym typ wyzwalacza. W razie potrzeby można zmienić `scriptFile` w taki sposób, aby wywoływać inny plik w języku Python.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) i dane wyjściowe powiązania typu [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom funkcję, uruchamiając lokalny host środowiska uruchomieniowego Azure Functions w folderze *LocalFunctionProj* :

```
func start
```

Powinny pojawić się następujące dane wyjściowe. (Jeśli HttpExample nie pojawia się jak pokazano poniżej, prawdopodobnie uruchomiono hosta z poziomu folderu *HttpExample* . W takim przypadku użyj **klawiszy Ctrl**+**C** , aby zatrzymać hosta, przejdź do folderu nadrzędnego *LocalFunctionProj* i ponownie uruchom `func start`.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Skopiuj adres URL funkcji `HttpExample` z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<your-name>`, wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions`. W przeglądarce powinien zostać wyświetlony komunikat podobny do `Hello Functions`:

![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-create-first-function-python/function-test-local-browser.png)

Na terminalu, w którym uruchomiono `func start`, są także wyświetlane dane wyjściowe dziennika podczas wykonywania żądania.

Gdy wszystko będzie gotowe, **naciśnij klawisz Ctrl**+**C** , aby zatrzymać host funkcji.

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto usługi Azure Storage, które zachowuje stan i inne informacje o Twoich projektach.
- Aplikacja usługi Azure Functions, która udostępnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Aby utworzyć te elementy, Użyj poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie zawiera dane wyjściowe JSON po zakończeniu.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w regionie `westeurope`. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym się znajdujesz, przy użyciu dostępnego regionu z `az account list-locations` polecenie).

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.
    
1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) . W poniższym przykładzie Zastąp `<storage_name>` nazwą globalnie unikatową. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` określa typowe konto ogólnego przeznaczenia.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    W tym przewodniku szybki start konto magazynu odnosi się tylko do kilku centów USD.
    
1. Utwórz aplikację Functions za pomocą polecenia [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . W poniższym przykładzie Zastąp `<storage_name>` nazwą konta, które zostało użyte w poprzednim kroku, i Zastąp `<app_name>` globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. `<app_name>` jest również domyślną domeną DNS aplikacji funkcji.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    To polecenie tworzy aplikację funkcji, która uruchamia określony środowisko uruchomieniowe języka zgodnie z [planem zużycia Azure Functions](functions-scale.md#consumption-plan), który jest bezpłatny do użycia w tym miejscu nakładu pracy. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.
    
## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure

W przypadku niezbędnych zasobów możesz teraz przystąpić do wdrażania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure przy użyciu polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . W poniższym przykładzie Zastąp `<app_name>` nazwą aplikacji.

```
func azure functionapp publish <app_name>
```

Jeśli zobaczysz błąd, "nie można odnaleźć aplikacji o nazwie...", poczekaj kilka sekund i spróbuj ponownie, ponieważ platforma Azure mogła nie zostać w pełni zainicjowana po poleceniu `az functionapp create`.

Polecenie Publikuj wyświetla wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Wywoływanie funkcji na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, wywołuje ją, wysyłając żądanie HTTP do jego adresu URL w przeglądarce lub przy użyciu narzędzia, takiego jak zwinięcie. W obu wystąpieniach parametr `code` URL jest unikatowym kluczem funkcji, który autoryzuje wywołanie z punktem końcowym funkcji.

# <a name="browsertabbrowser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych polecenia Publikuj na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Azure`. Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[odsłon](#tab/curl)

Uruchom [zwinięcie](https://curl.haxx.se/) z **adresem URL Invoke**, dołączając parametr `&name=Azure`. Danymi wyjściowymi polecenia powinien być tekst "Hello Azure".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu programu zwinięcie](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Aby wyświetlić dzienniki niemal w czasie rzeczywistym dla opublikowanej aplikacji języka Python, użyj [Live Metrics Stream Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przejdziesz do następnego kroku, [Dodaj powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md), Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md)
