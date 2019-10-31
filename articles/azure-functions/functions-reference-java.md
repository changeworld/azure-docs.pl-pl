---
title: Dokumentacja dla deweloperów języka Java dla Azure Functions | Microsoft Docs
description: Dowiedz się, jak opracowywać funkcje przy użyciu języka Java.
author: ggailey777
manager: gwallace
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, dynamiczne obliczenia, architektura bezserwerowa, Java
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: 97c721c504c460856796e296fefc33bf01f002f8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176438"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dewelopera Azure Functions Java

Środowisko uruchomieniowe Azure Functions obsługuje środowisko [Java SE 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Ten przewodnik zawiera informacje na temat złożonego pisania Azure Functions przy użyciu języka Java.

Podobnie jak w przypadku innych języków, aplikacja funkcji może mieć jedną lub więcej funkcji. Funkcja języka Java jest metodą `public`, uzupełnioną `@FunctionName`adnotacją. Ta metoda definiuje wpis dla funkcji języka Java i musi być unikatowa w konkretnym pakiecie. Jeden aplikacja funkcji zapisany w języku Java może mieć wiele klas z adnotacjami z wieloma metodami publicznymi z `@FunctionName`.

W tym artykule przyjęto założenie, że już odczytano [informacje dotyczące deweloperów Azure Functions](functions-reference.md). Należy również wykonać funkcję Szybki Start, aby utworzyć pierwszą funkcję przy użyciu [Visual Studio Code](functions-create-first-function-vs-code.md) lub [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Model programowania 

Pojęcia związane z [wyzwalaczami i powiązaniami](functions-triggers-bindings.md) mają podstawowe znaczenie dla Azure Functions. Wyzwalacze rozpoczynają wykonywanie kodu. Powiązania umożliwiają przekazywanie danych do i zwracanie danych z funkcji bez konieczności pisania niestandardowego kodu dostępu do danych.

## <a name="create-java-functions"></a>Tworzenie funkcji języka Java

Aby ułatwić tworzenie funkcji języka Java, dostępne są narzędzia oparte na Maven i Archetypes, które używają wstępnie zdefiniowanych szablonów języka Java, które ułatwiają tworzenie projektów z określonym wyzwalaczem funkcji.    

### <a name="maven-based-tooling"></a>Narzędzia oparte na Maven

Następujące środowiska deweloperskie zawierają Azure Functions narzędzia, które umożliwiają tworzenie projektów funkcji języka Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Powyższe linki artykułu pokazują, jak utworzyć swoje pierwsze funkcje przy użyciu wybranego środowiska IDE. 

### <a name="project-scaffolding"></a>Tworzenie szkieletu projektu

Jeśli wolisz opracowywanie wiersza polecenia z terminalu, najprostszym sposobem tworzenia szkieletów projektów funkcji opartych na języku Java jest użycie `Apache Maven` Archetypes. Obecnie istnieją dwie funkcje Archetypes dla Maven:

+ **Java Archetype**: Opublikowano w następujących identyfikatorach GroupID i artifactId [com. Microsoft. Azure: Azure-Functions-Archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Aby rozpocząć korzystanie z tego Archetype, zobacz [Przewodnik Szybki Start dla języka Java](functions-create-first-java-maven.md). 

+ **Kotlin Archetype (wersja zapoznawcza)** opublikowano w następujących identyfikatorach GroupID i artifactId [com. Microsoft. Azure: Azure-Functions-Kotlin-Archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

Kod źródłowy tych Archetypes można znaleźć w [repozytorium usługi Azure Maven Archetypes](https://github.com/microsoft/azure-maven-archetypes)w witrynie GitHub.


## <a name="folder-structure"></a>Struktura folderów

Oto struktura folderów projektu Java Azure Functions:

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

_* Projekt Kotlin wygląda bardzo podobnie, ponieważ jest nadal Maven_

Aby skonfigurować aplikację funkcji, można użyć udostępnionego pliku [host. JSON](functions-host-json.md) . Każda funkcja ma własny plik kodu (. Java) i plik konfiguracji powiązania (Function. JSON).

W projekcie można umieścić więcej niż jedną funkcję. Unikaj umieszczania funkcji w osobnych jarsach. `FunctionApp` w katalogu docelowym to wdrożenie wdrożone w aplikacji funkcji na platformie Azure.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

 Funkcje są wywoływane przez wyzwalacz, taki jak żądanie HTTP, czasomierz lub aktualizacja danych. Funkcja musi przetworzyć ten wyzwalacz i wszelkie inne dane wejściowe, aby utworzyć co najmniej jedno wyjście.

Użyj adnotacji języka Java zawartych w pakiecie [com. Microsoft. Azure. Functions. Annotation. *](/java/api/com.microsoft.azure.functions.annotation) , aby powiązać dane wejściowe i wyjściowe z metodami. Aby uzyskać więcej informacji, zobacz [dokumenty referencyjne języka Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Musisz skonfigurować konto usługi Azure Storage w pliku [Local. Settings. JSON](/azure/azure-functions/functions-run-local#local-settings-file) , aby uruchamiać lokalnie usługi Azure Blob Storage, Azure queue storage lub Azure Table Storage.

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

Poniżej przedstawiono wygenerowane `function.json` przez [platformę Azure-Functions-Maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="jdk-runtime-availability-and-support"></a>Dostępność i obsługa środowiska uruchomieniowego JDK 

W przypadku lokalnego opracowywania aplikacji funkcji Java Pobierz i użyj programu [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 zestawy JDK z [systemów Azul](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions korzysta z Azul środowiska uruchomieniowego Java 8 JDK podczas wdrażania aplikacji funkcji w chmurze.

[Pomoc techniczna systemu Azure](https://azure.microsoft.com/support/) dotycząca problemów z aplikacjami zestawy JDK i funkcji jest dostępna z [zakwalifikowanym planem pomocy technicznej](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Dostosuj JVM

Funkcje umożliwiają dostosowanie maszyny wirtualnej Java (JVM) używanej do uruchamiania funkcji języka Java. [Następujące opcje JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) są używane domyślnie:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

W ustawieniach aplikacji można podać dodatkowe argumenty o nazwie `JAVA_OPTS`. Możesz dodać ustawienia aplikacji do aplikacji funkcji wdrożonej na platformie Azure w Azure Portal lub interfejsie wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Azure Portal

W [Azure Portal](https://portal.azure.com)Użyj [karty Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) , aby dodać ustawienie `JAVA_OPTS`.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ustawić `JAVA_OPTS`, można użyć polecenia [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings) , jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Ten przykład włącza tryb bezobsługowy. Zastąp `<APP_NAME>` nazwą swojej aplikacji funkcji i `<RESOURCE_GROUP>` z grupą zasobów.

> [!WARNING]  
> W [planie zużycia](functions-scale.md#consumption-plan)należy dodać ustawienie `WEBSITE_USE_PLACEHOLDER` z wartością `0`.  
To ustawienie powoduje zwiększenie czasu zimnego uruchamiania funkcji języka Java.

## <a name="third-party-libraries"></a>Biblioteki innych firm 

Azure Functions obsługuje korzystanie z bibliotek innych firm. Domyślnie wszystkie zależności określone w pliku `pom.xml` projektu są automatycznie dowiązane podczas [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) celu. W przypadku bibliotek, które nie są określone jako zależności w pliku `pom.xml`, umieść je w katalogu `lib` w katalogu głównym funkcji. Zależności umieszczane w katalogu `lib` są dodawane do modułu ładującego klasy systemu w czasie wykonywania.

Zależność `com.microsoft.azure.functions:azure-functions-java-library` jest domyślnie dostępna w ścieżce klasy i nie musi być uwzględniona w katalogu `lib`. Ponadto [platforma Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) dodaje zależności wymienione [tutaj](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) do ścieżki klas.

## <a name="data-type-support"></a>Obsługa typu danych

Można używać zwykłych obiektów języka Java (Pojo), typów zdefiniowanych w `azure-functions-java-library`lub typów danych pierwotnych, takich jak String i Integer, aby powiązać dane wejściowe lub wyjściowe.

### <a name="pojos"></a>Pojo

Do konwertowania danych wejściowych na POJO, [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) używa biblioteki [gson](https://github.com/google/gson) . Typy POJO używane jako dane wejściowe do funkcji powinny być `public`.

### <a name="binary-data"></a>Dane binarne

Powiąż binarne dane wejściowe lub wyjściowe do `byte[]`, ustawiając pole `dataType` w funkcji Function. JSON na `binary`:

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

Jeśli oczekujesz wartości null, użyj `Optional<T>`.

## <a name="bindings"></a>Powiązania

Powiązania danych wejściowych i wyjściowych zapewniają deklaratywny sposób nawiązywania połączenia z danymi z poziomu kodu. Funkcja może mieć wiele powiązań wejściowych i wyjściowych.

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

Wywołaj tę funkcję przy użyciu żądania HTTP. 
- Ładunek żądania HTTP jest przenoszona jako `String` dla argumentu `inputReq`.
- Jeden wpis jest pobierany z magazynu tabel i jest przenoszona jako `TestInputData` do argumentu `inputData`.

Aby otrzymać wsadowe dane wejściowe, można powiązać z `String[]`, `POJO[]`, `List<String>`lub `List<POJO>`.

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

Ta funkcja jest wyzwalana za każdym razem, gdy w skonfigurowanym centrum zdarzeń są nowe dane. Ponieważ `cardinality` jest ustawiona na `MANY`, funkcja otrzymuje partię komunikatów z centrum zdarzeń. `EventData` z centrum zdarzeń zostanie przekonwertowany na `TestEventData` na potrzeby wykonywania funkcji.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Można powiązać powiązanie danych wyjściowych z wartością zwracaną przy użyciu `$return`. 

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

Jeśli istnieje wiele powiązań wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

Aby wysłać wiele wartości wyjściowych, użyj `OutputBinding<T>` zdefiniowanych w pakiecie `azure-functions-java-library`. 

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

Wywołujesz tę funkcję na wywołaniu HttpRequest. Zapisuje wiele wartości do magazynu kolejek.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage i HttpResponseMessage

 Są one zdefiniowane w `azure-functions-java-library`. Są to typy pomocnika do pracy z funkcjami HttpTrigger.

| Typ wyspecjalizowany      |       Cel        | Typowy sposób użycia                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz HTTP     | Pobiera metodę, nagłówki lub zapytania |
| `HttpResponseMessage` | Powiązanie danych wyjściowych HTTP | Zwraca stan inny niż 200   |

## <a name="metadata"></a>Metadane

Niektóre wyzwalacze wysyłają [metadane wyzwalacza](/azure/azure-functions/functions-triggers-bindings) wraz z danymi wejściowymi. Można użyć `@BindingName` adnotacji do powiązania z metadanymi wyzwalacza.


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
W poprzednim przykładzie `queryValue` jest powiązany z parametrem ciągu zapytania `name` w adresie URL żądania HTTP, `http://{example.host}/api/metadata?name=test`. Oto inny przykład przedstawiający sposób powiązania z `Id` z metadanych wyzwalacza kolejki.

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
> Nazwa podana w adnotacji musi być zgodna z właściwością metadanych.

## <a name="execution-context"></a>Kontekst wykonywania

`ExecutionContext`, zdefiniowane w `azure-functions-java-library`, zawiera metody pomocnika do komunikowania się ze środowiskiem uruchomieniowym funkcji.

### <a name="logger"></a>Rejestratora

Użyj `getLogger`zdefiniowanych w `ExecutionContext`, aby pisać dzienniki z kodu funkcji.

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

Interfejsu wiersza polecenia platformy Azure można użyć do przesyłania strumieniowego rejestrowania w języku Java stdout i stderr, a także do rejestrowania innych aplikacji. 

Poniżej przedstawiono sposób konfigurowania aplikacji funkcji do zapisywania rejestrowania aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Aby przesłać strumieniowo dane wyjściowe rejestrowania dla aplikacji funkcji przy użyciu interfejsu wiersza polecenia platformy Azure, Otwórz nowy wiersz poleceń, bash lub sesję terminala, a następnie wprowadź następujące polecenie:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Polecenie [AZ webapp log tail](/cli/azure/webapp/log) zawiera opcje filtrowania danych wyjściowych przy użyciu opcji `--provider`. 

Aby pobrać pliki dzienników jako pojedynczy plik ZIP przy użyciu interfejsu wiersza polecenia platformy Azure, Otwórz nowy wiersz poleceń, bash lub sesję terminala, a następnie wprowadź następujące polecenie:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Przed uruchomieniem tego polecenia należy włączyć rejestrowanie systemu plików w Azure Portal lub interfejsie wiersza polecenia platformy Azure.

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień przy użyciu `System.getenv("AzureWebJobsStorage")`.

Poniższy przykład pobiera [ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)z kluczem o nazwie `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programowania w języku Java Azure Functions, zobacz następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* Lokalne programowanie i debugowanie za pomocą [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)i [zaćmienie](functions-create-maven-eclipse.md)
* [Zdalne debugowanie Azure Functions Java z Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Wtyczka Maven dla Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Usprawnij Tworzenie funkcji za pomocą celu `azure-functions:add` i przygotuj katalog przemieszczania dla [wdrożenia pliku zip](deployment-zip-push.md).
