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
ms.openlocfilehash: d74d1c33816b3c028a26335af4c6d5b23b7a2046
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958487"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dewelopera w języku Python Azure Functions

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu języka Python. W poniższej zawartości przyjęto założenie, że został już odczytany [Przewodnik deweloperów Azure Functions](functions-reference.md). 

Przykładowe projekty funkcji autonomicznych w języku Python znajdują się w sekcji [przykłady funkcji języka Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Model programowania

Azure Functions oczekuje, że funkcja będzie metodą bezstanową w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje metody, która ma być zaimplementowana jako metoda globalna o nazwie `main()` w pliku `__init__.py`. Można również [określić alternatywny punkt wejścia](#alternate-entry-point).

Dane z wyzwalaczy i powiązań są powiązane z funkcją za pośrednictwem atrybutów metody przy użyciu właściwości `name` zdefiniowanej w pliku *Function. JSON* . Na przykład, _Funkcja. JSON_ poniżej opisuje prostą funkcję wyzwalaną przez żądanie HTTP o nazwie `req`:

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

Plik `__init__.py` zawiera następujący kod funkcji:

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

Domyślne zachowanie funkcji można zmienić, opcjonalnie określając `scriptFile` i `entryPoint` właściwości w pliku *Function. JSON* . Na przykład plik _Function. JSON_ poniżej nakazuje środowisko uruchomieniowe użycie metody `customentry()` w pliku _Main.py_ jako punktu wejścia dla funkcji platformy Azure.

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

Dane wejściowe są podzielone na dwie kategorie w Azure Functions: Wyzwól dane wejściowe i dodatkowe. Chociaż różnią się one w pliku `function.json`, użycie jest identyczne w kodzie języka Python.  Parametry połączenia lub wpisy tajne dla wyzwalacza i źródła danych wejściowych są mapowane na wartości w pliku `local.settings.json` podczas uruchamiania lokalnego oraz ustawienia aplikacji podczas uruchamiania na platformie Azure. 

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

Gdy funkcja jest wywoływana, żądanie HTTP jest przesyłane do funkcji jako `req`. Wpis zostanie pobrany z usługi Azure Blob Storage w oparciu o _Identyfikator_ w adresie URL trasy i udostępniony jako `obj` w treści funkcji.  W tym miejscu określone konto magazynu to parametry połączenia Znalezione w programie, które jest tym samym kontem magazynu, które jest używane przez aplikację funkcji.


## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe można wyrazić zarówno w wartości zwracanej, jak i wyjściowych. Jeśli istnieje tylko jedno wyjście, zalecamy użycie wartości zwracanej. W przypadku wielu wyjść należy użyć parametrów wyjściowych.

Aby użyć wartości zwracanej przez funkcję jako wartości powiązania danych wyjściowych, właściwość `name` powiązania powinna być ustawiona na `$return` w `function.json`.

Aby wygenerować wiele danych wyjściowych, należy użyć metody `set()` dostarczonej przez interfejs [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) do przypisania wartości do powiązania. Na przykład następująca funkcja może wypchnąć komunikat do kolejki i zwrócić odpowiedź HTTP.

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

Dostęp do rejestratora środowiska uruchomieniowego Azure Functions jest dostępny za pośrednictwem głównego programu obsługi [`logging`](https://docs.python.org/3/library/logging.html#module-logging) w aplikacji funkcji. Ten rejestrator jest powiązany z Application Insights i pozwala na Flagowanie ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

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

Wyzwalacz HTTP jest zdefiniowany w pliku Function. Jan. @No__t-0 powiązania musi być zgodna z nazwanym parametrem w funkcji. W poprzednich przykładach nazwa powiązania `req` jest używana. Ten parametr jest obiektem [HttpRequest] i zwracany jest obiekt [HttpResponse] .

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

W tej funkcji wartość parametru zapytania `name` jest uzyskiwana z parametru `params` obiektu [HttpRequest] . Treść komunikatu zakodowanego w formacie JSON jest odczytywana przy użyciu metody `get_json`. 

Analogicznie, można ustawić `status_code` i `headers` dla komunikatu odpowiedzi w zwracanym obiekcie [HttpResponse] .

## <a name="concurrency"></a>Współbieżność

Domyślnie środowisko uruchomieniowe języka Python może przetwarzać tylko jedno wywołanie funkcji w danym momencie. Ten poziom współbieżności może być niewystarczający w przypadku co najmniej jednego z następujących warunków:

+ Próbujesz obsłużyć wiele wywołań w tym samym czasie.
+ Przetwarzasz dużą liczbę zdarzeń we/wy.
+ Twoja aplikacja jest powiązana ze we/wy.

W takich sytuacjach można poprawić wydajność, uruchamiając asynchronicznie i używając wielu procesów roboczych języka.  

### <a name="async"></a>Asynchroniczne

Zalecamy użycie instrukcji `async def`, aby funkcja była uruchamiana jako procedura asynchroniczna.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Gdy funkcja `main()` jest synchroniczna (bez kwalifikatora `async`), funkcja jest automatycznie uruchamiana w puli wątków `asyncio`.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Korzystanie z wielu procesów roboczych języka

Domyślnie każde wystąpienie hosta funkcji ma proces roboczy o pojedynczym języku. Jednak obsługa wielu procesów roboczych języka dla każdego wystąpienia hosta. Wywołania funkcji mogą być następnie dystrybuowane równomiernie między tymi procesami roboczymi. Aby zmienić tę wartość, użyj ustawienia aplikacji [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . 

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji podczas wykonywania, należy uwzględnić w podpisie argument [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) . 

Na przykład:

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

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień, deklarując `import os`, a następnie używając `setting = os.environ["setting-name"]`.

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

Podczas programowania lokalnego przy użyciu Azure Functions Core Tools lub Visual Studio Code Dodaj nazwy i wersje wymaganych pakietów do pliku `requirements.txt` i zainstaluj je przy użyciu `pip`.

Na przykład można użyć poniższego pliku wymagań i polecenia PIP, aby zainstalować pakiet `requests` z PyPI.

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

Aby utworzyć lokalnie i skonfigurować wymagane pliki binarne, [Zainstaluj platformę Docker](https://docs.docker.com/install/) na komputerze lokalnym i uruchom następujące polecenie w celu opublikowania przy użyciu [Azure Functions Core Tools](functions-run-local.md#v2) (Func). Pamiętaj, aby zastąpić `<app name>` nazwą swojej aplikacji funkcji na platformie Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Pod względem okładek podstawowe narzędzia będą używać platformy Docker do uruchamiania obrazu [MCR.Microsoft.com/Azure-Functions/Python](https://hub.docker.com/r/microsoft/azure-functions/) jako kontenera na komputerze lokalnym. Korzystając z tego środowiska, program utworzy i zainstaluje wymagane moduły z dystrybucji źródłowej przed ich spakowaniem w celu wdrożenia końcowego na platformie Azure.

Aby skompilować zależności i publikować przy użyciu systemu ciągłego dostarczania, [użyj Azure Pipelines](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Testowanie jednostkowe

Funkcje w języku Python można testować podobnie jak w przypadku innych kodów języka Python przy użyciu standardowych platform testowania. W przypadku większości powiązań można utworzyć obiekt wejściowy imitacji, tworząc wystąpienie odpowiedniej klasy z pakietu `azure.functions`. Ponieważ pakiet [`azure.functions`](https://pypi.org/project/azure-functions/) nie jest natychmiast dostępny, należy go zainstalować za pośrednictwem pliku `requirements.txt`, zgodnie z opisem w powyższej sekcji dotyczącej [wersji i zarządzania pakietami języka Python](#python-version-and-package-management) .

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

Azure Functions obsługuje udostępnianie zasobów między źródłami (CORS). Mechanizm CORS jest konfigurowany [w portalu](functions-how-to-use-azure-function-app-settings.md#cors) i za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/cors). Lista źródeł dozwolonych CORS jest stosowana na poziomie aplikacji funkcji. Po włączeniu funkcji CORS odpowiedzi zawierają nagłówek `Access-Control-Allow-Origin`. Aby uzyskać więcej informacji, zobacz temat [Współużytkowanie zasobów między źródłami](functions-how-to-use-azure-function-app-settings.md#cors).

Lista dozwolonych źródeł [nie jest obecnie obsługiwana](https://github.com/Azure/azure-functions-python-worker/issues/444) dla aplikacji funkcji języka Python. Ze względu na to ograniczenie należy jawnie ustawić nagłówek `Access-Control-Allow-Origin` w funkcjach HTTP, jak pokazano w następującym przykładzie:

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

Więcej informacji zawierają następujące zasoby:

* [Dokumentacja interfejsu API pakietu Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Powiązania magazynu obiektów BLOB](functions-bindings-storage-blob.md)
* [Powiązania protokołu HTTP i elementu webhook](functions-bindings-http-webhook.md)
* [Kolejki powiązań magazynu](functions-bindings-storage-queue.md)
* [Wyzwalacz czasomierza](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
