---
title: Wyzwalacz siatki zdarzeń dla usługi Azure Functions
description: Zrozumienie sposobu obsługi zdarzenia siatki zdarzeń w usługi Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 7e0fb3cee8d4ec72e1ec44f7444264fabb1dd202
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724734"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Wyzwalacz siatki zdarzeń dla usługi Azure Functions

W tym artykule opisano sposób obsługi [siatki zdarzeń](../event-grid/overview.md) zdarzenia w funkcji platformy Azure.

Siatka zdarzeń jest usługą platformy Azure, która wysyła żądania HTTP do powiadamiania o zdarzeniach, które pojawiają się w *wydawców*. Wydawca jest usługa lub zasób, którego źródłem zdarzenia. Na przykład konta magazynu obiektów blob platformy Azure jest wydawcą, i [przekazywanie obiektu blob lub usunięciu jest zdarzenie](../storage/blobs/storage-blob-event-overview.md). Niektóre [usług Azure ma wbudowaną obsługę publikowania zdarzeń w siatce zdarzeń](../event-grid/overview.md#event-sources). 

Zdarzenia *obsługi* odbierania i przetwarzania zdarzeń. Azure Functions to jeden z kilku [usług platformy Azure, które mają wbudowaną obsługę Obsługa zdarzeń siatki zdarzeń](../event-grid/overview.md#event-handlers). W tym artykule dowiesz się wywołanie funkcji po odebraniu zdarzenia z siatki zdarzeń przy użyciu wyzwalacza zdarzenia siatki.

Jeśli wolisz, można użyć wyzwalacza HTTP do obsługi zdarzeń siatki zdarzeń; zobacz [użyć wyzwalacza HTTP jako wyzwalacz zdarzenia siatki](#use-an-http-trigger-as-an-event-grid-trigger) dalszej części tego artykułu.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Wyzwalacz zdarzenia siatki znajduje się w [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pakietu NuGet, wersja 1.x. Kod źródłowy dla pakietu jest w [eventgrid rozszerzenie, azure funkcji w-](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Wyzwalacz zdarzenia siatki znajduje się w [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu jest w [eventgrid rozszerzenie, azure funkcji w-](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka wyzwalacz siatki zdarzeń:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [JavaScript](#javascript-example)

Na przykład wyzwalacza HTTP, zobacz [sposób użycia wyzwalacza HTTP](#use-an-http-trigger-as-an-event-grid-trigger) dalszej części tego artykułu.

### <a name="c-example"></a>Przykład C#

W poniższym przykładzie pokazano funkcje 1.x [C# funkcja](functions-dotnet-class-library.md) który wiąże `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

W poniższym przykładzie pokazano funkcje 2.x [C# funkcja](functions-dotnet-class-library.md) który wiąże `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.Data.ToString());
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [pakiety](#packages), [atrybuty](#attributes), [konfiguracji](#configuration), i [użycia](#usage).

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono powiązanie wyzwalacza w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod skryptu 1.x C# funkcji, która jest powiązana z `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Oto kod skryptu 2.x C# funkcji, która jest powiązana z `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.Data.ToString());
}
```

Aby uzyskać więcej informacji, zobacz [pakiety](#packages), [atrybuty](#attributes), [konfiguracji](#configuration), i [użycia](#usage).

### <a name="javascript-example"></a>Przykład JavaScript

W poniższym przykładzie przedstawiono powiązanie wyzwalacza w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania.

W tym miejscu jest powiązanie danych *function.json* pliku:

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
     
## <a name="attributes"></a>Atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) atrybutu.

Oto `EventGridTrigger` atrybutu w podpisie metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Pełny przykład, zobacz [przykład C#](#c-example).

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku. Nie ma żadnych parametrów konstruktora lub właściwości można ustawić w `EventGridTrigger` atrybutu.

|Właściwość Function.JSON |Opis|
|---------|---------|----------------------|
| **type** | Wymagana — musi być ustawiona `eventGridTrigger`. |
| **direction** | Wymagana — musi być ustawiona `in`. |
| **Nazwa** | Wymagana — nazwa zmiennej używane w kodzie funkcji dla parametru, który odbiera dane zdarzenia. |

## <a name="usage"></a>Sposób użycia

Dla funkcji języka C# i F # na platformie Azure funkcji 1.x, możesz użyć następujących typów parametru wyzwalacza siatki zdarzeń:

* `JObject`
* `string`

Dla języka C# i F # funkcji usługi Azure Functions 2.x, masz również możliwość użycia następującego typu parametru dla wyzwalacza siatki zdarzeń:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Definiuje właściwości pól, które są wspólne dla wszystkich typów zdarzeń.

> [!NOTE]
> W funkcji v1 próba powiązania `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilator zostanie wyświetlony komunikat "przestarzałe" i zaleca się użyć `Microsoft.Azure.EventGrid.Models.EventGridEvent` zamiast tego. Odwoływać się do użycia nowszego typu, [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet pakietu i pełnej kwalifikacji `EventGridEvent` nazwę typu na podstawie prefiksu z `Microsoft.Azure.EventGrid.Models`. Aby uzyskać informacje o sposobie odwołania się do pakietów NuGet w funkcji skryptu C#, zobacz [pakietów za pomocą NuGet](functions-reference-csharp.md#using-nuget-packages)

Dla funkcji JavaScript, parametr o nazwie *function.json* `name` właściwość zawiera odwołanie do obiektu zdarzenia.

## <a name="event-schema"></a>Schemat zdarzeń

Dane dla zdarzenia siatki zdarzeń odebranych jako obiekt JSON w treści żądania HTTP. Kod JSON wygląda podobnie do poniższego przykładu:

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

Przykład pokazany jest tablicą jednego elementu. Siatka zdarzeń zawsze wysyła tablicy i nie może wysłać więcej niż jedno zdarzenie w tablicy. Środowisko uruchomieniowe wywołuje funkcji raz dla każdego elementu tablicy.

Właściwości najwyższego poziomu w przypadku danych JSON są takie same, wśród wszystkich typów zdarzeń podczas zawartość `data` właściwości są specyficzne dla poszczególnych typów zdarzeń. Przykład pokazany jest zdarzenie magazynu obiektów blob.

Objaśnienia dotyczące zdarzeń i wspólnych właściwości, zobacz [właściwości zdarzenia](../event-grid/event-schema.md#event-properties) w dokumentacji zdarzeń siatki.

`EventGridEvent` Typ definiuje najwyższego poziomu właściwości; `Data` jest właściwość `JObject`. 

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Aby rozpocząć odbieranie żądań HTTP siatki zdarzeń, Utwórz subskrypcję siatki zdarzeń, który określa adres URL punktu końcowego, który wywołuje funkcję.

### <a name="azure-portal"></a>Azure Portal

Do funkcji tworzonych w portalu Azure z wyzwalaczem siatki zdarzenia, wybierz **subskrypcji dodać siatki zdarzeń**.

![Utwórz subskrypcję w portalu](media/functions-bindings-event-grid/portal-sub-create.png)

Po wybraniu to łącze powoduje otwarcie portalu **Utwórz subskrypcję zdarzeń** wstępnie strony z adresu URL punktu końcowego.

![Adres URL punktu końcowego wstępnie](media/functions-bindings-event-grid/endpoint-url.png)

Aby uzyskać więcej informacji o sposobie tworzenia subskrypcji przy użyciu portalu Azure, zobacz [Tworzenie zdarzenia niestandardowe - portalu Azure](../event-grid/custom-event-quickstart-portal.md) w dokumentacji zdarzeń siatki.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć subskrypcję za pomocą [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), użyj [tworzenia subskrypcji zdarzeń az eventgrid](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) polecenia.

Polecenie wymaga adresu URL punktu końcowego, który wywołuje funkcję. W poniższym przykładzie przedstawiono wzorca adresu URL:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Klucz systemu jest kluczem autoryzacji, który musi zostać uwzględniony w adresie URL punktu końcowego dla wyzwalacza zdarzenia siatki. Poniższa sekcja zawiera opis pobrać klucz systemu.

Oto przykład, w którym subskrybuje konto magazynu obiektów blob (z symbolem zastępczym dla klucza systemu):

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Aby uzyskać więcej informacji o sposobie tworzenia subskrypcji, zobacz [Szybki Start magazynu obiektów blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) lub innych quickstarts zdarzeń siatki.

### <a name="get-the-system-key"></a>Pobierz klucz systemowy

Klucz systemu można uzyskać za pomocą następujących interfejsu API (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

To jest administratorem interfejsu API, dzięki czemu wymaga programu [klucz administratora](functions-bindings-http-webhook.md#authorization-keys). Nie należy mylić klucz systemowy (do wywoływania funkcji wyzwalacza zdarzenia siatki) przy użyciu klucza administratora (do wykonywania zadań administracyjnych w aplikacji funkcji). Po zasubskrybowaniu tematu siatki zdarzeń, należy użyć klucza systemu.

Oto przykład odpowiedzi, który zawiera klucz systemowy:

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

Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook.md#authorization-keys) w artykule odwołanie wyzwalacza HTTP. 

Alternatywnie możesz wysłać HTTP PUT do samodzielnie określić wartość tego klucza.

## <a name="local-testing-with-requestbin"></a>Testowanie za pomocą RequestBin lokalnego

> [!NOTE]
> RequestBin lokacji nie jest obecnie dostępna, ale można użyć tej metody z https://hookbin.com zamiast tego. Jeśli w tej lokacji jest wyłączony, możesz użyć [ngrok](#local-testing-with-ngrok).

Aby przetestować wyzwalacz zdarzenia siatki masz lokalnie, pobrać żądań HTTP siatki zdarzenia dostarczone z ich pochodzenia w chmurze na komputerze lokalnym. Jest jednym ze sposobów to zrobić przez Przechwytywanie żądań online i ręcznie wysłać je na komputerze lokalnym:

2. [Tworzenie punktu końcowego RequestBin](#create-a-RequestBin-endpoint).
3. [Tworzenie subskrypcji zdarzeń siatki](#create-an-event-grid-subscription) która wysyła zdarzenia do punktu końcowego RequestBin.
4. [Generuje żądanie](#generate-a-request) i skopiuj z witryny RequestBin treści żądania.
5. [Ręcznie Opublikuj żądania](#manually-post-the-request) URL localhost siatka zdarzeń funkcja wyzwalacza.

Po zakończeniu testowania, możesz użyć tej samej subskrypcji w środowisku produkcyjnym wg aktualizowanie punktu końcowego. Użyj [az eventgrid subskrypcji zdarzeń aktualizacji](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) polecenia wiersza polecenia platformy Azure.

### <a name="create-a-requestbin-endpoint"></a>Tworzenie punktu końcowego RequestBin

RequestBin jest narzędziem open source, które akceptuje żądania HTTP i pokazuje treści żądania. http://requestb.in Szczególnego traktowania siatki zdarzeń platformy Azure pobiera adres URL. Aby ułatwić testowanie, siatki zdarzeń wysyła zdarzenia do adresu URL RequestBin bez konieczności poprawne odpowiedzi na żądania weryfikacji subskrypcji. Jedno narzędzie testowania otrzymuje takie samo traktowanie: http://hookbin.com.

RequestBin nie jest przeznaczony do użycia z wysokiej przepływności. W przypadku wypchnięcia więcej niż jednego zdarzenia w tym samym czasie w narzędziu mogą nie być widoczne wszystkie zdarzenia.

Tworzenie punktu końcowego.

![Tworzenie punktu końcowego RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Skopiuj adres URL punktu końcowego.

![Skopiuj RequestBin punktu końcowego](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji zdarzeń siatki

Utwórz subskrypcję siatki zdarzeń typu, który ma zostać przetestowana i nadaj mu RequestBin punktu końcowego. Aby uzyskać informacje o sposobie tworzenia subskrypcji, zobacz [Utwórz subskrypcję](#create-a-subscription) we wcześniejszej części tego artykułu.

### <a name="generate-a-request"></a>Generuje żądanie

Wywołanie zdarzenia, który zostanie wygenerowany przez ruch HTTP do punktu końcowego RequestBin.  Na przykład jeśli została utworzona subskrypcja magazynu obiektów blob, przekazać lub usunąć obiektu blob. Gdy żądanie zostaną wyświetlone na stronie RequestBin, skopiuj treści żądania.

Żądanie sprawdzania poprawności subskrypcji będą odbierane najpierw; Ignoruj wszystkie żądania weryfikacji i skopiuj żądania zdarzenia.

![Skopiuj treści żądania z RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Ręcznie Opublikuj żądania

Uruchom lokalnie funkcji zdarzeń siatki.

Za pomocą narzędzia, takie jak [Postman](https://www.getpostman.com/) lub [curl](https://curl.haxx.se/docs/httpscripting.html) do utworzenia żądania HTTP POST:

* Ustaw `Content-Type: application/json` nagłówka.
* Ustaw `aeg-event-type: Notification` nagłówka.
* Wklejanie danych RequestBin do treści żądania. 
* POST na adres URL funkcji wyzwalacza zdarzenia siatki, przy użyciu następującego wzorca:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` Parametr musi być nazwa określona w `FunctionName` atrybutu.

Poniższe zrzuty ekranu pokazać nagłówki i treści w Postman żądania:

![Nagłówki w Postman](media/functions-bindings-event-grid/postman2.png)

![Treść żądania w Postman](media/functions-bindings-event-grid/postman.png)

Funkcja wyzwalacza zdarzenia siatki wykonuje i przedstawia dzienniki podobny do poniższego przykładu:

![Dzienniki funkcji wyzwalacza zdarzenia siatki próbki](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Testowanie za pomocą ngrok lokalnego

Innym sposobem sprawdzenia lokalnie wyzwalacz zdarzenia siatki jest można zautomatyzować połączenia HTTP od Internetu, a komputer deweloperski. Możesz to zrobić za pomocą narzędzia open source o nazwie [ngrok](https://ngrok.com/):

3. [Tworzenie punktu końcowego ngrok](#create-an-ngrok-endpoint).
4. [Uruchamianie funkcji wyzwalacza zdarzenia siatki](#run-the-event-grid-trigger-function).
5. [Tworzenie subskrypcji zdarzeń siatki](#create-a-subscription) która wysyła zdarzenia do punktu końcowego ngrok.
6. [Wyzwolenie zdarzenia](#trigger-an-event).

Po zakończeniu testowania, możesz użyć tej samej subskrypcji w środowisku produkcyjnym wg aktualizowanie punktu końcowego. Użyj [az eventgrid subskrypcji zdarzeń aktualizacji](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) polecenia wiersza polecenia platformy Azure.

### <a name="create-an-ngrok-endpoint"></a>Tworzenie punktu końcowego ngrok

Pobierz *ngrok.exe* z [ngrok](https://ngrok.com/)i uruchom przy użyciu następującego polecenia:

```
ngrok http -host-header=localhost 7071
```

Host-parametr nagłówka jest potrzebna, ponieważ środowisko uruchomieniowe functions oczekuje żądania od hosta lokalnego po uruchomieniu na hoście lokalnym. 7071 jest domyślny numer portu, gdy środowisko uruchomieniowe działa lokalnie.

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

Adres URL https://{subdomain}.ngrok.io będzie używany dla Twojej subskrypcji zdarzeń siatki.

### <a name="run-the-event-grid-trigger-function"></a>Uruchamianie funkcji wyzwalacza zdarzenia siatki

Adres URL ngrok nie poznasz specjalnej obsługi siatki zdarzeń, dzięki czemu funkcji musi być uruchomiona lokalnie, podczas tworzenia subskrypcji. Jeśli nie, nie jest wysyłana w odpowiedzi weryfikacji i utworzenie subskrypcji nie powiedzie się.

### <a name="create-a-subscription"></a>Tworzenie subskrypcji

Utwórz subskrypcję siatki zdarzeń typu, który ma zostać przetestowana i nadaj mu ngrok punktu końcowego, przy użyciu następującego wzorca:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` Parametr musi być nazwa określona w `FunctionName` atrybutu.

Oto przykład przy użyciu wiersza polecenia platformy Azure:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Aby uzyskać informacje o sposobie tworzenia subskrypcji, zobacz [Utwórz subskrypcję](#create-a-subscription) we wcześniejszej części tego artykułu.

### <a name="trigger-an-event"></a>Wyzwalanie zdarzenia

Wywołanie zdarzenia, który zostanie wygenerowany przez ruch HTTP do punktu końcowego ngrok.  Na przykład jeśli została utworzona subskrypcja magazynu obiektów blob, przekazać lub usunąć obiektu blob.

Funkcja wyzwalacza zdarzenia siatki wykonuje i przedstawia dzienniki podobny do poniższego przykładu:

![Dzienniki funkcji wyzwalacza zdarzenia siatki próbki](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Użyj wyzwalacza HTTP jako wyzwalacz zdarzenia siatki

Zdarzenie siatki zdarzenia są odbierane jako żądania HTTP, więc może obsługiwać zdarzeń przy użyciu wyzwalacza HTTP zamiast wyzwalacz zdarzenia siatki. Jedną z możliwych przyczyn operacją jest aby uzyskać większą kontrolę nad adres URL punktu końcowego, który wywołuje funkcję. 

Użycie wyzwalacza HTTP, należy napisać kod dla wyzwalacza zdarzenia siatki czego automatycznie:

* Wysyła odpowiedź weryfikacji [żądanie weryfikacji subskrypcji](../event-grid/security-authentication.md#webhook-event-delivery).
* Wywołuje funkcję raz dla każdego elementu tablicy zdarzeń zawartych w treści żądania.

Następujący kod C# dla wyzwalacza HTTP symuluje zachowanie wyzwalacza siatki zdarzeń:

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
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
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Następujący przykładowy kod JavaScript dla wyzwalacza HTTP symuluje zachowanie wyzwalacza siatki zdarzeń:

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: JSON.stringify({validationResponse: messages[0].data.validationCode}) }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
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

Kod obsługi zdarzeń przejdzie do wewnątrz pętli za pośrednictwem `messages` tablicy.

Aby uzyskać informacje o adresie URL do użycia podczas wywoływania funkcji lokalnie lub po uruchomieniu na platformie Azure, zobacz [dokumentację referencyjną powiązanie wyzwalacza HTTP](functions-bindings-http-webhook.md) 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zdarzeń siatki](../event-grid/overview.md)
