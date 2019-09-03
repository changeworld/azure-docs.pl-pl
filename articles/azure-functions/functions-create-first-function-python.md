---
title: Tworzenie funkcji wyzwalanej przez protokół HTTP na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję języka Python na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: cb7f5a10169c8baaecae0fc1916a439d61bfbf7c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170877"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP na platformie Azure

W tym artykule pokazano, jak za pomocą narzędzi wiersza polecenia utworzyć projekt w języku Python, który działa w Azure Functions. Utworzona funkcja jest wyzwalana przez żądania HTTP. Na koniec opublikujesz projekt do uruchamiania jako [Funkcja](functions-scale.md#consumption-plan) bezserwerowa na platformie Azure.

Ten artykuł jest pierwszym z dwóch przewodników szybki start dla Azure Functions. Po wykonaniu tego artykułu należy [dodać powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md) do funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

+ Zainstaluj język [Python 3.6. x](https://www.python.org/downloads/).

+ Zainstaluj program [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.7.1575 lub nowszej.

+ Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2. x lub nowszej.

+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Tworzenie i aktywowanie środowiska wirtualnego (opcjonalnie)

Aby lokalnie opracowywać i testować funkcje języka Python, zaleca się używanie środowiska Python 3,6. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

### <a name="bash"></a>Bash

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Program PowerShell lub wiersz polecenia systemu Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Pozostałe polecenia są uruchamiane w środowisku wirtualnym.

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Projekt funkcji jest odpowiednikiem aplikacji funkcji na platformie Azure. Może mieć wiele funkcji, które współużytkują te same konfiguracje lokalne i hostingowe.

W środowisku wirtualnym Uruchom następujące polecenie, wybierając język **Python** jako środowisko uruchomieniowe procesu roboczego.

```console
func init MyFunctionProj
```

Zostanie utworzony folder o nazwie _MyFunctionProj_ , który zawiera następujące trzy pliki:

* `local.settings.json`służy do przechowywania ustawień aplikacji i parametrów połączenia podczas lokalnego uruchamiania. Ten plik nie jest publikowany na platformie Azure.
* `requirements.txt`zawiera listę pakietów do zainstalowania podczas publikowania na platformie Azure.
* `host.json`zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje w aplikacji funkcji. Ten plik jest publikowany na platformie Azure.

Przejdź do nowego folderu MyFunctionProj:

```console
cd MyFunctionProj
```

## <a name="create-a-function"></a>Utwórz funkcję

Aby dodać funkcję do projektu, uruchom następujące polecenie:

```console
func new
```

Wybierz szablon **wyzwalacza http** , wpisz `HttpTrigger` jako nazwę funkcji, a następnie naciśnij klawisz ENTER.

Zostanie utworzony podfolder o nazwie _HttpTrigger_ , który zawiera następujące pliki:

* **Function. JSON**: plik konfiguracji, który definiuje funkcję, wyzwalacz i inne powiązania. Przejrzyj ten plik i sprawdź, czy wartość `scriptFile` wskazuje plik zawierający funkcję, podczas gdy wyzwalacz wywołania i powiązania są zdefiniowane `bindings` w tablicy.

  Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz http ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook.md#trigger) i powiązania danych wyjściowych typu. [`http`](functions-bindings-http-webhook.md#output)

* **init.PR\_: plik skryptu, który jest funkcją wyzwalaną przez protokół http.\_ \_ \_** Przejrzyj ten skrypt i sprawdź, czy zawiera on domyślny `main()`. Dane http z wyzwalacza są przesyłane do tej funkcji przy użyciu `req` nazwanego parametru powiązania. Zdefiniowane w funkcji Function. JSON `req` jest wystąpieniem [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Obiekt zwracany, zdefiniowany jako `$return` w funkcji Function. JSON, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Następujące polecenie uruchamia aplikację funkcji, która jest uruchamiana lokalnie przy użyciu tego samego Azure Functions środowiska uruchomieniowego, które znajduje się na platformie Azure.

```console
func host start
```

Po uruchomieniu hosta usługi Functions zapisuje informacje podobne do następujących, które zostały obcięte pod kątem czytelności:

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

Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Poniżej pokazano wyświetloną w przeglądarce odpowiedź na żądanie GET zwróconą przez funkcję lokalną:

![Testowanie lokalne w przeglądarce](./media/functions-create-first-function-python/function-test-local-browser.png)

Teraz, gdy funkcja została uruchomiona lokalnie, możesz utworzyć aplikację funkcji i inne wymagane zasoby na platformie Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Tworzenie aplikacji funkcji na platformie Azure

Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi.

Uruchom poniższe polecenie, używając unikatowej nazwy aplikacji funkcji zamiast symbolu zastępczego `<APP_NAME>` i nazwy konta magazynu zamiast symbolu zastępczego `<STORAGE_NAME>`. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> Aplikacje dla systemów Linux i Windows nie mogą być hostowane w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `myResourceGroup` przy użyciu aplikacji funkcji systemu Windows lub aplikacji sieci Web, musisz użyć innej grupy zasobów.

To polecenie spowoduje również udostępnienie skojarzonego wystąpienia usługi Azure Application Insights w tej samej grupie zasobów, która może być używana do monitorowania i wyświetlania dzienników.

Teraz możesz przystąpić do publikowania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

Po utworzeniu aplikacji funkcji na platformie Azure Możesz użyć [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) podstawowego narzędzia do wdrożenia kodu projektu na platformie Azure. W tych przykładach Zamień `<APP_NAME>` na nazwę aplikacji z poprzedniego kroku.

```command
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` Opcja kompiluje projekt języka Python zdalnie na platformie Azure z plików w pakiecie wdrożeniowym. 

Zobaczysz dane wyjściowe podobne do następujących, które zostały obcięte pod kątem czytelności:

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

`Invoke url` Skopiuj wartość `HttpTrigger`dla elementu, którego możesz teraz użyć do testowania funkcji na platformie Azure. Adres URL zawiera `code` wartość ciągu zapytania, który jest kluczem funkcji. Ten klucz utrudnia innym osobom wywoływanie punktu końcowego wyzwalacza HTTP na platformie Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Aby przeglądać dzienniki niemal w czasie rzeczywistym dla opublikowanej aplikacji języka Python, zalecamy użycie [Live Metrics Stream Application Insights](functions-monitoring.md#streaming-logs)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Python z funkcją wyzwalaną przez protokół HTTP, uruchom go na komputerze lokalnym i wdrożony na platformie Azure. Teraz możesz rozłożyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-python.md)
