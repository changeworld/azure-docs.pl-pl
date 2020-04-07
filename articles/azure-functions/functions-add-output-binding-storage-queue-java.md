---
title: Łączenie funkcji Java z usługą Azure Storage
description: Dowiedz się, jak połączyć z usługą Azure Storage funkcję Java wyzwalaną przez protokół HTTP przy użyciu powiązania danych wyjściowych magazynu kolejki.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673285"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Łączenie funkcji Java z usługą Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak zintegrować funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-java-maven.md) z kolejką usługi Azure Storage. Powiązanie danych wyjściowych, które można dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

Większość powiązań wymaga przechowywanego ciągu połączenia, którego funkcja używa do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić to połączenie, należy użyć konta Magazyn utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już `AzureWebJobsStorage`przechowywane w ustawieniach aplikacji o nazwie .  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu wykonaj kroki opisane w [części 1 przewodnika Java Szybki start](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Włączanie pakietów rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Teraz możesz przystąpić do lokalnego wypróbowania nowego powiązania danych wyjściowych.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Tak jak poprzednio, użyj następującego polecenia, aby utworzyć projekt i uruchomić środowisko wykonawcze functions lokalnie:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Ponieważ pakiety rozszerzeń zostały włączone w pliku host.json, [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#add-to-your-functions-app) zostało pobrane i zainstalowane podczas uruchamiania, wraz z innymi rozszerzeniami powiązania firmy Microsoft.

Tak jak poprzednio, wyzwalanie funkcji z wiersza polecenia za pomocą cURL w nowym oknie terminala:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tym razem powiązanie danych wyjściowych `outqueue` tworzy również kolejkę o nazwie na koncie magazynu i dodaje komunikat z tym samym ciągiem.

Następnie użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdzić, czy wiadomość została dodana. Kolejkę można również wyświetlić za pomocą [Eksploratora magazynu platformy Microsoft Azure][Azure Storage Explorer] lub w [witrynie Azure.](https://portal.azure.com)

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Ponowne wdrożenie projektu 

Aby zaktualizować opublikowaną aplikację, uruchom ponownie następujące polecenie:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Ponownie można użyć cURL, aby przetestować wdrożoną funkcję. Tak jak poprzednio, przekaż wartość `AzureFunctions` w treści żądania POST do adresu URL, jak w tym przykładzie:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Można [ponownie sprawdzić komunikat kolejki magazynu,](#query-the-storage-queue) aby sprawdzić, czy powiązanie danych wyjściowych generuje nową wiadomość w kolejce, zgodnie z oczekiwaniami.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Zaktualizowano funkcję wyzwalaną przez protokół HTTP, aby zapisywać dane w kolejce magazynu. Aby dowiedzieć się więcej na temat tworzenia usług Azure Functions w języku Java, zobacz [przewodnik dla deweloperów języka Azure Functions Java](functions-reference-java.md) oraz [wyzwalacze i powiązania usług Azure Functions.](functions-triggers-bindings.md) Przykłady kompletnych projektów funkcji w języku Java można znaleźć w [przykładach funkcji Java](/samples/browse/?products=azure-functions&languages=Java). 

Następnie należy włączyć monitorowanie usługi Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
