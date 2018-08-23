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
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: d895258a4c8a38d00932d81600dc8633d7d70112
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "42056660"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dla deweloperów w usłudze Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Model programowania 

Funkcji platformy Azure powinna być metoda bezstanowe klasy, która przetwarza dane wejściowe i generuje dane wyjściowe. Mimo że można napisać metody wystąpienia, funkcja nie może zależeć od wszystkie pola wystąpienia klasy. Wszystkie metody funkcji musi mieć `public` modyfikator dostępu.

Można umieścić więcej niż jedną funkcję w projekcie. Należy unikać umieszczenie funkcji w oddzielnych plikach JAR.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

 Usługa Azure functions są wywoływane przez wyzwalacz, takie jak żądania HTTP, czasomierz lub aktualizacji danych. Funkcja musi przetworzyć tego wyzwalacza i inne dane wejściowe do wyprodukowania jednego lub więcej danych wyjściowych.

Korzystanie z adnotacji Java objęte [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pakietu, aby powiązać dane wejściowe i wyjściowe metody. Przykładowy kod przy użyciu adnotacji jest dostępna w [dokumenty referencyjne języka Java](/java/api/com.microsoft.azure.functions.annotation) każdej adnotacji, w dokumentacji usługi Azure Functions powiązania odniesienia, takiego jak dla [wyzwalaczy HTTP](/azure/azure-functions/functions-bindings-http-webhook).

Dane wejściowe wyzwalacza i dane wyjściowe można także definiować w [function.json](/azure/azure-functions/functions-reference#function-code) funkcji zamiast za pomocą adnotacji. Za pomocą `function.json` zamiast adnotacje w ten sposób nie jest zalecane.

> [!IMPORTANT] 
> Należy skonfigurować konto usługi Azure Storage w swojej [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) lokalnie uruchomić wyzwalacze usługi Azure Storage Blob, kolejki lub tabeli.

Przykład korzystanie z adnotacji:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="third-party-libraries"></a>Bibliotek innych firm 

Usługa Azure Functions obsługuje korzystanie z bibliotek innych firm. Domyślnie wszystkie zależności są określone w projekcie `pom.xml` pliku zostaną automatycznie dołączone podczas `mvn package` cel. W przypadku bibliotek, które nie jest określony jako zależności w `pom.xml` pliku, umieść je w `lib` katalogu w katalogu głównym funkcji. Zależności są umieszczane w `lib` katalog zostanie dodany do modułu ładującego klasę systemu w czasie wykonywania.

## <a name="data-types"></a>Typy danych

Wszystkie typy danych w języku Java można użyć dla danych wejściowych i wyjściowych, w tym natywnych typów; dostosowane typy języka Java i wyspecjalizowane Azure typów zdefiniowanych w `azure-functions-java-library` pakietu. Usługi Azure Functions, który próbuje środowiska uruchomieniowego skonwertować danych wejściowych, odebranych na typ żądanego przez kod.

### <a name="strings"></a>Ciągi

Wartości przekazane do funkcji metod będzie rzutowane na ciągi, jeśli odpowiedni typ parametru wejściowego dla tej funkcji jest typu `String`. 

### <a name="plain-old-java-objects-pojos"></a>Zwykłych starych obiektów Java (Pojo)

Ciągi sformatowane przy użyciu formatu JSON będzie być rzutowane na typy języka Java, jeśli podpis wejściowy funkcji oczekuje typu języka Java. Ta konwersja umożliwia przekazywanie w formacie JSON i pracują z typami środowiska Java.

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

Może być puste wartości wejściowe `null` jako argument funkcji, ale zalecany sposób radzenia sobie z potencjał jest użycie wartości puste `Optional<T>`.


## <a name="function-method-overloading"></a>Przeciążenie metody — funkcja

Możesz przeciążyć metody funkcji o tej samej nazwie, ale z różnymi typami. Na przykład, może mieć jednocześnie `String echo(String s)` i `String echo(MyType s)` w klasie. Usługa Azure Functions decyduje, jakiej metody do wywołania na podstawie danych wejściowych typu (dla danych wejściowych, typ MIME HTTP `text/plain` prowadzi do `String` podczas `application/json` reprezentuje `MyType`).

## <a name="inputs"></a>Dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w usłudze Azure Functions: jeden z nich to dane wejściowe wyzwalacza, a drugi to dodatkowe dane wejściowe. Mimo że są inne w przypadku `function.json`, użycie jest identyczna w kodzie języka Java. Jako przykład Weźmy poniższy fragment kodu:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Gdy ta funkcja jest wyzwalana, żądanie HTTP jest przekazywany do funkcji przez `String in`. Wpis zostanie pobrany z usługi Azure Table Storage, na podstawie Identyfikatora w adresie URL trasy i wprowadziliśmy dostępne jako `obj` w treści funkcji.

## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe mogą być wyrażone zarówno wartości zwracane lub parametry wyjściowe. Jeśli istnieje tylko jedno wyjście, są zalecane do użycia wartości zwracanej. Wiele wyjść należy użyć parametrów wyjściowych.

Wartość zwracana jest najprostsza forma dane wyjściowe, po prostu zwraca wartość dowolnego typu i środowisko uruchomieniowe usługi Azure Functions będzie próbował kierować je do rzeczywistego typu (na przykład odpowiedź HTTP).  Można zastosować adnotacji w danych wyjściowych metody — funkcja (właściwości name obiektu adnotacji musi być $return) do definiowania danych wyjściowych wartość zwracaną.

Aby utworzyć wiele wartości danych wyjściowych, należy użyć `OutputBinding<T>` typ zdefiniowany w elemencie `azure-functions-java-library` pakietu. Jeśli musisz wprowadzić odpowiedź HTTP i wypychania komunikat do kolejki, a także można napisać mniej więcej tak:

Na przykład zawartość obiektu blob, kopiowanie funkcji można zdefiniować jako następujący kod. `@StorageAccount` Adnotacja służy tutaj, aby uniknąć duplikowania właściwości połączenia dla obu `@BlobTrigger` i `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Użyj `OutputBinding<byte[]`> Aby dane binarne dane wyjściowe wartość (parametry); wartości zwracane, wystarczy użyć `byte[]`.

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
import com.microsoft.azure.functions.annotation.*;


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

## <a name="execution-context"></a>Kontekst wykonywania

Wchodzić w interakcje ze środowiskiem wykonywania usługi Azure Functions za pomocą `ExecutionContext` obiektu zdefiniowany w `azure-functions-java-library` pakietu. Użyj `ExecutionContext` obiekt, aby użyć informacji o wywołania i środowisko uruchomieniowe funkcji w kodzie.

### <a name="logging"></a>Rejestrowanie

Dostęp do funkcji rejestrowania środowiska uruchomieniowego jest dostępna za pośrednictwem `ExecutionContext` obiektu. Tego rejestratora jest powiązany z usługi Azure monitor i pozwala flagi ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład kodu rejestruje komunikat ostrzegawczy, gdy Odebrano treść żądania jest pusta.

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

## <a name="environment-variables"></a>Zmienne środowiskowe

Zachowaj informacje poufne, takie jak kluczy lub tokenów z kodu źródłowego ze względów bezpieczeństwa. Użyj klawiszy i tokenów w kodzie funkcji, odczytując je ze zmiennych środowiskowych.

Do ustawiania zmiennych środowiskowych podczas uruchamiania usługi Azure Functions lokalnie, można dodać te zmienne do pliku local.settings.json. Jeśli nie jest obecny w katalogu głównym projektu funkcji, można go utworzyć. Oto jak powinien wyglądać plik:

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
> Jeśli takie podejście jest zajęta, można się, że można dodać local.settings.json pliku do repozytorium zignorować pliku, tak, aby nie jest zatwierdzona.

W kodzie teraz w zależności od tych zmiennych środowiskowych można logowaniu do portalu Azure, aby ustawić ten sam klucz / wartość pary w ustawieniach aplikacji funkcji, aby kod ekwiwalentnie podczas testowania lokalnie, jak i podczas wdrażania na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* [Zdalne debugowanie środowiska Java w usłudze Azure Functions przy użyciu programu Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
