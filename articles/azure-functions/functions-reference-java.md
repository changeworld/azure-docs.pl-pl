---
title: Dokumentacja dla deweloperów języka Java dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu tworzenia funkcje za pomocą języka Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure funkcji, funkcji, przetwarzania elementów webhook, dynamiczne obliczeń, architektura niekorzystającą, java zdarzeń
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 3f63cb5a16b74458f9b53fddaea13a61ec1196a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dewelopera usługi Azure Java funkcji

## <a name="programming-model"></a>Model programowania 

Funkcja Azure powinna być metody bezstanowych klasy, która przetwarza dane wejściowe i generuje dane wyjściowe. Mimo że można zapisać wystąpienia metody, funkcji nie może zależeć na wszystkie pola wystąpienia klasy. Wszystkie metody funkcja muszą mieć `public` modyfikator dostępu.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacji

Zazwyczaj Azure funkcja jest wywoływana z powodu wyzwalacza zewnętrznego. Funkcja musi przetworzyć tego wyzwalacza i jego skojarzone dane wejściowe i tworzyć dane wyjściowe z jednego lub więcej.

Adnotacje Java znajdują się w `azure-functions-java-core` pakietu do powiązania danych wejściowych i wyjściowych metody. Obsługiwane wyzwalacze wejściowy i wyjściowy powiązanie adnotacje znajdują się w poniższej tabeli:

Powiązanie | Adnotacja
---|---
CosmosDB | ND
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | ND
Notification Hubs | ND
Obiektu Blob magazynu | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Kolejka magazynu | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabela magazynu | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Czasomierz | <ul><li>`TimerTrigger`</li></ul>
Twilio | ND

Wyzwalacz wejściami i wyjściami może być także definiowane w [function.json](/azure/azure-functions/functions-reference#function-code) dla aplikacji.

> [!IMPORTANT] 
> Należy skonfigurować konto magazynu Azure w sieci [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) do uruchomienia lokalnie wyzwalacze obiektu Blob magazynu Azure, kolejki lub tabeli.

Przykład korzystanie z adnotacji:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Tej samej funkcji bez adnotacji:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

z odpowiednimi `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
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

## <a name="data-types"></a>Typy danych

Wolnych do użycia dla danych wejściowych i wyjściowych w tym typy natywne; wszystkich typów danych w języku Java dostosowane typach Java i specjalne Azure typów zdefiniowanych w `azure-functions-java-core` pakietu. Azure Functions, który próbuje środowiska uruchomieniowego przekonwertować danych wejściowych odebranych na typ wymagane przez kod.

### <a name="strings"></a>Ciągi

Wartości przekazanych do funkcji metod będzie można rzutować na ciągi, jeśli z odpowiadającym typem parametru wejściowego dla funkcji jest typu `String`. 

### <a name="plain-old-java-objects-pojos"></a>Zwykły stare obiekty Java (Pojo)

Ciągi sformatowane JSON będą być rzutowane na typy Java, jeśli dane wejściowe metody funkcja oczekuje typu Java. Ta konwersja służy do przekazania danych wejściowych JSON do funkcji i pracę z typami Java w kodzie, bez konieczności wykonania konwersji w swoim własnym kodem.

Używane jako dane wejściowe do funkcji musi takie same typy typu POJO `public` modyfikator dostępu metody funkcji są one używane w. Nie należy zadeklarować typu POJO pola klasy `public`. Na przykład ciąg JSON `{ "x": 3 }` może być konwertowane na typ następującego typu POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Dane binarne

Dane binarne jest reprezentowany jako `byte[]` w kodzie funkcji platformy Azure. Powiązać binarne wejściowych i wyjściowych funkcji ustawiając `dataType` w Twojej function.json do `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Następnie użyć jej w kodzie funkcji:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Użyj `OutputBinding<byte[]>` typu, aby utworzyć powiązanie binarne dane wyjściowe.


## <a name="function-method-overloading"></a>Przeciążanie funkcji — metoda

Możesz przeciążyć metody funkcja o tej samej nazwie, ale z różnymi typami. Na przykład może mieć zarówno `String echo(String s)` i `String echo(MyType s)` w jedną klasę i usługi Azure Functions środowiska uruchomieniowego decyduje o tym, które z nich do wywołania przez zbadanie rzeczywisty typ danych wejściowych (HTTP wejściowych, typu MIME `text/plain` prowadzi do `String` podczas `application/json` reprezentuje `MyType`).

## <a name="inputs"></a>Dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w usługi Azure Functions: jedna jest danych wejściowych wyzwalacza, a drugi to dodatkowe dane wejściowe. Mimo że różnią się one w `function.json`, jest identyczne w kodzie języka Java. Na przykład Przyjrzyjmy się poniższy fragment kodu:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

`@BindingName` Akceptuje adnotacji `String` właściwość, która reprezentuje nazwę powiązania/wyzwalacza zdefiniowane w `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Dlatego po wywołaniu tej funkcji ładunku żądania HTTP przekazuje opcjonalny `String` argumentu `in` i Azure Table Storage `MyObject` typ został przekazany do argumentu `obj`. Użyj `Optional<T>` typu do obsługi danych wejściowych do funkcji, które mogą mieć wartości null.

## <a name="outputs"></a>Dane wyjściowe

Zarówno w zwracanej wartości lub parametry wyjściowe można wyrazić danych wyjściowych. Jeśli istnieje tylko jedno wyjście, zalecane jest Użyj wartości zwracanej. Dla wielu wyjść należy użyć parametrów wyjściowych.

Wartość zwracana jest najprostsza forma danych wyjściowych, tylko zwrócić wartość dowolnego typu i środowiska uruchomieniowego usługi Azure Functions spróbuje ona kierować do typu rzeczywistego (np. dotyczące odpowiedzi HTTP). W `functions.json`, możesz użyć `$return` jako nazwę powiązania w danych wyjściowych.

Aby utworzyć wiele wartości danych wyjściowych, należy użyć `OutputBinding<T>` typ zdefiniowany w elemencie `azure-functions-java-core` pakietu. Odpowiedź HTTP i push wiadomości do kolejki, jak również należy napisać wyglądać mniej więcej tak:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

które należy zdefiniować powiązania danych wyjściowych w `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Typy specjalne

Czasami funkcję musi dokładną kontrolę nad wejścia i wyjścia. Specjalizowany typów w `azure-functions-java-core` pakietu znajdują się manipulować informacjami żądania i dostosować to wyzwalacza HTTP:

| Specjalistyczną odmianą      |       Środowisko docelowe        | Typowy sposób                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz protokołu HTTP     | Pobierz — metoda, nagłówkach lub zapytania |
| `HttpResponseMessage<T>` | Powiązanie wyniku HTTP | Zwrotny stan innych niż 200   |

> [!NOTE] 
> Można również użyć `@BindingName` adnotacji można pobrać nagłówków HTTP i zapytania. Na przykład `@BindingName("name") String query` iteruje nagłówków żądań HTTP i zapytań i przekazać tę wartość do metody. Na przykład `query` będzie `"test"` Jeśli adres URL żądania jest `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadane

Metadane pochodzą z różnych źródeł, takich jak nagłówki HTTP, kwerendy HTTP i [wyzwolenia metadanych](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Użyj `@BindingName` adnotacji z nazwą metadanych można uzyskać wartość.

Na przykład `queryValue` w poniższym kodzie będzie fragment `"test"` Jeśli żądany adres URL jest `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Kontekst wykonywania funkcji

Interakcji z usługi Azure Functions środowiska wykonawczego za pośrednictwem `ExecutionContext` obiekt zdefiniowany w `azure-functions-java-core` pakietu. Użyj `ExecutionContext` obiekt do wywołania informacji i funkcji środowiska uruchomieniowego informacje przedstawione w kodzie.

### <a name="logging"></a>Rejestrowanie

Dostęp do funkcji rejestratora środowiska wykonawczego jest dostępna za pośrednictwem `ExecutionContext` obiektu. Tego rejestratora jest powiązany z monitorem Azure i umożliwia flagi ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykładowy kod zarejestruje komunikat ostrzegawczy, gdy Odebrano treść żądania jest pusta.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Często jest to pożądane do wyodrębnienia tajnych informacji z kodu źródłowego ze względów bezpieczeństwa. Dzięki temu kod publikowane do repozytoriów kodu źródłowego bez przypadkowo podawania poświadczeń innych deweloperów. Można to osiągnąć, po prostu, używając zmiennych środowiskowych podczas uruchamiania usługi Azure Functions lokalnego i w przypadku wdrażania funkcji Azure.

Można łatwo ustawić zmienne środowiskowe podczas uruchamiania usługi Azure Functions lokalnie, można dodać do pliku local.settings.json tych zmiennych. Jeśli jedno nie jest obecny w katalogu głównym projektu funkcji, możesz go utworzyć. Oto, jak powinna wyglądać pliku:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Każdy klucz-wartość mapowanie w `values` mapy zostanie udostępniona w czasie wykonywania jako zmienną środowiskową, dostępny przez wywołanie metody `System.getenv("<keyname>")`, na przykład `System.getenv("AzureWebJobsStorage")`. Dodawanie dodatkowych klucz / wartość pary i akceptowany jest zalecana praktyka.

> [!NOTE]
> Jeśli takie podejście jest zajęty, można się, że wziąć pod uwagę, czy dodawanie local.settings.json pliku do repozytorium, zignorować pliku, tak, aby nie została przekazana.

Swoim własnym kodem teraz w zależności od tych zmiennych środowiskowych możesz zalogować się w portalu Azure, aby ustawić taki sam klucz / wartość pary w ustawieniach aplikacji funkcji, aby kod ekwiwalentnie podczas testowania lokalnie, jak i po wdrożeniu na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure funkcje wyzwalaczy i powiązań](functions-triggers-bindings.md)
* [Funkcje zdalnego debugowania języka Java Azure z kodem Visual Studio](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
