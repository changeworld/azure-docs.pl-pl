---
title: Odwołanie do dewelopera języka Python dla usług Azure Functions
description: Dowiedz się, jak tworzyć funkcje za pomocą języka Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276688"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dla deweloperów usługi Azure Functions Python

Ten artykuł jest wprowadzeniem do tworzenia usług Azure Functions przy użyciu języka Python. Poniższa zawartość zakłada, że został już przeczytany [przewodnik deweloperów usługi Azure Functions.](functions-reference.md) 

W przypadku autonomicznych projektów przykładowych funkcji w języku Python zobacz [przykłady funkcji języka Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Model programowania

Usługa Azure Functions oczekuje, że funkcja będzie bezstanową metodą w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko wykonawcze oczekuje, że metoda zostanie zaimplementowana jako metoda globalna wywoływana `main()` w `__init__.py` pliku. Można również [określić alternatywny punkt wejścia](#alternate-entry-point).

Dane z wyzwalaczy i powiązań jest powiązany `name` z funkcją za pomocą atrybutów metody przy użyciu właściwości zdefiniowanej w pliku *function.json.* Na przykład _funkcja.json_ poniżej opisuje prostą funkcję wyzwalaną przez żądanie HTTP o nazwie: `req`

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Na podstawie tej `__init__.py` definicji plik zawierający kod funkcji może wyglądać następująco:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Można również jawnie zadeklarować typy atrybutów i typu zwracanego w funkcji przy użyciu adnotacji typu Python. Pomaga to korzystać z funkcji intellisense i autouzupełnianie dostarczonych przez wielu edytorów kodu Języka Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Użyj adnotacji języka Python zawartych w pakiecie [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) do powiązania danych wejściowych i wyjściowych z metodami.

## <a name="alternate-entry-point"></a>Alternatywny punkt wejścia

Domyślne zachowanie funkcji można zmienić, opcjonalnie `scriptFile` `entryPoint` określając właściwości i właściwości w pliku *function.json.* Na przykład _function.json_ poniżej informuje środowiska `customentry()` wykonawczego, aby użyć metody w pliku _main.py,_ jako punkt wejścia dla funkcji platformy Azure.

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

Zalecana struktura folderów dla projektu funkcji języka Python wygląda następująco:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
Główny folder projektu\_\_\_\_(aplikacja) może zawierać następujące pliki:

* *local.settings.json*: Służy do przechowywania ustawień aplikacji i ciągów połączeń podczas uruchamiania lokalnie. Ten plik nie zostanie opublikowany na platformie Azure. Aby dowiedzieć się więcej, zobacz [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt:* Zawiera listę pakietów instalowane przez system podczas publikowania na platformie Azure.
* *host.json*: Zawiera globalne opcje konfiguracji, które wpływają na wszystkie funkcje w aplikacji funkcji. Ten plik zostanie opublikowany na platformie Azure. Nie wszystkie opcje są obsługiwane podczas uruchamiania lokalnego. Aby dowiedzieć się więcej, zobacz [host.json](functions-host-json.md).
* *.funcignore*: (Opcjonalnie) deklaruje pliki, które nie powinny zostać opublikowane na platformie Azure.
* *.gitignore*: (Opcjonalnie) deklaruje pliki, które są wykluczone z repozytorium git, takie jak local.settings.json.

Każda funkcja ma swój własny plik kodu i plik konfiguracji powiązania (function.json). 

Podczas wdrażania projektu do aplikacji funkcji na platformie Azure cała zawartość głównego projektu*\_\_(aplikacja)\_* folder powinien być uwzględniony w pakiecie, ale nie sam folder. W tym przykładzie `tests`zaleca się zachowanie testów w folderze oddzielonym od folderu projektu. Dzięki temu można wdrożyć kod testowy z aplikacją. Aby uzyskać więcej informacji, zobacz [Testowanie jednostek](#unit-testing).

## <a name="import-behavior"></a>Zachowanie importu

Moduły można importować w kodzie funkcji przy użyciu odwołań względnych jawnych i bezwzględnych. Na podstawie struktury folderów pokazanej powyżej, następujące importy działają z poziomu * \_ \_aplikacji\_\_pliku\_funkcji \moja pierwsza\_funkcja\\_\_init\_\_.py:*

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

Następujące importy *nie działają* z tego samego pliku:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Udostępniony kod powinien być przechowywany * \_ \_w\_* osobnym folderze w aplikacji . Aby odwołać się do modułów w folderze *kodu udostępnionego,\_* można użyć następującej składni:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Wyzwalacze i wejścia

Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: dane wejściowe wyzwalacza i dodatkowe dane wejściowe. Chociaż są one `function.json` różne w pliku, użycie jest identyczne w kodzie Pythona.  Parametry połączenia lub wpisy tajne dla źródeł wyzwalacza i wejściowych są mapowane na wartości w `local.settings.json` pliku podczas uruchamiania lokalnie oraz ustawienia aplikacji podczas uruchamiania na platformie Azure. 

Na przykład poniższy kod pokazuje różnicę między tymi dwoma:

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

Po wywołaniu funkcji żądanie HTTP jest przekazywane do `req`funkcji jako . Wpis zostanie pobrany z usługi Azure Blob Storage na podstawie _identyfikatora_ `obj` w adresie URL trasy i udostępnione jako w treści funkcji.  W tym miejscu określone konto magazynu jest ciąg połączenia znalezionych w ustawieniu aplikacji AzureWebJobsStorage, który jest tym samym kontem magazynu używane przez aplikację funkcji.


## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe mogą być wyrażone zarówno w wartości zwracanej, jak i parametrach wyjściowych. Jeśli istnieje tylko jedno dane wyjściowe, zaleca się użycie wartości zwracanej. W przypadku wielu wyjść należy użyć parametrów wyjściowych.

Aby użyć zwracanej wartości funkcji jako wartości powiązania `name` danych wyjściowych, właściwość `$return` `function.json`powiązania powinna być ustawiona w .

Aby utworzyć wiele wyjść, `set()` należy użyć [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) metody dostarczonej przez interfejs, aby przypisać wartość do powiązania. Na przykład następująca funkcja może wypchnąć wiadomość do kolejki, a także zwrócić odpowiedź HTTP.

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

Dostęp do rejestratora środowiska wykonawczego usługi Azure [`logging`](https://docs.python.org/3/library/logging.html#module-logging) Functions jest dostępny za pośrednictwem programu obsługi głównego w aplikacji funkcji. Ten rejestrator jest powiązany z usługą Application Insights i umożliwia flagowanie ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład rejestruje komunikat informacyjny, gdy funkcja jest wywoływana za pośrednictwem wyzwalacza HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Dostępne są dodatkowe metody rejestrowania, które umożliwiają zapisywanie w konsoli na różnych poziomach śledzenia:

| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapisuje komunikat z poziomem KRYTYCZNY na rejestratorze głównym.  |
| **`error(_message_)`**   | Zapisuje komunikat z poziomem ERROR na rejestratorze głównym.    |
| **`warning(_message_)`**    | Zapisuje komunikat z poziomem OSTRZEŻENIE na rejestratorze głównym.  |
| **`info(_message_)`**    | Zapisuje komunikat z poziomem INFO na rejestratorze głównym.  |
| **`debug(_message_)`** | Zapisuje komunikat z poziomem DEBUG na rejestratorze głównym.  |

Aby dowiedzieć się więcej o rejestrowaniu, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Wyzwalacz HTTP i powiązania

Wyzwalacz HTTP jest zdefiniowany w pliku function.jon. Powiązanie `name` musi odpowiadać nazwanemu parametrowi w funkcji. W poprzednich przykładach używana `req` jest nazwa powiązania. Ten parametr jest obiektem [HttpRequest] i zwracany jest obiekt [HttpResponse.]

Z [HttpRequest] obiektu, można uzyskać nagłówki żądania, parametry kwerendy, parametry trasy i treści wiadomości. 

Poniższy przykład pochodzi z [szablonu wyzwalacza HTTP dla języka Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

W tej funkcji wartość `name` parametru kwerendy `params` jest uzyskiwana z parametru obiektu [HttpRequest.] Treść wiadomości zakodowana w UJ `get_json` jest odczytywana przy użyciu metody. 

Podobnie można ustawić `status_code` i `headers` dla komunikatu odpowiedzi w zwróconym [HttpResponse] obiektu.

## <a name="scaling-and-concurrency"></a>Skalowanie i współbieżność

Domyślnie usługa Azure Functions automatycznie monitoruje obciążenie aplikacji i tworzy dodatkowe wystąpienia hosta dla języka Python w razie potrzeby. Funkcje używają wbudowanych (nie konfigurowalne przez użytkownika) progów dla różnych typów wyzwalaczy, aby zdecydować, kiedy dodać wystąpienia, takie jak wiek wiadomości i rozmiar kolejki dla QueueTrigger. Aby uzyskać więcej informacji, zobacz [Jak działają plany zużycie i premia](functions-scale.md#how-the-consumption-and-premium-plans-work).

To zachowanie skalowania jest wystarczające dla wielu aplikacji. Zastosowania o dowolnej z następujących cech mogą jednak nie być skalowane tak skutecznie:

- Aplikacja musi obsługiwać wiele równoczesnych wywołań.
- Aplikacja przetwarza dużą liczbę zdarzeń we/wy.
- Aplikacja jest powiązana we/wy.

W takich przypadkach można zwiększyć wydajność, stosując wzorce asynchronicznego i używając wielu procesów procesu roboczego języka.

### <a name="async"></a>Async

Ponieważ Python jest jednowątkowym środowisko uruchomieniowe, wystąpienie hosta dla języka Python może przetwarzać tylko jedno wywołanie funkcji w czasie. W przypadku aplikacji przetwarzanych przez dużą liczbę zdarzeń we/wy i/lub związanych we/wy można zwiększyć wydajność, uruchamiając funkcje asynchronicznie.

Aby uruchomić funkcję asynchronicznie, `async def` należy użyć instrukcji, która uruchamia funkcję bezpośrednio z [asyncio:](https://docs.python.org/3/library/asyncio.html)

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Funkcja bez `async` słowa kluczowego jest uruchamiana automatycznie w puli wątków asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Używanie wielu procesów procesów roboczych w języku

Domyślnie każde wystąpienie hosta funkcji ma proces roboczy jednego języka. Można zwiększyć liczbę procesów roboczych na hoście (do 10) przy użyciu ustawienia [aplikacji FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) Usługa Azure Functions następnie próbuje równomiernie rozpowszechniać wywołania funkcji równoczesnych między tymi pracownikami. 

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który funkcja tworzy podczas skalowania w poziomie aplikacji w celu zaspokojenia popytu. 

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji podczas wykonywania, [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) należy uwzględnić argument w jego podpisie. 

Przykład:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klasa [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) ma następujące atrybuty ciągu:

`function_directory`  
Katalog, w którym funkcja jest uruchomiona.

`function_name`  
Nazwa funkcji.

`invocation_id`  
Identyfikator bieżącego wywołania funkcji.

## <a name="global-variables"></a>Zmienne globalne

Nie jest gwarantowana, że stan aplikacji zostaną zachowane dla przyszłych wykonań. Jednak środowisko uruchomieniowe usługi Azure Functions często ponownie używa tego samego procesu dla wielu wykonań tej samej aplikacji. Aby buforować wyniki kosztownych obliczeń, zadeklaruj go jako zmienną globalną. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach [ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są widoczne jako zmienne środowiskowe podczas wykonywania. Dostęp do tych ustawień `import os` można uzyskać, `setting = os.environ["setting-name"]`deklarując, a następnie używając .

Poniższy przykład pobiera [ustawienie aplikacji,](functions-how-to-use-azure-function-app-settings.md#settings) `myAppSetting`z kluczem o nazwie:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

W przypadku rozwoju lokalnego ustawienia aplikacji są [zachowywane w pliku local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Wersja języka Python 

Usługa Azure Functions obsługuje następujące wersje języka Python:

| Wersja funkcji | Wersje<sup>*</sup> Pythona |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Oficjalne dystrybucje CPython

Aby zażądać określonej wersji języka Python podczas tworzenia `--runtime-version` aplikacji [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) funkcji na platformie Azure, użyj opcji polecenia. Wersja środowiska wykonawczego funkcji jest `--functions-version` ustawiana przez tę opcję. Wersja języka Python jest ustawiana podczas tworzenia aplikacji funkcji i nie można jej zmienić.  

Podczas uruchamiania lokalnie środowisko wykonawcze używa dostępnej wersji języka Python. 

## <a name="package-management"></a>Zarządzanie pakietami

Podczas tworzenia lokalnie przy użyciu narzędzi podstawowe usług Azure Functions Core Tools lub `requirements.txt` Visual Studio Code `pip`dodaj nazwy i wersje wymaganych pakietów do pliku i zainstaluj je za pomocą programu . 

Na przykład do zainstalowania pakietu z pypi `requests` można użyć następującego pliku wymagań i polecenia pip.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Gdy wszystko będzie gotowe do opublikowania, upewnij się, że wszystkie publicznie dostępne zależności są wymienione w pliku requirements.txt, który znajduje się w katalogu głównym projektu. 

Pliki i foldery projektu, które są wykluczone z publikowania, w tym folder środowiska wirtualnego, są wymienione w pliku funcignore.

Istnieją trzy akcje kompilacji obsługiwane do publikowania projektu języka Python na platformie Azure:

+ Kompilacja zdalna: zależności są uzyskiwane zdalnie na podstawie zawartości pliku requirements.txt. [Kompilacja zdalna](functions-deployment-technologies.md#remote-build) jest zalecaną metodą kompilacji. Remote jest również domyślną opcją kompilacji narzędzi platformy Azure. 
+ Kompilacja lokalna: zależności są uzyskiwane lokalnie na podstawie zawartości pliku requirements.txt. 
+ Zależności niestandardowe: Projekt używa pakietów, które nie są publicznie dostępne dla naszych narzędzi. (Wymaga platformy Docker).

Aby utworzyć zależności i publikować przy użyciu systemu ciągłego dostarczania (CD), [należy użyć usługi Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Kompilacja zdalna

Domyślnie narzędzia Podstawowe usługi Azure Functions żąda zdalnej kompilacji, gdy używasz następującego polecenia [publikowania funkcji platformy func azure,](functions-run-local.md#publish) aby opublikować projekt języka Python na platformie Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Pamiętaj, `<APP_NAME>` aby zastąpić nazwą aplikacji funkcji na platformie Azure.

[Rozszerzenie funkcji platformy Azure dla programu Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) wymaga również kompilacji zdalnej domyślnie. 

### <a name="local-build"></a>Kompilacja lokalna

Można zapobiec wykonywania kompilacji zdalnej przy użyciu następującego [polecenia func azure functionapp publikowania](functions-run-local.md#publish) do publikowania za pomocą kompilacji lokalnej. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Pamiętaj, `<APP_NAME>` aby zastąpić nazwą aplikacji funkcji na platformie Azure. 

Korzystając `--build local` z tej opcji, zależności projektu są odczytywane z pliku requirements.txt, a te pakiety zależne są pobierane i instalowane lokalnie. Pliki projektu i zależności są wdrażane z komputera lokalnego na platformie Azure. Powoduje to, że większy pakiet wdrażania jest przekazywany na platformę Azure. Jeśli z jakiegoś powodu zależności w pliku requirements.txt nie mogą zostać nabyte przez narzędzia podstawowe, należy użyć opcji zależności niestandardowych do publikowania. 

### <a name="custom-dependencies"></a>Zależności niestandardowe

Jeśli projekt używa pakietów, które nie są publicznie dostępne dla naszych narzędzi, \_ \_możesz\_\_udostępnić je aplikacji, umieszczając je w katalogu aplikacji /.python_packages. Przed opublikowaniem uruchom następujące polecenie, aby zainstalować zależności lokalnie:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Podczas korzystania z zależności niestandardowych, należy użyć opcji `--no-build` publikowania, ponieważ zostały już zainstalowane zależności.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Pamiętaj, `<APP_NAME>` aby zastąpić nazwą aplikacji funkcji na platformie Azure.

## <a name="unit-testing"></a>Testowanie jednostkowe

Funkcje napisane w języku Python mogą być testowane jak inny kod Języka Python przy użyciu standardowych platform testowych. Dla większości powiązań jest możliwe do utworzenia obiektu wprowadzania makiety przez `azure.functions` utworzenie wystąpienia odpowiedniej klasy z pakietu. Ponieważ [`azure.functions`](https://pypi.org/project/azure-functions/) pakiet nie jest natychmiast dostępny, należy `requirements.txt` go zainstalować za pomocą pliku, jak opisano w powyższej sekcji [zarządzania pakietami.](#package-management) 

Na przykład poniżej znajduje się test makiety funkcji wyzwalanej HTTP:

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

Oto kolejny przykład z funkcją wyzwalaną kolejką:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>Pliki tymczasowe

Metoda `tempfile.gettempdir()` zwraca folder tymczasowy, który `/tmp`w systemie Linux jest . Aplikacja może używać tego katalogu do przechowywania plików tymczasowych generowanych i używanych przez funkcje podczas wykonywania. 

> [!IMPORTANT]
> Pliki zapisane w katalogu tymczasowym nie są gwarantowane do utrwalania w wywołaniach. Podczas skalowania w poziomie pliki tymczasowe nie są współużytkowane przez wystąpienia. 

Poniższy przykład tworzy nazwany plik tymczasowy w`/tmp`katalogu tymczasowym ( ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Zaleca się zachowanie testów w folderze oddzielonym od folderu projektu. Dzięki temu można wdrożyć kod testowy z aplikacją. 

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

Wszystkie znane problemy i żądania funkcji są śledzone przy użyciu listy [problemów z githubem.](https://github.com/Azure/azure-functions-python-worker/issues) Jeśli napotkasz problem i nie możesz znaleźć problemu w usłudze GitHub, otwórz nowy problem i dołącz szczegółowy opis problemu.

### <a name="cross-origin-resource-sharing"></a>Współużytkowanie zasobów między źródłami

Usługa Azure Functions obsługuje współużytkowane udostępnianie zasobów między źródłami (CORS). Cors jest skonfigurowany [w portalu](functions-how-to-use-azure-function-app-settings.md#cors) i za pośrednictwem [interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/cors). Lista dozwolonych źródeł CORS ma zastosowanie na poziomie aplikacji funkcji. Po włączeniu cors odpowiedzi `Access-Control-Allow-Origin` obejmują nagłówek. Aby uzyskać więcej informacji, zobacz temat [Współużytkowanie zasobów między źródłami](functions-how-to-use-azure-function-app-settings.md#cors).

Lista dozwolonych źródeł [nie jest obecnie obsługiwana](https://github.com/Azure/azure-functions-python-worker/issues/444) w aplikacjach funkcji Języka Python. Z powodu tego ograniczenia należy wyraźnie `Access-Control-Allow-Origin` ustawić nagłówek w funkcjach HTTP, jak pokazano w poniższym przykładzie:

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

Upewnij się, że również zaktualizować function.json do obsługi opcji HTTP metody:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Ta metoda HTTP jest używana przez przeglądarki internetowe do negocjowania listy dozwolonych źródeł. 

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Dokumentacja interfejsu API pakietu usługi Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)
* [Powiązania magazynu obiektów blob](functions-bindings-storage-blob.md)
* [Powiązania HTTP i Webhook](functions-bindings-http-webhook.md)
* [Powiązania magazynu kolejek](functions-bindings-storage-queue.md)
* [Wyzwalacz czasomierza](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
