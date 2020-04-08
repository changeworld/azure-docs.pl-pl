---
title: Funkcje HTTP w funkcjach trwałych — funkcje platformy Azure
description: Dowiedz się więcej o zintegrowanych funkcjach HTTP w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: fece1155d2f707f11dda9f3896bd8a08deff1557
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802387"
---
# <a name="http-features"></a>Funkcje HTTP

Funkcje trwałe ma kilka funkcji, które ułatwiają włączenie trwałych aranżacji i jednostek do przepływów pracy HTTP. W tym artykule opisano szczegółowo niektóre z tych funkcji.

## <a name="exposing-http-apis"></a>Udostępnianie interfejsów API HTTP

Aranżacji i jednostek można wywoływać i zarządzać przy użyciu żądań HTTP. Rozszerzenie Funkcje trwałe udostępnia wbudowane interfejsy API HTTP. Zapewnia również interfejsy API do interakcji z aranżacji i jednostek z wewnątrz funkcji wyzwalanych przez HTTP.

### <a name="built-in-http-apis"></a>Wbudowane interfejsy API HTTP

Rozszerzenie funkcje trwałe automatycznie dodaje zestaw interfejsów API HTTP do hosta usług Azure Functions. Za pomocą tych interfejsów API można wchodzić w interakcje z aranżacjami i jednostkami i zarządzać nimi bez pisania kodu.

Obsługiwane są następujące wbudowane interfejsy API HTTP.

* [Rozpocznij nową aranżację](durable-functions-http-api.md#start-orchestration)
* [Wystąpienie aranżacji kwerendy](durable-functions-http-api.md#get-instance-status)
* [Zakończenie wystąpienia aranżacji](durable-functions-http-api.md#terminate-instance)
* [Wysyłanie zdarzenia zewnętrznego do aranżacji](durable-functions-http-api.md#raise-event)
* [Historia aranżacji oczyszczania](durable-functions-http-api.md#purge-single-instance-history)
* [Wysyłanie zdarzenia operacji do encji](durable-functions-http-api.md#signal-entity)
* [Uzyskaj stan encji](durable-functions-http-api.md#get-entity)
* [Kwerenda na liście encji](durable-functions-http-api.md#list-entities)

Zobacz [artykuł interfejsów API HTTP,](durable-functions-http-api.md) aby uzyskać pełny opis wszystkich wbudowanych interfejsów API HTTP udostępniane przez rozszerzenie funkcje trwałe.

### <a name="http-api-url-discovery"></a>Odnajdowanie adresu URL interfejsu API HTTP

[Powiązanie klienta aranżacji](durable-functions-bindings.md#orchestration-client) udostępnia interfejsy API, które mogą generować wygodne ładunki odpowiedzi HTTP. Na przykład można utworzyć odpowiedź zawierającą łącza do interfejsów API zarządzania dla określonego wystąpienia aranżacji. Poniższe przykłady pokazują funkcję wyzwalacza HTTP, która pokazuje, jak używać tego interfejsu API dla nowego wystąpienia aranżacji:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**indeks.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**funkcja.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Uruchamianie funkcji koordynatora przy użyciu funkcji wyzwalacza HTTP pokazanych wcześniej można wykonać za pomocą dowolnego klienta HTTP. Następujące polecenie cURL uruchamia funkcję koordynatora o nazwie: `DoWork`

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Dalej jest przykład odpowiedzi dla aranżacji, która ma `abc123` jako swój identyfikator. Niektóre szczegóły zostały usunięte dla jasności.

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

W poprzednim przykładzie każde z `Uri` pól kończących się odpowiada wbudowanemu interfejsowi API HTTP. Za pomocą tych interfejsów API można zarządzać wystąpieniem aranżacji docelowej.

> [!NOTE]
> Format adresów URL elementu webhook zależy od wersji hosta usługi Azure Functions, który jest uruchomiony. Poprzedni przykład dotyczy hosta usługi Azure Functions 2.0.

Opis wszystkich wbudowanych interfejsów API HTTP można znaleźć w [odwołaniu do interfejsu API HTTP](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Śledzenie operacji asynchroniiowych

Odpowiedź HTTP, o których wspomniano wcześniej, ma na celu ułatwienie implementacji długotrwałych interfejsów API asynchronii HTTP z funkcjami trwałymi. Ten wzorzec jest czasami określany jako *wzorzec konsumenta sondowania*. Przepływ klient/serwer działa w następujący sposób:

1. Klient wystawia żądanie HTTP, aby rozpocząć długotrwały proces, taki jak funkcja koordynatora.
1. Docelowy wyzwalacz HTTP zwraca odpowiedź HTTP 202 z nagłówkiem Lokalizacja, który ma wartość "statusQueryGetUri".
1. Klient sonduje adres URL w nagłówku Lokalizacja. Klient kontynuuje wyświetlanie odpowiedzi HTTP 202 z nagłówkiem Lokalizacja.
1. Po zakończeniu lub awarii wystąpienia punkt końcowy w nagłówku Lokalizacja zwraca http 200.

Ten protokół umożliwia koordynację długotrwałych procesów z klientami zewnętrznymi lub usługami, które mogą sondować punkt końcowy HTTP i podążać za nagłówkiem Lokalizacja. Implementacje klienta i serwera tego wzorca są wbudowane w interfejsy API HTTP funkcji trwałych.

> [!NOTE]
> Domyślnie wszystkie akcje oparte na protoko>>> platformy HTTP udostępniane przez [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) obsługują standardowy wzorzec operacji asynchronicznego. Ta funkcja umożliwia osadzanie długotrwałej funkcji trwałe jako część przepływu pracy aplikacji logiki. Więcej informacji na temat obsługi asynchronicznych wzorców HTTP w usłudze Azure Logic Apps można znaleźć więcej szczegółów na temat obsługi asynchronicznych wzorców HTTP w [przypadku akcji przepływu pracy usługi Azure Logic Apps i wyzwalania dokumentacji.](../../logic-apps/logic-apps-workflow-actions-triggers.md)

> [!NOTE]
> Interakcje z aranżacjami można wykonywać z dowolnego typu funkcji, a nie tylko z funkcji wyzwalanych przez http.

Aby uzyskać więcej informacji na temat zarządzania aranżacjami i jednostkami przy użyciu interfejsów API klienta, zobacz [artykuł o zarządzaniu wystąpieniami](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Korzystanie z interfejsów API HTTP

Zgodnie z opisem w [ograniczeniach kodu funkcji koordynatora,](durable-functions-code-constraints.md)funkcje koordynatora nie mogą wykonywać we/wy bezpośrednio. Zamiast tego zazwyczaj wywołują [funkcje działania,](durable-functions-types-features-overview.md#activity-functions) które wykonują operacje we/wy.

Począwszy od funkcji trwałych 2.0, aranżacje mogą natywnie korzystać z interfejsów API HTTP przy użyciu [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger).

Poniższy przykładowy kod pokazuje funkcję koordynatora, która tworzy wychodzące żądanie HTTP:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Korzystając z akcji "wywołaj HTTP", można wykonać następujące czynności w funkcjach koordynatora:

* Wywołaj interfejsy API HTTP bezpośrednio z funkcji aranżacji, z pewnymi ograniczeniami, które są wymienione później.
* Automatycznie obsługuje wzorce sondowania stanu HTTP 202 po stronie klienta.
* Użyj [tożsamości zarządzanych platformy Azure,](../../active-directory/managed-identities-azure-resources/overview.md) aby autoryzować wywołania HTTP do innych punktów końcowych platformy Azure.

Możliwość korzystania z interfejsów API HTTP bezpośrednio z funkcji koordynatora jest przeznaczona jako wygoda dla określonego zestawu typowych scenariuszy. Wszystkie te funkcje można zaimplementować samodzielnie za pomocą funkcji działania. W wielu przypadkach funkcje działania może dać większą elastyczność.

### <a name="http-202-handling"></a>Obsługa protokołu HTTP 202

Interfejs API "wywołania HTTP" można automatycznie zaimplementować po stronie klienta wzorca konsumenta sondowania. Jeśli wywoływany interfejs API zwraca odpowiedź HTTP 202 z nagłówkiem Lokalizacja, funkcja aranżatora automatycznie sonduje zasób Lokalizacja do momentu otrzymania odpowiedzi innej niż 202. Ta odpowiedź będzie odpowiedzią zwróconą do kodu funkcji koordynatora.

> [!NOTE]
> Funkcje programu Orchestrator również natywnie obsługują wzorzec konsumenta sondowania po stronie serwera, zgodnie z opisem w [śledzenia operacji Asynchna.](#async-operation-tracking) Ta obsługa oznacza, że aranżacje w jednej aplikacji funkcji można łatwo koordynować funkcje koordynatora w innych aplikacjach funkcji. Jest to podobne do koncepcji [podaranżacji,](durable-functions-sub-orchestrations.md) ale z obsługą komunikacji między aplikacjami. Ta obsługa jest szczególnie przydatna w przypadku tworzenia aplikacji w stylu mikrousług.

### <a name="managed-identities"></a>Tożsamości zarządzane

Funkcje trwałe natywnie obsługuje wywołania interfejsów API, które akceptują tokeny usługi Azure Active Directory (Azure AD) do autoryzacji. Ta pomoc techniczna używa [tożsamości zarządzanych platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) do uzyskania tych tokenów.

Poniższy kod jest przykładem funkcji orkiestra .NET. Funkcja sprawia, że uwierzytelnione wywołania ponownego uruchomienia maszyny wirtualnej przy użyciu usługi Azure Resource Manager [maszyny interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachines).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

W poprzednim przykładzie `tokenSource` parametr jest skonfigurowany do uzyskiwania tokenów usługi Azure AD dla [usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). Tokeny są identyfikowane przez `https://management.core.windows.net`identyfikator URI zasobu . W przykładzie przyjęto założenie, że bieżąca aplikacja funkcji jest uruchomiona lokalnie lub została wdrożona jako aplikacja funkcji o tożsamości zarządzanej. Zakłada się, że tożsamość lokalna lub tożsamość zarządzana mają `myRG`uprawnienia do zarządzania maszynami wirtualnymi w określonej grupie zasobów .

W czasie wykonywania skonfigurowane źródło tokenu automatycznie zwraca token dostępu OAuth 2.0. Źródło następnie dodaje token jako token na okaziciela do nagłówka autoryzacji żądania wychodzącego. Ten model jest ulepszeniem w porównaniu z ręcznym dodawaniem nagłówków autoryzacji do żądań HTTP z następujących powodów:

* Odświeżanie tokenu jest obsługiwane automatycznie. Nie musisz się martwić o wygasłe tokeny.
* Tokeny nigdy nie są przechowywane w stanie trwałej aranżacji.
* Nie trzeba pisać żadnego kodu do zarządzania pozyskiwaniem tokenów.

Bardziej kompletny przykład można znaleźć w [przykładzie wstępnie skompilowanych programów RestartVMs języka C#.](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)

Tożsamości zarządzane nie są ograniczone do zarządzania zasobami platformy Azure. Za pomocą tożsamości zarządzanych można uzyskać dostęp do dowolnego interfejsu API, który akceptuje tokeny nośnika usługi Azure AD, w tym usługi platformy Azure od firmy Microsoft i aplikacje sieci Web od partnerów. Aplikacja internetowa partnera może być nawet inną aplikacją funkcyjną. Aby uzyskać listę usług platformy Azure firmy Microsoft obsługujących uwierzytelnianie za pomocą usługi Azure AD, zobacz [usługi platformy Azure obsługujące uwierzytelnianie usługi Azure AD.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

### <a name="limitations"></a>Ograniczenia

Wbudowana obsługa wywoływania interfejsów API HTTP jest wygodną funkcją. Nie jest odpowiedni dla wszystkich scenariuszy.

Żądania HTTP wysyłane przez funkcje koordynatora i ich odpowiedzi są serializowane i trwałe jako wiadomości kolejki. To zachowanie kolejkowania zapewnia, że wywołania HTTP są [niezawodne i bezpieczne dla powtarzania aranżacji.](durable-functions-orchestrations.md#reliability) Jednak zachowanie kolejkowania ma również ograniczenia:

* Każde żądanie HTTP wiąże się z dodatkowym opóźnieniem w porównaniu z natywnym klientem HTTP.
* Duże żądania lub odpowiedzi wiadomości, które nie mogą zmieścić się w komunikacie kolejki może znacznie obniżyć wydajność aranżacji. Obciążenie związane z odciążaniem ładunków komunikatów do magazynu obiektów blob może spowodować potencjalne obniżenie wydajności.
* Przesyłanie strumieniowe, fragmentowane i ładunki binarne nie są obsługiwane.
* Możliwość dostosowania zachowania klienta HTTP jest ograniczona.

Jeśli którekolwiek z tych ograniczeń może mieć wpływ na przypadek użycia, należy rozważyć zamiast tego przy użyciu funkcji działania i bibliotek klienta HTTP specyficzne dla języka do wychodzących wywołań HTTP.

> [!NOTE]
> Jeśli jesteś deweloperem platformy .NET, możesz się zastanawiać, dlaczego ta funkcja używa typów **TrwałehttpRequest** i **trwałehttpResponse** zamiast wbudowanych typów **.NET HttpRequestMessage** i **HttpResponseMessage.**
>
> Ten wybór projektu jest zamierzony. Głównym powodem jest to, że typy niestandardowe pomagają zapewnić, że użytkownicy nie popełniają niepoprawnych założeń dotyczących obsługiwanych zachowań wewnętrznego klienta HTTP. Typy specyficzne dla funkcji trwałe umożliwiają również uproszczenie projektowania interfejsu API. Mogą również łatwiej udostępniać specjalne funkcje, takie jak [integracja tożsamości zarządzanej](#managed-identities) i [wzorzec konsumenta sondowania.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Rozszerzalność (tylko.NET)

Dostosowywanie zachowania wewnętrznego klienta HTTP aranżacji jest możliwe przy użyciu [iniekcji zależności usługi Azure Functions .NET.](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) Ta zdolność może być przydatna do wprowadzania małych zmian w zachowaniu. Może być również przydatne do jednostki testowania klienta HTTP przez wstrzykiwanie makiety obiektów.

W poniższym przykładzie pokazano przy użyciu iniekcji zależności, aby wyłączyć sprawdzanie poprawności certyfikatu TLS/SSL dla funkcji koordynatora, które wywołują zewnętrzne punkty końcowe HTTP.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o trwałych elementach](durable-functions-entities.md)
