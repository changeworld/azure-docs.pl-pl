---
title: Wyzwalacz siatki zdarzeń dla usługi Azure Functions
description: Dowiedz się, jak do obsługi zdarzeń usługi Event Grid w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: b2ab07e40ac2652d97e912f8c7bd3b8893bfc114
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438760"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Wyzwalacz siatki zdarzeń dla usługi Azure Functions

W tym artykule opisano sposób obsługi [usługi Event Grid](../event-grid/overview.md) zdarzenia w usłudze Azure Functions.

Usługa Event Grid jest usługą platformy Azure, która wysyła żądania HTTP, powiadomimy Cię o zdarzenia mające miejsce w *wydawców*. Wydawca jest usługą lub zasobem, który pochodzi zdarzenie. Na przykład konto usługi Azure blob storage jest wydawcą, a [przekazywania obiektów blob lub usunięciu to zdarzenie](../storage/blobs/storage-blob-event-overview.md). Niektóre [usług platformy Azure ma wbudowaną obsługą publikowania zdarzeń do usługi Event Grid](../event-grid/overview.md#event-sources).

Zdarzenie *obsługi* odbierania i przetwarzania zdarzeń. Usługa Azure Functions jest jednym z kilku [usług platformy Azure, które mają wbudowaną obsługę zdarzeń usługi Event Grid](../event-grid/overview.md#event-handlers). W tym artykule dowiesz się, jak za pomocą wyzwalacza usługi Event Grid, wywołaj funkcję po odebraniu zdarzenia z usługi Event Grid.

Jeśli wolisz, można użyć wyzwalacza HTTP do obsługi zdarzeń usługi Event Grid; zobacz [użyć wyzwalacza HTTP jako wyzwalacz usługi Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) w dalszej części tego artykułu. Obecnie nie można użyć wyzwalacza usługi Event Grid dla aplikacji usługi Azure Functions, gdy zdarzenie jest dostarczany za [schematu CloudEvents](../event-grid/cloudevents-schema.md). Zamiast tego należy użyć wyzwalacza HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Wyzwalacz usługi Event Grid jest podawany jako [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Wyzwalacz usługi Event Grid jest podawany jako [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pakietu NuGet w wersji 1.x. Kod źródłowy dla pakietu znajduje się w [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Przykład

Zobacz przykład charakterystyczny dla wyzwalacza usługi Event Grid:

* C#
* [Skryptu C# (csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Na przykład wyzwalacz protokołu HTTP, zobacz [sposób użycia wyzwalacza HTTP](#use-an-http-trigger-as-an-event-grid-trigger) w dalszej części tego artykułu.

### <a name="c-2x"></a>C#(2.x)

W poniższym przykładzie pokazano funkcje 2.x [funkcja języka C#](functions-dotnet-class-library.md) który wiąże `EventGridEvent`:

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

Aby uzyskać więcej informacji, zobacz pakietów, [atrybuty](#attributes), [konfiguracji](#configuration), i [użycia](#usage).

### <a name="c-version-1x"></a>C#(W wersji 1.x)

W poniższym przykładzie pokazano funkcje 1.x [funkcja języka C#](functions-dotnet-class-library.md) który wiąże `JObject`:

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

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa.

Oto powiązanie danych w *function.json* pliku:

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

#### <a name="c-script-version-2x"></a>C#Skrypt (wersja 2.x)

Poniżej przedstawiono funkcje kod skryptu 2.x języka C#, która jest powiązywana z `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Aby uzyskać więcej informacji, zobacz pakietów, [atrybuty](#attributes), [konfiguracji](#configuration), i [użycia](#usage).

#### <a name="c-script-version-1x"></a>C#Skrypt (w wersji 1.x)

Poniżej przedstawiono funkcje kod skryptu 1.x C#, która jest powiązywana z `JObject`:

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

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa.

Oto powiązanie danych w *function.json* pliku:

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

### <a name="python-example"></a>Przykładem w języku Python

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.json* pliku i [funkce Pythonu](functions-reference-python.md) powiązania, który używa.

Oto powiązanie danych w *function.json* pliku:

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

Poniżej przedstawiono kod języka Python:

```python
import logging
import azure.functions as func

def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Wyzwalacz - przykładów w języku Java

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacza usługi Event Grid, parametr ciągu](#event-grid-trigger-string-parameter-java)
* [Wyzwalacza usługi Event Grid, parametr obiektu typu POJO](#event-grid-trigger-pojo-parameter-java)

W poniższych przykładach pokazano powiązania wyzwalacza w *function.json* pliku i [funkcjach języka Java](functions-reference-java.md) , stosować powiązanie i wydrukuj zdarzenie, najpierw odbierającego zdarzenie jako ```String``` a drugi jako obiektu typu POJO.

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

#### <a name="event-grid-trigger-string-parameter-java"></a>Wyzwalacza usługi Event Grid, parametr ciągu (Java)

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

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Wyzwalacza usługi Event Grid, parametr obiektu typu POJO (Java)

W tym przykładzie użyto następujących obiektu typu POJO, reprezentujący najwyższego poziomu właściwości zdarzenia usługi Event Grid:

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

Po dostarczeniu, ładunek JSON zdarzenia jest zdeserializowany do ```EventSchema``` obiektu typu POJO do użytku przez funkcję. Dzięki temu funkcja dostępu do właściwości zdarzenia w sposób zorientowane obiektowo.

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

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `EventGridTrigger` adnotacji w parametrach, którego wartość może pochodzić z EventGrid. Parametry z tymi adnotacjami spowodować, że funkcja do uruchomienia po odebraniu zdarzenia.  Ta adnotacja mogą być używane z typami natywnymi Java, obiektów typu Pojo lub wartości dopuszczających wartości null przy użyciu `Optional<T>`.

## <a name="attributes"></a>Atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) atrybutu.

Oto `EventGridTrigger` atrybutu w podpisie metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz C# przykład.

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku. Nie ma żadnych parametrów Konstruktor lub właściwości można ustawić w `EventGridTrigger` atrybutu.

|Właściwość Function.JSON |Opis|
|---------|---------|
| **type** | Wymagana — musi być równa `eventGridTrigger`. |
| **direction** | Wymagana — musi być równa `in`. |
| **Nazwa** | Wymagana — nazwa zmiennej, używane w kodzie funkcji parametr, który odbiera dane zdarzenia. |

## <a name="usage"></a>Sposób użycia

Aby uzyskać C# i F# funkcji na platformie Azure funkcji 1.x, można użyć następujących typów parametru wyzwalacza usługi Event Grid:

* `JObject`
* `string`

Aby uzyskać C# i F# funkcji w usłudze Azure Functions 2.x, istnieje również możliwość użycia następującego typu parametru dla wyzwalacza usługi Event Grid:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Definiuje właściwości pól, które są wspólne dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji wersji 1, jeśli zostanie podjęta próba powiązać `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilator będzie wyświetlać komunikat "przestarzałe" i zaleca się używać `Microsoft.Azure.EventGrid.Models.EventGridEvent` zamiast tego. Aby użyć nowszego typu, należy odwołać [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet pakietu i pełnej kwalifikacji `EventGridEvent` nazwy typu, dodając ją za pomocą `Microsoft.Azure.EventGrid.Models`. Aby dowiedzieć się, jak odwoływać się do pakietów NuGet w funkcji skryptu języka C#, zobacz [pakietów NuGet za pomocą](functions-reference-csharp.md#using-nuget-packages)

Dla funkcji języka JavaScript, parametr o nazwie określonej przez *function.json* `name` właściwość ma odwołanie do obiektu zdarzenia.

## <a name="event-schema"></a>Schemat zdarzeń

Dane dla zdarzeń usługi Event Grid została odebrana jako obiekt JSON w treści żądania HTTP. Za pomocą pliku JSON wygląda podobnie do poniższego przykładu:

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

Przykład pokazany jest tablicą z jednym elementem. Usługa Event Grid zawsze wysyła tablicy i może wysyłać więcej niż jedno zdarzenie w tablicy. Środowisko uruchomieniowe wywołuje funkcję jeden raz dla każdego elementu tablicy.

Właściwości najwyższego poziomu, w tym przypadku dane JSON są takie same, wśród wszystkich typów zdarzeń podczas zawartość `data` właściwości są specyficzne dla poszczególnych typów zdarzeń. To przykład pokazany dla zdarzenia magazynu obiektów blob.

Objaśnienia dotyczące właściwości wspólne i specyficznych dla zdarzeń, zobacz [właściwości zdarzenia](../event-grid/event-schema.md#event-properties) w dokumentacji usługi Event Grid.

`EventGridEvent` Typ definiuje tylko właściwości najwyższego poziomu; `Data` właściwość `JObject`.

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Aby rozpocząć odbieranie żądań HTTP siatki zdarzeń, należy utworzyć subskrypcję usługi Event Grid, który określa adres URL punktu końcowego, który wywołuje funkcję.

### <a name="azure-portal"></a>Azure Portal

Dla funkcji, które tworzysz w witrynie Azure portal przy użyciu wyzwalacza usługi Event Grid, wybierz **subskrypcję usługi Event Grid Dodaj**.

![Tworzenie subskrypcji w portalu](media/functions-bindings-event-grid/portal-sub-create.png)

Po zaznaczeniu tego linku spowoduje otwarcie portalu **Utwórz subskrypcję zdarzeń** wstępnie strony za pomocą adresu URL punktu końcowego.

![Adres URL punktu końcowego wstępnie](media/functions-bindings-event-grid/endpoint-url.png)

Aby uzyskać więcej informacji o sposobie tworzenia subskrypcji przy użyciu witryny Azure portal, zobacz [Tworzenie zdarzenia niestandardowego — witryna Azure portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji usługi Event Grid.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć subskrypcję za pomocą [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), użyj [Tworzenie subskrypcji zdarzeń az eventgrid](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) polecenia.

To polecenie wymaga adresu URL punktu końcowego, który wywołuje funkcję. Wzorzec URL specyficzny dla wersji można znaleźć w poniższym przykładzie:

#### <a name="version-2x-runtime"></a>Środowisko uruchomieniowe 2.x wersji

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Środowisko uruchomieniowe 1.x wersji

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Klucz systemu jest kluczem autoryzacji, który musi być ujęta w adresie URL punktu końcowego dla wyzwalacza usługi Event Grid. Poniższa sekcja wyjaśnia, jak uzyskać klucz systemu.

Oto przykład, która ją subskrybuje konta usługi blob storage (z symbolem zastępczym dla klucza systemu):

#### <a name="version-2x-runtime"></a>Środowisko uruchomieniowe 2.x wersji

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Środowisko uruchomieniowe 1.x wersji

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Aby uzyskać więcej informacji o tym, jak można utworzyć subskrypcji, zobacz [Szybki Start z magazynu obiektów blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) lub inne usługi Event Grid Przewodniki Szybki Start.

### <a name="get-the-system-key"></a>Pobierz klucz systemu

Klucz systemu można uzyskać za pomocą następujący interfejs API (HTTP GET):

#### <a name="version-2x-runtime"></a>Środowisko uruchomieniowe 2.x wersji

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Środowisko uruchomieniowe 1.x wersji

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Umożliwia to administrator interfejsu API wymaga, aby Twoja aplikacja funkcji [klucz główny](functions-bindings-http-webhook.md#authorization-keys). Nie należy mylić klucz systemu (w przypadku wywoływania funkcji wyzwalacza usługi Event Grid) za pomocą klucza głównego (w przypadku wykonywania zadań administracyjnych na aplikację funkcji). Podczas subskrybowania tematu usługi Event Grid, pamiętaj użyć klucza systemu.

Poniżej przedstawiono przykład odpowiedzi, który zawiera klucz systemu:

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

Klucz główny można uzyskać dla aplikacji funkcji z **ustawień aplikacji funkcji** karty w portalu.

> [!IMPORTANT]
> Klucz główny zapewnia dostęp administratora do aplikacji funkcji. Nie udostępniać tego klucza osobom trzecim ani rozpowszechniać ją w aplikacji klienta natywnego.

Aby uzyskać więcej informacji, zobacz [autoryzacji klucze](functions-bindings-http-webhook.md#authorization-keys) w artykule odwołanie wyzwalacza HTTP.

Alternatywnie możesz wysłać HTTP PUT do samodzielnie określić wartość tego klucza.

## <a name="local-testing-with-viewer-web-app"></a>Lokalne testowanie za pomocą przeglądarki sieci web aplikacji

Aby przetestować wyzwalacza usługi Event Grid masz lokalnie, pobieranie żądań HTTP siatki zdarzeń dostarczane z ich źródła w chmurze na komputer lokalny. Jest jednym ze sposobów, aby to zrobić przez Przechwytywanie żądań online i ręcznie wysłać je na komputerze lokalnym:

1. [Tworzenie aplikacji sieci web podglądu](#create-a-viewer-web-app) znajdują się komunikaty o zdarzeniach.
1. [Utwórz subskrypcję usługi Event Grid](#create-an-event-grid-subscription) , wysyła zdarzenia do aplikacji przeglądarki.
1. [Generuje żądanie](#generate-a-request) i skopiuj treść żądania z aplikacji przeglądarki.
1. [Ręcznie Opublikuj żądanie](#manually-post-the-request) adres URL localhost usługi Event Grid funkcja wyzwalacza.

Po zakończeniu testowania, skorzystać z tej samej subskrypcji w środowisku produkcyjnym aktualizowanie punktu końcowego. Użyj [aktualizacji subskrypcji zdarzeń eventgrid az](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) polecenia wiersza polecenia platformy Azure.

### <a name="create-a-viewer-web-app"></a>Tworzenie aplikacji sieci web podglądu

Aby uprościć przechwytywania komunikaty o zdarzeniach, można wdrożyć [aplikacji sieci web wstępnie skompilowanych](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Zobaczysz witrynę, w której nie opublikowano jeszcze żadnych zdarzeń.

![Wyświetlanie nowej witryny](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz subskrypcję usługi Event Grid typu, który ma zostać przetestowana i przypisz do niego adres URL z aplikacji sieci web jako punktu końcowego dla powiadomień o zdarzeniach. Punkt końcowy dla aplikacji sieci Web musi zawierać sufiks `/api/updates/`. Dlatego jest pełny adres URL `https://<your-site-name>.azurewebsites.net/api/updates`

Aby uzyskać informacje o sposobie tworzenia subskrypcji przy użyciu witryny Azure portal, zobacz [Tworzenie zdarzenia niestandardowego — witryna Azure portal](../event-grid/custom-event-quickstart-portal.md) w dokumentacji usługi Event Grid.

### <a name="generate-a-request"></a>Generuje żądanie

Wyzwól zdarzenie, który zostanie wygenerowany przez ruch HTTP do punktu końcowego usługi sieci web aplikacji.  Na przykład jeśli utworzono subskrypcję magazynu obiektów blob, przekazywanie lub usuwanie obiektu blob. Gdy żądanie zostaną wyświetlone w aplikacji sieci web, skopiuj treść żądania.

Żądanie sprawdzania poprawności subskrypcji będą odbierane najpierw; Ignoruj wszystkie żądania weryfikacji, a następnie skopiuj żądanie zdarzenia.

![Kopiuj treść żądania z aplikacji sieci web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ręcznie Opublikuj żądanie

Uruchom lokalnie funkcji usługi Event Grid.

Użyj narzędzia takiego jak [Postman](https://www.getpostman.com/) lub [curl](https://curl.haxx.se/docs/httpscripting.html) do utworzenia żądania HTTP POST:

* Ustaw `Content-Type: application/json` nagłówka.
* Ustaw `aeg-event-type: Notification` nagłówka.
* Wklej dane RequestBin do treści żądania.
* Opublikuj adres URL funkcji wyzwalacza usługi Event Grid.
  * Aby uzyskać 2.x używają następującego wzorca:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Do użytku 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName` Parametr musi być nazwa określona w `FunctionName` atrybutu.

Poniższe zrzuty ekranu Pokaż nagłówki i treść w narzędziu Postman żądania:

![Nagłówki w narzędziu Postman](media/functions-bindings-event-grid/postman2.png)

![Treść żądania w narzędziu Postman](media/functions-bindings-event-grid/postman.png)

Funkcja wyzwalacza usługi Event Grid wykonuje i dzienniki zostaną wyświetlone podobne do poniższego przykładu:

![Dzienniki funkcji wyzwalacza usługi Event Grid próbki](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Lokalne testowanie za pomocą ngrok

Innym sposobem przetestowania wyzwalacza usługi Event Grid lokalnie jest zautomatyzować połączenia HTTP między Internetem a komputer deweloperski. Możesz to zrobić za pomocą narzędzia typu open source o nazwie [ngrok](https://ngrok.com/):

1. [Tworzenie punktu końcowego ngrok](#create-an-ngrok-endpoint).
1. [Uruchamianie funkcji wyzwalacza usługi Event Grid](#run-the-event-grid-trigger-function).
1. [Utwórz subskrypcję usługi Event Grid](#create-a-subscription) , wysyła zdarzenia do ngrok endpoint.
1. [Wyzwól zdarzenie](#trigger-an-event).

Po zakończeniu testowania, skorzystać z tej samej subskrypcji w środowisku produkcyjnym aktualizowanie punktu końcowego. Użyj [aktualizacji subskrypcji zdarzeń eventgrid az](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) polecenia wiersza polecenia platformy Azure.

### <a name="create-an-ngrok-endpoint"></a>Tworzenie punktu końcowego ngrok

Pobierz *ngrok.exe* z [ngrok](https://ngrok.com/)i uruchom za pomocą następującego polecenia:

```
ngrok http -host-header=localhost 7071
```

Host-parametr Nagłówek jest niezbędne, ponieważ środowisko uruchomieniowe usługi functions oczekuje żądania od hosta lokalnego, jeśli działa na hoście lokalnym. 7071 jest domyślny numer portu, gdy środowisko uruchomieniowe działa lokalnie.

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

Użyjesz `https://{subdomain}.ngrok.io` adres URL dla Twojej subskrypcji usługi Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Uruchamianie funkcji wyzwalacza usługi Event Grid

Adres URL ngrok nie uzyskuje specjalnej obsługi przez usługę Event Grid, dzięki funkcji musi być uruchomiony lokalnie, po utworzeniu subskrypcji. Jeśli nie, nie są wysyłane odpowiedzi weryfikacji i utworzenie subskrypcji nie powiedzie się.

### <a name="create-a-subscription"></a>Tworzenie subskrypcji

Utwórz subskrypcję usługi Event Grid typu, który ma zostać przetestowana i przypisz do niego ngrok punktu końcowego.

Użyj tego wzorca punktu końcowego dla funkcji 2.x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Użyj tego wzorca punktu końcowego dla funkcji 1.x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

`{FUNCTION_NAME}` Parametr musi być nazwa określona w `FunctionName` atrybutu.

Oto przykład korzystający z wiersza polecenia platformy Azure:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Aby uzyskać informacje o tym, jak można utworzyć subskrypcji, zobacz [Utwórz subskrypcję](#create-a-subscription) we wcześniejszej części tego artykułu.

### <a name="trigger-an-event"></a>Wyzwalanie zdarzenia

Wyzwól zdarzenie, który zostanie wygenerowany przez ruch HTTP do punktu końcowego usługi ngrok.  Na przykład jeśli utworzono subskrypcję magazynu obiektów blob, przekazywanie lub usuwanie obiektu blob.

Funkcja wyzwalacza usługi Event Grid wykonuje i dzienniki zostaną wyświetlone podobne do poniższego przykładu:

![Dzienniki funkcji wyzwalacza usługi Event Grid próbki](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Użyj wyzwalacza HTTP jako wyzwalacz usługi Event Grid

Zdarzeń usługi Event Grid są odbierane na żądania HTTP, aby można było obsłużyć zdarzenia przy użyciu wyzwalacza HTTP zamiast wyzwalacza usługi Event Grid. Jedną z możliwych przyczyn do wykonywania, który ma uzyskać większą kontrolę nad adres URL punktu końcowego, który wywołuje funkcję. Inną przyczyną jest zaspokajanie odebrane zdarzenia w [schematu CloudEvents](../event-grid/cloudevents-schema.md). Obecnie usługa wyzwalacza usługi Event Grid nie obsługuje schematu CloudEvents. W przykładach w tej sekcji przedstawiono rozwiązania dla usługi Event Grid schematu i schematu CloudEvents.

Jeśli używasz wyzwalacza HTTP, trzeba napisać kod dla wyzwalacza usługi Event Grid działanie automatyczne:

* Wysyła odpowiedź do sprawdzania poprawności [żądanie weryfikacji subskrypcji](../event-grid/security-authentication.md#webhook-event-delivery).
* Wywołuje funkcję raz na element tablicy zdarzeń znajdujących się w treści żądania.

Aby uzyskać informacje o adresie URL na potrzeby wywoływania funkcji lokalnie lub po uruchomieniu na platformie Azure, zobacz [dokumentację referencyjną powiązania wyzwalacz HTTP](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Schemat usługi Event Grid

Następujący przykładowy kod C# dla wyzwalacza HTTP symuluje zachowanie wyzwalacza usługi Event Grid. Zdarzenia dostarczone w zdarzeniu siatka schematu można użyć w tym przykładzie.

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

Następujący przykładowy kod JavaScript do wyzwalacza HTTP symuluje zachowanie wyzwalacza usługi Event Grid. Zdarzenia dostarczone w zdarzeniu siatka schematu można użyć w tym przykładzie.

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

Kod obsługi zdarzeń przechodzi wewnątrz pętli za pośrednictwem `messages` tablicy.

### <a name="cloudevents-schema"></a>Schematu CloudEvents

Następujący przykładowy kod C# dla wyzwalacza HTTP symuluje zachowanie wyzwalacza usługi Event Grid.  Zdarzenia dostarczone w schematu CloudEvents można użyć w tym przykładzie.

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

Następujący przykładowy kod JavaScript do wyzwalacza HTTP symuluje zachowanie wyzwalacza usługi Event Grid. Zdarzenia dostarczone w schematu CloudEvents można użyć w tym przykładzie.

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Event Grid](../event-grid/overview.md)
