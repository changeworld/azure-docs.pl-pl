---
title: Wyzwalacz Event Grid dla Azure Functions
description: Informacje na temat obsługi zdarzeń Event Grid w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: 812875be47cabdd23e6307403bb95d8d6ff174ec
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167505"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Wyzwalacz Event Grid dla Azure Functions

W tym artykule wyjaśniono, jak obsłużyć zdarzenia [Event Grid](../event-grid/overview.md) w Azure Functions. Aby uzyskać szczegółowe informacje na temat obsługi komunikatów Event Grid w punkcie końcowym HTTP, przeczytaj temat [odbieranie zdarzeń do punktu końcowego http](../event-grid/receive-events.md).

Event Grid to usługa platformy Azure, która wysyła żądania HTTP w celu powiadomienia o zdarzeniach, które wystąpiły w *wydawcach*. Wydawca jest usługą lub zasobem, który pochodzi ze zdarzenia. Na przykład konto usługi Azure Blob Storage jest wydawcą, a [przekazywanie lub usuwanie obiektów BLOB jest zdarzeniem](../storage/blobs/storage-blob-event-overview.md). Niektóre [usługi platformy Azure mają wbudowaną obsługę publikowania zdarzeń do Event Grid](../event-grid/overview.md#event-sources).

*Programy obsługi* zdarzeń odbierają i przetwarzają zdarzenia. Azure Functions to jedna z kilku [usług platformy Azure, która ma wbudowaną obsługę obsługi zdarzeń Event Grid](../event-grid/overview.md#event-handlers). W tym artykule dowiesz się, jak używać wyzwalacza Event Grid do wywołania funkcji po odebraniu zdarzenia z Event Grid.

Jeśli wolisz, możesz użyć wyzwalacza HTTP do obsługi zdarzeń Event Grid; Zobacz [odbieranie zdarzeń do punktu końcowego http](../event-grid/receive-events.md). Obecnie nie można używać wyzwalacza Event Grid dla aplikacji Azure Functions, gdy zdarzenie jest dostarczane w [schemacie CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Zamiast tego należy użyć wyzwalacza HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Wyzwalacz Event Grid jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Wyzwalacz Event Grid jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aby zapoznać się z przykładem wyzwalacza HTTP, zobacz [odbieranie zdarzeń do punktu końcowego http](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x i nowsze)

W poniższym przykładzie pokazano [ C# funkcję](functions-dotnet-class-library.md) , która wiąże się z `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz pakiety, [atrybuty](#attributes), [Konfiguracja](#configuration)i [użycie](#usage).

### <a name="version-1x"></a>Wersja 1. x

Poniższy przykład pokazuje [ C# funkcję Functions](functions-dotnet-class-library.md) 1. x, która wiąże się z `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania.

Oto dane powiązania w pliku *Function. JSON* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x-and-higher"></a>C#skrypt (wersja 2. x i nowsze)

Oto przykład, który wiąże się z `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Aby uzyskać więcej informacji, zobacz pakiety, [atrybuty](#attributes), [Konfiguracja](#configuration)i [użycie](#usage).

### <a name="version-1x"></a>Wersja 1. x

Oto kod skryptu funkcji 1. C# x, który wiąże się z `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania.

Oto dane powiązania w pliku *Function. JSON* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* i [funkcji języka Python](functions-reference-python.md) , która używa powiązania.

Oto dane powiązania w pliku *Function. JSON* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Oto kod języka Python:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz Event Grid, parametr ciągu](#event-grid-trigger-string-parameter)
* [Wyzwalacz Event Grid, parametr POJO](#event-grid-trigger-pojo-parameter)

W poniższych przykładach pokazano powiązanie wyzwalacza w [języku Java](functions-reference-java.md) , które używa powiązania i drukuje zdarzenie, po raz pierwszy otrzymuje zdarzenie jako `String` i drugie jako Pojo.

### <a name="event-grid-trigger-string-parameter"></a>Wyzwalacz Event Grid, parametr ciągu

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Wyzwalacz Event Grid, parametr POJO

W tym przykładzie zastosowano następujący POJO reprezentujący właściwości najwyższego poziomu zdarzenia Event Grid:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Po przybyciu ładunek JSON zdarzenia jest deserializowany do ```EventSchema``` POJO do użycia przez funkcję. Dzięki temu funkcja uzyskuje dostęp do właściwości zdarzenia w sposób zorientowany obiektowo.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `EventGridTrigger` w przypadku parametrów, których wartość pochodzi z EventGrid. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

---

## <a name="attributes"></a>Atrybuty

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) .

Oto atrybut `EventGridTrigger` w sygnaturze metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Aby zapoznać się z pełnym przykładem, zobacz C# przykład.

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Adnotacja [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) umożliwia deklaratywne skonfigurowanie powiązania Event Grid przez podanie wartości konfiguracyjnych. Więcej szczegółów można znaleźć w sekcjach [przykładowych](#example) i [konfiguracyjnych](#configuration) .

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* . Brak parametrów lub właściwości konstruktora do ustawienia w atrybucie `EventGridTrigger`.

|Właściwość Function.JSON |Opis|
|---------|---------|
| **type** | Wymagane — musi być ustawiony na `eventGridTrigger`. |
| **direction** | Wymagane — musi być ustawiony na `in`. |
| **Nazwij** | Wymagane — nazwa zmiennej używana w kodzie funkcji dla parametru, który odbiera dane zdarzenia. |

## <a name="usage"></a>Sposób użycia

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W Azure Functions 1. x można użyć następujących typów parametrów dla wyzwalacza Event Grid:

* `JObject`
* `string`

W Azure Functions 2. x i wyższych można także użyć następującego typu parametru dla wyzwalacza Event Grid:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`— definiuje właściwości dla pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji V1, jeśli próbujesz powiązać z `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilator wyświetli komunikat "przestarzałe" i zaleci użycie `Microsoft.Azure.EventGrid.Models.EventGridEvent` zamiast tego. Aby użyć nowszego typu, należy odwołać się do pakietu NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) i w pełni zakwalifikować nazwę typu `EventGridEvent`, tworząc prefiks z `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

W Azure Functions 1. x można użyć następujących typów parametrów dla wyzwalacza Event Grid:

* `JObject`
* `string`

W Azure Functions 2. x i wyższych można także użyć następującego typu parametru dla wyzwalacza Event Grid:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`— definiuje właściwości dla pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji V1, jeśli próbujesz powiązać z `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilator wyświetli komunikat "przestarzałe" i zaleci użycie `Microsoft.Azure.EventGrid.Models.EventGridEvent` zamiast tego. Aby użyć nowszego typu, należy odwołać się do pakietu NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) i w pełni zakwalifikować nazwę typu `EventGridEvent`, tworząc prefiks z `Microsoft.Azure.EventGrid.Models`. Aby uzyskać informacje o sposobach odwoływania się do C# pakietów NuGet w funkcji skryptu, zobacz [Korzystanie z pakietów NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Wystąpienie Event Grid jest dostępne za pośrednictwem parametru skonfigurowanego we właściwości `name` pliku *Function. JSON* .

# <a name="pythontabpython"></a>[Python](#tab/python)

Wystąpienie Event Grid jest dostępne za pośrednictwem parametru skonfigurowanego w właściwości `name` pliku *Function. JSON* , który wpisano jako `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

Wystąpienie zdarzenia Event Grid jest dostępne za pośrednictwem parametru skojarzonego z atrybutem `EventGridTrigger`, który wpisano jako `EventSchema`. Zobacz [przykład](#example) , aby uzyskać więcej szczegółów.

---

## <a name="event-schema"></a>Schemat zdarzeń

Dane dla zdarzenia Event Grid są odbierane jako obiekt JSON w treści żądania HTTP. KOD JSON wygląda podobnie do poniższego przykładu:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Pokazany przykład jest tablicą jednego elementu. Event Grid zawsze wysyła tablicę i może wysłać więcej niż jedno zdarzenie w tablicy. Środowisko uruchomieniowe wywołuje funkcję jeden raz dla każdego elementu tablicy.

Właściwości najwyższego poziomu w danych JSON zdarzenia są takie same dla wszystkich typów zdarzeń, natomiast zawartość właściwości `data` jest specyficzna dla każdego typu zdarzenia. Pokazany przykład dotyczy zdarzenia magazynu obiektów BLOB.

Aby uzyskać wyjaśnienia dotyczące wspólnych i specyficznych dla zdarzeń właściwości, zobacz [właściwości zdarzeń](../event-grid/event-schema.md#event-properties) w dokumentacji Event Grid.

Typ `EventGridEvent` definiuje tylko właściwości najwyższego poziomu; Właściwość `Data` jest `JObject`.

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Aby rozpocząć otrzymywanie Event Grid żądania HTTP, Utwórz subskrypcję Event Grid, która określa adres URL punktu końcowego, który wywołuje funkcję.

### <a name="azure-portal"></a>Portalu Azure

W przypadku funkcji, które tworzysz w Azure Portal z wyzwalaczem Event Grid, wybierz pozycję **Dodaj subskrypcję Event Grid**.

![Utwórz subskrypcję w portalu](media/functions-bindings-event-grid/portal-sub-create.png)

Po wybraniu tego linku w portalu zostanie otwarta strona **Tworzenie subskrypcji zdarzeń** z wstępnie wypełnionym adresem URL punktu końcowego.

![Wstępnie wypełniony adres URL punktu końcowego](media/functions-bindings-event-grid/endpoint-url.png)

Aby uzyskać więcej informacji o sposobach tworzenia subskrypcji przy użyciu Azure Portal, zobacz [Tworzenie niestandardowych zdarzeń Azure Portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji Event Grid.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć subskrypcję przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), użyj polecenie [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

Polecenie wymaga adresu URL punktu końcowego, który wywołuje funkcję. Poniższy przykład przedstawia wzorzec adresu URL specyficznego dla wersji:

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2. x (lub nowszej)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Wersja 1. x środowiska uruchomieniowego

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Klucz systemowy jest kluczem autoryzacji, który musi być uwzględniony w adresie URL punktu końcowego dla wyzwalacza Event Grid. W poniższej sekcji wyjaśniono, jak uzyskać klucz systemowy.

Oto przykład, który subskrybuje konto usługi BLOB Storage (z symbolem zastępczym dla klucza systemowego):

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2. x (lub nowszej)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Wersja 1. x środowiska uruchomieniowego

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Aby uzyskać więcej informacji na temat sposobu tworzenia subskrypcji, zobacz [Przewodnik Szybki Start](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) dotyczący usługi BLOB Storage lub innych Event Grid przewodników Szybki Start.

### <a name="get-the-system-key"></a>Pobieranie klucza systemowego

Klucz systemowy można uzyskać za pomocą następującego interfejsu API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2. x (lub nowszej)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Wersja 1. x środowiska uruchomieniowego

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Jest to interfejs API administratora, więc wymaga [klucza głównego](functions-bindings-http-webhook.md#authorization-keys)aplikacji funkcji. Nie należy mylić klucza systemowego (do wywoływania funkcji wyzwalacza Event Grid) za pomocą klucza głównego (na potrzeby wykonywania zadań administracyjnych w aplikacji funkcji). Gdy subskrybujesz temat Event Grid, pamiętaj, aby użyć klucza systemowego.

Oto przykład odpowiedzi, która dostarcza klucz systemowy:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Klucz główny aplikacji funkcji można uzyskać z karty **Ustawienia aplikacji funkcji** w portalu.

> [!IMPORTANT]
> Klucz główny zapewnia administratorowi dostęp do aplikacji funkcji. Nie udostępniaj tego klucza podmiotom trzecim ani nie rozpowszechniaj go w natywnych aplikacjach klienckich.

Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook.md#authorization-keys) w artykule odwołanie wyzwalacza http.

Alternatywnie można wysłać polecenie HTTP PUT, aby samodzielnie określić wartość klucza.

## <a name="local-testing-with-viewer-web-app"></a>Testowanie lokalne przy użyciu aplikacji internetowej przeglądarki

Aby przetestować wyzwalacz Event Grid lokalnie, należy uzyskać Event Grid żądania HTTP dostarczone z ich pochodzenia w chmurze do komputera lokalnego. Jednym ze sposobów jest przechwycenie żądań online i ręczne ponowne wysłanie ich na komputer lokalny:

1. [Utwórz aplikację sieci Web w przeglądarce](#create-a-viewer-web-app) , która przechwytuje komunikaty o zdarzeniach.
1. [Utwórz subskrypcję Event Grid](#create-an-event-grid-subscription) , która wysyła zdarzenia do aplikacji przeglądarki.
1. [Wygeneruj żądanie](#generate-a-request) i skopiuj treść żądania z aplikacji przeglądarki.
1. [Ręcznie Opublikuj żądanie](#manually-post-the-request) na adres URL hosta lokalnego funkcji wyzwalacza Event Grid.

Po zakończeniu testowania możesz użyć tej samej subskrypcji dla środowiska produkcyjnego, aktualizując punkt końcowy. Użyj polecenia [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) platformy Azure.

### <a name="create-a-viewer-web-app"></a>Tworzenie aplikacji sieci Web w przeglądarce

W celu uproszczenia przechwytywania komunikatów zdarzeń można wdrożyć [wstępnie utworzoną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer) , która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Zobaczysz witrynę, w której nie opublikowano jeszcze żadnych zdarzeń.

![Wyświetlanie nowej witryny](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz subskrypcję Event Grid typu, który chcesz przetestować, i nadaj jej adres URL z aplikacji sieci Web jako punkt końcowy powiadomienia o zdarzeniu. Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`. Dlatego pełny adres URL jest `https://<your-site-name>.azurewebsites.net/api/updates`

Aby uzyskać informacje o sposobach tworzenia subskrypcji przy użyciu Azure Portal, zobacz [Tworzenie niestandardowych zdarzeń Azure Portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji Event Grid.

### <a name="generate-a-request"></a>Generuj żądanie

Wyzwól zdarzenie, które będzie generować ruch HTTP do punktu końcowego aplikacji sieci Web.  Na przykład jeśli utworzono subskrypcję usługi BLOB Storage, Przekaż lub usuń obiekt BLOB. Gdy żądanie zostanie wyświetlone w aplikacji sieci Web, skopiuj treść żądania.

Żądanie weryfikacji subskrypcji zostanie najpierw odebrane. Zignoruj wszystkie żądania weryfikacji i skopiuj żądanie zdarzenia.

![Kopiuj treść żądania z aplikacji sieci Web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ręcznie Opublikuj żądanie

Uruchom lokalnie funkcję Event Grid.

Użyj narzędzia takiego jak [Ogłoś](https://www.getpostman.com/) lub [zwinięcie](https://curl.haxx.se/docs/httpscripting.html) , aby utworzyć żądanie HTTP Post:

* Ustaw `Content-Type: application/json` nagłówek.
* Ustaw `aeg-event-type: Notification` nagłówek.
* Wklej dane RequestBin do treści żądania.
* Opublikuj na adres URL funkcji wyzwalacza Event Grid.
  * Dla 2. x i wyższych należy użyć następującego wzorca:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Użycie 1. x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametr `functionName` musi być nazwą określoną w atrybucie `FunctionName`.

Poniższe zrzuty ekranu pokazują nagłówki i treść żądania w programie Poster:

![Nagłówki w programie Poster](media/functions-bindings-event-grid/postman2.png)

![Treść żądania w programie Poster](media/functions-bindings-event-grid/postman.png)

Funkcja wyzwalacza Event Grid wykonuje i pokazuje dzienniki podobne do poniższego przykładu:

![Przykładowe dzienniki funkcji wyzwalacza Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Event Grid](../event-grid/overview.md)
