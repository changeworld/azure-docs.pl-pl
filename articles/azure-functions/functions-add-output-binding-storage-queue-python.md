---
title: Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python
description: Dowiedz się, jak dodać powiązanie danych wyjściowych kolejki usługi Azure Storage do funkcji języka Python za pomocą interfejsu wiersza polecenia platformy Azure i podstawowych narzędzi.
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
ms.openlocfilehash: 34ec7c678410b2e0814f8dbb7a69257886cb891d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639114"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python

Azure Functions umożliwia łączenie usług platformy Azure i innych zasobów z funkcjami bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są przekazywane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tym artykule pokazano, jak zintegrować funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-function-python.md) z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić to połączenie, użyj konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu wykonaj kroki opisane w [części 1 przewodnika Szybki Start](functions-create-first-function-python.md)dla języka Python.

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W poprzednim artykule szybki start utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do pliku Local. Settings. JSON. Uruchom następujące polecenie Azure Functions Core Tools, aby pobrać ustawienia do pliku Local. Settings. JSON, `<APP_NAME>` zastępując je nazwą aplikacji funkcji z poprzedniego artykułu:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Może być konieczne zalogowanie się do konta platformy Azure.

> [!IMPORTANT]  
> Ponieważ zawiera wpisy tajne, plik Local. Settings. JSON nigdy nie jest publikowany i powinien być wykluczony z kontroli źródła.

Wymagana jest wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. To połączenie służy do sprawdzania, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.

## <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teraz możesz dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction` `type`, aby w pliku Function. JSON został zdefiniowany `name` , a i unikatowy. W zależności od typu powiązania mogą być wymagane dodatkowe właściwości. [Konfiguracja wyjściowa kolejki](functions-bindings-storage-queue.md#output---configuration) opisuje pola wymagane dla powiązania kolejki usługi Azure Storage.

Aby utworzyć powiązanie, należy dodać obiekt konfiguracji powiązania do pliku Function. JSON. Edytuj plik Function. JSON w folderze HttpTrigger, aby dodać obiekt do `bindings` tablicy, która ma następujące właściwości:

| Właściwość | Value | Opis |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Nazwa identyfikująca parametr powiązania, do którego odwołuje się kod. |
| **`type`** | `queue` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **`direction`** | `out` | Powiązanie jest powiązaniem wyjściowym. |
| **`queueName`** | `outqueue` | Nazwa kolejki, w której ma zostać zapisywany powiązania. Gdy element `queueName` nie istnieje, powiązanie tworzy je przy pierwszym użyciu. |
| **`connection`** | `AzureWebJobsStorage` | Nazwa ustawienia aplikacji, które zawiera parametry połączenia dla konta magazynu. `AzureWebJobsStorage` Ustawienie zawiera parametry połączenia dla konta magazynu utworzonego za pomocą aplikacji funkcji. |

Plik Function. JSON powinien teraz wyglądać podobnie do tego przykładu:

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

`name` Po skonfigurowaniu programu można rozpocząć korzystanie z niego, aby uzyskać dostęp do powiązania jako atrybutu metody w podpisie funkcji. W poniższym przykładzie `msg` jest wystąpieniem [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest).

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

W przypadku korzystania z powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Tak jak wcześniej, użyj następującego polecenia, aby uruchomić środowisko uruchomieniowe funkcji lokalnie:

```bash
func host start
```

> [!NOTE]  
> Ponieważ w poprzednim przewodniku szybki start włączono pakiety rozszerzeń w pliku host. JSON, [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#packages---functions-2x) zostało pobrane i zainstalowane dla Ciebie podczas uruchamiania, wraz z innymi rozszerzeniami powiązań firmy Microsoft.

Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg `?name=<yourname>` zapytania do tego adresu URL i uruchom żądanie. W przeglądarce powinna zostać wyświetlona taka sama odpowiedź jak w poprzednim artykule.

Tym razem powiązanie danych wyjściowych tworzy również kolejkę o `outqueue` nazwie na koncie magazynu i dodaje komunikat z tym samym ciągiem.

Następnie użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdzić, czy wiadomość została dodana. Możesz również wyświetlić kolejkę za pomocą [Eksplorator usługi Microsoft Azure Storage][Azure Storage Explorer] lub [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz plik Local. Settings. JSON i skopiuj wartość `AzureWebJobsStorage`, która jest ciągiem połączenia konta magazynu. Ustaw zmienną `AZURE_STORAGE_CONNECTION_STRING` środowiskową na parametry połączenia za pomocą tego polecenia bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Po ustawieniu parametrów połączenia w `AZURE_STORAGE_CONNECTION_STRING` zmiennej środowiskowej można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelniania za każdym razem.

### <a name="query-the-storage-queue"></a>Wykonywanie zapytań względem kolejki magazynu

Możesz użyć [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na koncie, tak jak w poniższym przykładzie:

```azurecli-interactive
az storage queue list --output tsv
```

Dane wyjściowe tego polecenia obejmują kolejkę o nazwie `outqueue`, która jest kolejką utworzoną podczas uruchamiania funkcji.

Następnie użyj [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) polecenia, aby wyświetlić komunikaty w tej kolejce, jak w tym przykładzie:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Zwracany ciąg powinien być taki sam jak komunikat wysłany do przetestowania funkcji.

> [!NOTE]  
> Poprzedni przykład dekoduje zwracany ciąg z base64. Wynika to z faktu, że powiązania magazynu kolejek zapisują i odczytują dane z usługi Azure Storage jako [ciągi Base64](functions-bindings-storage-queue.md#encoding).

Teraz można ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Ponownie można użyć zawieszeń lub przeglądarki do przetestowania wdrożonej funkcji. Tak jak wcześniej, dołącz ciąg `&name=<yourname>` zapytania do adresu URL, jak w poniższym przykładzie:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Możesz [sprawdzić komunikat kolejki magazynu](#query-the-storage-queue) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie wygenerowało nową wiadomość w kolejce.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat opracowywania Azure Functions przy użyciu języka Python, zobacz [Przewodnik dla deweloperów Azure Functions Python](functions-reference-python.md) oraz [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md).

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Włącz integrację Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/