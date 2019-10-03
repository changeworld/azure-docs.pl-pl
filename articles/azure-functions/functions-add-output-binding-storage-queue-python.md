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
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839193"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Dodawanie powiązania kolejki usługi Azure Storage do funkcji języka Python

Azure Functions umożliwia łączenie usług platformy Azure i innych zasobów z funkcjami bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są przekazywane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tym artykule pokazano, jak zintegrować funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-function-python.md) z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić to połączenie, użyj konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu wykonaj kroki opisane w [części 1 przewodnika Szybki Start](functions-create-first-function-python.md)dla języka Python.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teraz możesz dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction`, `type` i unikatowych `name` do zdefiniowania w pliku Function. JSON. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

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
> Ponieważ w poprzednim przewodniku szybki start włączono pakiety rozszerzeń w pliku host. JSON, [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#packages---functions-2x) zostało pobrane i zainstalowane dla Ciebie podczas uruchamiania, wraz z innymi rozszerzeniami powiązań firmy Microsoft.

Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i uruchom żądanie. W przeglądarce powinna zostać wyświetlona taka sama odpowiedź jak w poprzednim artykule.

Tym razem powiązanie danych wyjściowych tworzy również kolejkę o nazwie `outqueue` na koncie magazynu i dodaje komunikat z tym samym ciągiem.

Następnie użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdzić, czy wiadomość została dodana. Możesz również wyświetlić kolejkę za pomocą [Eksplorator usługi Microsoft Azure Storage][Azure Storage Explorer] lub [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Wykonywanie zapytań względem kolejki magazynu

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Teraz można ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Ponownie można użyć zawieszeń lub przeglądarki do przetestowania wdrożonej funkcji. Tak jak wcześniej, należy dołączyć ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Możesz [sprawdzić komunikat kolejki magazynu](#query-the-storage-queue) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie wygenerowało nową wiadomość w kolejce.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat opracowywania Azure Functions przy użyciu języka Python, zobacz [Przewodnik dla deweloperów Azure Functions Python](functions-reference-python.md) oraz [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md). Aby zapoznać się z przykładami kompletnych projektów funkcji w języku Python, zobacz [przykłady funkcji języka Python](/samples/browse/?products=azure-functions&languages=python). 

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/