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
ms.openlocfilehash: 28f2b395c7f9be1b194b500ef20456be8ff405b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021278"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions Python

Ten artykuł stanowi wprowadzenie do projektowania usługi Azure Functions przy użyciu języka Python. Zawartość poniżej założono, że zostały już przeczytane [przewodnik dla deweloperów usługi Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Model programowania

Funkcja platformy Azure powinna być bezstanowe metody w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje, że ten element, aby można zaimplementować jako metoda globalna o nazwie `main()` w `__init__.py` pliku.

Można zmienić konfigurację domyślną, określając `scriptFile` i `entryPoint` właściwości w `function.json` pliku. Na przykład _function.json_ informuje, poniżej środowiska uruchomieniowego do użycia _customentry()_ method in Class metoda _main.py_ pliku jako punkt wejścia dla funkcji platformy Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Dane z wyzwalaczami i powiązaniami jest powiązany z funkcji za pomocą atrybutów metody przy użyciu `name` właściwości zdefiniowanych w `function.json` pliku konfiguracji. Na przykład _function.json_ poniżej opisano prostej funkcji wyzwalanej przez żądanie HTTP o nazwie `req`:

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

Opcjonalnie można również zadeklarować typy parametrów i zwracany typ funkcji za pomocą języka Python typu adnotacji. Na przykład taką samą funkcję można pisać przy użyciu adnotacji, w następujący sposób:

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
 | - extensions.csproj
 | - bin
```

Brak udostępnionej [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu i pliku konfiguracji powiązania (function.json). 

Udostępniony kod powinny być przechowywane w oddzielnym folderze. Aby odwoływać się do modułów w folderze SharedCode, można użyć następującej składni:

```
from ..SharedCode import myFirstHelperFunction
```

Rozszerzenia powiązania, używany przez środowisko uruchomieniowe usługi Functions są definiowane w `extensions.csproj` pliku z plikami rzeczywistej biblioteki w `bin` folderu. Podczas tworzenia lokalnie, należy najpierw [zarejestrować rozszerzeń powiązania](./functions-bindings-register.md#local-development-azure-functions-core-tools) przy użyciu podstawowych narzędzi usługi Azure Functions. 

Podczas wdrażania projektu funkcji do aplikacji funkcji na platformie Azure, całą zawartość folderu FunctionApp powinny być objęte pakietu, ale nie sam folder.

## <a name="inputs"></a>Dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: dane wejściowe wyzwalacza i dodatkowych danych wejściowych. Mimo że są inne w przypadku `function.json`, użycie jest identyczna w kodzie języka Python. Jako przykład Weźmy poniższy fragment kodu:

```json
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

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Po wywołaniu funkcji żądanie HTTP jest przekazywane do tej funkcji jako `req`. Na podstawie identyfikatora (_id_) w adresie URL trasy z usługi Azure Blob Storage jest pobierany wpis, który następnie zostaje udostępniony jako `obj` w treści funkcji.

## <a name="outputs"></a>Dane wyjściowe

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

## <a name="importing-shared-code-into-a-function-module"></a>Importowanie udostępnionego kodu w module — funkcja

Moduły języka Python opublikowanych wraz z funkcją modułów muszą zostać zaimportowane przy użyciu składni względne importu:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Alternatywnie umieść kod udostępniony pakietu autonomicznego opublikujesz go w publiczny lub prywatny wystąpienia PyPI i określić go jako regularne zależności.

## <a name="async"></a>asynchroniczne

Ponieważ aplikacja funkcji może istnieć tylko jeden proces języka Python, zalecane jest Implementowanie funkcji platformy Azure co w koprocedury asynchronicznego `async def` instrukcji.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

W przypadku funkcji main() synchroniczne (nie `async` kwalifikator) firma Microsoft automatycznie uruchomić w `asyncio` puli wątków.

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

## <a name="python-version-and-package-management"></a>Zarządzanie wersji i pakiet języka Python

Obecnie usługa Azure Functions obsługuje tylko Python 3.6.x (oficjalna CPython dystrybucja).

Podczas tworzenia lokalnie przy użyciu podstawowych narzędzi usługi Azure Functions lub Visual Studio Code, Dodaj nazwy i wersji wymagane pakiety do `requirements.txt` pliku, a następnie zainstaluj je przy użyciu `pip`.

Na przykład wymagania dotyczące konfiguracji narzędzia pip i pliku polecenie może służyć do zainstalowania `requests` pakiet z PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Gdy wszystko będzie gotowe do opublikowania, upewnij się, że wszystkie zależności są wymienione w `requirements.txt` pliku, znajdującego się w folderze głównym katalogu projektu. Aby pomyślnie wykonać usługi Azure Functions, plik wymagania powinien zawierać co najmniej następujące pakiety:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Jeśli używasz pakietu wymaga kompilatora, która nie obsługuje instalacji koła manylinux zgodnym z PyPI publikowanie na platformie Azure zakończy się niepowodzeniem z powodu następującego błędu: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Aby automatycznie skompilować i skonfigurować wymagane pliki binarne [zainstalować platformę Docker](https://docs.docker.com/install/) na komputer lokalny i uruchom następujące polecenie, aby opublikować za pomocą [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#v2) (func). Pamiętaj, aby zastąpić `<app name>` nazwę aplikacji funkcji na platformie Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Wewnętrznie, podstawowe narzędzia będzie korzystać z aparatu docker do uruchomienia [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) obrazu kontenera na komputerze lokalnym. Korzystając z tego środowiska, go następnie zbudujesz i zainstalować wymagane moduły z dystrybucji źródłowego, przed spakowaniem ich potrzeby końcowego wdrażanie na platformie Azure.

> [!NOTE]
> Podstawowe narzędzia (func) korzysta z programu PyInstaller można zablokować użytkownika kod i zależności w jednym autonomicznego pliku wykonywalnego do uruchomienia na platformie Azure. Ta funkcja jest obecnie dostępna w wersji zapoznawczej i mogą nie dotyczyć wszystkich typów pakietów języka Python. Jeśli nie możesz zaimportować moduły, spróbuj publikowania, ponownie, używając `--no-bundler` opcji. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Jeśli nadal występują problemy, prosimy o kontakt przez [otwierania problemu](https://github.com/Azure/azure-functions-core-tools/issues/new) i w tym opis problemu. 


Aby skompilować zależności i opublikować za pomocą ciągłej integracji (CI) i ciągłe dostarczanie (CD) systemu, można użyć [potoku Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) lub [skryptu niestandardowego rozwiązania Travis CI](https://docs.travis-ci.com/user/deployment/script/). 

Poniżej znajduje się przykład `azure-pipelines.yml` skryptu w procesie kompilacji i publikowania.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Poniżej znajduje się przykład `.travis.yaml` skryptu w procesie kompilacji i publikowania.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

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
