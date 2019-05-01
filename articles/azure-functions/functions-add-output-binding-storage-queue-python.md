---
title: Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python
description: Dowiedz się, jak dodać powiązanie z funkcją języka Python przy użyciu wiersza polecenia platformy Azure i funkcje podstawowe narzędzia danych wyjściowych kolejki usługi Azure Storage.
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
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870040"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>Dodawanie powiązania kolejki usługi Azure Storage do funkcji

Usługa Azure Functions umożliwia łączenie z usługami platformy Azure i innych zasobów do funkcji bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują dane wejściowe i dane wyjściowe są zadeklarowane w ramach definicji funkcji. Dane z powiązań podano funkcji jako parametrów. Wyzwalacz jest specjalny typ powiązania danych wejściowych. Gdy funkcja ma tylko jeden wyzwalacz, ona mają wielu danych wejściowych i wyjściowych powiązania. Aby dowiedzieć się więcej, zobacz [pojęcia powiązania i Wyzwalacze usługi Azure Functions](functions-triggers-bindings.md).

W tym artykule dowiesz się, jak zintegrować funkcję został utworzony w [poprzednim artykule Szybki Start](functions-create-first-function-python.md) z kolejki usługi Azure Storage. Powiązania danych wyjściowych, które możesz dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce. 

Większość powiązania wymaga parametrów połączenia przechowywanych korzystającą z funkcji w celu uzyskania dostępu do powiązanej usługi. Aby ułatwić, należy użyć konta magazynu, który został utworzony za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywany w aplikacji, ustawienie o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, w tym artykule, wykonaj kroki [części 1 tego przewodnika Szybki Start języka Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Pobieranie ustawień aplikacji funkcji

W poprzednim artykule Szybki Start utworzono aplikację funkcji na platformie Azure wraz z kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule Zapisz komunikaty do kolejki na tym samym koncie magazynu. Aby połączyć się z kontem magazynu podczas uruchamiania funkcji lokalnie, możesz pobrać ustawień aplikacji do pliku local.settings.json. Uruchom następujące polecenie podstawowych narzędzi usługi Azure Functions, aby pobrać ustawienia do pliku local.settings.json, zastępując `<APP_NAME>` nazwę aplikacji funkcji z poprzednim artykule:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Może być konieczne Zaloguj się do konta platformy Azure.

> [!IMPORTANT]  
> Ponieważ w nim klucze tajne pliku local.settings.json nigdy nie zostanie opublikowany i powinny zostać wyłączone spod kontroli źródła.

Wartość będzie potrzebna `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. To połączenie umożliwia Sprawdź, czy powiązania danych wyjściowych działa zgodnie z oczekiwaniami.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W przypadku funkcji wymaga każdego typu powiązania `direction`, `type`oraz unikatową `name` zdefiniowanych w pliku function.json. W zależności od typu powiązania dodatkowe właściwości mogą być wymagane. [Konfiguracja danych wyjściowych kolejki](functions-bindings-storage-queue.md#output---configuration) opisuje pól wymaganych dla powiązania kolejki usługi Azure Storage.

Aby utworzyć powiązanie, Dodaj obiekt konfiguracji powiązania aby `function.json` pliku. Edytuj plik function.json w folderze HttpTrigger, aby dodać obiekt do `bindings` tablica, która ma następujące właściwości:

| Właściwość | Wartość | Opis |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Nazwa identyfikująca parametr wiązania, do którego odwołuje się kod. |
| **`type`** | `queue` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **`direction`** | `out` | Powiązanie to powiązanie danych wyjściowych. |
| **`queueName`** | `outqueue` | Nazwa kolejki, która zapisuje powiązania. Gdy *queueName* nie istnieje, tworzy go przy pierwszym użyciu powiązania. |
| **`connection`** | `AzureWebJobsStorage` | Nazwa ustawienia aplikacji zawierającego parametry połączenia dla konta magazynu. `AzureWebJobsStorage` Zawiera parametry połączenia dla konta magazynu, które utworzono za pomocą aplikacji funkcji. |

Plik function.json powinna teraz wyglądać podobnie jak w poniższym przykładzie:

```json
{
  "scriptFile": "__init__.py",
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
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po skonfigurowaniu, aby zacząć korzystać `name` powiązania dostępu do niego jako atrybut metody w sygnaturze funkcji. W poniższym przykładzie `msg` jest wystąpieniem [ `azure.functions.InputStream class` ](/python/api/azure-functions/azure.functions.httprequest).

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Za pomocą powiązania danych wyjściowych, nie trzeba użyć kodu zestawu SDK usługi Azure Storage do uwierzytelniania, pobieranie odwołania do kolejki lub zapisywania danych. Funkcje środowiska uruchomieniowego i kolejki danych wyjściowych powiązania wykonywanie tych zadań za Ciebie.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Aby uruchomić środowisko uruchomieniowe usługi Functions lokalnie, tak jak poprzednio, użyj następującego polecenia:

```bash
func host start
```

> [!NOTE]  
> Ponieważ miał poprzednim artykule, należy włączyć rozszerzenie pakiety w host.json [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#packages---functions-2x) został pobrany i zainstalowany automatycznie podczas uruchamiania.

Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Powinieneś zobaczyć tę samą odpowiedź w przeglądarce, tak jak w poprzednim artykule.

Tym razem powiązania danych wyjściowych również tworzy kolejkę o nazwie `outqueue` w magazynie konta, a następnie dodaje komunikat przy użyciu tego samego ciągu.

Następnie użyj wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdź, czy wiadomość została dodana. Można również wyświetlić przy użyciu kolejki [Microsoft Azure Storage Explorer] [ Azure Storage Explorer] lub [witryny Azure portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Ustaw połączenie konta magazynu

Otwórz pliku local.settings.json, a następnie skopiuj wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Ustaw `AZURE_STORAGE_CONNECTION_STRING` zmiennej środowiskowej, aby parametry połączenia, używając następującego polecenia powłoki Bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Przy użyciu parametrów połączenia należy ustawić w `AZURE_STORAGE_CONNECTION_STRING` zmiennej środowiskowej, uzyskanie dostępu do konta magazynu bez konieczności ponownego uwierzytelnienia każdorazowo.

### <a name="query-the-storage-queue"></a>Kolejki magazynu zapytań

Możesz użyć [ `az storage queue list` ](/cli/azure/storage/queue#az-storage-queue-list) polecenie, aby wyświetlić kolejki usługi Storage na koncie, jak w poniższym przykładzie:

```azurecli-interactive
az storage queue list --output tsv
```

Dane wyjściowe tego polecenia obejmuje kolejkę o nazwie `outqueue`, czyli kolejki, który został utworzony, gdy uruchomiono funkcję.

Następnie użyj [ `az storage message peek` ](/cli/azure/storage/message#az-storage-message-peek) polecenie, aby wyświetlić komunikaty w tej kolejce, jak w poniższym przykładzie.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Ciąg zwracany powinna być taka sama, jak komunikat wysłany do testowania funkcji.

> [!NOTE]  
> Poprzedni przykład dekoduje zwracanego ciągu z formatu base64. Jest to spowodowane powiązania kolejki magazynu zapisu i odczytu z usługi Azure Storage jako [ciągi base64](functions-bindings-storage-queue.md#encoding).

Teraz nadszedł czas na ponownym opublikowaniu aplikacji zaktualizowanych funkcji na platformie Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Ponownie można użyć programu cURL lub w przeglądarce do przetestuj wdrożoną funkcję. Tak jak poprzednio Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Możesz [Sprawdź komunikat kolejki magazynu](#query-the-storage-queue) Aby zweryfikować, że powiązanie danych wyjściowych ponownie generuje nowy komunikat w kolejce.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

Użytkownik zaktualizował funkcję wyzwalaną przez protokół HTTP, aby zapisać dane w kolejce magazynu. Aby dowiedzieć się więcej na temat tworzenia funkcji platformy Azure przy użyciu języka Python, zobacz [przewodnik dla deweloperów języka Python w funkcji Azure](functions-reference-python.md) i [Wyzwalacze usługi Azure Functions i powiązania](functions-triggers-bindings.md).

Następnie należy włączyć monitorowanie usługi Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Włączanie integracji usługi Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/