---
title: Tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP
description: Dowiedz się, jak utworzyć funkcję z wiersza polecenia, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 2a02e1481d975f877508bde02948bc65561b9f13
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272747"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Szybki Start: Tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP

W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji reagującej na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

Istnieje również oparta na [Visual Studio Code wersja](functions-create-first-function-vs-code.md) tego artykułu.

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](./functions-run-local.md#v2) wersja 2.7.1846 lub nowsza wersja 2. x.

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.76 lub nowszej. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), Active LTS i Maintenance LTS wersje (zalecane 8.11.1 i 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ Środowisko [python 3,7](https://www.python.org/downloads/release/python-375/) lub [Python 3,6](https://www.python.org/downloads/release/python-368/), które są obsługiwane przez Azure Functions. Środowisko Python 3,8 i jego nowsze wersje nie są jeszcze obsługiwane. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Zestaw .NET Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Sprawdzanie środowiska

+ W terminalu lub oknie poleceń Uruchom `func --version`, aby sprawdzić, czy Azure Functions Core Tools jest w wersji 2.7.1846 lub nowszej wersji 2. x.

+ Uruchom `az --version`, aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.76 lub nowszego.

+ Uruchom `az login`, aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Uruchom `node --version`, aby sprawdzić, czy w wersji Node. js są raportowane 8. x lub 10. x.
::: zone-end
::: zone pivot="programming-language-python"
+ Uruchom `python --version` (Linux/MacOS) lub `py --version` (Windows), aby sprawdzić raporty wersji języka Python 3.7. x lub 3.6. x.

## <a name="create-venv"></a>Tworzenie i aktywowanie środowiska wirtualnego

W odpowiednim folderze Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`. Upewnij się, że używasz języka Python 3,7 lub 3,6, który jest obsługiwany przez Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Wszystkie kolejne polecenia są uruchamiane w tym aktywowanym środowisku wirtualnym. (Aby wyjść z środowiska wirtualnego, uruchom `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

1. W środowisku wirtualnym Uruchom polecenie `func init`, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [Local. Settings. JSON](functions-run-local.md#local-settings-file) i pliku [host. JSON](functions-host-json.md). Ponieważ *Local. Settings. JSON* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

1. Przejdź do folderu projektu:

    ```
    cd LocalFunctionProj
    ```
    
1. Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie argument `--name` jest unikatową nazwą funkcji, a argument `--template` Określa wyzwalacz funkcji. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` tworzy plik kodu HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` tworzy podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *Function. JSON*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>Obowiązkowe Sprawdzanie zawartości pliku

W razie potrzeby możesz pominąć, aby [uruchomić funkcję lokalnie](#run-the-function-locally) i później przejrzeć zawartość pliku.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* zawiera metodę `Run`, która odbiera dane żądania w zmiennej `req` jest identyfikatorem [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , który jest dekoracyjny z **HttpTriggerAttribute**, który definiuje zachowanie wyzwalacza. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Zwracany obiekt to element [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , który zwraca komunikat odpowiedzi jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_. PR

*\_\_init\_\_. pr* `main()` zawiera funkcję języka Python, która jest wyzwalana zgodnie z konfiguracją w *funkcji Function. JSON*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` zgodnie z definicją w *funkcji Function. JSON*. `req` to wystąpienie [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, zdefiniowany jako `$return` w *funkcji Function. JSON*, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index. js

plik *index. js* eksportuje funkcję, która jest wyzwalana zgodnie z konfiguracją w *funkcji Function. JSON*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` zgodnie z definicją w *funkcji Function. JSON*. Obiekt zwracany, zdefiniowany jako `$return` w *funkcji Function. JSON*, jest odpowiedzią. Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>indeks. TS

*index. TS* eksportuje funkcję, która jest wyzwalana zgodnie z konfiguracją w *funkcji Function. JSON*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` typu **HttpRequest** zgodnie z definicją w *Function. JSON*. Obiekt zwracany, zdefiniowany jako `$return` w *funkcji Function. JSON*, jest odpowiedzią. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>Uruchom. ps1

*Run. ps1* definiuje skrypt funkcji, który jest wyzwalany zgodnie z konfiguracją w *funkcji Function. JSON*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania przekazywane do `$Request` param zdefiniowanego w *funkcji Function. JSON*. Obiekt zwracany, zdefiniowany jako `Response` w *funkcji Function. JSON*, jest przesyłany do `Push-OutputBinding` polecenie cmdlet jako odpowiedź. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*Function. JSON* to plik konfiguracji, który definiuje wejściowe i wyjściowe `bindings` dla funkcji, w tym typ wyzwalacza. 
::: zone-end

::: zone pivot="programming-language-python"
W razie potrzeby można zmienić `scriptFile` w taki sposób, aby wywoływać inny plik w języku Python.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) i dane wyjściowe powiązania typu [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto magazynu, które zachowuje stan i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby utworzyć te elementy. Każde polecenie zawiera dane wyjściowe JSON po zakończeniu.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w regionie `westeurope`. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym się znajdujesz, przy użyciu dostępnego regionu z `az account list-locations` polecenie).

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.
    ::: zone-end  
    
1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) . W poniższym przykładzie Zastąp `<STORAGE_NAME>` nazwą globalnie unikatową. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    W tym przewodniku szybki start konto magazynu odnosi się tylko do kilku centów (USD).
    
1. Utwórz aplikację Functions za pomocą polecenia [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . W poniższym przykładzie Zastąp `<STORAGE_NAME>` nazwą konta, które zostało użyte w poprzednim kroku, i Zastąp `<APP_NAME>` globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 

    ::: zone pivot="programming-language-python"  
    Jeśli używasz języka Python 3,6, Zmień również `--runtime-version` na `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Jeśli używasz środowiska Node. js 8, Zmień również `--runtime-version` na `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    To polecenie tworzy aplikację funkcji działającą w określonym środowisku uruchomieniowym języka zgodnie z [planem zużycia Azure Functions](functions-scale.md#consumption-plan), który jest bezpłatny dla nakładu pracy w tym miejscu. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.
    
## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure

::: zone pivot="programming-language-typescript"  
Przed rozpoczęciem wdrażania projektu na platformie Azure za pomocą podstawowych narzędzi można utworzyć kompilację plików JavaScript w środowisku produkcyjnym na podstawie plików źródłowych TypeScript.

Następujące polecenie przygotowuje projekt TypeScript do wdrożenia:

```
npm run build:production 
```
::: zone-end  

W przypadku niezbędnych zasobów możesz teraz przystąpić do wdrażania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure przy użyciu polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . W poniższym przykładzie Zastąp `<APP_NAME>` nazwą aplikacji.

```
func azure functionapp publish <APP_NAME>
```

Jeśli zobaczysz błąd, "nie można odnaleźć aplikacji o nazwie...", poczekaj kilka sekund i spróbuj ponownie, ponieważ platforma Azure mogła nie zostać w pełni zainicjowana po poleceniu `az functionapp create`.

Polecenie Publikuj wyświetla wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):

<pre>
...

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
</pre>

## <a name="invoke-the-function-on-azure"></a>Wywoływanie funkcji na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, wywołuje ją, wysyłając żądanie HTTP do jego adresu URL w przeglądarce lub przy użyciu narzędzia, takiego jak zwinięcie. W obu wystąpieniach parametr `code` URL jest unikatowym [kluczem funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys) , który autoryzuje wywołanie punktu końcowego funkcji.

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych polecenia Publikuj na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Functions`. Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[odsłon](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) przy użyciu **adresu URL wywołania**, dołączając `&name=Functions`parametru. Danymi wyjściowymi polecenia powinien być tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu programu zwinięcie](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Aby przeglądać dzienniki niemal w czasie rzeczywistym dla opublikowanej aplikacji funkcji, użyj [Live Metrics Stream Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przejdziesz do następnego kroku, [Dodaj powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md), Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md)
