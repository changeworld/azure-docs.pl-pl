---
title: Wyzwalacz Event Grid dla Azure Functions
description: Informacje na temat obsługi zdarzeń Event Grid w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: fbe41bdc5f253f1a605aa291a31191b7339b9850
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030584"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Wyzwalacz Event Grid dla Azure Functions

W tym artykule wyjaśniono, jak obsłużyć zdarzenia [Event Grid](../event-grid/overview.md) w Azure Functions.

Event Grid to usługa platformy Azure, która wysyła żądania HTTP w celu powiadomienia o zdarzeniach, które wystąpiły w *wydawcach*. Wydawca jest usługą lub zasobem, który pochodzi ze zdarzenia. Na przykład konto usługi Azure Blob Storage jest wydawcą, a [przekazywanie lub usuwanie obiektów BLOB jest zdarzeniem](../storage/blobs/storage-blob-event-overview.md). Niektóre [usługi platformy Azure mają wbudowaną obsługę publikowania zdarzeń do Event Grid](../event-grid/overview.md#event-sources).

*Programy obsługi* zdarzeń odbierają i przetwarzają zdarzenia. Azure Functions to jedna z kilku [usług platformy Azure, która ma wbudowaną obsługę obsługi zdarzeń Event Grid](../event-grid/overview.md#event-handlers). W tym artykule dowiesz się, jak używać wyzwalacza Event Grid do wywołania funkcji po odebraniu zdarzenia z Event Grid.

Jeśli wolisz, możesz użyć wyzwalacza HTTP do obsługi zdarzeń Event Grid; Zobacz [Używanie wyzwalacza http jako wyzwalacza Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) w dalszej części tego artykułu. Obecnie nie można używać wyzwalacza Event Grid dla aplikacji Azure Functions, gdy zdarzenie jest dostarczane w [schemacie CloudEvents](../event-grid/cloudevents-schema.md). Zamiast tego należy użyć wyzwalacza HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2. x

Wyzwalacz Event Grid jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Wyzwalacz Event Grid jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka dla wyzwalacza Event Grid:

* C#
* [C#skrypt (. CSX)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Aby zapoznać się z przykładem wyzwalacza HTTP, zobacz [jak używać wyzwalacza http](#use-an-http-trigger-as-an-event-grid-trigger) w dalszej części tego artykułu.

### <a name="c-2x"></a>C#(2. x)

W poniższym przykładzie pokazano [ C# funkcję Functions](functions-dotnet-class-library.md) 2. x, która wiąże się z `EventGridEvent`:

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

### <a name="c-version-1x"></a>C#(Wersja 1. x)

W poniższym przykładzie pokazano [ C# funkcję Functions](functions-dotnet-class-library.md) 1. x, która wiąże się z `JObject`:

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

### <a name="c-script-example"></a>C#przykład skryptu

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

#### <a name="c-script-version-2x"></a>C#skrypt (wersja 2. x)

Oto kod skryptu funkcji 2. C# x, który wiąże się z `EventGridEvent`:

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

#### <a name="c-script-version-1x"></a>C#skrypt (wersja 1. x)

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

### <a name="javascript-example"></a>Przykład JavaScript

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

Oto kod JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

### <a name="python-example"></a>Przykład języka Python

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
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Wyzwalacz — przykłady dla języka Java

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz Event Grid, parametr ciągu](#event-grid-trigger-string-parameter-java)
* [Wyzwalacz Event Grid, parametr POJO](#event-grid-trigger-pojo-parameter-java)

W poniższych przykładach pokazano powiązanie wyzwalacza w pliku *Function. JSON* i [funkcjach języka Java](functions-reference-java.md) , które używają powiązania i wydrukują zdarzenie, a pierwsze otrzymywanie zdarzenia jako ```String``` i sekundy jako Pojo.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Wyzwalacz Event Grid, parametr ciągu (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Wyzwalacz Event Grid, POJO parametru (Java)

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
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `EventGridTrigger` w przypadku parametrów, których wartość pochodzi z EventGrid. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

## <a name="attributes"></a>Atrybuty

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

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* . Brak parametrów lub właściwości konstruktora do ustawienia w atrybucie `EventGridTrigger`.

|Function. JSON — Właściwość |Opis|
|---------|---------|
| **type** | Wymagany — musi być ustawiony na wartość `eventGridTrigger`. |
| **direction** | Wymagany — musi być ustawiony na wartość `in`. |
| **Nazwij** | Wymagane — nazwa zmiennej używana w kodzie funkcji dla parametru, który odbiera dane zdarzenia. |

## <a name="usage"></a>Użycie

Dla C# i F# funkcji w Azure Functions 1. x można użyć następujących typów parametrów dla wyzwalacza Event Grid:

* `JObject`
* `string`

Dla C# programu F# i w Azure Functions 2. x jest również dostępna opcja użycia następującego typu parametru dla wyzwalacza Event Grid:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-definiuje właściwości dla pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji v1 w przypadku próby utworzenia powiązania z `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` kompilator wyświetli komunikat "przestarzałe" i zaleci użycie `Microsoft.Azure.EventGrid.Models.EventGridEvent`. Aby użyć nowszego typu, należy odwołać się do pakietu NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) i w pełni zakwalifikować nazwę typu `EventGridEvent`, tworząc prefiks przy użyciu `Microsoft.Azure.EventGrid.Models`. Aby uzyskać informacje o sposobach odwoływania się do C# pakietów NuGet w funkcji skryptu, zobacz [Korzystanie z pakietów NuGet](functions-reference-csharp.md#using-nuget-packages)

W przypadku funkcji JavaScript parametr nazwany przez właściwość *Function. json* `name` ma odwołanie do obiektu zdarzenia.

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

### <a name="azure-portal"></a>Azure Portal

W przypadku funkcji, które tworzysz w Azure Portal z wyzwalaczem Event Grid, wybierz pozycję **Dodaj subskrypcję Event Grid**.

![Utwórz subskrypcję w portalu](media/functions-bindings-event-grid/portal-sub-create.png)

Po wybraniu tego linku w portalu zostanie otwarta strona **Tworzenie subskrypcji zdarzeń** z wstępnie wypełnionym adresem URL punktu końcowego.

![Wstępnie wypełniony adres URL punktu końcowego](media/functions-bindings-event-grid/endpoint-url.png)

Aby uzyskać więcej informacji o sposobach tworzenia subskrypcji przy użyciu Azure Portal, zobacz [Tworzenie niestandardowych zdarzeń Azure Portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji Event Grid.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć subskrypcję przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), użyj polecenie [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

Polecenie wymaga adresu URL punktu końcowego, który wywołuje funkcję. Poniższy przykład przedstawia wzorzec adresu URL specyficznego dla wersji:

#### <a name="version-2x-runtime"></a>Środowisko uruchomieniowe w wersji 2. x

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Wersja 1. x środowiska uruchomieniowego

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Klucz systemowy jest kluczem autoryzacji, który musi być uwzględniony w adresie URL punktu końcowego dla wyzwalacza Event Grid. W poniższej sekcji wyjaśniono, jak uzyskać klucz systemowy.

Oto przykład, który subskrybuje konto usługi BLOB Storage (z symbolem zastępczym dla klucza systemowego):

#### <a name="version-2x-runtime"></a>Środowisko uruchomieniowe w wersji 2. x

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

#### <a name="version-2x-runtime"></a>Środowisko uruchomieniowe w wersji 2. x

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

* Ustaw nagłówek `Content-Type: application/json`.
* Ustaw nagłówek `aeg-event-type: Notification`.
* Wklej dane RequestBin do treści żądania.
* Opublikuj na adres URL funkcji wyzwalacza Event Grid.
  * Dla 2. x Użyj następującego wzorca:

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

## <a name="local-testing-with-ngrok"></a>Testowanie lokalne przy użyciu ngrok

Innym sposobem na przetestowanie wyzwalacza Event Grid lokalnie jest Automatyzacja połączenia HTTP między Internetem a komputerem deweloperskim. Możesz to zrobić za pomocą narzędzia, takiego jak [ngrok](https://ngrok.com/):

1. [Utwórz punkt końcowy ngrok](#create-an-ngrok-endpoint).
1. [Uruchom funkcję wyzwalacza Event Grid](#run-the-event-grid-trigger-function).
1. [Utwórz subskrypcję Event Grid](#create-a-subscription) , która wysyła zdarzenia do punktu końcowego ngrok.
1. [Wyzwól zdarzenie](#trigger-an-event).

Po zakończeniu testowania możesz użyć tej samej subskrypcji dla środowiska produkcyjnego, aktualizując punkt końcowy. Użyj polecenia [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) platformy Azure.

### <a name="create-an-ngrok-endpoint"></a>Tworzenie punktu końcowego ngrok

Pobierz program *ngrok. exe* z programu [ngrok](https://ngrok.com/)i uruchom polecenie przy użyciu następującego polecenia:

```
ngrok http -host-header=localhost 7071
```

Parametr-Host-header jest wymagany, ponieważ środowisko uruchomieniowe Functions oczekuje żądań z hosta lokalnego, gdy jest ono uruchomione na hoście lokalnym. 7071 jest domyślnym numerem portu, gdy środowisko uruchomieniowe działa lokalnie.

Polecenie tworzy dane wyjściowe podobne do następujących:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Będziesz używać adresu URL `https://{subdomain}.ngrok.io` dla subskrypcji Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Uruchamianie funkcji wyzwalacza Event Grid

Ngrok URL nie ma specjalnej obsługi przez Event Grid, więc funkcja musi być uruchomiona lokalnie podczas tworzenia subskrypcji. Jeśli nie, odpowiedź na weryfikację nie zostanie wysłana, a Tworzenie subskrypcji nie powiedzie się.

### <a name="create-a-subscription"></a>Tworzenie subskrypcji

Utwórz subskrypcję Event Grid typu, który chcesz przetestować, i nadaj jej punktowi końcowemu ngrok.

Użyj tego wzorca punktu końcowego dla funkcji 2. x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Użyj tego wzorca punktu końcowego dla funkcji 1. x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

Parametr `{FUNCTION_NAME}` musi być nazwą określoną w atrybucie `FunctionName`.

Oto przykład użycia interfejsu wiersza polecenia platformy Azure:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Aby uzyskać informacje na temat sposobu tworzenia subskrypcji, zobacz [Tworzenie subskrypcji](#create-a-subscription) wcześniej w tym artykule.

### <a name="trigger-an-event"></a>Wyzwalanie zdarzenia

Wyzwól zdarzenie, które będzie generować ruch HTTP do punktu końcowego ngrok.  Na przykład jeśli utworzono subskrypcję usługi BLOB Storage, Przekaż lub usuń obiekt BLOB.

Funkcja wyzwalacza Event Grid wykonuje i pokazuje dzienniki podobne do poniższego przykładu:

![Przykładowe dzienniki funkcji wyzwalacza Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Użyj wyzwalacza HTTP jako wyzwalacza Event Grid

Zdarzenia Event Grid są odbierane jako żądania HTTP, dlatego można obsługiwać zdarzenia przy użyciu wyzwalacza HTTP zamiast wyzwalacza Event Grid. Jedną z możliwych przyczyn tego problemu jest uzyskanie większej kontroli nad adresem URL punktu końcowego, który wywołuje funkcję. Kolejną przyczyną jest to, że należy odebrać zdarzenia w [schemacie CloudEvents](../event-grid/cloudevents-schema.md). Obecnie wyzwalacz Event Grid nie obsługuje schematu CloudEvents. W przykładach w tej sekcji przedstawiono rozwiązania Event Grid schematu i schematu CloudEvents.

W przypadku korzystania z wyzwalacza HTTP należy napisać kod, który automatycznie wyzwalacz Event Grid:

* Wysyła odpowiedź weryfikacji do [żądania weryfikacji subskrypcji](../event-grid/security-authentication.md#webhook-event-delivery).
* Wywołuje funkcję jeden raz dla elementu tablicy zdarzeń zawartej w treści żądania.

Aby uzyskać informacje o adresie URL używanym do wywoływania funkcji lokalnie lub uruchamianej na platformie Azure, zobacz [dokumentację referencyjną powiązania wyzwalacza http](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Schemat usługi Event Grid

Następujący przykładowy C# kod dla wyzwalacza http symuluje działanie wyzwalacza Event Grid. Użyj tego przykładu dla zdarzeń dostarczonych w schemacie Event Grid.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Następujący przykładowy kod JavaScript dla wyzwalacza HTTP symuluje działanie wyzwalacza Event Grid. Użyj tego przykładu dla zdarzeń dostarczonych w schemacie Event Grid.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Kod obsługi zdarzeń przechodzi wewnątrz pętli przez tablicę `messages`.

### <a name="cloudevents-schema"></a>Schemat CloudEvents

Następujący przykładowy C# kod dla wyzwalacza http symuluje działanie wyzwalacza Event Grid.  Użyj tego przykładu dla zdarzeń dostarczonych w schemacie CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Następujący przykładowy kod JavaScript dla wyzwalacza HTTP symuluje działanie wyzwalacza Event Grid. Użyj tego przykładu dla zdarzeń dostarczonych w schemacie CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Event Grid](../event-grid/overview.md)
