---
title: Skalowanie z wieloma wystąpieniami — usługa Azure SignalR
description: W wielu scenariuszach skalowania klient często musi aprowizować wiele wystąpień i skonfigurować, aby używać ich razem, aby utworzyć wdrożenie na dużą skalę. Na przykład dzielenia na fragmenty wymaga obsługi wielu wystąpień.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158157"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Jak skalować usługę SignalR z wieloma wystąpieniami?
Najnowszy zestaw SDK usługi SignalR obsługuje wiele punktów końcowych dla wystąpień usługi SignalR. Za pomocą tej funkcji można skalować połączenia równoczesne lub używać jej do obsługi wiadomości między regionami.

## <a name="for-aspnet-core"></a>Dla ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak dodać wiele punktów końcowych z configu?

Config z `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` kluczem lub dla ciągu połączenia SignalR Service.

Jeśli klucz zaczyna `Azure:SignalR:ConnectionString:`się od , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`powinien `Name` być `EndpointType` w formacie `ServiceEndpoint` , gdzie i są właściwości obiektu i są dostępne z kodu.

Można dodać wiele ciągów połączeń `dotnet` wystąpienia za pomocą następujących poleceń:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak dodać wiele punktów końcowych z kodu?

Klasa `ServicEndpoint` jest wprowadzana do opisywania właściwości punktu końcowego usługi Azure SignalR.
Można skonfigurować wiele punktów końcowych wystąpienia przy użyciu usługi Azure SignalR Service SDK za pośrednictwem:
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

### <a name="how-to-customize-endpoint-router"></a>Jak dostosować router punktu końcowego?

Domyślnie SDK używa [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) do pobrania punktów końcowych.

#### <a name="default-behavior"></a>Zachowanie domyślne 
1. Routing żądań klienta

    Gdy `/negotiate` klient z serwerem aplikacji. Domyślnie zestaw SDK **losowo wybiera** jeden punkt końcowy z zestawu dostępnych punktów końcowych usługi.

2. Routing wiadomości serwera

    Gdy *wysyłanie wiadomości do określonego **połączenia***, a połączenie docelowe jest kierowane do bieżącego serwera, wiadomość przechodzi bezpośrednio do tego połączonego punktu końcowego. W przeciwnym razie wiadomości są emitowane do każdego punktu końcowego usługi Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Dostosowywanie algorytmu routingu
Można utworzyć własny router, gdy masz specjalną wiedzę, aby zidentyfikować punkty końcowe wiadomości powinny przejść do.

Router niestandardowy jest zdefiniowany poniżej `east-` jako przykład, gdy `east`grupy zaczynające się od zawsze przechodzą do punktu końcowego o nazwie:

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

Inny przykład poniżej, który zastępuje domyślne zachowanie negocjowania, aby wybrać punkty końcowe zależy od tego, gdzie znajduje się serwer aplikacji.

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

Nie zapomnij zarejestrować routera do kontenera DI przy użyciu:

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

## <a name="for-aspnet"></a>Dla ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak dodać wiele punktów końcowych z configu?

Config z `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` kluczem lub dla ciągu połączenia SignalR Service.

Jeśli klucz zaczyna `Azure:SignalR:ConnectionString:`się od , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`powinien `Name` być `EndpointType` w formacie `ServiceEndpoint` , gdzie i są właściwości obiektu i są dostępne z kodu.

Do `web.config`:

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

Klasa `ServicEndpoint` jest wprowadzana do opisywania właściwości punktu końcowego usługi Azure SignalR.
Można skonfigurować wiele punktów końcowych wystąpienia przy użyciu usługi Azure SignalR Service SDK za pośrednictwem:

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

### <a name="how-to-customize-router"></a>Jak dostosować router?

Jedyną różnicą między ASP.NET SignalR i ASP.NET Core SignalR jest `GetNegotiateEndpoint`typ kontekstu http dla . Dla ASP.NET SignalR, jest typu [IOwinContext.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)

Poniżej znajduje się niestandardowy przykład negocjowania ASP.NET SignalR:

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

Nie zapomnij zarejestrować routera do kontenera DI przy użyciu:

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

Obiekt `ServiceEndpoint` ma `EndpointType` właściwość `primary` o `secondary`wartości lub .

`primary`punkty końcowe są preferowanymi punktami końcowymi do odbierania ruchu klienta i są uważane za bardziej niezawodne połączenia sieciowe; `secondary` punkty końcowe są uważane za mniej niezawodne połączenia sieciowe i są używane tylko do zabierania serwera do ruchu klienta, na przykład emitowania wiadomości, a nie do podejmowania klienta do ruchu serwera.

W przypadkach między regionami sieć może być niestabilna. Dla jednego serwera aplikacji znajdującego się we *wschodnich stanach USA*punkt końcowy `primary` usługi SignalR znajdujący się `secondary`w tym samym regionie *wschodnich stanów USA* może być skonfigurowany jako punkty końcowe w innych regionach oznaczonych jako . W tej konfiguracji punkty końcowe usługi w innych regionach mogą **odbierać** wiadomości z tego serwera aplikacji *wschodnich stanów USA,* ale nie będzie żadnych klientów **między regionami** kierowanych do tego serwera aplikacji. Architektura jest pokazana na poniższym diagramie:

![Podczerwień międzygłówna](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Gdy klient `/negotiate` próbuje z serwerem aplikacji, z routerem domyślnym, zestaw SDK `primary` **losowo wybiera** jeden punkt końcowy z zestawu dostępnych punktów końcowych. Gdy podstawowy punkt końcowy nie jest dostępny, SDK następnie `secondary` **losowo wybiera** ze wszystkich dostępnych punktów końcowych. Punkt końcowy jest oznaczony jako **dostępny,** gdy połączenie między serwerem a punktem końcowym usługi jest aktywne.

W scenariuszu między regionami, `/negotiate` gdy klient próbuje z serwerem aplikacji hostowanym `primary` we wschodnich stanach *USA,* domyślnie zawsze zwraca punkt końcowy znajdujący się w tym samym regionie. Gdy wszystkie punkty końcowe *wschodnich stanów USA* nie są dostępne, klient jest przekierowywał do punktów końcowych w innych regionach. W sekcji trybu fail-over poniżej opisano szczegółowo scenariusz.

![Normalne negocjacje](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Przewija się w pracy awaryjnej

Gdy `primary` wszystkie punkty końcowe nie są `/negotiate` dostępne, klient `secondary` wybiera z dostępnych punktów końcowych. Ten mechanizm awaryjnego wymaga, aby każdy `primary` punkt końcowy służył jako punkt końcowy do co najmniej jednego serwera aplikacji.

![Przewija się w pracy awaryjnej](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak skonfigurować wiele wystąpień w tej samej aplikacji do skalowania, dzielenia na fragmenty i scenariuszy między regionami.

Wiele punktów końcowych obsługuje może być również używany w scenariuszach wysokiej dostępności i odzyskiwania po awarii.

> [!div class="nextstepaction"]
> [Usługa SignalR instalatora do odzyskiwania po awarii i wysokiej dostępności](./signalr-concept-disaster-recovery.md)
