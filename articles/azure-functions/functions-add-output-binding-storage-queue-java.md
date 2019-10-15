---
title: Łączenie funkcji języka Java z usługą Azure Storage
description: Dowiedz się, jak połączyć funkcję Java wyzwalaną przez protokół HTTP z usługą Azure Storage przy użyciu powiązania wyjściowego magazynu kolejki.
author: ggailey777
ms.author: glenga
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: c78630af7d09cc911862c8e823c5dfeee9cabbd9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333465"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Łączenie funkcji języka Java z usługą Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak zintegrować funkcję utworzoną w [poprzednim artykule szybki start](functions-create-first-java-maven.md) z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić to połączenie, użyj konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu wykonaj kroki opisane w [części 1 przewodnika Szybki Start dotyczącej języka Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Teraz możesz dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W projekcie Java powiązania są zdefiniowane jako adnotacje wiążące dla metody Function. Plik *Function. JSON* jest następnie generowany automatycznie na podstawie tych adnotacji.

Przejdź do lokalizacji kodu funkcji w obszarze _src/Main/Java_, Otwórz plik projektu *Functions. Java* i Dodaj następujący parametr do definicji metody `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametr `msg` jest typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , który reprezentuje kolekcję ciągów, które są zapisywane jako komunikaty do powiązania wyjściowego po zakończeniu działania funkcji. W takim przypadku dane wyjściowe są kolejki magazynu o nazwie `outqueue`. Parametry połączenia dla konta magazynu są ustawiane za pomocą metody `connection`. Zamiast parametrów połączenia należy przekazać ustawienie aplikacji, które zawiera parametry połączenia konta magazynu.

Definicja metody `run` powinna teraz wyglądać podobnie do poniższego przykładu:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Teraz można użyć nowego parametru `msg` do zapisu w powiązaniu danych wyjściowych z kodu funkcji. Dodaj następujący wiersz kodu przed odpowiedzią sukcesu, aby dodać wartość `name` do powiązania wyjściowego `msg`.

```java
msg.setValue(name);
```

W przypadku korzystania z powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

Metoda `run` powinna teraz wyglądać podobnie do poniższego przykładu:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Aktualizowanie testów

Ponieważ Archetype również tworzy zestaw testów, należy zaktualizować te testy, aby obsłużyć nowy parametr `msg` w sygnaturze metody `run`.  

Przejdź do lokalizacji kodu testu w obszarze _src/test/Java_, Otwórz plik projektu *Functions. Java* i Zastąp wiersz kodu w obszarze `//Invoke` następującym kodem.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Teraz możesz przystąpić do wypróbowania lokalnego nowego powiązania danych wyjściowych.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Tak jak wcześniej, użyj następującego polecenia, aby skompilować projekt i uruchomić środowisko uruchomieniowe funkcji lokalnie:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Ponieważ włączono pakiety rozszerzeń w pliku host. JSON, [rozszerzenie powiązania magazynu](functions-bindings-storage-blob.md#packages---functions-2x) zostało pobrane i zainstalowane dla Ciebie podczas uruchamiania, wraz z innymi rozszerzeniami powiązań firmy Microsoft.

Jak wcześniej, wyzwól funkcję z wiersza polecenia przy użyciu Zwinięciea w nowym oknie terminalu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tym razem powiązanie danych wyjściowych tworzy również kolejkę o nazwie `outqueue` na koncie magazynu i dodaje komunikat z tym samym ciągiem.

Następnie użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić nową kolejkę i sprawdzić, czy wiadomość została dodana. Możesz również wyświetlić kolejkę za pomocą [Eksplorator usługi Microsoft Azure Storage][Azure Storage Explorer] lub [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Wdróż ponownie projekt 

Aby zaktualizować opublikowaną aplikację, ponownie uruchom następujące polecenie:  

```azurecli
mvn azure-functions:deploy
```

Ponownie można użyć Zwinięciea do przetestowania wdrożonej funkcji. Tak jak wcześniej, należy przekazać wartość `AzureFunctions` w treści żądania POST na adres URL, jak w poniższym przykładzie:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Możesz [sprawdzić ponownie komunikat kolejki magazynu](#query-the-storage-queue) , aby sprawdzić, czy powiązanie danych wyjściowych generuje nowy komunikat w kolejce zgodnie z oczekiwaniami.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat tworzenia Azure Functions przy użyciu języka Java, zobacz [przewodnik dewelopera Azure Functions Java](functions-reference-java.md) oraz [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md). Aby zapoznać się z przykładami kompletnych projektów funkcji w języku Java, zobacz [przykłady funkcji języka Java](/samples/browse/?products=azure-functions&languages=Java). 

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/