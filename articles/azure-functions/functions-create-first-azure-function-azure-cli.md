---
title: Tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP
description: Dowiedz się, jak utworzyć funkcję z wiersza polecenia, a następnie opublikować projekt lokalny do hostingu bezserwerowego w usłudze Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 89b6a9f31414cbaa9cc92c1a0d881a1354180990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282736"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Szybki start: tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP

W tym artykule za pomocą narzędzi wiersza polecenia można utworzyć funkcję odpowiadającą na żądania HTTP. Po przetestowaniu kodu lokalnie, można wdrożyć go w środowisku bezserwerowym usługi Azure Functions. Ukończenie tego przewodnika Szybki start wiąże się z niewielkim kosztem kilku centów USD lub mniej na koncie platformy Azure.

Istnieje również wersja tego artykułu [oparta na kodzie programu Visual Studio.](functions-create-first-function-vs-code.md)

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy mieć następujące właściwości:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Podstawowe narzędzia azure functions](./functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej wersji 2.x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Język Python 3.6 i 3.7 wymagają [narzędzia Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej wersji 2.x. Python 3.8 wymaga [wersji 3.x](./functions-run-local.md#v2) podstawowych narzędzi.
::: zone-end

+ Interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.76 lub nowszej. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS i konserwacja lts wersje (8.11.1 i 10.14.1 zalecane).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/), które są obsługiwane przez usługę Azure Functions (x64).
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ .NET [Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Sprawdź swoje środowisko

+ W oknie terminala lub `func --version` polecenia uruchom, aby sprawdzić, czy podstawowe narzędzia usługi Azure Functions są w wersji 2.7.1846 lub nowszej wersji 2.x.

+ Uruchom, `az --version` aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure jest 2.0.76 lub nowsza.

+ Uruchom, `az login` aby zalogować się na platformie Azure i zweryfikować aktywną subskrypcję.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Uruchom, `node --version` aby sprawdzić raporty wersji Node.js 8.x lub 10.x.
::: zone-end
::: zone pivot="programming-language-python"
+ Uruchom `python --version` (Linux/MacOS) `py --version` lub (Windows), aby sprawdzić raporty wersji języka Python 3.8.x, 3.7.x lub 3.6.x.

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Tworzenie i aktywowanie środowiska wirtualnego

W odpowiednim folderze uruchom następujące polecenia, aby utworzyć `.venv`i aktywować środowisko wirtualne o nazwie . Pamiętaj, aby używać języka Python 3.8, 3.7 lub 3.6, które są obsługiwane przez usługi Azure Functions.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli Python nie zainstalował pakietu venv w dystrybucji systemu Linux, uruchom następujące polecenie:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

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

Wszystkie kolejne polecenia można uruchomić w tym aktywowanym środowisku wirtualnym. (Aby wyjść ze środowiska `deactivate`wirtualnego, uruchom .)

::: zone-end

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W usłudze Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej pojedynczej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie współużytkują te same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

1. W środowisku wirtualnym `func init` uruchom polecenie, aby utworzyć projekt funkcji w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:

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


    Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.json](functions-run-local.md#local-settings-file) i [host.json](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *gitignore.*

1. Przejdź do folderu projektu:

    ```
    cd LocalFunctionProj
    ```
    
1. Dodaj funkcję do projektu za pomocą następującego `--name` polecenia, gdzie argument jest `--template` unikatową nazwą funkcji, a argument określa wyzwalacz funkcji. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new`tworzy plik kodu HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new`tworzy podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni do wybranego języka projektu i plik konfiguracyjny o nazwie *function.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Opcjonalnie) Sprawdzanie zawartości pliku

W razie potrzeby można przejść do [uruchom funkcję lokalnie](#run-the-function-locally) i zbadać zawartość pliku później.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* `Run` zawiera metodę, która odbiera dane `req` żądania w zmiennej jest [HttpRequest,](/dotnet/api/microsoft.aspnetcore.http.httprequest) który jest ozdobiony **HttpTriggerAttribute**, który definiuje zachowanie wyzwalacza. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Obiekt zwracany jest [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) który zwraca komunikat odpowiedzi jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, zobacz [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* `main()` zawiera funkcję Języka Python, która jest wyzwalana zgodnie z konfiguracją w *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

W przypadku wyzwalacza HTTP funkcja odbiera `req` dane żądania w zmiennej zdefiniowanej w *pliku function.json*. `req`jest wystąpieniem [klasy azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, `$return` zdefiniowany jako w *pliku function.json,* jest wystąpieniem [klasy azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js


*index.js* eksportuje funkcję wyzwalaną zgodnie z konfiguracją w *pliku function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

W przypadku wyzwalacza HTTP funkcja odbiera `req` dane żądania w zmiennej zdefiniowanej w *pliku function.json*. Obiekt zwracany, `$return` zdefiniowany jako w *function.json*, jest odpowiedzią. Aby dowiedzieć się więcej, zobacz [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>indeks.ts

*index.ts* eksportuje funkcję wyzwalaną zgodnie z konfiguracją w *pliku function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

W przypadku wyzwalacza HTTP funkcja odbiera `req` dane żądania w zmiennej typu **HttpRequest** zgodnie z definicją w *pliku function.json*. Obiekt zwracany, `$return` zdefiniowany jako w *function.json*, jest odpowiedzią. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definiuje skrypt funkcji, który jest wyzwalany zgodnie z konfiguracją w *pliku function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

W przypadku wyzwalacza HTTP funkcja odbiera `$Request` dane żądania przekazywane do param zdefiniowanego w *pliku function.json*. Obiekt zwracany, `Response` zdefiniowany jako w *pliku function.json,* jest przekazywany do `Push-OutputBinding` polecenia cmdlet jako odpowiedź. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* jest plikiem konfiguracyjnym `bindings` definiującym dane wejściowe i wyjściowe dla funkcji, w tym typ wyzwalacza. 
::: zone-end

::: zone pivot="programming-language-python"
Można zmienić, `scriptFile` aby wywołać inny plik Języka Python w razie potrzeby.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie [`httpTrigger`](functions-bindings-http-webhook-trigger.md) wejściowe typu [`http`](functions-bindings-http-webhook-output.md)i wyjściowego typu .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie zasobów platformy Azure dla swojej funkcji

Przed wdrożeniem kodu funkcji na platformie Azure należy utworzyć trzy zasoby:

- Grupa zasobów, która jest logicznym kontenerem dla powiązanych zasobów.
- Konto magazynu, które przechowuje stan i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji mapuje do lokalnego projektu funkcji i umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania i udostępniania zasobów.

Aby utworzyć te elementy, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie zapewnia wyjście JSON po zakończeniu.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się na platformę Azure za pomocą polecenia [logowania az:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w `westeurope` regionie. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie w pobliżu, używając dostępnego regionu z `az account list-locations` polecenia).

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.
    ::: zone-end  
    
1. Utwórz ogólne konto magazynu w grupie zasobów i regionie za pomocą [polecenia tworzenia konta magazynu az.](/cli/azure/storage/account#az-storage-account-create) W poniższym przykładzie zastąp `<STORAGE_NAME>` globalnie unikatową nazwą odpowiednią dla Ciebie. Nazwy muszą zawierać tylko od trzech do 24 cyfr i małych liter. `Standard_LRS`określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Konto magazynu wiąże się tylko z kilkoma centami (USD) za ten szybki start.
    
1. Utwórz aplikację funkcji za pomocą polecenia [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) W poniższym przykładzie zastąp `<STORAGE_NAME>` nazwą konta użytego w `<APP_NAME>` poprzednim kroku i zastąp globalnie unikatową nazwą odpowiednią dla Użytkownika. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 

    ::: zone pivot="programming-language-python"  
    Jeśli używasz Pythona 3.8, `--functions_version` `3`zmień `--runtime-version` na `3.8` i na .
    
    Jeśli używasz Pythona 3.6, zmień na `--runtime-version` `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Jeśli używasz node.js 8, `--runtime-version` `8`również zmień na .

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    To polecenie tworzy aplikację funkcji działającą w określonym czasie wykonywania języka w ramach [planu użycia funkcji platformy Azure](functions-scale.md#consumption-plan), która jest bezpłatna dla ilości użycia, które poniesiesz tutaj. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki go nie aktywujesz.
    
## <a name="deploy-the-function-project-to-azure"></a>Wdrażanie projektu funkcji na platformie Azure

::: zone pivot="programming-language-typescript"  
Przed użyciem narzędzi Core Tools do wdrożenia projektu na platformie Azure należy utworzyć gotową do produkcji kompilację plików JavaScript z plików źródłowych TypeScript.

Następujące polecenie przygotowuje projekt TypeScript do wdrożenia:

```
npm run build:production 
```
::: zone-end  

Z niezbędnych zasobów w miejscu, teraz można przystąpić do wdrażania projektu funkcji lokalnych do aplikacji funkcji na platformie Azure przy użyciu [polecenia publikowania func azure functionapp.](functions-run-local.md#project-file-deployment) W poniższym przykładzie zamień `<APP_NAME>` na nazwę aplikacji.

```
func azure functionapp publish <APP_NAME>
```

Jeśli zostanie wyświetlony błąd "Nie można znaleźć aplikacji o nazwie ...", odczekaj kilka sekund i spróbuj `az functionapp create` ponownie, ponieważ platforma Azure może nie zostać w pełni zainicjowana po poprzednim poleceniu.

Polecenie publish pokazuje wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):

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

Ponieważ funkcja używa wyzwalacza HTTP, można wywołać go, wysyłając żądanie HTTP do jego adresu URL w przeglądarce lub za pomocą narzędzia, takiego jak curl. W obu przypadkach `code` parametr URL jest unikatowym [kluczem funkcji,](functions-bindings-http-webhook-trigger.md#authorization-keys) który autoryzuje wywołanie punktu końcowego funkcji.

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL invoke** wyświetlany w danych wyjściowych polecenia `&name=Functions`publikowania na pasku adresu przeglądarki, dołączając parametr kwerendy . Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak po uruchomieniu funkcji lokalnie.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) z **adresem URL invoke**, `&name=Functions`dołączając parametr . Dane wyjściowe polecenia powinny być tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Aby wyświetlić dzienniki w czasie zbliżonym do rzeczywistego dla opublikowanej aplikacji funkcji, użyj [strumienia metryk na żywo usługi Application Insights.](functions-monitoring.md#streaming-logs)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przejdziesz do następnego kroku, [dodaj powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md), zachowaj wszystkie zasoby w miejscu, ponieważ będziesz opierać się na tym, co już zrobiłeś.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie jej zawarte zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Łączenie się z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md)
