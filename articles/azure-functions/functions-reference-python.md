---
title: Dokumentacja dla deweloperów języka Python dla Azure Functions
description: Informacje na temat tworzenia funkcji w języku Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 374770c50300b9ca37253b3323a02c3371bfa521
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903234"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dewelopera w języku Python Azure Functions

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu języka Python. W poniższej zawartości przyjęto założenie, że został już odczytany [Przewodnik deweloperów Azure Functions](functions-reference.md). 

Przykładowe projekty funkcji autonomicznych w języku Python znajdują się w sekcji [przykłady funkcji języka Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Model programowania

Azure Functions oczekuje, że funkcja będzie metodą bezstanową w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje metody, która ma być zaimplementowana jako metoda globalna o nazwie `main()` w pliku `__init__.py`. Można również [określić alternatywny punkt wejścia](#alternate-entry-point).

Dane z wyzwalaczy i powiązań są powiązane z funkcją za pośrednictwem atrybutów metod przy użyciu właściwości `name` zdefiniowanej w pliku *Function. JSON* . Na przykład, _Funkcja. JSON_ poniżej opisuje prostą funkcję wyzwalaną przez żądanie HTTP o nazwie `req`:

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

Zalecana struktura folderów dla projektu funkcji w języku Python wygląda następująco:

```
 __app__
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
 tests
```
Główny folder projektu (\_\_aplikacji\_\_) może zawierać następujące pliki:

* *Local. Settings. JSON*: służy do przechowywania ustawień aplikacji i parametrów połączenia podczas lokalnego uruchamiania. Ten plik nie jest publikowany na platformie Azure. Aby dowiedzieć się więcej, zobacz [Local. Settings. File](functions-run-local.md#local-settings-file).
* *Requirements. txt*: zawiera listę pakietów instalowanych przez system podczas publikowania na platformie Azure.
* plik *host. JSON*: zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje w aplikacji funkcji. Ten plik jest publikowany na platformie Azure. Nie wszystkie opcje są obsługiwane w przypadku uruchamiania lokalnego. Aby dowiedzieć się więcej, zobacz plik [host. JSON](functions-host-json.md).
* *. funcignore*: (opcjonalnie) deklaruje pliki, które nie powinny zostać opublikowane na platformie Azure.
* *. gitignore*: (opcjonalnie) deklaruje pliki, które są wykluczone z repozytorium git, takie jak Local. Settings. JSON.

Każda funkcja ma własny plik kodu i plik konfiguracji powiązania (Function. JSON). 

Udostępniony kod powinien być przechowywany w osobnym folderze w \_\_aplikacji\_\_. Aby odwoływać się do modułów w folderze SharedCode, można użyć następującej składni:

```python
from __app__.SharedCode import myFirstHelperFunction
```

Aby odwołać się do modułów lokalnych do funkcji, można użyć składni względnego importu w następujący sposób:

```python
from . import example
```

Podczas wdrażania projektu w aplikacji funkcji na platformie Azure cała zawartość folderu *FunctionApp* powinna być dołączona do pakietu, ale nie do samego folderu. Zalecane jest, aby zachować testy w folderze oddzielonym od folderu projektu, w tym przykładzie `tests`. Dzięki temu można wdrożyć kod testowy w aplikacji. Aby uzyskać więcej informacji, zobacz [testowanie jednostkowe](#unit-testing).

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

Po wywołaniu funkcji żądanie HTTP jest przekazywane do tej funkcji jako `req`. Wpis zostanie pobrany z usługi Azure Blob Storage w oparciu o _Identyfikator_ w adresie URL trasy i udostępniony jako `obj` w treści funkcji.  W tym miejscu jest określone konto magazynu z parametrami połączenia znalezionymi w ustawieniu aplikacji AzureWebJobsStorage, które jest tym samym kontem magazynu, które jest używane przez aplikację funkcji.


## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe można wyrazić zarówno w wartości zwracanej, jak i wyjściowych. Jeśli istnieje tylko jedno wyjście, zalecamy użycie wartości zwracanej. W przypadku wielu wyjść należy użyć parametrów wyjściowych.

Aby użyć wartości zwracanej przez funkcję jako wartości powiązania danych wyjściowych, właściwość `name` powiązania powinna być ustawiona na `$return` w `function.json`.

Aby wygenerować wiele danych wyjściowych, użyj metody `set()` dostarczonej przez interfejs [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) do przypisania wartości do powiązania. Na przykład następująca funkcja może wypchnąć komunikat do kolejki i zwrócić odpowiedź HTTP.

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

Wyzwalacz HTTP jest zdefiniowany w pliku Function. Jan. `name` powiązania musi być zgodna z nazwanym parametrem w funkcji. W poprzednich przykładach jest używana nazwa powiązania `req`. Ten parametr jest obiektem [HttpRequest] i zwracany jest obiekt [HttpResponse] .

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

W tej funkcji wartość parametru zapytania `name` jest uzyskiwana z `params` parametr obiektu [HttpRequest] . Treść komunikatu zakodowanego w formacie JSON jest odczytywana przy użyciu metody `get_json`. 

Analogicznie, można ustawić `status_code` i `headers` dla komunikatu odpowiedzi w zwracanym obiekcie [HttpResponse] .

## <a name="scaling-and-concurrency"></a>Skalowanie i współbieżność

Domyślnie Azure Functions automatycznie monitoruje obciążenie aplikacji i tworzy dodatkowe wystąpienia hosta dla języka Python zgodnie z potrzebami. Funkcja używa wbudowanych wartości progowych (nie można skonfigurować użytkownika) dla różnych typów wyzwalaczy, aby określić, kiedy należy dodać wystąpienia, takie jak wiek komunikatów i rozmiar kolejki dla QueueTrigger. Aby uzyskać więcej informacji, zobacz [jak działają plany zużycia i Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

To zachowanie skalowania jest wystarczające dla wielu aplikacji. Jednak aplikacje z jedną z następujących cech mogą nie skalować się efektywnie:

- Aplikacja musi obsługiwać wiele współbieżnych wywołań.
- Aplikacja przetwarza dużą liczbę zdarzeń we/wy.
- Aplikacja jest powiązana ze we/wy.

W takich przypadkach można dodatkowo poprawić wydajność, używając wzorców Async i procesów roboczych wielu języków.

### <a name="async"></a>Async

Ponieważ Python jest środowiskiem uruchomieniowym jednowątkowym, wystąpienie hosta dla języka Python może jednocześnie przetwarzać tylko jedno wywołanie funkcji. W przypadku aplikacji, które przetwarzają dużą liczbę zdarzeń we/wy i/lub są powiązane we/wy, można zwiększyć wydajność, uruchamiając funkcje asynchronicznie.

Aby uruchomić funkcję asynchronicznie, użyj instrukcji `async def`, która uruchamia funkcję z [asyncio](https://docs.python.org/3/library/asyncio.html) bezpośrednio:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Funkcja bez słowa kluczowego `async` jest uruchamiana automatycznie w puli wątków asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Korzystanie z wielu procesów roboczych języka

Domyślnie każde wystąpienie hosta funkcji ma proces roboczy o pojedynczym języku. Liczbę procesów roboczych można zwiększyć na hosta (do 10) przy użyciu ustawienia aplikacji [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions następnie próbuje równomiernie rozpowszechnić jednoczesne wywołania funkcji przez tych pracowników. 

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który tworzy funkcje podczas skalowania aplikacji w celu spełnienia wymagań. 

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji podczas wykonywania, należy uwzględnić w podpisie argument [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) . 

Przykład:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klasa [**kontekstu**](/python/api/azure-functions/azure.functions.context?view=azure-python) ma następujące atrybuty ciągu:

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

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień, deklarując `import os` a następnie używając `setting = os.environ["setting-name"]`.

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

## <a name="python-version"></a>Wersja języka Python 

Obecnie Azure Functions obsługuje zarówno Język Python 3.6. x, jak i 3.7. x (oficjalne dystrybucje CPython). W przypadku uruchamiania lokalnego środowisko uruchomieniowe korzysta z dostępnej wersji języka Python. Aby zażądać określonej wersji języka Python podczas tworzenia aplikacji funkcji na platformie Azure, użyj opcji `--runtime-version` polecenia [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) . Zmiana wersji jest dozwolona tylko w przypadku tworzenia aplikacja funkcji.  

## <a name="package-management"></a>Zarządzanie pakietami

Podczas programowania lokalnego przy użyciu Azure Functions Core Tools lub Visual Studio Code Dodaj nazwy i wersje wymaganych pakietów do pliku `requirements.txt` i zainstaluj je za pomocą `pip`. 

Na przykład można użyć poniższego pliku wymagań i polecenia PIP, aby zainstalować pakiet `requests` z PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Gdy wszystko będzie gotowe do opublikowania, upewnij się, że wszystkie dostępne publicznie zależności są wymienione w pliku Requirements. txt, który znajduje się w katalogu głównym katalogu projektu. 

Pliki projektu i foldery, które są wykluczone z publikowania, łącznie z folderem środowiska wirtualnego, są wymienione w pliku. funcignore.

Istnieją trzy akcje kompilacji, które są obsługiwane w przypadku publikowania projektu w języku Python na platformie Azure:

+ Kompilacja zdalna: zależności są uzyskiwane zdalnie na podstawie zawartości pliku Requirements. txt. [Zdalna kompilacja](functions-deployment-technologies.md#remote-build) jest zalecaną metodą kompilacji. Opcja zdalna jest również opcją domyślna kompilacja narzędzi platformy Azure. 
+ Lokalna kompilacja: zależności są uzyskiwane lokalnie na podstawie zawartości pliku Requirements. txt. 
+ Zależności niestandardowe: projekt używa pakietów, które nie są publicznie dostępne dla naszych narzędzi. (Wymaga platformy Docker).

Aby skompilować zależności i publikować przy użyciu systemu ciągłego dostarczania, [użyj Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Kompilacja zdalna

Domyślnie Azure Functions Core Tools żąda kompilacji zdalnej, gdy do publikowania projektu języka Python na platformie Azure jest używane następujące polecenie [Func Azure functionapp Publish](functions-run-local.md#publish) . 

```bash
func azure functionapp publish <APP_NAME>
```

Pamiętaj, aby zastąpić `<APP_NAME>` nazwą aplikacji funkcji na platformie Azure.

[Rozszerzenie Azure Functions dla Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) również domyślnie żąda kompilacji zdalnej. 

### <a name="local-build"></a>Lokalna kompilacja

Można zapobiec wykonywaniu zdalnej kompilacji przy użyciu następującego polecenia [Func usługi Azure functionapp Publish](functions-run-local.md#publish) do publikowania z lokalną kompilacją. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Pamiętaj, aby zastąpić `<APP_NAME>` nazwą aplikacji funkcji na platformie Azure. 

Przy użyciu opcji `--build local` zależności projektu są odczytywane z pliku Requirements. txt, a pakiety zależne są pobierane i instalowane lokalnie. Pliki i zależności projektu są wdrażane z komputera lokalnego na platformie Azure. Powoduje to przekazanie większego pakietu wdrożeniowego do platformy Azure. Jeśli z jakiegoś powodu zależności w pliku Requirements. txt nie mogą zostać uzyskane przez podstawowe narzędzia, należy użyć opcji zależności niestandardowe do opublikowania. 

### <a name="custom-dependencies"></a>Zależności niestandardowe

Jeśli projekt używa pakietów, które nie są publicznie dostępne dla naszych narzędzi, możesz udostępnić je aplikacji, umieszczając je w \_\_aplikacji\_\_/. python_packages. Przed opublikowaniem Uruchom następujące polecenie, aby zainstalować zależności lokalnie:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

W przypadku korzystania z zależności niestandardowych należy użyć opcji publikowania `--no-build`, ponieważ zostały już zainstalowane zależności.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Pamiętaj, aby zastąpić `<APP_NAME>` nazwą aplikacji funkcji na platformie Azure.

## <a name="unit-testing"></a>Testowanie jednostkowe

Funkcje w języku Python można testować podobnie jak w przypadku innych kodów języka Python przy użyciu standardowych platform testowania. W przypadku większości powiązań można utworzyć obiekt wejściowy makiety, tworząc wystąpienie odpowiedniej klasy z pakietu `azure.functions`. Ponieważ pakiet [`azure.functions`](https://pypi.org/project/azure-functions/) nie jest natychmiast dostępny, pamiętaj, aby go zainstalować za pośrednictwem pliku `requirements.txt`, zgodnie z opisem w powyższej sekcji [Zarządzanie pakietami](#package-management) . 

Na przykład poniżej znajduje się przykładowy test funkcji wyzwalanej przez protokół HTTP:

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

Oto kolejny przykład z funkcją wyzwalaną przez kolejkę:

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

Metoda `tempfile.gettempdir()` zwraca tymczasowy folder, który w systemie Linux jest `/tmp`. Aplikacja może używać tego katalogu do przechowywania plików tymczasowych generowanych i używanych przez funkcje podczas wykonywania. 

> [!IMPORTANT]
> Pliki zapisywane w katalogu tymczasowym nie są gwarantowane zachowywania się między wywołaniami. Podczas skalowania w poziomie pliki tymczasowe nie są współdzielone między wystąpieniami. 

Poniższy przykład tworzy nazwany plik tymczasowy w katalogu tymczasowym (`/tmp`):

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

Zalecane jest, aby zachować testy w folderze innym niż folder projektu. Dzięki temu można wdrożyć kod testowy w aplikacji. 

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

Ta metoda HTTP jest używana przez przeglądarki sieci Web do negocjowania listy dozwolonych źródeł. 

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
