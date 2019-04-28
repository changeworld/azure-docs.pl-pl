---
title: Jak skalować z wieloma wystąpieniami usługi Azure SignalR Service
description: W wielu scenariuszach skalowania klient często potrzebuje aprowizować wiele wystąpień i konfigurowanie do używania ich ze sobą, można utworzyć wdrożenia na dużą skalę. Na przykład fragmentowania wymaga, aby obsługiwać wiele wystąpień.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809212"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Jak skalować usługi SignalR z wieloma wystąpieniami?
Najnowszy zestaw SDK usługi SignalR obsługuje wiele punktów końcowych dla wystąpień usługi SignalR. Ta funkcja umożliwia skalowanie równoczesnych połączeń lub użyć go do obsługi komunikatów między regionami.

## <a name="for-aspnet-core"></a>For ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak dodać wiele punktów końcowych z konfiguracji?

Konfiguracja z kluczem `Azure:SignalR:ConnectionString` lub `Azure:SignalR:ConnectionString:` dla parametrów połączenia usługi SignalR.

Jeśli klucz, który rozpoczyna się od `Azure:SignalR:ConnectionString:`, powinien być w formacie `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, gdzie `Name` i `EndpointType` są właściwościami `ServiceEndpoint` obiektu i są dostępne z kodu.

Można dodać wiele parametry połączenia wystąpienia za pomocą następujących `dotnet` poleceń:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak dodać wiele punktów końcowych z kodu?

Element `ServicEndpoint` klasy został wprowadzony do opisywania właściwości punktu końcowego usługi Azure SignalR Service.
Można skonfigurować wiele punktów końcowych wystąpień, korzystając z usługi Azure SignalR Service SDK za pomocą:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Jak dostosowywać routera punktu końcowego?

Domyślnie zestaw SDK używa [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) do pobrania punktów końcowych.

#### <a name="default-behavior"></a>Zachowanie domyślne 
1. Routing żądań klienta

    Gdy klient `/negotiate` z serwerem aplikacji. Domyślnie zestaw SDK **losowo wybiera** jeden punkt końcowy z zestawu dostępnych punktów końcowych usługi.

2. Routing komunikatów serwera

    Gdy * wysyłania komunikatu do określonego ** połączenia *** i połączenie docelowe jest kierowany do bieżącego serwera, wiadomość jest wysyłana bezpośrednio do tego punktu końcowego w połączonych. W przeciwnym razie komunikaty są wysłano do każdego punktu końcowego usługi Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Dostosowywanie algorytm routingu
W przypadku specjalnej wiedzy, aby zidentyfikować, które punkty końcowe, przejdź do wiadomości, można utworzyć własne routera.

Router niestandardowy jest zdefiniowana poniżej jako przykład podczas grupy rozpoczynające się od `east-` zawsze przejdź do punktu końcowego o nazwie `east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Inny przykład poniżej, która zastępuje domyślną negocjowania zachowanie, aby wybrać punkty końcowe zależy gdzie znajduje się serwer aplikacji.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Należy pamiętać zarejestrować router tak, aby DI kontenera przy użyciu:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Dla platformy ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak dodać wiele punktów końcowych z konfiguracji?

Konfiguracja z kluczem `Azure:SignalR:ConnectionString` lub `Azure:SignalR:ConnectionString:` dla parametrów połączenia usługi SignalR.

Jeśli klucz, który rozpoczyna się od `Azure:SignalR:ConnectionString:`, powinien być w formacie `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, gdzie `Name` i `EndpointType` są właściwościami `ServiceEndpoint` obiektu i są dostępne z kodu.

Można dodać wiele wystąpień parametry połączenia do `web.config`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak dodać wiele punktów końcowych z kodu?

Element `ServicEndpoint` klasy został wprowadzony do opisywania właściwości punktu końcowego usługi Azure SignalR Service.
Można skonfigurować wiele punktów końcowych wystąpień, korzystając z usługi Azure SignalR Service SDK za pomocą:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Jak dostosowywać routera?

Jedyną różnicą między biblioteki SignalR platformy ASP.NET i biblioteki SignalR platformy ASP.NET Core jest typ kontekstu http dla `GetNegotiateEndpoint`. Dla elementu SignalR platformy ASP.NET, jest on [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) typu.

Poniżej znajduje się niestandardowy negocjacji przykład dla biblioteki SignalR platformy ASP.NET:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Należy pamiętać zarejestrować router tak, aby DI kontenera przy użyciu:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguracja w scenariuszach między regionami

`ServiceEndpoint` Obiekt ma `EndpointType` właściwość z wartością `primary` lub `secondary`.

`primary` punkty końcowe są preferowane punkty końcowe do odbierania ruchu klienta i jest uznawany za bardziej niezawodne połączenia sieciowe; `secondary` punkty końcowe jest uznawany za mniej niezawodne połączenia sieciowe i są używane tylko dla wykonywanie serwera, aby ruch sieciowy klienta, na przykład, emituje komunikaty, a nie dla pobierania klienta do serwera ruchu.

W przypadkach, między regionami sieć może być niestabilny. Dla serwera jednej aplikacji na terenie *wschodnie stany USA*, punkt końcowy usługi SignalR znajduje się w tym samym *wschodnie stany USA* regionu można skonfigurować jako `primary` , punktów końcowych w innych regionach jest oznaczona jako `secondary`. W tej konfiguracji można punktów końcowych usługi w innych regionach **otrzymywać** komunikaty z tego *wschodnie stany USA* serwera aplikacji, ale nie będzie można nie **między regionami** klientów kierowane do Ten serwer aplikacji. Na poniższym diagramie przedstawiono architekturę:

![Geograficzna między lokalizacjami Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Gdy klient próbuje `/negotiate` z serwerem aplikacji, z routerem domyślną, zestaw SDK **losowo wybiera** jeden punkt końcowy z zestawu dostępnych `primary` punktów końcowych. Gdy punkt końcowy jest dostępny, zestaw SDK następnie **losowo wybiera** spośród wszystkich dostępnych `secondary` punktów końcowych. Punkt końcowy jest oznaczony jako **dostępne** podczas połączenia między serwerem a punkt końcowy usługi jest aktywny.

W scenariuszu między regionami, gdy klient próbuje `/negotiate` z serwerem aplikacji hostowanej w *wschodnie stany USA*, domyślne go zawsze zwraca `primary` punktu końcowego, który znajduje się w tym samym regionie. Gdy wszystkie *wschodnie stany USA* punktów końcowych nie są dostępne, klient zostanie przekierowany do punktów końcowych w innych regionach. Tryb failover poniżej opisano scenariusz szczegółowo.

![Normalny negocjowania](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Tryb failover

Gdy wszystkie `primary` punktów końcowych nie są dostępne, klienta `/negotiate` wybiera spośród dostępnych `secondary` punktów końcowych. Ten mechanizm pracy awaryjnej wymaga, że każdy punkt końcowy powinien służyć jako `primary` punktu końcowego serwera co najmniej jedną aplikację.

![Tryb failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku opisano sposób konfigurowania wielu wystąpień w tej samej aplikacji do skalowania, dzielenie na fragmenty i scenariuszy między regionami.

Wiele punktów końcowych obsługuje można również w przypadku wysoką dostępność i odzyskiwanie po awarii scenariuszy odzyskiwania.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi SignalR dla odzyskiwania po awarii i wysoka dostępność](./signalr-concept-disaster-recovery.md)
