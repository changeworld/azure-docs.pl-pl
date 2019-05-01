---
title: Dokumentacja dla deweloperów języka Java dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Zrozumienie, jak tworzyć funkcje za pomocą języka Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bezserwerowa, języka java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: cc598afbbdf7f3a1b12089b50ba747c5220ba1fa
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922928"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions Java

Środowisko uruchomieniowe usługi Azure Functions obsługuje [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/).

Ten przewodnik zawiera informacje dotyczące niewymagającego pisania usługi Azure Functions przy użyciu języka Java.

Funkcja języka Java jest `public` metoda ozdobione adnotacji `@FunctionName`. Ta metoda definiuje wpis dla funkcji języka java i musi być unikatowa w danym pakietu. 

W tym artykule założono, że już znasz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md). Należy również wykonać szybkiego startu usługi Functions, aby utworzyć pierwszą funkcję, za pomocą [programu Visual Studio Code](functions-create-first-function-vs-code.md) lub [przy użyciu narzędzia maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Model programowania 

Pojęcia związane z [wyzwalaczy i powiązań](functions-triggers-bindings.md) mają zasadnicze znaczenie w usłudze Azure Functions. Wyzwalacze Rozpocznij wykonywanie Twojego kodu. Powiązania umożliwiają sposób przekazywania danych do i zwrócić dane z funkcji, bez konieczności pisania kodu dostępu do danych niestandardowych.

## <a name="folder-structure"></a>Struktura folderów

Poniżej przedstawiono strukturę folderu projektu funkcji platformy Azure w języku Java:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Brak udostępnionej [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu (początku) i plik konfiguracji powiązania (function.json).

Można umieścić więcej niż jedną funkcję w projekcie. Należy unikać umieszczenie funkcji w oddzielnych plikach JAR. FunctionApp w katalogu docelowym jest o tym, co zostanie wdrożona do aplikacji funkcji na platformie Azure.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

 Usługa Azure functions są wywoływane przez wyzwalacz, takie jak żądania HTTP, czasomierz lub aktualizacji danych. Funkcja musi przetworzyć tego wyzwalacza i inne dane wejściowe do wyprodukowania jednego lub więcej danych wyjściowych.

Korzystanie z adnotacji Java objęte [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pakietu, aby powiązać dane wejściowe i wyjściowe metody. Aby uzyskać więcej informacji, zobacz [dokumenty referencyjne języka Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Należy skonfigurować konto usługi Azure Storage w swojej [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) lokalnie uruchomić wyzwalacze usługi Azure Storage Blob, kolejki lub tabeli.

Przykład:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Oto wygenerowanego odpowiadającego `function.json` przez [maven wtyczki, azure functions w-](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Zestaw JDK środowiska uruchomieniowego dostępność i pomoc techniczna 

Pobieranie i używanie [Azul Zulu Enterprise na platformie Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDK 8 z [firmy Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) potrzeby lokalnego programowania aplikacji funkcji w języku Java. Usługa Azure Functions używa środowiska uruchomieniowego Azul Java 8 JDK, podczas wdrażania aplikacji funkcji w chmurze.

[Pomoc techniczna platformy Azure](https://azure.microsoft.com/support/) w przypadku problemów z JDK i funkcja jest dostępna z aplikacji [plan pomocy technicznej kwalifikowaną](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Bibliotek innych firm 

Usługa Azure Functions obsługuje korzystanie z bibliotek innych firm. Domyślnie wszystkie zależności są określone w projekcie `pom.xml` pliku zostaną automatycznie dołączone podczas [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cel. W przypadku bibliotek, które nie jest określony jako zależności w `pom.xml` pliku, umieść je w `lib` katalogu w katalogu głównym funkcji. Zależności są umieszczane w `lib` katalog zostanie dodany do modułu ładującego klasę systemu w czasie wykonywania.

`com.microsoft.azure.functions:azure-functions-java-library` Zależności znajduje się na ścieżce klasy domyślnie i nie musi być objęte `lib` katalogu. Ponadto na liście zależności [tutaj](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) zostaną dodane do ścieżki klasy przez [azure-functions-java-proces roboczy](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Obsługa typu danych

Można użyć zwykłych starych obiektów Java (Pojo), typy zdefiniowane w `azure-functions-java-library` lub pierwotne typy danych, takich jak ciąg, liczba całkowita, aby powiązać powiązania wejścia/wyjścia.

### <a name="plain-old-java-objects-pojos"></a>Zwykłych starych obiektów Java (Pojo)

Do konwersji danych wejściowych do obiektu typu POJO, [azure-functions-java-proces roboczy](https://github.com/Azure/azure-functions-java-worker) używa [gson](https://github.com/google/gson) biblioteki. Obiektu typu POJO typy używane jako dane wejściowe do funkcji powinny być `public`.

### <a name="binary-data"></a>Dane binarne

Powiąż binarnych danych wejściowych lub wyjściowych, aby `byte[]` , ustawiając `dataType` w swojej function.json do `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Użyj `Optional<T>` IF oczekuje wartości null

## <a name="bindings"></a>Powiązania

Powiązania danych wejściowych i wyjściowych zapewniają deklaratywną metodę, aby nawiązać połączenie danych z poziomu kodu. Funkcja mogą mieć wiele danych wejściowych i wyjściowych powiązania.

### <a name="example-input-binding"></a>Przykład powiązania danych wejściowych

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Ta funkcja jest wywoływana z żądania HTTP. 
- Ładunek żądania HTTP jest przekazywany jako `String` dla argumentu `inputReq`
- Jeden wpis jest pobierany z usługi Azure Table Storage i jest przekazywany jako `TestInputData` do argumentu `inputData`.

Aby móc odbierać partii danych wejściowych, można powiązać `String[]`, `POJO[]`, `List<String>` lub `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Ta funkcja pobiera wyzwalane, gdy ma nowych danych w Centrum zdarzeń skonfigurowany. Jako `cardinality` ustawiono `MANY`, funkcja otrzymuje partię komunikatów z Centrum zdarzeń. EventData z Centrum zdarzeń są konwertowane na `TestEventData` do wykonywania funkcji.

### <a name="example-output-binding"></a>Przykład powiązania danych wyjściowych

Można powiązać przy użyciu wartości zwracanej przez powiązanie danych wyjściowych `$return` 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

W przypadku wielu powiązania danych wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

Aby wysłać wiele wartości danych wyjściowych, należy użyć `OutputBinding<T>` zdefiniowane w `azure-functions-java-library` pakietu. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Powyżej funkcja jest wywoływana HttpRequest i zapisuje wielu wartości w kolejce platformy Azure

## <a name="httprequestmessage-and-httpresponsemessage"></a>Obiekt HttpRequestMessage i obiektu HttpResponseMessage

 Obiekt HttpRequestMessage i obiektu HttpResponseMessage typy są definiowane w `azure-functions-java-library` typów pomocnika do pracy z funkcjami HttpTrigger

| Specjalistyczną odmianą      |       Środowisko docelowe        | Typowy                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz HTTP     | Metoda, nagłówków lub zapytania |
| `HttpResponseMessage` | Powiązanie danych wyjściowych HTTP | Status powrotu niż 200   |

## <a name="metadata"></a>Metadane

Wyślij kilka wyzwalaczy [wyzwolić metadanych](/azure/azure-functions/functions-triggers-bindings) wraz z danych wejściowych. Można użyć adnotacji `@BindingName` do powiązania do wyzwolenia metadanych


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
W powyższym przykładzie `queryValue` jest powiązany z parametru ciągu zapytania `name` adres URL żądania Http `http://{example.host}/api/metadata?name=test`. Oto inny przykład, który powiązanie `Id` z metadanych wyzwalacz kolejki

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Nazwa podana w adnotacji musi być zgodna właściwości metadanych

## <a name="execution-context"></a>Kontekst wykonywania

`ExecutionContext` zdefiniowane w `azure-functions-java-library` zawiera metody pomocnicze do komunikowania się z środowisko uruchomieniowe usługi functions.

### <a name="logger"></a>Rejestrator

Użyj `getLogger` zdefiniowane w `ExecutionContext` na zapisywanie dzienników z kodu funkcji.

Przykład:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Wyświetlanie dzienników i śledzenia

Można użyć wiersza polecenia platformy Azure do strumienia Java stdout i stderr rejestrowania, a także innych rejestrowanie aplikacji. 

Konfigurowanie aplikacji funkcji do zapisania rejestrowania aplikacji przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Przesyłanie strumieniowe danych wyjściowych rejestrowania dla aplikacji funkcji przy użyciu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, Bash lub sesji terminalowej i wprowadź następujące polecenie:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log) polecenie ma funkcje umożliwiające filtrowanie danych wyjściowych przy użyciu `--provider` opcji. 

Aby pobrać pliki dziennika jako pojedynczy plik ZIP, przy użyciu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, Bash lub sesji terminalowej i wprowadź następujące polecenie:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Musisz włączyć systemu plików, logowania w witrynie Azure Portal lub interfejsu wiersza polecenia platformy Azure, przed uruchomieniem tego polecenia.

## <a name="environment-variables"></a>Zmienne środowiskowe

W przypadku funkcji [ustawienia aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), takie jak połączenia z usługą ciągów, są widoczne jako zmiennych środowiskowych podczas wykonywania. Możesz uzyskać dostęp tych ustawień, korzystając z, `System.getenv("AzureWebJobsStorage")`

Przykład:

Dodaj [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) przy użyciu nazwy testAppSettingValue testAppSetting i wartości

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat tworzenia funkcji platformy Azure w języku Java zobacz następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* Lokalne programowanie i debugowanie za pomocą [programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), i [Eclipse](functions-create-maven-eclipse.md). 
* [Zdalne debugowanie środowiska Java w usłudze Azure Functions przy użyciu programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Wtyczka maven plugin for Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) -Usprawnij Tworzenie funkcji przy użyciu `azure-functions:add` cel i przygotowywanie katalogu przemieszczania dla [wdrażanie plików ZIP](deployment-zip-push.md).
