---
title: Dokumentacja dla deweloperów języka Python dla usługi Azure Functions
description: Zrozumienie, jak tworzyć funkcje za pomocą języka Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bezserwerowa, języka python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 249e5ac33b1420ada2cda45ea729471351f21adf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341988"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions Python

Ten artykuł stanowi wprowadzenie do projektowania usługi Azure Functions przy użyciu języka Python. Zawartość poniżej założono, że zostały już przeczytane [przewodnik dla deweloperów usługi Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Model programowania

Funkcja platformy Azure powinna być bezstanowe metody w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje, że metoda można zaimplementować jako metoda globalna o nazwie `main()` w `__init__.py` pliku.

Można zmienić konfigurację domyślną, określając `scriptFile` i `entryPoint` właściwości w *function.json* pliku. Na przykład _function.json_ informuje, poniżej środowiska uruchomieniowego do użycia `customentry()` method in Class metoda _main.py_ pliku jako punkt wejścia dla funkcji platformy Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Dane z wyzwalaczami i powiązaniami jest powiązany z funkcji za pomocą atrybutów metody przy użyciu `name` właściwości zdefiniowanych w *function.json* pliku. Na przykład _function.json_ poniżej opisano prostej funkcji wyzwalanej przez żądanie HTTP o nazwie `req`:

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

Opcjonalnie korzystać z technologii intellisense i funkcji automatycznego uzupełniania, dostarczone przez Edytor kodu, można również zadeklarować typ atrybutu, który i zwracany typ funkcji za pomocą języka Python typu adnotacji. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Korzystanie z adnotacji Python objęte [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) pakietu, aby powiązać dane wejściowe i wyjściowe metody.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektów języka Python funkcje wygląda podobnie do poniższego:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Brak udostępnionej [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu i pliku konfiguracji powiązania (function.json). 

Udostępniony kod powinny być przechowywane w oddzielnym folderze. Aby odwoływać się do modułów w folderze SharedCode, można użyć następującej składni:

```
from __app__.SharedCode import myFirstHelperFunction
```

Podczas wdrażania projektu funkcji do aplikacji funkcji na platformie Azure, całą zawartość *FunctionApp* folderu powinny być uwzględnione w pakiecie, ale nie sam folder.

## <a name="triggers-and-inputs"></a>Wyzwalacze i dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: dane wejściowe wyzwalacza i dodatkowych danych wejściowych. Mimo że są inne w przypadku `function.json` pliku, użycie jest identyczna w kodzie języka Python.  Parametry połączenia lub wpisów tajnych dla źródeł danych wejściowych i wyzwalaczy mapy do wartości w `local.settings.json` pliku podczas uruchamiania lokalnego oraz ustawienia aplikacji, podczas uruchamiania na platformie Azure. 

Na przykład poniższy kod ilustruje różnicę między nimi:

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

Po wywołaniu funkcji żądanie HTTP jest przekazywane do tej funkcji jako `req`. Wpis zostanie pobrany z usługi Azure Blob Storage, na podstawie _identyfikator_ w adresie URL trasy i udostępniane jako `obj` w treści funkcji.  W tym miejscu konta magazynu określony ciąg połączenia znajduje się w `AzureWebJobsStorage` czyli tego samego konta magazynu używany przez aplikację funkcji.


## <a name="outputs"></a>outputs

Dane wyjściowe mogą być wyrażone zarówno w zwracanej wartości i parametry wyjściowe. Jeśli istnieje tylko jedno wyjście, zaleca się przy użyciu wartości zwracanej. Dla wiele wyjść należy użyć parametrów wyjściowych.

Używać zwracanej wartości funkcji z wartością powiązania danych wyjściowych `name` powinna być równa właściwości powiązania `$return` w `function.json`.

Aby utworzyć wiele wyjść, należy użyć `set()` metody dostarczone przez `azure.functions.Out` interfejsu, aby przypisać wartość do powiązania. Na przykład następującą funkcję można wypchnąć komunikat do kolejki i również zwrócić odpowiedź HTTP.

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

Dostęp do rejestratora środowiska uruchomieniowego usługi Azure Functions jest dostępny za pośrednictwem głównego [ `logging` ](https://docs.python.org/3/library/logging.html#module-logging) obsługi w aplikacji funkcji. Tego rejestratora jest powiązany z usługi Application Insights i pozwala flagi ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład rejestruje komunikat informacyjny, gdy funkcja jest wywoływana przy użyciu wyzwalacza HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Dostępne są dodatkowe metody rejestrowania umożliwiające zapisu do konsoli w różnymi poziomami śledzenia:

| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| rejestrowanie. **krytyczne (_komunikat_)**   | Zapisuje komunikat o KRYTYCZNYM poziomie na rejestratora głównego.  |
| rejestrowanie. **błąd (_komunikat_)**   | Zapisuje komunikat z powodu błędu w poziomie na rejestratora głównego.    |
| rejestrowanie. **ostrzeżenie (_komunikat_)**    | Zapisuje komunikat z OSTRZEŻENIEM poziomu na rejestratora głównego.  |
| rejestrowanie. **info (_komunikat_)**    | Zapisuje komunikat z informacjami o poziomie na rejestratora głównego.  |
| logging.**debug(_message_)** | Zapisuje komunikat z poziomu debugowania na rejestratora głównego.  |

## <a name="async"></a>asynchroniczne

Firma Microsoft zaleca pisania funkcji platformy Azure co w koprocedury asynchronicznego `async def` instrukcji.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

W przypadku funkcji main() synchroniczne (nie `async` kwalifikator) firma Microsoft automatycznie uruchomić funkcję `asyncio` puli wątków.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji w trakcie wykonywania, należy dołączyć `context` argumentu w podpisie. 

Na przykład:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**Kontekstu** klasa ma następujące metody:

`function_directory`  
Katalog, w którym jest uruchomiona funkcja.

`function_name`  
Nazwa funkcji.

`invocation_id`  
Identyfikator bieżącego wywołania funkcji.

## <a name="global-variables"></a>Zmienne globalne

Nie gwarantuje, że stan aplikacji zostaną zachowane dla przyszłych wykonań. Środowisko uruchomieniowe usługi Azure Functions używa często ten sam proces wiele wykonań tej samej aplikacji. W celu buforowania wyników obliczeń kosztowne, Zadeklaruj go jako zmienną globalną. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Zarządzanie wersji i pakiet języka Python

Obecnie usługa Azure Functions obsługuje tylko Python 3.6.x (oficjalna CPython dystrybucja).

Podczas tworzenia lokalnie przy użyciu podstawowych narzędzi usługi Azure Functions lub Visual Studio Code, Dodaj nazwy i wersji wymagane pakiety do `requirements.txt` pliku, a następnie zainstaluj je przy użyciu `pip`.

Na przykład wymagania dotyczące konfiguracji narzędzia pip i pliku polecenie może służyć do zainstalowania `requests` pakiet z PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Gdy wszystko będzie gotowe do opublikowania, upewnij się, że wszystkie zależności są wymienione w *requirements.txt* pliku, który znajduje się w folderze głównym katalogu projektu. Jeśli używasz pakietu wymaga kompilatora, która nie obsługuje instalacji koła manylinux zgodnym z PyPI publikowanie na platformie Azure zakończy się niepowodzeniem z powodu następującego błędu: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Aby automatycznie skompilować i skonfigurować wymagane pliki binarne [zainstalować platformę Docker](https://docs.docker.com/install/) na komputer lokalny i uruchom następujące polecenie, aby opublikować za pomocą [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#v2) (func). Pamiętaj, aby zastąpić `<app name>` nazwę aplikacji funkcji na platformie Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Wewnętrznie, podstawowe narzędzia będzie korzystać z aparatu docker do uruchomienia [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) obrazu kontenera na komputerze lokalnym. Korzystając z tego środowiska, go następnie zbudujesz i zainstalować wymagane moduły z dystrybucji źródłowego, przed spakowaniem ich potrzeby końcowego wdrażanie na platformie Azure.

Aby skompilować zależności i opublikować za pomocą systemu ciągłe dostarczanie (CD), [potoków metodyki DevOps platformy Azure użyj](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Testowanie jednostkowe

Można przetestować Functions napisanej w języku Python, takich jak inny kod Python przy użyciu standardowych środowisk testowych. Dla większości powiązania jest możliwość utworzenia makiety obiektu wejściowego, tworząc wystąpienie odpowiedniej klasy z `azure.functions` pakietu.

Na przykład poniżej przedstawiono makiety test funkcję wyzwalaną przez protokół HTTP:

```python
# myapp/__init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/my_function',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            'Hello, Test!',
        )
```

Oto inny przykład za pomocą funkcji wyzwalanej przez kolejkę:

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

Wszystkie znane problemy i sugestie funkcji są śledzone za pomocą [problemy usługi GitHub](https://github.com/Azure/azure-functions-python-worker/issues) listy. W przypadku napotkania problemów i nie można odnaleźć problem w serwisie GitHub, otwórz nowy problem i zawierają szczegółowy opis problemu.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* [Powiązania magazynu obiektów blob](functions-bindings-storage-blob.md)
* [Powiązania protokołu HTTP i elementu Webhook](functions-bindings-http-webhook.md)
* [Powiązania magazynu kolejki](functions-bindings-storage-queue.md)
* [Wyzwalacz czasomierza](functions-bindings-timer.md)
