---
title: Funkcje HTTP w Durable Functions-Azure Functions
description: Dowiedz się więcej na temat zintegrowanych funkcji protokołu HTTP w rozszerzeniu Durable Functions Azure Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935796"
---
# <a name="http-features"></a>Funkcje HTTP

Durable Functions ma kilka funkcji, które ułatwiają dołączanie trwałych aranżacji i jednostek do przepływów pracy protokołu HTTP. W tym artykule przedstawiono szczegółowe informacje o niektórych z tych funkcji.

## <a name="exposing-http-apis"></a>Uwidacznianie interfejsów API protokołu HTTP

Aranżacje i jednostki mogą być wywoływane i zarządzane przy użyciu żądań HTTP. Rozszerzenie Durable Functions udostępnia wbudowane interfejsy API protokołu HTTP, a także udostępnia interfejsy API do współdziałania z aranżacjami i jednostkami z poziomu funkcji wyzwalanych przez protokół HTTP.

### <a name="built-in-http-apis"></a>Wbudowane interfejsy API protokołu HTTP

Rozszerzenie Durable Functions automatycznie dodaje zestaw interfejsów API protokołu HTTP do hosta Azure Functions. Te interfejsy API umożliwiają współdziałanie z aranżacjami i jednostkami oraz zarządzanie nimi bez konieczności pisania kodu.

Obsługiwane są następujące wbudowane interfejsy API protokołu HTTP.

* [Rozpocznij nową aranżację](durable-functions-http-api.md#start-orchestration)
* [Wystąpienie aranżacji zapytania](durable-functions-http-api.md#get-instance-status)
* [Przerwij wystąpienie aranżacji](durable-functions-http-api.md#terminate-instance)
* [Wyślij zdarzenie zewnętrzne do aranżacji](durable-functions-http-api.md#raise-event)
* [Przeczyść historię aranżacji](durable-functions-http-api.md#purge-single-instance-history)
* [Wyślij zdarzenie operacji do jednostki](durable-functions-http-api.md#signal-entity)
* [Zapytanie o stan jednostki](durable-functions-http-api.md#query-entity)

Zobacz artykuł dotyczący [interfejsów API protokołu HTTP](durable-functions-http-api.md) , aby uzyskać pełny opis wszystkich wbudowanych interfejsów API protokołu HTTP uwidocznionych przez rozszerzenie Durable Functions.

### <a name="http-api-url-discovery"></a>Odnajdowanie adresów URL interfejsu API protokołu HTTP

[Powiązanie klienta aranżacji](durable-functions-bindings.md#orchestration-client) udostępnia interfejsy API, których można użyć do generowania wygodnych ładunków odpowiedzi HTTP. Na przykład może utworzyć odpowiedź zawierającą linki do interfejsów API zarządzania dla określonego wystąpienia aranżacji. Oto przykład funkcji wyzwalacza HTTP, która demonstruje, jak używać tego interfejsu API dla nowego wystąpienia aranżacji:

#### <a name="precompiled-c"></a>PrekompilowanegoC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C#Napisy

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Uruchamianie funkcji programu Orchestrator za pomocą funkcji wyzwalacza HTTP pokazanych powyżej można wykonać przy użyciu dowolnego klienta HTTP. Następujące polecenie zwinięcie uruchamia funkcję programu Orchestrator o nazwie `DoWork`.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Oto przykładowa odpowiedź dla aranżacji wraz z `abc123` identyfikatorem (niektóre szczegóły zostały usunięte w celu przejrzystości):

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Każde z `~Uri` pól w poprzednim przykładzie odpowiada wbudowanym interfejsom API protokołu HTTP. Te interfejsy API mogą służyć do zarządzania docelowym wystąpieniem aranżacji.

> [!NOTE]
> Format adresów URL elementu webhook może się różnić w zależności od używanej wersji hosta Azure Functions. Powyższy przykład dotyczy hosta Azure Functions 2,0.

Opis wszystkich wbudowanych interfejsów API protokołu HTTP można znaleźć w dokumentacji [dotyczącej interfejsu API protokołu HTTP](durable-functions-http-api.md) .

### <a name="async-operation-tracking"></a>Śledzenie operacji asynchronicznych

Wspomniana wcześniej odpowiedź HTTP została zaprojektowana w celu ułatwienia wdrożenia długotrwałych asynchronicznych interfejsów API protokołu HTTP przy użyciu Durable Functions. Ten wzorzec jest czasami nazywany *wzorcem klienta sondowania*. Przepływ klient/serwer działa w następujący sposób:

1. Klient wysyła żądanie HTTP w celu uruchomienia długotrwałego procesu, takiego jak funkcja programu Orchestrator.
2. Docelowy wyzwalacz http zwraca odpowiedź HTTP 202 z `Location` nagłówkiem `statusQueryGetUri` o wartości.
3. Klient sonduje adres URL w `Location` nagłówku. Nadal widzisz odpowiedzi HTTP 202 z `Location` nagłówkiem.
4. Po zakończeniu wystąpienia (lub niepowodzenia) punkt końcowy w `Location` nagłówku zwraca http 200.

Ten protokół umożliwia koordynowanie długotrwałych procesów z zewnętrznymi klientami lub usługami, które obsługują sondowanie punktu końcowego http i po `Location` nagłówku. Zarówno implementacja klienta, jak i serwera tego wzorca jest wbudowana w Durable Functions interfejsów API protokołu HTTP.

> [!NOTE]
> Domyślnie wszystkie działania oparte na protokole HTTP zapewniane przez [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) obsługują standardowy wzorzec operacji asynchronicznej. Ta funkcja umożliwia osadzenie długotrwałej funkcji trwałej w ramach przepływu pracy Logic Apps. Więcej szczegółów na Logic Apps temat obsługi asynchronicznych wzorców HTTP można znaleźć w temacie [Azure Logic Apps akcje przepływu pracy i informacje o wyzwalaczach](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Korzystanie z aranżacji może odbywać się z dowolnego typu funkcji, a nie tylko funkcji wyzwalanych przez protokół HTTP.

Aby uzyskać więcej informacji na temat zarządzania aranżacjami i jednostkami przy użyciu interfejsów API klienta, zobacz artykuł dotyczący [zarządzania wystąpieniem](durable-functions-instance-management.md) .

## <a name="consuming-http-apis"></a>Używanie interfejsów API protokołu HTTP

Funkcje programu Orchestrator nie mogą wykonywać operacji we/wy bezpośrednio, zgodnie z opisem w temacie [ograniczenia kodów funkcji](durable-functions-code-constraints.md)w programie Orchestrator. Zamiast tego funkcje programu Orchestrator zazwyczaj wywołują [funkcje działania](durable-functions-types-features-overview.md#activity-functions) , które wykonują operacje we/wy.

Począwszy od Durable Functions 2,0, aranżacje mogą natywnie korzystać z interfejsów API protokołu HTTP przy użyciu [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> Możliwość wywołania punktów końcowych HTTP bezpośrednio z funkcji programu Orchestrator nie jest jeszcze dostępna w języku JavaScript.

Poniższy przykładowy kod przedstawia funkcję programu C# Orchestrator wykonującą wychodzące żądanie HTTP przy `CallHttpAsync` użyciu interfejsu API platformy .NET:

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

Akcja "Call HTTP" umożliwia wykonywanie następujących czynności w funkcjach programu Orchestrator:

* Wywołaj interfejsy API protokołu HTTP bezpośrednio z funkcji aranżacji (z pewnymi ograniczeniami wymienionymi w dalszej części).
* Automatyczne obsługiwanie wzorców sondowania stanu HTTP 202 po stronie klienta.
* Użyj [tożsamości zarządzanych przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać autoryzowane wywołania http do innych punktów końcowych platformy Azure.

Możliwość korzystania z interfejsów API protokołu HTTP bezpośrednio z funkcji programu Orchestrator jest zaprojektowana jako wygoda dla pewnego zestawu typowych scenariuszy. Możliwe jest zaimplementowanie wszystkich tych funkcji samodzielnie za pomocą funkcji działania. W wielu przypadkach funkcje działania mogą zapewnić większą elastyczność.

### <a name="http-202-handling"></a>Obsługa protokołu HTTP 202

Interfejs API "Call HTTP" może automatycznie implementować stronę klienta *wzorca sondowania*. Jeśli interfejs API o nazwie zwróci odpowiedź HTTP 202 z `Location` nagłówkiem, funkcja programu Orchestrator automatycznie `Location` sonduje zasób do momentu przywrócenia odpowiedzi z powrotem do 202. Odpowiedź w postaci innej niż 202 będzie odpowiedzią zwracaną do kodu funkcji programu Orchestrator.

> [!NOTE]
> Program Orchestrator Functions również natywnie obsługuje *wzorzec klienta sondowania*po stronie serwera, zgodnie z opisem w temacie [śledzenie operacji asynchronicznych](#async-operation-tracking). Oznacza to, że aranżacje w jednej aplikacji funkcji mogą łatwo koordynować funkcje programu Orchestrator w innych aplikacjach funkcji. Jest to podobne do koncepcji [aranżacji podrzędnej](durable-functions-sub-orchestrations.md) , ale z obsługą komunikacji między aplikacjami. Jest to szczególnie przydatne w przypadku tworzenia aplikacji w stylu mikrousług.

### <a name="managed-identities"></a>Tożsamości zarządzane

Durable Functions natywnie obsługuje wywoływanie interfejsów API, które akceptują tokeny usługi Azure AD na potrzeby autoryzacji. Ta obsługa używa [tożsamości zarządzanych przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md) do uzyskiwania tych tokenów.

Poniższy kod stanowi przykład funkcji programu .NET Orchestrator, która wykonuje uwierzytelnione wywołania do ponownego uruchomienia maszyny wirtualnej przy użyciu [interfejsu API REST Virtual Machines](https://docs.microsoft.com/rest/api/compute/virtualmachines)Azure Resource Manager.

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

W poprzednim przykładzie `tokenSource` parametr jest skonfigurowany do uzyskiwania tokenów usługi Azure AD dla [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (identyfikowanych przez "identyfikator URI zasobu" `https://management.core.windows.net`). W przykładzie przyjęto założenie, że bieżąca aplikacja funkcji działa lokalnie lub została wdrożona jako aplikacja Azure Functionsa z tożsamością zarządzaną. Przyjęto założenie, `myRG`że lokalna tożsamość lub zarządzana tożsamość ma uprawnienia do zarządzania maszynami wirtualnymi w określonej grupie zasobów.

W czasie wykonywania skonfigurowane Źródło tokenu automatycznie zwraca token dostępu OAuth 2,0 i dodaje go jako token okaziciela do `Authorization` nagłówka żądania wychodzącego. Ten model to poprawa ręcznego dodawania nagłówków autoryzacji do żądań HTTP, ponieważ:

* Odświeżanie tokenu jest obsługiwane automatycznie. Nie musisz martwić się o wygasłe tokeny.
* Tokeny nigdy nie są przechowywane w stanie nietrwałej aranżacji.
* Nie trzeba pisać żadnego kodu, aby obsłużyć pozyskiwanie tokenów.

Więcej informacji można znaleźć we [wstępnie skompilowanych C# przykładach "RestartVMs"](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Zarządzane tożsamości nie są ograniczone do zarządzania zasobami platformy Azure. Tożsamości zarządzane mogą służyć do uzyskiwania dostępu do dowolnych interfejsów API, które akceptują tokeny okaziciela usługi Azure AD, w tym usługi platformy Azure w pierwszej kolejności lub aplikacje sieci Web innych firm. Aplikacja sieci Web innej firmy może nawet być inną aplikacją funkcji. Aby uzyskać listę usług platformy Azure, które obsługują uwierzytelnianie w usłudze Azure AD, zobacz [usługi platformy Azure, które obsługują uwierzytelnianie w usłudze Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Ograniczenia

Wbudowana obsługa wywoływania interfejsów API protokołu HTTP jest wygodną funkcją i nie jest odpowiednia dla wszystkich scenariuszy. Żądania HTTP wysyłane przez funkcje programu Orchestrator i ich odpowiedzi są serializowane i utrwalane jako komunikaty w kolejce. To zachowanie w kolejce zapewnia niezawodne i bezpieczne wywoływanie połączeń HTTP [w celu aranżacji](durable-functions-orchestrations.md#reliability). Jednak to zachowanie kolejkowania oznacza również, że:

* Każde żądanie HTTP wymaga dodatkowego opóźnienia w porównaniu do macierzystego klienta HTTP.
* Duże komunikaty żądania lub odpowiedzi, które nie pasują do komunikatu kolejki, mogą znacząco obniżyć wydajność aranżacji. Potencjalne obniżenie wydajności jest spowodowane obciążeniem odciążania ładunków komunikatów do magazynu obiektów BLOB.
* Przesyłanie strumieniowe, fragmenty i ładunki binarne nie są obsługiwane.
* Możliwość dostosowywania zachowania klienta HTTP jest ograniczona.

Jeśli którykolwiek z tych ograniczeń może mieć wpływ na przypadek użycia, rozważ użycie funkcji działania i bibliotek klienckich protokołu HTTP specyficznych dla języka, aby wychodzące wywołania HTTP.

> [!NOTE]
> Jeśli jesteś deweloperem platformy .NET, możesz zastanawiać się, dlaczego ta funkcja `DurableHttpRequest` używa `DurableHttpResponse` i typ zamiast wbudowanego oprogramowania .NET `HttpRequestMessage` i `HttpResponseMessage`. Ten wybór projektu zamierzone. Główną przyczyną jest to, że typy niestandardowe pomagają zapewnić, że użytkownicy nie będą wprowadzać nieprawidłowych założeń dotyczących obsługiwanych zachowań wewnętrznego klienta HTTP. Typy o trwałej charakterystyce umożliwiają również uproszczenie projektowania interfejsu API i łatwiejszym w użyciu funkcji specjalnych, takich jak [integracja tożsamości zarządzanej](#managed-identities) i wzorzec dla [odbiorców sondowania](#http-202-handling).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o jednostkach trwałych](durable-functions-entities.md)