---
title: Tworzenie funkcji wyzwalanej przez protokół HTTP w systemie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję języka Python na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 5ef30fbf647492f79c64508d8306868aa1f6b278
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444578"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP w systemie Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

W tym artykule pokazano, jak używać narzędzi wiersza polecenia do tworzenia projektu języka Python, który jest uruchamiany w usłudze Azure Functions. Funkcja, tworzonych jest wyzwalana przez żądania HTTP. Na koniec opublikować projekt w taki sposób, aby był uruchamiany jako [funkcję niewymagającą użycia serwera](functions-scale.md#consumption-plan) na platformie Azure.

Ten artykuł jest to pierwsza z dwóch przewodnikom Szybki Start dla usługi Azure Functions. Po ukończeniu tego artykułu możesz [Dodawanie usługi Azure Storage powiązania danych wyjściowych kolejki](functions-add-output-binding-storage-queue-python.md) do funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wykonywania tej procedury, musisz mieć następujące czynności:

+ Zainstaluj język [Python 3.6](https://www.python.org/downloads/).

+ Zainstaluj [podstawowych narzędzi usługi Azure Functions](./functions-run-local.md#v2) wersji 2.6.1071 lub nowszej.

+ Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) wersji 2.x lub nowszej wersji.

+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego

Aby lokalnie rozwijać i przetestować funkcje języka Python, możesz pracować w środowisku Python 3.6. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.env`.

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Lub wiersza polecenia Windows PowerShell:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Pozostałe polecenia są uruchamiane w środowisku wirtualnym.

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Projekt funkcji jest odpowiednikiem aplikacji funkcji na platformie Azure. Może mieć wiele funkcji, mających takie same konfiguracje lokalnych i hostingu.

W środowisku wirtualnym, uruchom następujące polecenia, wybierając **python** jako środowisko uruchomieniowe usługi procesu roboczego.

```console
func init MyFunctionProj
```

Folder o nazwie _MyFunctionProj_ zostanie utworzony, który zawiera trzy następujące pliki:

* `local.settings.json` Służy do przechowywania ustawień aplikacji i parametry połączenia podczas uruchamiania lokalnego. Ten plik nie uzyskać opublikowanej na platformie Azure.
* `requirements.txt` zawiera listę pakietów, należy zainstalować na publikowanie na platformie Azure.
* `host.json` zawiera opcje konfiguracji globalnej, które wpływają na wszystkie funkcje w aplikacji funkcji. Ten plik, Pobierz opublikowanej na platformie Azure.

Przejdź do nowego folderu MyFunctionProj:

```console
cd MyFunctionProj
```

Następnie należy zaktualizować pliku host.json, aby umożliwić rozszerzenie pakiety.  

## <a name="create-a-function"></a>Tworzenie funkcji

Aby dodać funkcję do projektu, uruchom następujące polecenie:

```console
func new
```

Wybierz **wyzwalacza HTTP** szablonu, należy wpisać `HttpTrigger` jako nazwy funkcji, naciśnij klawisz Enter.

Podfolder o nazwie _HttpTrigger_ zostanie utworzony, który zawiera następujące pliki:

* **Function.JSON**: plik konfiguracji, który definiuje funkcja, wyzwalacz i innych powiązań. Ten plik i zobacz, że wartość `scriptFile` wskazuje plik zawierający funkcję, podczas gdy wywołania wyzwalaczy i powiązań, które są zdefiniowane w `bindings` tablicy.

  Każde powiązanie wymaga kierunku, typ i unikatową nazwę. Wyzwalacz HTTP ma powiązanie danych wejściowych typu [ `httpTrigger` ](functions-bindings-http-webhook.md#trigger) i powiązania typu danych wyjściowych [ `http` ](functions-bindings-http-webhook.md#output).

* **\_\_init\_\_.py**: funkcja wyzwalana przez plik skryptu, który jest przez protokół HTTP. Przejrzyj ten skrypt i zobacz, czy zawiera on domyślnie `main()`. Dane HTTP z wyzwalacza jest przekazywany do tej funkcji za pomocą `req` o nazwie parametru wiązania. Zdefiniowane w function.json, `req` jest wystąpieniem [klasy azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Return, zdefiniowanego obiektu jako `$return` w function.json, jest wystąpieniem [klasy azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [usługi Azure Functions HTTP wyzwalaczy i powiązań](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Następujące polecenie uruchamia aplikację funkcji, który jest uruchamiany lokalnie przy użyciu tego samego środowiska usługi Azure Functions runtime, która znajduje się w usłudze Azure.

```bash
func host start
```

Po uruchomieniu hosta funkcji zapisuje podobną do następujących danych wyjściowych, który został skrócony tak, aby zwiększyć czytelność:

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

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
> Usługa Azure Functions planu zużycie dla systemu Linux jest obecnie w wersji zapoznawczej i jest dostępna tylko w następujących regionach: Zachodnie stany USA, wschodnie stany USA, Europa Zachodnia, Azja Wschodnia. Ponadto aplikacje dla systemu Linux i Windows, nie mogą być hostowane w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `myResourceGroup` za pomocą aplikacji funkcji Windows lub aplikacji sieci web, należy użyć innej grupy zasobów.

Teraz możesz przystąpić do opublikowania projektu funkcji lokalnych do aplikacji funkcji na platformie Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu projektu funkcji języka Python za pomocą funkcji wyzwalanej przez protokół HTTP, uruchom go na komputerze lokalnym i wdrażać je na platformie Azure. Teraz można rozszerzyć przez funkcję...

> [!div class="nextstepaction"]
> [Dodawanie usługi Azure Storage powiązania danych wyjściowych kolejki](functions-add-output-binding-storage-queue-python.md)
