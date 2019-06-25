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
ms.openlocfilehash: acd873cd19cafb785f968fd3d8671640bcfafed8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163701"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions Java

Środowisko uruchomieniowe usługi Azure Functions obsługuje [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Ten przewodnik zawiera informacje dotyczące niewymagającego pisania usługi Azure Functions przy użyciu języka Java.

Funkcja języka Java jest `public` metodzie, z adnotacją `@FunctionName`. Ta metoda definiuje wpis dla funkcji języka Java i muszą być unikatowe w określonym pakiecie. 

W tym artykule założono, że już znasz [dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md). Należy również wykonać szybkiego startu usługi Functions, aby utworzyć pierwszą funkcję, za pomocą [programu Visual Studio Code](functions-create-first-function-vs-code.md) lub [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Model programowania 

Pojęcia związane z [wyzwalaczy i powiązań](functions-triggers-bindings.md) mają zasadnicze znaczenie w usłudze Azure Functions. Wyzwalacze Rozpocznij wykonywanie Twojego kodu. Powiązania umożliwiają sposób przekazywania danych do i zwrócić dane z funkcji, bez konieczności pisania kodu dostępu do danych niestandardowych.

## <a name="folder-structure"></a>Struktura folderów

Poniżej przedstawiono strukturę folderu projektu Java usługi Azure Functions:

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

Możesz użyć udostępnionego [host.json](functions-host-json.md) pliku, aby skonfigurować aplikację funkcji. Każda funkcja ma swój własny plik kodu (początku) i plik konfiguracji powiązania (function.json).

Można umieścić więcej niż jedną funkcję w projekcie. Należy unikać umieszczenie funkcji w oddzielnych plikach JAR. `FunctionApp` w elemencie docelowym katalog to, co zostanie wdrożona do aplikacji funkcji na platformie Azure.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

 Funkcje są wywoływane przez wyzwalacz, takie jak żądania HTTP, czasomierz lub aktualizacji danych. Funkcja musi przetworzyć tego wyzwalacza i wszystkie inne dane wejściowe, aby wygenerować dane wyjściowe z co najmniej jeden.

Korzystanie z adnotacji Java objęte [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pakietu, aby powiązać dane wejściowe i wyjściowe metody. Aby uzyskać więcej informacji, zobacz [dokumenty referencyjne języka Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Należy skonfigurować konto usługi Azure Storage w swojej [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) do uruchomienia usługi Azure Blob storage, Azure Queue storage lub Azure Table storage wyzwalaczy lokalnie.

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

Potrzeby lokalnego programowania aplikacji funkcji w języku Java, pobieranie i używanie [Azul Zulu Enterprise na platformie Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDK 8 z [firmy Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Usługa Azure Functions używa środowiska uruchomieniowego Azul Java 8 JDK, podczas wdrażania aplikacji funkcji w chmurze.

[Pomoc techniczna platformy Azure](https://azure.microsoft.com/support/) w przypadku problemów z JDK i funkcja jest dostępna z aplikacji [plan pomocy technicznej kwalifikowaną](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Dostosowywanie maszyny JVM

Functions umożliwia dostosowywanie maszyny wirtualnej Java (JVM), umożliwia uruchamianie funkcji języka Java. [Następujące opcje maszyny JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) są używane przez domyślny:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Możesz podać dodatkowe argumenty w aplikacji, ustawienie o nazwie `JAVA_OPTS`. Ustawienia aplikacji można dodać do aplikacji funkcji wdrożona na platformie Azure w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Azure Portal

W [witryny Azure portal](https://portal.azure.com), użyj [kartę ustawień aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) dodać `JAVA_OPTS` ustawienie.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz użyć [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) polecenie, aby ustawić `JAVA_OPTS`, jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Ten przykład włącza tryb nienadzorowanej. Zastąp `<APP_NAME>` o nazwie aplikację funkcji i `<RESOURCE_GROUP>` z grupą zasobów.

> [!WARNING]  
> W [planu zużycie](functions-scale.md#consumption-plan), należy dodać `WEBSITE_USE_PLACEHOLDER` ustawienie z wartością `0`.  
To ustawienie powoduje zwiększenie razy zimnego dla funkcji języka Java.

## <a name="third-party-libraries"></a>Bibliotek innych firm 

Usługa Azure Functions obsługuje korzystanie z bibliotek innych firm. Domyślnie wszystkie zależności są określone w projekcie `pom.xml` pliku są automatycznie powiązane podczas [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cel. W przypadku bibliotek, które nie jest określony jako zależności w `pom.xml` pliku, umieść je w `lib` katalogu w katalogu głównym funkcji. Zależności są umieszczane w `lib` katalogu zostaną dodane do modułu ładującego klasę systemu w czasie wykonywania.

`com.microsoft.azure.functions:azure-functions-java-library` Zależnościach znajduje się na ścieżce klasy domyślnie i nie muszą być dołączone w `lib` katalogu. Ponadto [azure-functions-java-proces roboczy](https://github.com/Azure/azure-functions-java-worker) dodaje składników zależnych wymienionych [tutaj](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) do ścieżki.

## <a name="data-type-support"></a>Obsługa typu danych

Można użyć zwykłych starych obiektów Java (Pojo), typy zdefiniowane w `azure-functions-java-library`, lub typów danych pierwotnych, takich jak String i Integer, aby powiązać dane wejściowe lub wyjściowe powiązania.

### <a name="pojos"></a>POJOs

Do konwersji danych wejściowych do obiektu typu POJO, [azure-functions-java-proces roboczy](https://github.com/Azure/azure-functions-java-worker) używa [gson](https://github.com/google/gson) biblioteki. Obiektu typu POJO typy używane jako dane wejściowe do funkcji powinny być `public`.

### <a name="binary-data"></a>Dane binarne

Powiąż binarnych danych wejściowych lub wyjściowych, aby `byte[]`, ustawiając `dataType` w swojej function.json do `binary`:

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

Jeśli oczekujesz, że wartości null, użyj `Optional<T>`.

## <a name="bindings"></a>Powiązania

Powiązania danych wejściowych i wyjściowych zapewniają deklaratywną metodę, aby nawiązać połączenie danych z poziomu kodu. Funkcja mogą mieć wiele danych wejściowych i wyjściowych powiązania.

### <a name="input-binding-example"></a>Przykład powiązania danych wejściowych

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

Możesz wywołać tę funkcję za pomocą żądania HTTP. 
- Ładunek żądania HTTP jest przekazywany jako `String` dla argumentu `inputReq`.
- Jeden wpis jest pobierany z usługi Table storage i jest przekazywany jako `TestInputData` do argumentu `inputData`.

Aby móc odbierać partii danych wejściowych, można powiązać `String[]`, `POJO[]`, `List<String>`, lub `List<POJO>`.

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

Ta funkcja pobiera wyzwalane, gdy ma nowych danych w Centrum zdarzeń skonfigurowany. Ponieważ `cardinality` ustawiono `MANY`, funkcja otrzymuje partię komunikatów z Centrum zdarzeń. `EventData` z event hub są konwertowane na `TestEventData` do wykonywania funkcji.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Powiązanie danych wyjściowych można powiązać wartości zwracanej przy użyciu `$return`. 

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

Możesz wywołać tę funkcję na HttpRequest. Zapisuje ona wiele wartości, do usługi Queue storage.

## <a name="httprequestmessage-and-httpresponsemessage"></a>Obiekt HttpRequestMessage i obiektu HttpResponseMessage

 Są one zdefiniowane w `azure-functions-java-library`. Są one typy pomocnika do pracy z funkcjami HttpTrigger.

| Specjalistyczną odmianą      |       Cel        | Typowy                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz HTTP     | Pobiera metody, nagłówków lub zapytania |
| `HttpResponseMessage` | Powiązanie danych wyjściowych HTTP | Zwraca stan innych niż 200   |

## <a name="metadata"></a>Metadane

Wyślij kilka wyzwalaczy [wyzwolić metadanych](/azure/azure-functions/functions-triggers-bindings) wraz z danych wejściowych. Można użyć adnotacji `@BindingName` do powiązania do wyzwolenia metadanych.


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
W powyższym przykładzie `queryValue` jest powiązany z parametru ciągu zapytania `name` w adresie URL żądania http `http://{example.host}/api/metadata?name=test`. Oto inny przykład, w którym pokazano, jak powiązać `Id` z metadanych wyzwalacz kolejki.

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
> Nazwa podana w adnotacji musi być zgodna właściwości metadanych.

## <a name="execution-context"></a>Kontekst wykonywania

`ExecutionContext`, zdefiniowane w `azure-functions-java-library`, zawiera metody pomocnicze do komunikowania się z środowisko uruchomieniowe usługi functions.

### <a name="logger"></a>Logger

Użyj `getLogger`zdefiniowaną w `ExecutionContext`, zapisywanie dzienników z kodu funkcji.

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

Można użyć wiersza polecenia platformy Azure do strumienia Java stdout i stderr rejestrowania, a także rejestrowanie innych aplikacji. 

Oto jak skonfigurować aplikację funkcji, aby zapisać rejestrowanie aplikacji przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Do strumienia wyjściowego rejestrowanie dla aplikacji funkcji przy użyciu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, Bash lub sesji terminalowej i wprowadź następujące polecenie:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log) polecenie ma funkcje umożliwiające filtrowanie danych wyjściowych za pomocą `--provider` opcji. 

Aby pobrać pliki dziennika jako pojedynczy plik ZIP, przy użyciu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, Bash lub sesji terminalowej i wprowadź następujące polecenie:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Musisz włączyć systemu plików, logowania w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure, przed uruchomieniem tego polecenia.

## <a name="environment-variables"></a>Zmienne środowiskowe

W przypadku funkcji [ustawienia aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), takie jak połączenia z usługą ciągów, są widoczne jako zmiennych środowiskowych podczas wykonywania. Dostęp do tych ustawień, używając `System.getenv("AzureWebJobsStorage")`.

Na przykład można dodać [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), o nazwie `testAppSetting` i wartość `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących programowania Java usługi Azure Functions zobacz następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* Lokalne programowanie i debugowanie za pomocą [programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), i [środowiska Eclipse](functions-create-maven-eclipse.md)
* [Zdalne debugowanie środowiska Java w usłudze Azure Functions przy użyciu programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Wtyczka maven plugin for Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Usprawnij Tworzenie funkcji przy użyciu `azure-functions:add` celu i przygotowywanie katalogu przemieszczania dla [wdrażanie plików ZIP](deployment-zip-push.md).
