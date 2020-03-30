---
title: Wyzwalacz usługi Azure Event Grid dla funkcji platformy Azure
description: Dowiedz się, aby uruchomić kod, gdy zdarzenia w usłudze Azure Functions są wywoływane.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277728"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure Event Grid dla funkcji platformy Azure

Użyj wyzwalacza funkcji, aby odpowiedzieć na zdarzenie wysłane do tematu siatki zdarzeń.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-event-grid.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W przykładzie wyzwalacza HTTP zobacz [Odbieranie zdarzeń do punktu końcowego HTTP](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2.x lub wyższe)

W poniższym przykładzie pokazano funkcję `EventGridEvent`Języka [C#,](functions-dotnet-class-library.md) która wiąże się z:

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

Aby uzyskać więcej informacji, zobacz Pakiety, [Atrybuty](#attributes-and-annotations), [Konfiguracja](#configuration)i [Użycie](#usage).

### <a name="version-1x"></a>Wersja 1.x

W poniższym przykładzie przedstawiono funkcję Funkcji 1.x [C#,](functions-dotnet-class-library.md) która wiąże się `JObject`z:

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania.

Oto dane powiązania w pliku *function.json:*

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

### <a name="version-2x-and-higher"></a>Wersja 2.x lub nowsza

Oto przykład, który wiąże `EventGridEvent`się z:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Aby uzyskać więcej informacji, zobacz Pakiety, [Atrybuty](#attributes-and-annotations), [Konfiguracja](#configuration)i [Użycie](#usage).

### <a name="version-1x"></a>Wersja 1.x

Oto funkcje 1.x C# kod skryptu, który wiąże się z: `JObject`

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania.

Oto dane powiązania w pliku *function.json:*

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

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która używa powiązania.

Oto dane powiązania w pliku *function.json:*

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

Oto kod Pythona:

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

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz siatki zdarzeń, parametr String](#event-grid-trigger-string-parameter)
* [Wyzwalacz siatki zdarzeń, parametr POJO](#event-grid-trigger-pojo-parameter)

Poniższe przykłady pokazują powiązania wyzwalacza w [języku Java,](functions-reference-java.md) które używają powiązania `String` i wydrukować zdarzenie, najpierw odbierając zdarzenie jako i drugi jako POJO.

### <a name="event-grid-trigger-string-parameter"></a>Wyzwalacz siatki zdarzeń, parametr String

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

### <a name="event-grid-trigger-pojo-parameter"></a>Wyzwalacz siatki zdarzeń, parametr POJO

W tym przykładzie użyto następującego POJO, reprezentującego właściwości najwyższego poziomu zdarzenia Event Grid:

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

Po przybyciu ładunku JSON zdarzenia jest de-serialized do ```EventSchema``` POJO do użycia przez funkcję. Ten proces umożliwia funkcji dostęp do właściwości zdarzenia w sposób obiektowy.

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

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `EventGridTrigger` Java użyj adnotacji na temat parametrów, których wartość pochodziłaby z programu EventGrid. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia.  Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Oto `EventGridTrigger` atrybut w podpisie metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Na przykład pełne zobacz przykład języka C#.

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) umożliwia deklaratywne skonfigurowanie powiązania z siatką zdarzeń przez podanie wartości konfiguracji. Zobacz [przykład](#example) i sekcje [konfiguracji,](#configuration) aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w pliku *function.json.* Nie ma żadnych parametrów konstruktora `EventGridTrigger` lub właściwości do ustawienia w atrybucie.

|właściwość function.json |Opis|
|---------|---------|
| **Typu** | Wymagane - musi być `eventGridTrigger`ustawiona na . |
| **Kierunku** | Wymagane - musi być `in`ustawiona na . |
| **Nazwa** | Wymagane — nazwa zmiennej używana w kodzie funkcji dla parametru odbierania danych zdarzenia. |

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

W usłudze Azure Functions 1.x można użyć następujących typów parametrów dla wyzwalacza siatki zdarzeń:

* `JObject`
* `string`

W usłudze Azure Functions 2.x lub nowszej, masz również możliwość użycia następującego typu parametru dla wyzwalacza siatki zdarzeń:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definiuje właściwości dla pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji w wersji 1, jeśli spróbujesz powiązać `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`z kompilatorem `Microsoft.Azure.EventGrid.Models.EventGridEvent` wyświetli komunikat "przestarzałe" i zaleca się użycie zamiast tego. Aby użyć nowszego typu, odwołaj się do pakietu [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet i w pełni zakwalifikuj `EventGridEvent` nazwę typu, prefiksując go za pomocą `Microsoft.Azure.EventGrid.Models`pliku .

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W usłudze Azure Functions 1.x można użyć następujących typów parametrów dla wyzwalacza siatki zdarzeń:

* `JObject`
* `string`

W usłudze Azure Functions 2.x lub nowszej, masz również możliwość użycia następującego typu parametru dla wyzwalacza siatki zdarzeń:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definiuje właściwości dla pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji w wersji 1, jeśli spróbujesz powiązać `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`z kompilatorem `Microsoft.Azure.EventGrid.Models.EventGridEvent` wyświetli komunikat "przestarzałe" i zaleca się użycie zamiast tego. Aby użyć nowszego typu, odwołaj się do pakietu [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet i w pełni zakwalifikuj `EventGridEvent` nazwę typu, prefiksując go za pomocą `Microsoft.Azure.EventGrid.Models`pliku . Aby uzyskać informacje dotyczące odwoływania się do pakietów NuGet w funkcji skryptu języka C#, zobacz [Używanie pakietów NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Wystąpienie siatki zdarzeń jest dostępne za pośrednictwem parametru skonfigurowanego we `name` właściwości pliku *function.json.*

# <a name="python"></a>[Python](#tab/python)

Wystąpienie Siatki zdarzeń jest dostępne za pośrednictwem parametru skonfigurowanego `name` we właściwości `func.EventGridEvent`pliku *function.json,* wpisanego jako .

# <a name="java"></a>[Java](#tab/java)

Wystąpienie zdarzenia Event Grid jest dostępne za `EventGridTrigger` pośrednictwem parametru skojarzonego z atrybutem, wpisanego jako . `EventSchema` Zobacz [przykład,](#example) aby uzyskać więcej szczegółów.

---

## <a name="event-schema"></a>Schemat zdarzeń

Dane dla zdarzenia Siatki zdarzeń są odbierane jako obiekt JSON w treści żądania HTTP. JSON wygląda podobnie do następującego przykładu:

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

Pokazany przykład jest tablicą jednego elementu. Usługa Event Grid zawsze wysyła tablicę i może wysyłać więcej niż jedno zdarzenie w tablicy. Środowisko wykonawcze wywołuje funkcję raz dla każdego elementu tablicy.

Właściwości najwyższego poziomu w przypadku danych JSON są takie same dla wszystkich `data` typów zdarzeń, podczas gdy zawartość właściwości są specyficzne dla każdego typu zdarzenia. Pokazany przykład jest dla zdarzenia magazynu obiektów blob.

Aby uzyskać szczegółowe opisy właściwości wspólnych i specyficznych dla zdarzenia, zobacz [Właściwości zdarzeń](../event-grid/event-schema.md#event-properties) w dokumentacji siatki zdarzeń.

Typ `EventGridEvent` definiuje tylko właściwości najwyższego poziomu; `Data` obiekt jest `JObject`.

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Aby rozpocząć odbieranie żądań HTTP w siatce zdarzeń, utwórz subskrypcję usługi Event Grid, która określa adres URL punktu końcowego, który wywołuje tę funkcję.

### <a name="azure-portal"></a>Portal Azure

W przypadku funkcji tworzonych w witrynie Azure portal z wyzwalaczem siatki zdarzeń wybierz pozycję **Dodaj subskrypcję siatki zdarzeń**.

![Tworzenie subskrypcji w portalu](media/functions-bindings-event-grid/portal-sub-create.png)

Po wybraniu tego łącza portal otworzy stronę **Utwórz subskrypcję zdarzeń** z wstępnie wypełnionym adresem URL punktu końcowego.

![Wstępnie wypełniony adres URL punktu końcowego](media/functions-bindings-event-grid/endpoint-url.png)

Aby uzyskać więcej informacji na temat tworzenia subskrypcji przy użyciu witryny Azure portal, zobacz [Tworzenie zdarzenia niestandardowego — witryna Azure portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji usługi Event Grid.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć subskrypcję przy użyciu [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)należy użyć polecenia [tworzenia subskrypcji zdarzeń az.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create)

Polecenie wymaga adresu URL punktu końcowego, który wywołuje funkcję. W poniższym przykładzie przedstawiono wzorzec adresu URL specyficznego dla wersji:

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2.x (i nowszej)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Środowisko wykonawcze wersji 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Klucz systemowy jest kluczem autoryzacji, który musi zostać uwzględniony w adresie URL punktu końcowego wyzwalacza siatki zdarzeń. W poniższej sekcji wyjaśniono, jak uzyskać klucz systemowy.

Oto przykład, który subskrybuje konto magazynu obiektów blob (z symbolem zastępczym klucza systemowego):

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2.x (i nowszej)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Środowisko wykonawcze wersji 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Aby uzyskać więcej informacji na temat tworzenia subskrypcji, zobacz [szybki start magazynu obiektów blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) lub inne przewodniki Szybki start usługi Event Grid.

### <a name="get-the-system-key"></a>Pobierz klucz systemowy

Klucz systemowy można uzyskać za pomocą następującego interfejsu API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2.x (i nowszej)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Środowisko wykonawcze wersji 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Jest to interfejs API administratora, więc wymaga [klucza głównego](functions-bindings-http-webhook-trigger.md#authorization-keys)aplikacji funkcji . Nie należy mylić klucza systemowego (do wywoływania funkcji wyzwalacza siatki zdarzeń) z kluczem głównym (do wykonywania zadań administracyjnych w aplikacji funkcji). Podczas subskrybowania tematu siatki zdarzeń należy użyć klucza systemowego.

Oto przykład odpowiedzi, która zawiera klucz systemowy:

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

Klucz główny aplikacji funkcji można uzyskać na karcie **Ustawienia aplikacji Funkcji** w portalu.

> [!IMPORTANT]
> Klucz główny zapewnia administratorowi dostęp do aplikacji funkcji. Nie udostępniaj tego klucza stronom trzecim ani nie rozpowszechniaj go w natywnych aplikacjach klienckich.

Aby uzyskać więcej informacji, zobacz [Klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys) w artykule odwołania do wyzwalacza HTTP.

Alternatywnie można wysłać HTTP PUT, aby samodzielnie określić wartość klucza.

## <a name="local-testing-with-viewer-web-app"></a>Testowanie lokalne za pomocą aplikacji internetowej przeglądarki

Aby przetestować wyzwalacz siatki zdarzeń lokalnie, musisz uzyskać żądania HTTP usługi Event Grid dostarczane z ich pochodzenia w chmurze do komputera lokalnego. Jednym ze sposobów, aby to zrobić, jest przechwytywanie żądań online i ręczne ponowne wysłanie ich na komputerze lokalnym:

1. [Utwórz aplikację internetową przeglądarki,](#create-a-viewer-web-app) która przechwytuje komunikaty o zdarzeniach.
1. [Utwórz subskrypcję usługi Event Grid,](#create-an-event-grid-subscription) która wysyła zdarzenia do aplikacji przeglądarki.
1. [Wygeneruj żądanie](#generate-a-request) i skopiuj treść żądania z aplikacji przeglądarki.
1. [Ręcznie opublikuj żądanie na](#manually-post-the-request) adresie URL hosta lokalnego funkcji wyzwalania siatki zdarzeń.

Po zakończeniu testowania, można użyć tej samej subskrypcji dla produkcji, aktualizując punkt końcowy. Użyj polecenia [az eventgrid eventgrid update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-a-viewer-web-app"></a>Tworzenie aplikacji sieci Web przeglądarki

Aby uprościć przechwytywanie komunikatów o zdarzeniach, można wdrożyć [wstępnie szesnasty aplikację sieci web,](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Zobaczysz witrynę, w której nie opublikowano jeszcze żadnych zdarzeń.

![Wyświetlanie nowej witryny](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz subskrypcję usługi Event Grid typu, który chcesz przetestować, i nadaj jej adres URL z aplikacji sieci web jako punkt końcowy powiadomienia o zdarzeniu. Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`. Tak więc pełny adres URL jest`https://<your-site-name>.azurewebsites.net/api/updates`

Aby uzyskać informacje dotyczące tworzenia subskrypcji przy użyciu witryny Azure portal, zobacz [Tworzenie zdarzenia niestandardowego — witryna Azure portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji usługi Event Grid.

### <a name="generate-a-request"></a>Generowanie żądania

Wyzwalanie zdarzenia, które wygeneruje ruch HTTP do punktu końcowego aplikacji sieci web.  Na przykład jeśli utworzono subskrypcję magazynu obiektów blob, przekaż lub usuń obiekt blob. Gdy żądanie pojawi się w aplikacji sieci web, skopiuj treść żądania.

Żądanie weryfikacji subskrypcji zostanie odebrane jako pierwsze; zignorować wszystkie żądania sprawdzania poprawności i skopiować żądanie zdarzenia.

![Kopiowanie treści żądania z aplikacji sieci Web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ręcznie opublikuj żądanie

Uruchom funkcję siatki zdarzeń lokalnie.

Użyj narzędzia, takiego jak [Listonosz](https://www.getpostman.com/) lub [zwiń,](https://curl.haxx.se/docs/httpscripting.html) aby utworzyć żądanie HTTP POST:

* Ustawianie `Content-Type: application/json` nagłówka.
* Ustawianie `aeg-event-type: Notification` nagłówka.
* Wklej dane RequestBin do treści żądania.
* Opublikuj adres URL funkcji wyzwalania siatki zdarzeń.
  * W przypadku 2.x i nowszych należy użyć następującego wzoru:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * W przypadku zastosowania 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametr `functionName` musi być nazwą określoną w atrybucie. `FunctionName`

Poniższe zrzuty ekranu pokazują nagłówki i treść żądania w postmanie:

![Nagłówki w listonoszu](media/functions-bindings-event-grid/postman2.png)

![Treść żądania w listonoszu](media/functions-bindings-event-grid/postman.png)

Funkcja wyzwalania siatki zdarzeń wykonuje i pokazuje dzienniki podobne do następującego przykładu:

![Przykładowe dzienniki funkcji wyzwalania siatki zdarzeń](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Następne kroki

* [Wysyłanie zdarzenia w sieci zdarzeń](./functions-bindings-event-grid-trigger.md)
