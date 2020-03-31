---
title: Odwołanie do dewelopera języka Java dla usług Azure Functions
description: Dowiedz się, jak tworzyć funkcje za pomocą języka Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276753"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dla deweloperów języka Java w usłudze Azure Functions

Środowisko uruchomieniowe usługi Azure Functions obsługuje [technologię Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64).](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/) Ten przewodnik zawiera informacje o zawiłościach pisania usługi Azure Functions w języku Java.

Jak to się dzieje w innych językach, aplikacja funkcji może mieć jedną lub więcej funkcji. Funkcja Java jest `public` metodą, ozdobioną adnotacją `@FunctionName`. Ta metoda definiuje wpis dla funkcji Java i musi być unikatowa w określonym pakiecie. Jedna aplikacja funkcji napisana w języku Java może mieć `@FunctionName`wiele klas z wieloma metodami publicznymi z adnotacją .

W tym artykule założono, że zostały już odczytane [odwołanie dewelopera usługi Azure Functions.](functions-reference.md) Należy również ukończyć szybki start funkcji, aby utworzyć pierwszą funkcję, przy użyciu [programu Visual Studio Code](functions-create-first-function-vs-code.md) lub [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Model programowania 

Pojęcia [wyzwalaczy i powiązań](functions-triggers-bindings.md) mają podstawowe znaczenie dla usługi Azure Functions. Wyzwalacze rozpocząć wykonywanie kodu. Powiązania umożliwiają przekazywanie danych do funkcji i zwracanie danych z funkcji bez konieczności zapisywania niestandardowego kodu dostępu do danych.

## <a name="create-java-functions"></a>Tworzenie funkcji Java

Aby ułatwić tworzenie funkcji języka Java, istnieją narzędzia oparte na maven i archetypy, które używają wstępnie zdefiniowanych szablonów Java, aby ułatwić tworzenie projektów z wyzwalaczem określonej funkcji.    

### <a name="maven-based-tooling"></a>Oprzyrządowanie oparte na maven

Następujące środowiska deweloperskie mają narzędzia usługi Azure Functions, które umożliwiają tworzenie projektów funkcji Java: 

+ [Kod programu Visual Studio](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Powyższe łącza do artykułu pokazują, jak utworzyć pierwsze funkcje przy użyciu wybranego ide. 

### <a name="project-scaffolding"></a>Rusztowania projektu

Jeśli wolisz tworzenie wiersza polecenia z terminala, najprostszym sposobem tworzenia projektów `Apache Maven` funkcji opartych na javie jest użycie archetypów. Obecnie istnieją dwa archetypy funkcji dla Maven:

+ **Archetype Java:** opublikowane w ramach następujących groupId i artifactId [com.microsoft.azure:azure-functions-archetype:](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/)

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Aby rozpocząć korzystanie z tego archetypu, zobacz [szybki start języka Java](functions-create-first-java-maven.md). 

+ **Archetyp Kotlin (wersja zapoznawcza)** opublikowany pod następującym groupId i artifactId [com.microsoft.azure:azure-functions-kotlin-archetype:](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/)

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

Kod źródłowy tych archetypów można znaleźć w [repozytorium Azure Maven Archetypes GitHub.](https://github.com/microsoft/azure-maven-archetypes)


## <a name="folder-structure"></a>Struktura folderów

Oto struktura folderów projektu Java usługi Azure Functions:

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

_* Projekt Kotlin wygląda bardzo podobnie, ponieważ nadal jest Maven_

Można użyć udostępnionego pliku [host.json,](functions-host-json.md) aby skonfigurować aplikację funkcji. Każda funkcja ma swój własny plik kodu (.java) i powiązanie pliku konfiguracyjnego (function.json).

Można umieścić więcej niż jedną funkcję w projekcie. Unikaj umieszczania funkcji w oddzielnych słoikach. W `FunctionApp` katalogu docelowym jest to, co zostanie wdrożony do aplikacji funkcji na platformie Azure.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

 Funkcje są wywoływane przez wyzwalacz, takie jak żądanie HTTP, czasomierz lub aktualizacja danych. Funkcja musi przetwarzać ten wyzwalacz i inne dane wejściowe, aby uzyskać jeden lub więcej wyjść.

Użyj adnotacji java zawartych w pakiecie [com.microsoft.azure.functions.adnotation.*](/java/api/com.microsoft.azure.functions.annotation) do powiązania danych wejściowych i wyjściowych z metodami. Aby uzyskać więcej informacji, zobacz [dokumenty dotyczące odwołania do oprogramowania Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Konto usługi Azure Storage należy skonfigurować w [local.settings.json,](/azure/azure-functions/functions-run-local#local-settings-file) aby uruchamiać lokalne magazyny obiektów Blob, usługę Azure Queue storage lub magazyn tabel platformy Azure.

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

Oto generowane odpowiadające `function.json` [azure-functions-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

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

Aby rozwijać lokalne aplikacje funkcji Java, pobierz i użyj [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK z [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Usługa Azure Functions używa środowiska wykonawczego Azul Java 8 JDK podczas wdrażania aplikacji funkcji w chmurze.

[Obsługa platformy Azure](https://azure.microsoft.com/support/) w przypadku problemów z zestawami JDK i aplikacjami funkcji jest dostępna z [kwalifikowanym planem pomocy technicznej.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Dostosowywanie jvm

Funkcje umożliwiają dostosowanie maszyny wirtualnej Java (JVM) używanej do uruchamiania funkcji Java. Domyślnie używane są [następujące opcje JVM:](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Dodatkowe argumenty można podać w `JAVA_OPTS`ustawieniach aplikacji o nazwie . Ustawienia aplikacji można dodać do aplikacji funkcji wdrożonej na platformie Azure w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Portal Azure

W [witrynie Azure portal](https://portal.azure.com)użyj karty `JAVA_OPTS` Ustawienia [aplikacji,](functions-how-to-use-azure-function-app-settings.md#settings) aby dodać to ustawienie.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ustawić `JAVA_OPTS`polecenie [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) , tak jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
W tym przykładzie włącza tryb bezgłowy. Zamień `<APP_NAME>` na nazwę aplikacji `<RESOURCE_GROUP>` funkcyjnej i na grupę zasobów.

> [!WARNING]  
> W [planie zużycia](functions-scale.md#consumption-plan)należy dodać `WEBSITE_USE_PLACEHOLDER` ustawienie o `0`wartości .  
To ustawienie zwiększa czas rozruchu zimna dla funkcji Java.

## <a name="third-party-libraries"></a>Biblioteki innych firm 

Usługa Azure Functions obsługuje korzystanie z bibliotek innych firm. Domyślnie wszystkie zależności określone w `pom.xml` pliku projektu są automatycznie [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) łączone podczas celu. W przypadku bibliotek, które nie `pom.xml` zostały określone jako `lib` zależności w pliku, umieść je w katalogu w katalogu głównym funkcji. Zależności umieszczone w `lib` katalogu są dodawane do modułu ładującego klasy systemu w czasie wykonywania.

Zależność `com.microsoft.azure.functions:azure-functions-java-library` jest domyślnie dostępna na ścieżce klasy i nie musi `lib` być uwzględniona w katalogu. Ponadto [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) dodaje zależności wymienione [w tym miejscu](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) do ścieżki klasy.

## <a name="data-type-support"></a>Obsługa typów danych

Można użyć zwykłych starych obiektów Java (POJO), typów zdefiniowanych w `azure-functions-java-library`typach danych lub pierwotnych, takich jak String i Integer, aby powiązać z powiązaniami wejściowymi lub wyjściowymi.

### <a name="pojos"></a>PoJO

Do konwersji danych wejściowych do POJO, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) używa biblioteki [gson.](https://github.com/google/gson) Typy POJO używane jako wejścia `public`do funkcji powinny być .

### <a name="binary-data"></a>Dane binarne

Powiąż dane wejściowe lub wyjściowe binarne z `byte[]`, ustawiając `dataType` pole w pliku function.json na `binary`:

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

Jeśli oczekujesz wartości `Optional<T>`null, użyj .

## <a name="bindings"></a>Powiązania

Powiązania danych wejściowych i wyjściowych zapewniają deklaratywny sposób łączenia się z danymi z poziomu kodu. Funkcja może mieć wiele powiązań wejściowych i wyjściowych.

### <a name="input-binding-example"></a>Przykład wiązania danych wejściowych

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

Wywołanie tej funkcji za pomocą żądania HTTP. 
- Ładunek żądania HTTP `String` jest przekazywany jako argument `inputReq`.
- Jeden wpis jest pobierany z magazynu `TestInputData` tabel i `inputData`jest przekazywany do argumentu .

Aby otrzymać partię danych wejściowych, `String[]` `POJO[]`można `List<String>`powiązać z , , , lub `List<POJO>`.

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

Ta funkcja jest wyzwalana za każdym razem, gdy w skonfigurowanym centrum zdarzeń pojawią się nowe dane. Ponieważ `cardinality` jest ustawiona na `MANY`, funkcja odbiera partię komunikatów z centrum zdarzeń. `EventData`z centrum zdarzeń zostanie `TestEventData` przekonwertowany do wykonania funkcji.

### <a name="output-binding-example"></a>Przykład wiązania danych wyjściowych

Powiązanie danych wyjściowych można powiązać `$return`z wartością zwracaną przy użyciu programu . 

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

Jeśli istnieje wiele powiązań danych wyjściowych, należy użyć zwracanej wartości tylko dla jednego z nich.

Aby wysłać wiele `OutputBinding<T>` wartości wyjściowych, należy użyć zdefiniowanego w `azure-functions-java-library` pakiecie. 

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

Wywołać tę funkcję na HttpRequest. Zapisuje wiele wartości do magazynu kolejki.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage i HttpResponseMessage

 Są one `azure-functions-java-library`zdefiniowane w pliku . Są to typy pomocnika do pracy z funkcjami HttpTrigger.

| Typ specjalistyczny      |       Środowisko docelowe        | Typowe użycie                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz HTTP     | Pobiera metodę, nagłówki lub kwerendy |
| `HttpResponseMessage` | Powiązanie danych wyjściowych HTTP | Zwraca stan inny niż 200   |

## <a name="metadata"></a>Metadane

Kilka wyzwalaczy wysyła [metadane wyzwalacza](/azure/azure-functions/functions-triggers-bindings) wraz z danymi wejściowymi. Adnotacje `@BindingName` można użyć do powiązania do wyzwalania metadanych.


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
W poprzednim przykładzie `queryValue` jest powiązany z `name` parametrem ciągu zapytania `http://{example.host}/api/metadata?name=test`w adresie URL żądania HTTP, . Oto kolejny przykład, pokazujący, `Id` jak powiązać z metadanych wyzwalacza kolejki.

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

`ExecutionContext`, zdefiniowane `azure-functions-java-library`w , zawiera metody pomocnicze do komunikowania się ze środowiska wykonawczego funkcji.

### <a name="logger"></a>Rejestratora

Użyj `getLogger`, `ExecutionContext`zdefiniowane w , aby zapisać dzienniki z kodu funkcji.

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

Za pomocą interfejsu wiersza polecenia platformy Azure można przesyłać strumieniowo rejestrowanie stdout i stderr java, a także rejestrowanie innych aplikacji. 

Poniżej opisano, jak skonfigurować aplikację funkcji do zapisywania rejestrowania aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Aby przesyłać strumieniowo dane wyjściowe rejestrowania dla aplikacji funkcji przy użyciu interfejsu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, Bash lub sesję terminala i wprowadź następujące polecenie:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Polecenie [az webapp log tail](/cli/azure/webapp/log) ma opcje `--provider` filtrowania danych wyjściowych za pomocą tej opcji. 

Aby pobrać pliki dziennika jako pojedynczy plik ZIP przy użyciu interfejsu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, bash lub sesję terminala i wprowadź następujące polecenie:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Przed uruchomieniem tego polecenia musi być włączone rejestrowanie systemu plików w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="environment-variables"></a>Zmienne środowiskowe

W [funkcji, ustawienia aplikacji](functions-app-settings.md), takich jak parametry połączenia usługi, są widoczne jako zmienne środowiskowe podczas wykonywania. Dostęp do tych ustawień `System.getenv("AzureWebJobsStorage")`można uzyskać za pomocą programu .

Poniższy przykład pobiera [ustawienie aplikacji,](functions-how-to-use-azure-function-app-settings.md#settings) `myAppSetting`z kluczem o nazwie:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia środowiska Java w usłudze Azure Functions, zobacz następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)
* Lokalne opracowywanie i debugowanie za pomocą [programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)i [Eclipse](functions-create-maven-eclipse.md)
* [Zdalne debugowanie funkcji platformy Java Azure z kodem programu Visual Studio](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Wtyczka Maven dla funkcji platformy Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Usprawnij tworzenie funkcji `azure-functions:add` poprzez cel i przygotuj katalog przemieszczania do [wdrożenia plików ZIP](deployment-zip-push.md).
