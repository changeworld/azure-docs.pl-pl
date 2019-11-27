---
title: Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python
description: Dowiedz się, jak dodać powiązanie danych wyjściowych kolejki usługi Azure Storage do funkcji języka Python.
ms.date: 10/02/2019
ms.topic: quickstart
ms.openlocfilehash: dede135da56e9ed1eaaed2ae0f7b5cd14d08195c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231250"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak zintegrować funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-function-python.md) z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić to połączenie, użyj konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu wykonaj kroki opisane w [części 1 przewodnika Szybki Start](functions-create-first-function-python.md)dla języka Python.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teraz możesz dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction`, `type`i unikatowy `name` do zdefiniowania w pliku Function. JSON. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

W przypadku korzystania z powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Tak jak wcześniej, użyj następującego polecenia, aby uruchomić środowisko uruchomieniowe funkcji lokalnie:

```bash
func host start
```

> [!NOTE]  
> Ponieważ włączono pakiety rozszerzeń w pliku host. JSON, [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#packages---functions-2x) zostało pobrane i zainstalowane dla Ciebie podczas uruchamiania, wraz z innymi rozszerzeniami powiązań firmy Microsoft.

Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i uruchom żądanie. W przeglądarce powinna zostać wyświetlona taka sama odpowiedź jak w poprzednim artykule.

Tym razem powiązanie danych wyjściowych tworzy również kolejkę o nazwie `outqueue` na koncie magazynu i dodaje komunikat z tym samym ciągiem.

Następnie użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdzić, czy wiadomość została dodana. Możesz również wyświetlić kolejkę za pomocą [Eksplorator usługi Microsoft Azure Storage][Azure Storage Explorer] lub [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Wdróż ponownie projekt 

Aby zaktualizować opublikowaną aplikację, użyj polecenia [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools, aby wdrożyć kod projektu na platformie Azure. W tym przykładzie Zastąp `<APP_NAME>` nazwą aplikacji.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Ponownie można użyć zawieszeń lub przeglądarki do przetestowania wdrożonej funkcji. Tak jak wcześniej, należy dołączyć ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Możesz [sprawdzić ponownie komunikat kolejki magazynu](#query-the-storage-queue) , aby sprawdzić, czy powiązanie danych wyjściowych generuje nowy komunikat w kolejce zgodnie z oczekiwaniami.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat opracowywania Azure Functions przy użyciu języka Python, zobacz [Przewodnik dla deweloperów Azure Functions Python](functions-reference-python.md) oraz [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md). Aby zapoznać się z przykładami kompletnych projektów funkcji w języku Python, zobacz [przykłady funkcji języka Python](/samples/browse/?products=azure-functions&languages=python). Aby dowiedzieć się więcej o cenach, zobacz [stronę cennika funkcji](https://azure.microsoft.com/pricing/details/functions/) oraz artykuł [szacowanie kosztów planu zużycia](functions-consumption-costs.md) .

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/