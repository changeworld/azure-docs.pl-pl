---
title: Dokumentacja dla deweloperów języka Python dla Azure Functions
description: Informacje na temat tworzenia funkcji w języku Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, dynamiczne obliczenia, architektura bezserwerowa, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 7922f07cfe08d0bd58827b59337b86387c624778
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844689"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dewelopera w języku Python Azure Functions

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu języka Python. W poniższej zawartości przyjęto założenie, że został już odczytany [Przewodnik deweloperów Azure Functions](functions-reference.md). 

Przykładowe projekty funkcji autonomicznych w języku Python znajdują się w sekcji [przykłady funkcji języka Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Model programowania

Azure Functions oczekuje, że funkcja będzie metodą bezstanową w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje metody, która ma być zaimplementowana jako metoda globalna `main()` wywołana `__init__.py` w pliku. Można również [określić alternatywny punkt wejścia](#alternate-entry-point).

Dane z wyzwalaczy i powiązań są powiązane z funkcją za pośrednictwem atrybutów metod przy `name` użyciu właściwości zdefiniowanej w pliku *Function. JSON* . Na przykład, _Funkcja. JSON_ poniżej opisuje prostą funkcję wyzwalaną przez żądanie HTTP o nazwie `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` Plik zawiera następujący kod funkcji:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

można również jawnie zadeklarować typy atrybutów i zwracany typ w funkcji przy użyciu adnotacji typu języka Python. Dzięki temu można korzystać z funkcji IntelliSense i autouzupełniania dostarczonych przez wiele edytorów kodu w języku Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Użyj adnotacji w języku Python zawartych w pakiecie [Azure. Functions. *](/python/api/azure-functions/azure.functions?view=azure-python) , aby powiązać dane wejściowe i wyjściowe z metodami.

## <a name="alternate-entry-point"></a>Alternatywny punkt wejścia

Domyślne zachowanie funkcji można zmienić, opcjonalnie określając `scriptFile` właściwości i `entryPoint` w pliku *Function. JSON* . Na przykład _Funkcja Function. JSON_ poinformuje środowisko uruchomieniowe, aby używała `customentry()` metody w pliku _Main.py_ jako punktu wejścia dla funkcji platformy Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu funkcji języka Python wygląda podobnie do poniższego przykładu:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Istnieje udostępniony plik [host. JSON](functions-host-json.md) , który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma własny plik kodu i plik konfiguracji powiązania (Function. JSON). 

Kod udostępniony powinien być przechowywany w osobnym folderze. Aby odwoływać się do modułów w folderze SharedCode, można użyć następującej składni:

```
from __app__.SharedCode import myFirstHelperFunction
```

Aby odwołać się do modułów lokalnych do funkcji, można użyć składni względnego importu w następujący sposób:

```
from . import example
```

Podczas wdrażania projektu funkcji w aplikacji funkcji na platformie Azure cała zawartość folderu *FunctionApp* powinna być dołączona do pakietu, ale nie do samego folderu.

## <a name="triggers-and-inputs"></a>Wyzwalacze i dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w Azure Functions: Wyzwól dane wejściowe i dodatkowe. Chociaż różnią się one w `function.json` pliku, użycie jest identyczne w kodzie języka Python.  Parametry połączenia lub wpisy tajne dla wyzwalacza i źródła danych wejściowych są `local.settings.json` mapowane na wartości w pliku podczas uruchamiania lokalnego oraz ustawienia aplikacji podczas uruchamiania na platformie Azure. 

Na przykład poniższy kod ilustruje różnicę między tymi dwoma:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Po wywołaniu funkcji żądanie HTTP jest przekazywane do tej funkcji jako `req`. Wpis zostanie pobrany z usługi Azure Blob Storage w oparciu o _Identyfikator_ w adresie URL trasy i udostępniony jako `obj` w treści funkcji.  W tym miejscu określone konto magazynu to ciąg połączenia, w `AzureWebJobsStorage` którym znajduje się to samo konto magazynu używane przez aplikację funkcji.


## <a name="outputs"></a>outputs

Dane wyjściowe można wyrazić zarówno w wartości zwracanej, jak i wyjściowych. Jeśli istnieje tylko jedno wyjście, zalecamy użycie wartości zwracanej. W przypadku wielu wyjść należy użyć parametrów wyjściowych.

Aby użyć wartości zwracanej funkcji jako wartości powiązania danych wyjściowych, `name` właściwość powiązania powinna być ustawiona na `$return` wartość w `function.json`.

Aby utworzyć wiele wyjść, użyj `set()` metody dostarczonej [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) przez interfejs do przypisania wartości do powiązania. Na przykład następująca funkcja może wypchnąć komunikat do kolejki i zwrócić odpowiedź HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Rejestrowanie

Dostęp do rejestratora środowiska uruchomieniowego Azure Functions jest dostępny za [`logging`](https://docs.python.org/3/library/logging.html#module-logging) pośrednictwem głównego programu obsługi w aplikacji funkcji. Ten rejestrator jest powiązany z Application Insights i pozwala na Flagowanie ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład rejestruje komunikat informacyjny, gdy funkcja jest wywoływana za pośrednictwem wyzwalacza HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Dostępne są dodatkowe metody rejestrowania umożliwiające zapisanie w konsoli na różnych poziomach śledzenia:

| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapisuje komunikat o poziomie KRYTYCZNym w rejestratorze głównym.  |
| **`error(_message_)`**   | Zapisuje komunikat z BŁĘDem poziomu w rejestratorze głównym.    |
| **`warning(_message_)`**    | Zapisuje komunikat z OSTRZEŻENIEm dotyczącym poziomu rejestratora głównego.  |
| **`info(_message_)`**    | Zapisuje komunikat z informacjami o poziomie w rejestratorze głównym.  |
| **`debug(_message_)`** | Zapisuje komunikat z DEBUGOWANIEm na poziomie w rejestratorze głównym.  |

Aby dowiedzieć się więcej o rejestrowaniu, zobacz [Monitor Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Wyzwalacz i powiązania HTTP

Wyzwalacz HTTP jest zdefiniowany w pliku Function. Jan. `name` Powiązanie musi być zgodne z nazwanym parametrem w funkcji. W poprzednich przykładach jest używana nazwa `req` powiązania. Ten parametr jest obiektem [HttpRequest] i zwracany jest obiekt [HttpResponse] .

Z obiektu [HttpRequest] można uzyskać nagłówki żądań, parametry zapytania, parametry tras i treść wiadomości. 

Poniższy przykład pochodzi z [szablonu wyzwalacza http dla języka Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

W tej funkcji wartość `name` parametru zapytania jest uzyskiwana `params` z parametru obiektu [HttpRequest] . Treść komunikatu zakodowanego w formacie JSON jest odczytywana `get_json` przy użyciu metody. 

Analogicznie, można ustawić `status_code` i `headers` dla komunikatu odpowiedzi w zwracanym obiekcie [HttpResponse] .
                                                              
## <a name="async"></a>Asynchroniczne

Zalecamy pisanie funkcji platformy Azure jako procedury asynchronicznej przy użyciu `async def` instrukcji.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Jeśli funkcja Main () jest synchroniczna (brak kwalifikatora), automatycznie uruchamiamy funkcję w `asyncio` puli wątków.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji podczas wykonywania, należy uwzględnić [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) argument w jego podpisie. 

Przykład:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klasa [**kontekstu**](/python/api/azure-functions/azure.functions.context?view=azure-python) ma następujące metody:

`function_directory`  
Katalog, w którym działa funkcja.

`function_name`  
Nazwa funkcji.

`invocation_id`  
Identyfikator bieżącego wywołania funkcji.

## <a name="global-variables"></a>Zmienne globalne

Nie ma gwarancji, że stan aplikacji zostanie zachowany do przyszłych wykonań. Jednak środowisko uruchomieniowe Azure Functions często używa tego samego procesu dla wielu wykonań tej samej aplikacji. Aby buforować wyniki kosztownych obliczeń, należy zadeklarować ją jako zmienną globalną. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień, `import os` deklarując, a następnie `setting = os.environ["setting-name"]`używając,.

Poniższy przykład pobiera [ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)z kluczem o nazwie `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

W przypadku programowania lokalnego ustawienia aplikacji są [przechowywane w pliku Local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version-and-package-management"></a>Zarządzanie wersjami i pakietami w języku Python

Obecnie Azure Functions obsługuje tylko język Python 3.6. x (oficjalna dystrybucja CPython).

Podczas programowania lokalnego przy użyciu Azure Functions Core Tools lub Visual Studio Code Dodaj nazwy i wersje wymaganych pakietów do `requirements.txt` pliku i zainstaluj je za pomocą polecenia. `pip`

Na przykład można użyć następującego pliku wymagań i polecenia PIP, aby zainstalować `requests` pakiet z PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Gdy wszystko jest gotowe do opublikowania, upewnij się, że wszystkie zależności są wymienione w pliku *Requirements. txt* , który znajduje się w katalogu głównym katalogu projektu. Azure Functions można [zdalnie kompilować](functions-deployment-technologies.md#remote-build) te zależności.

Pliki projektu i foldery, które są wykluczone z publikowania, łącznie z folderem środowiska wirtualnego, są wymienione w pliku. funcignore.  

Aby wdrożyć platformę Azure i przeprowadzić kompilację zdalną, użyj następującego polecenia:

```bash
func azure functionapp publish <app name> --build remote
```

Jeśli nie korzystasz z kompilacji zdalnej i korzystasz z pakietu wymagającego kompilatora i nie obsługuje on instalacji wielu kół zgodnych z systemem Linux z PyPI, publikowanie na platformie Azure bez tworzenia lokalnie zakończy się niepowodzeniem z powodu następującego błędu:

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Aby utworzyć lokalnie i skonfigurować wymagane pliki binarne, [Zainstaluj platformę Docker](https://docs.docker.com/install/) na komputerze lokalnym i uruchom następujące polecenie w celu opublikowania przy użyciu [Azure Functions Core Tools](functions-run-local.md#v2) (Func). Pamiętaj, aby `<app name>` zamienić na nazwę aplikacji funkcji na platformie Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Pod względem okładek podstawowe narzędzia będą używać platformy Docker do uruchamiania obrazu [MCR.Microsoft.com/Azure-Functions/Python](https://hub.docker.com/r/microsoft/azure-functions/) jako kontenera na komputerze lokalnym. Korzystając z tego środowiska, program utworzy i zainstaluje wymagane moduły z dystrybucji źródłowej przed ich spakowaniem w celu wdrożenia końcowego na platformie Azure.

Aby skompilować zależności i publikować przy użyciu systemu ciągłego dostarczania, [użyj Azure Pipelines](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Testowanie jednostkowe

Funkcje w języku Python można testować podobnie jak w przypadku innych kodów języka Python przy użyciu standardowych platform testowania. W przypadku większości powiązań można utworzyć obiekt wejściowy imitacji, tworząc wystąpienie odpowiedniej klasy z `azure.functions` pakietu. Ponieważ pakiet nie jest natychmiast dostępny, należy go zainstalować `requirements.txt` za pośrednictwem pliku zgodnie z opisem w powyższej sekcji dotyczącej [wersji i zarządzania pakietami języka Python](#python-version-and-package-management) . [`azure.functions`](https://pypi.org/project/azure-functions/)

Na przykład poniżej znajduje się przykładowy test funkcji wyzwalanej przez protokół HTTP:

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
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

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Oto kolejny przykład z funkcją wyzwalaną przez kolejkę:

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

Wszystkie znane problemy i żądania funkcji są śledzone za pomocą listy [problemów usługi GitHub](https://github.com/Azure/azure-functions-python-worker/issues) . Jeśli napotkasz problem i nie możesz znaleźć problemu w usłudze GitHub, Otwórz nowy problem i podaj szczegółowy opis problemu.

### <a name="cross-origin-resource-sharing"></a>Współużytkowanie zasobów między źródłami

Azure Functions obsługuje udostępnianie zasobów między źródłami (CORS). Mechanizm CORS jest konfigurowany [w portalu](functions-how-to-use-azure-function-app-settings.md#cors) i za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/cors). Lista źródeł dozwolonych CORS jest stosowana na poziomie aplikacji funkcji. Po włączeniu funkcji CORS odpowiedzi zawierają `Access-Control-Allow-Origin` nagłówek. Aby uzyskać więcej informacji, zobacz temat [Współużytkowanie zasobów między źródłami](functions-how-to-use-azure-function-app-settings.md#cors).

Lista dozwolonych źródeł [nie jest obecnie obsługiwana](https://github.com/Azure/azure-functions-python-worker/issues/444) dla aplikacji funkcji języka Python. Ze względu na to ograniczenie należy wyraźnie ustawić `Access-Control-Allow-Origin` nagłówek w funkcjach http, jak pokazano w następującym przykładzie:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Upewnij się, że Zaktualizowano również funkcję Function. JSON, aby obsługiwała metodę HTTP:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Ta metoda jest używana przez przeglądarkę Chrome do negocjowania listy dozwolonych źródeł. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące zasoby:

* [Dokumentacja interfejsu API pakietu Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Powiązania magazynu obiektów BLOB](functions-bindings-storage-blob.md)
* [Powiązania protokołu HTTP i elementu webhook](functions-bindings-http-webhook.md)
* [Kolejki powiązań magazynu](functions-bindings-storage-queue.md)
* [Wyzwalacz czasomierza](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
