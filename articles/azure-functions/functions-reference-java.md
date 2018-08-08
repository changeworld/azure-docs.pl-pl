---
title: Dokumentacja dla deweloperów języka Java dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Zrozumienie, jak tworzyć funkcje za pomocą języka Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bezserwerowa, języka java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 65964372cf2a0aa42be967f7c93749c58a9f56dd
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621773"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Model programowania 

Funkcji platformy Azure powinna być metoda bezstanowe klasy, która przetwarza dane wejściowe i generuje dane wyjściowe. Chociaż możesz napisać metody wystąpienia, funkcja nie może zależeć od wszystkie pola wystąpienia klasy. Wszystkie metody funkcji musi mieć `public` modyfikator dostępu.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

Zazwyczaj funkcję platformy Azure jest wywoływana z powodu wyzwalacza zewnętrznego. Funkcja musi przetwarzać tego wyzwalacza i jej skojarzone dane wejściowe i generować dane wyjściowe z jednego lub więcej.

Adnotacje Java są objęte `azure-functions-java-core` pakietu, aby powiązać dane wejściowe i wyjściowe metody. W poniższej tabeli znajdują się obsługiwane danych wejściowych wyzwalacze i powiązania adnotacje danych wyjściowych:

Powiązanie | Adnotacja
---|---
CosmosDB | ND
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | ND
Notification Hubs | ND
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Kolejka magazynu | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabela magazynu | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Czasomierz | <ul><li>`TimerTrigger`</li></ul>
Twilio | ND

Dane wejściowe wyzwalacza i dane wyjściowe można także definiować w [function.json](/azure/azure-functions/functions-reference#function-code) dla aplikacji.

> [!IMPORTANT] 
> Należy skonfigurować konto usługi Azure Storage w swojej [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) lokalnie uruchomić wyzwalacze usługi Azure Storage Blob, kolejki lub tabeli.

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

Tę samą funkcję zapisywane bez adnotacji:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

za pomocą odpowiednich `function.json`:

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

Jesteś bezpłatne korzystanie z wszystkich typów danych w języku Java na dane wejściowe i wyjściowe, w tym natywnych typów; dostosowane typy języka Java i wyspecjalizowane Azure typów zdefiniowanych w `azure-functions-java-core` pakietu. Usługi Azure Functions, który próbuje środowiska uruchomieniowego skonwertować danych wejściowych, odebranych na typ żądanego przez kod.

### <a name="strings"></a>Ciągi

Wartości przekazane do funkcji metod będzie rzutowane na ciągi, jeśli odpowiedni typ parametru wejściowego dla tej funkcji jest typu `String`. 

### <a name="plain-old-java-objects-pojos"></a>Zwykłych starych obiektów Java (Pojo)

Ciągi sformatowane przy użyciu formatu JSON będzie być rzutowane na typy języka Java, jeśli dane wejściowe funkcji metody oczekuje typu języka Java. Ta konwersja umożliwia przekazywanie danych wejściowych JSON do funkcji i Praca z typami środowiska Java w kodzie bez konieczności implementowania konwersji we własnym kodzie.

Obiektu typu POJO typy używane jako dane wejściowe do funkcji musi takie same `public` modyfikator dostępu metody funkcji, są one używane w. Nie trzeba zadeklarować obiektu typu POJO pola klasy `public`. Na przykład ciąg JSON `{ "x": 3 }` jest w stanie ma zostać przekonwertowane na następujący typ obiektu typu POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Dane binarne

Dane binarne, jest przedstawiana jako `byte[]` w kodzie funkcji platformy Azure. Powiązywanie binarnych danych wejściowych lub wyjściowych funkcji, ustawiając `dataType` w swojej function.json do `binary`:

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

Następnie użyj go w kodzie funkcji:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Użyj `OutputBinding<byte[]>` typu, aby powiązanie binarne dane wyjściowe.


## <a name="function-method-overloading"></a>Przeciążenie metody — funkcja

Możesz przeciążyć metody funkcji o tej samej nazwie, ale z różnymi typami. Na przykład, może mieć jednocześnie `String echo(String s)` i `String echo(MyType s)` w jednej klasy i Azure Functions runtime decyduje, co do wywołania przez zbadanie rzeczywisty typ danych wejściowych (dla danych wejściowych, typ MIME HTTP `text/plain` prowadzi do `String` podczas `application/json` reprezentuje `MyType`).

## <a name="inputs"></a>Dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: jeden z nich to dane wejściowe wyzwalacza, a drugi to dodatkowe dane wejściowe. Mimo że są inne w przypadku `function.json`, użycie jest identyczna w kodzie języka Java. Jako przykład Weźmy poniższy fragment kodu:

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

Dlatego po wywołaniu tej funkcji ładunek żądania HTTP przekazuje opcjonalny `String` dla argumentu `in` i Azure Table Storage `MyObject` typ przekazany do argumentu `obj`. Użyj `Optional<T>` typu do obsługi danych wejściowych do funkcji, które może mieć wartości null.

## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe mogą być wyrażone zarówno wartości zwracane lub parametry wyjściowe. Jeśli istnieje tylko jedno wyjście, są zalecane do użycia wartości zwracanej. Wiele wyjść należy użyć parametrów wyjściowych.

Wartość zwracana jest najprostsza forma dane wyjściowe, po prostu zwraca wartość dowolnego typu i środowisko uruchomieniowe usługi Azure Functions będzie próbował kierować je do rzeczywistego typu (na przykład odpowiedź HTTP). W `functions.json`, możesz użyć `$return` jako nazwa powiązania danych wyjściowych.

Aby utworzyć wiele wartości danych wyjściowych, należy użyć `OutputBinding<T>` typ zdefiniowany w elemencie `azure-functions-java-core` pakietu. Jeśli musisz wprowadzić odpowiedź HTTP i wypychania komunikat do kolejki, a także można napisać mniej więcej tak:

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

które powinien definiować powiązania danych wyjściowych w `function.json`:

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

Czasami funkcję muszą szczegółowe kontrolę nad dane wejściowe i wyjściowe. Wyspecjalizowane typów w `azure-functions-java-core` pakietu są dostarczane do manipulowania informacje dotyczące żądania i dostosować zwracany stan wyzwalacza HTTP:

| Specjalistyczną odmianą      |       Środowisko docelowe        | Typowy                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz HTTP     | Metoda, nagłówków lub zapytania |
| `HttpResponseMessage<T>` | Powiązanie danych wyjściowych HTTP | Status powrotu niż 200   |

> [!NOTE] 
> Można również użyć `@BindingName` adnotacji można pobrać nagłówków HTTP i zapytań. Na przykład `@BindingName("name") String query` dokonuje iteracji nagłówków żądań HTTP i zapytania i przekaż tę wartość do metody. Na przykład `query` będzie `"test"` Jeśli adres URL żądania jest `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadane

Metadane pochodzą z różnych źródeł, takich jak nagłówki HTTP, kwerendy HTTP i [wyzwolić metadanych](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Użyj `@BindingName` adnotacji wraz z nazwą metadanych w celu uzyskania wartości.

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

Możesz korzystać ze środowiska wykonawczego usługi Azure Functions za pomocą `ExecutionContext` obiektu zdefiniowany w `azure-functions-java-core` pakietu. Użyj `ExecutionContext` obiekt, aby użyć informacji o wywołania i środowisko uruchomieniowe funkcji w kodzie.

### <a name="logging"></a>Rejestrowanie

Dostęp do funkcji rejestrowania środowiska uruchomieniowego jest dostępna za pośrednictwem `ExecutionContext` obiektu. Tego rejestratora jest powiązany z usługi Azure monitor i pozwala flagi ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład kodu rejestruje komunikat ostrzegawczy, gdy Odebrano treść żądania jest pusta.

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

Często jest to pożądane do wyodrębnienia tajnych informacji z kodu źródłowego ze względów bezpieczeństwa. Dzięki temu kod w celu opublikowania repozytoriów kodu źródłowego bez przypadkowo podawania poświadczeń, aby inni deweloperzy. Można to osiągnąć, po prostu za pomocą zmiennych środowiskowych podczas uruchamiania usługi Azure Functions lokalnie i w przypadku wdrażania funkcji na platformie Azure.

W łatwy sposób ustawiania zmiennych środowiskowych podczas uruchamiania usługi Azure Functions lokalnie, można dodać te zmienne do pliku local.settings.json. Jeżeli nie są dostępne w katalogu głównym projektu funkcji, możesz ją utworzyć. Oto jak powinien wyglądać plik:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Każdy klucz-wartość mapowanie w `values` mapy zostaną udostępnione w czasie wykonywania jako zmienną środowiskową, dostępna poprzez wywołanie `System.getenv("<keyname>")`, na przykład `System.getenv("AzureWebJobsStorage")`. Dodawanie dodatkowy klucz / wartość pary jest akceptowane i zalecana praktyka.

> [!NOTE]
> Jeśli takie podejście jest zajęta, można się, że wziąć pod uwagę, czy dodanie local.settings.json pliku do repozytorium zignorować pliku, tak, aby nie jest zatwierdzona.

Z kodem teraz w zależności od tych zmiennych środowiskowych możesz zalogować się do portalu Azure, aby ustawić ten sam klucz / wartość pary w ustawieniach aplikacji funkcji, aby kod ekwiwalentnie podczas testowania lokalnie, jak i podczas wdrażania na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* [Zdalne debugowanie środowiska Java w usłudze Azure Functions przy użyciu programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
