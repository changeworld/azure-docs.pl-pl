---
title: Łączenie funkcji Java z usługą Azure Storage
description: Dowiedz się, jak połączyć z usługą Azure Storage funkcję Java wyzwalaną przez protokół HTTP przy użyciu powiązania danych wyjściowych magazynu kolejki.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78272801"
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

W projekcie Java powiązania są definiowane jako adnotacje wiązania w metodzie funkcji. Plik *function.json* jest następnie automatyczniegenerowany na podstawie tych adnotacji.

Przejdź do lokalizacji kodu funkcji pod _src/main/java_, otwórz plik projektu *Function.java* i `run` dodaj następujący parametr do definicji metody:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametr `msg` jest [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typem, który reprezentuje kolekcję ciągów, które są zapisywane jako komunikaty do powiązania danych wyjściowych po zakończeniu funkcji. W takim przypadku dane wyjściowe `outqueue`to kolejka magazynu o nazwie . Parametry połączenia dla konta magazynu jest `connection` ustawiana przez metodę. Zamiast samego ciągu połączenia należy przekazać ustawienie aplikacji zawierające parametry połączenia konta magazynu.

Definicja `run` metody powinna teraz wyglądać następująco:  

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

Teraz można użyć nowego `msg` parametru do zapisu do powiązania danych wyjściowych z kodu funkcji. Dodaj następujący wiersz kodu przed odpowiedzią na `name` powodzenie, aby dodać wartość do powiązania `msg` danych wyjściowych.

```java
msg.setValue(name);
```

Korzystając z powiązania danych wyjściowych, nie trzeba używać kodu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Powiązania środowiska wykonawczego i danych wyjściowych kolejki funkcji wykonaj te zadania za Ciebie.

Metoda `run` powinna teraz wyglądać następująco:

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

Ponieważ archetyp tworzy również zestaw testów, należy zaktualizować te testy `msg` do `run` obsługi nowego parametru w podpisie metody.  

Przejdź do lokalizacji kodu testowego pod _src/test/java_, otwórz plik projektu *Function.java* `//Invoke` i zastąp wiersz kodu pod następującym kodem.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Teraz możesz przystąpić do lokalnego wypróbowania nowego powiązania danych wyjściowych.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Tak jak poprzednio, użyj następującego polecenia, aby utworzyć projekt i uruchomić środowisko wykonawcze functions lokalnie:

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

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

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

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
