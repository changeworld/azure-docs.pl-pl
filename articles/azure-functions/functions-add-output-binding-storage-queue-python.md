---
title: Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python
description: Integrowanie kolejki usługi Azure Storage z funkcją języka Python za pomocą powiązania danych wyjściowych.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: f5527e0e636c3f8c9ee3723570ed9811f0df3641
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198483"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python

W tym artykule opisano integrację kolejki usługi Azure Storage z funkcją i kontem magazynu utworzonym w temacie [Tworzenie funkcji języka Python wyzwalanej przez protokół http](functions-create-first-function-python.md). Tę integrację można osiągnąć za pomocą *powiązania danych wyjściowych* , które zapisuje dane z żądania HTTP do wiadomości w kolejce. Wykonanie tego artykułu nie wiąże się z żadnymi dodatkowymi kosztami powyżej kilku centów z poprzedniego przewodnika Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki szybkiego startu, aby [utworzyć funkcję języka Python wyzwalającą protokół http](functions-create-first-function-python.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj ponownie instrukcje, aby odtworzyć aplikację Functions na platformie Azure, ale pozostawić zasoby na miejscu.

## <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie parametrów połączenia usługi Azure Storage

Po utworzeniu aplikacji funkcji na platformie Azure w poprzednim przewodniku szybki start utworzono również konto magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając ustawienia do pliku *Local. Settings. JSON* , można użyć zapisu tego połączenia do kolejki magazynu w ramach tego samego konta podczas lokalnego uruchamiania funkcji. 

1. Z poziomu katalogu głównego projektu uruchom następujące polecenie, zastępując `<app_name>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki Start. To polecenie spowoduje zastąpienie wszelkich istniejących wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otwórz plik *Local. Settings. JSON* i Znajdź wartość o nazwie `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Używasz nazwy `AzureWebJobsStorage` i parametrów połączenia w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *Local. Settings. JSON* zawiera wpisy tajne pobrane z platformy Azure, należy zawsze wykluczyć ten plik z kontroli źródła. Plik *. gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.

## <a name="add-an-output-binding-to-functionjson"></a>Dodawanie powiązania danych wyjściowych do funkcji Function. JSON

Chociaż funkcja może mieć tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych, które umożliwiają łączenie się z innymi usługami i zasobami platformy Azure bez konieczności pisania niestandardowego kodu integracji. Te powiązania należy zadeklarować w pliku *Function. JSON* w folderze funkcji odpowiednio do języka używanego przez funkcję.

Z poprzedniego przewodnika Szybki Start plik *Function. JSON* w folderze *HttpExample* zawiera dwa powiązania w kolekcji `bindings`:

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
    }
  ]
}
```

Każde powiązanie ma co najmniej typ, kierunek i nazwę. W powyższym przykładzie pierwsze powiązanie jest typu `httpTrigger` z kierunekem `in`. Dla `in` kierunek `name` określa nazwę parametru wejściowego, który jest wysyłany do funkcji po wywołaniu przez wyzwalacz.

Drugie powiązanie jest typu `http` z kierunkiem `out`, w tym przypadku specjalna `name` `$return` wskazuje, że to powiązanie używa wartości zwracanej funkcji zamiast podać parametr wejściowy.

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, należy dodać powiązanie `out` typu `queue` z nazwą `msg`, jak pokazano w poniższym kodzie:

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

W tym przypadku `msg` jest przyznany do funkcji jako argument wyjściowy. W przypadku typu `queue` należy również określić nazwę kolejki w `queueName` i podać *nazwę* połączenia usługi Azure Storage (z pliku *Local. Settings. json*) w `connection`.

Aby uzyskać więcej informacji na temat szczegółów powiązań, zobacz [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md) oraz [konfigurację wyjściową kolejki](functions-bindings-storage-queue.md#output---configuration).

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Za pomocą powiązania kolejki określonego w *funkcji Function. JSON*można teraz zaktualizować funkcję w taki sposób, aby otrzymywać `msg` parametr wyjściowy i pisać komunikaty do kolejki.

Zaktualizuj *HttpExample\\\_\_init\_\_. PR* aby dopasować następujący kod, dodając parametr `msg` do definicji funkcji i `msg.set(name)` w instrukcji `if name:`.

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

`msg` parametr jest wystąpieniem [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). `set` Metoda zapisuje komunikat ciągu do kolejki, w tym przypadku nazwa została przeniesiona do funkcji w ciągu zapytania adresu URL.

Zwróć uwagę, że *nie* musisz pisać żadnego kodu do uwierzytelniania, pobieranie odwołania do kolejki lub zapisywanie danych. Wszystkie te zadania integracji są wygodnie obsługiwane w środowisku uruchomieniowym Azure Functions i powiązania danych wyjściowych kolejki.

## <a name="run-and-test-the-function-locally"></a>Uruchom i Przetestuj funkcję lokalnie

1. W terminalu lub wierszu polecenia przejdź do folderu projektu funkcji, *LocalFunctionProj*.

1. Uruchom hosta Azure Functions Runtime przy użyciu następującego polecenia.

    ```
    func host start
    ```

1. Po zakończeniu uruchamiania i zobaczysz adres URL `HttpExample` punktu końcowego, skopiuj jego adres URL do przeglądarki i dołącz ciąg zapytania `?name=<your-name>`, wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Guido`. W przeglądarce powinien zostać wyświetlony komunikat podobny do `Hello Guido` jak w poprzednim artykule.

    Jeśli nie widzisz punktu końcowego `HttpExample`, Zatrzymaj hosta za pomocą **kombinacji Ctrl**+**C** i sprawdź, czy dane wyjściowe są błędne. Na przykład host nie aktywuje punktu końcowego, jeśli wystąpi błąd w *funkcji Function. JSON*. Sprawdź również, czy `func host start` jest uruchomiony z folderu Project Functions, a nie folderu *HttpExample* .

1. Gdy skończysz, Zatrzymaj hosta za pomocą **kombinacji klawiszy Ctrl**+**C**.

> [!TIP]
> Podczas uruchamiania Host pobiera i instaluje [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#add-to-your-functions-app) oraz inne rozszerzenia powiązań firmy Microsoft. Ta instalacja jest wykonywana, ponieważ rozszerzenia powiązań są domyślnie włączone w pliku *host. JSON* o następujących właściwościach:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Jeśli występują błędy związane z rozszerzeniami powiązań, sprawdź, czy powyższe właściwości znajdują się w pliku *host. JSON*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie komunikatu w kolejce usługi Azure Storage

Gdy funkcja generuje odpowiedź HTTP dla przeglądarki sieci Web, wywoła również `msg.set(name)`, która zapisuje komunikat do kolejki usługi Azure Storage o nazwie `outqueue`, jak określono w powiązaniu kolejki. Kolejkę można wyświetlić w [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) lub [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). Możesz również wyświetlić kolejkę w interfejsie wiersza polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórz plik *Local. JSON* projektu funkcji i skopiuj wartość parametrów połączenia. W terminalu lub w oknie polecenia Uruchom następujące polecenie, aby utworzyć zmienną środowiskową o nazwie `AZURE_STORAGE_CONNECTION_STRING`, wklejając określone parametry połączenia zamiast `<connection_string>`. (Ta zmienna środowiskowa oznacza, że nie trzeba podawać parametrów połączenia dla każdego kolejnego polecenia przy użyciu argumentu `--connection-string`).

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Obowiązkowe Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na Twoim koncie. Dane wyjściowe tego polecenia powinny zawierać kolejkę o nazwie `outqueue`, która została utworzona, gdy funkcja zapisała swój pierwszy komunikat do tej kolejki.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Użyj [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) polecenia, aby wyświetlić komunikaty w tej kolejce, która powinna być pierwszą nazwą użytą podczas testowania funkcji wcześniej. Polecenie pobiera pierwszy komunikat w kolejce w [kodowaniu Base64](functions-bindings-storage-queue.md#encoding), więc należy również zdekodować komunikat, aby był wyświetlany jako tekst.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    Ponieważ musisz usunąć odwołanie do kolekcji komunikatów i zdekodować ją z formatu Base64, uruchom program PowerShell i użyj polecenia programu PowerShell.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Ponowne wdrażanie projektu na platformie Azure

Teraz, gdy funkcja została przetestowana lokalnie i została zweryfikowana, że zapisała komunikat do kolejki usługi Azure Storage, możesz ponownie wdrożyć projekt, aby zaktualizować punkt końcowy działający na platformie Azure.

1. W folderze *LocalFunctionsProj* użyj polecenia [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) , aby ponownie wdrożyć projekt, zastępując`<app_name>` nazwą aplikacji.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Tak jak w poprzednim przewodniku Szybki Start, użyj przeglądarki lub zapełnienia, aby przetestować ponownie wdrożoną funkcję.

    # <a name="browsertabbrowser"></a>[Przeglądarka](#tab/browser)
    
    Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych polecenia Publikuj na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Azure`. Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

    ![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curltabcurl"></a>[odsłon](#tab/curl)
    
    Uruchom [zwinięcie](https://curl.haxx.se/) z **adresem URL Invoke**, dołączając parametr `&name=Azure`. Danymi wyjściowymi polecenia powinien być tekst "Hello Azure".
    
    ![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu programu zwinięcie](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Sprawdź ponownie kolejkę magazynu, zgodnie z opisem w poprzedniej sekcji, aby sprawdzić, czy zawiera ona nowy komunikat zapisany w kolejce.

    Jeśli używasz interfejsu wiersza polecenia platformy Azure do sprawdzenia kolejki, polecenie `az storage message peek` wyświetla tylko pierwszą wiadomość w kolejce. Aby symulować przetwarzanie komunikatów, należy zamiast tego użyć `az storage message get` ze wszystkimi tymi samymi argumentami. Polecenie `get` zwraca komunikat i usuwa go z kolejki. Następnie można powtórzyć to samo polecenie do momentu, gdy kolejka jest pusta (a polecenie powoduje wystąpienie błędu).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przejdziesz do następnego kroku, [Włącz integrację Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource), Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włączanie integracji Application Insights z aplikacją Azure Functions](functions-monitoring.md#manually-connect-an-app-insights-resource)

Inne zasoby:

- [Przykłady kompletnych projektów funkcji w języku Python](/samples/browse/?products=azure-functions&languages=python).
- [Przewodnik dewelopera w języku Python Azure Functions](functions-reference-python.md)
- [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md).
- [Strona cennika funkcji](https://azure.microsoft.com/pricing/details/functions/)
- [Szacowanie kosztów planu zużycia](functions-consumption-costs.md) .
