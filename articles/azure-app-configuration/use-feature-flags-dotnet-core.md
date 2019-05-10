---
title: Samouczek dotyczący przy użyciu flag funkcji w aplikacji platformy .NET Core | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach platformy .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28ba4397ca5a5fd3c281555238fc7eec8a82943d
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413673"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Samouczek: Używanie flag funkcji w aplikacji platformy .NET Core

Biblioteki .NET Core funkcja zarządzania umożliwiają idiomatyczną Implementowanie flagi funkcji w aplikacji .NET lub ASP.NET Core. Pozwalają one dodać flagi funkcji w kodzie bardziej deklaratywne, aby nie trzeba napisać cały `if` instrukcje dla nich ręcznie. Cykle życia flagi funkcji (na przykład, odświeżanie i pamięci podręcznej Flaga stanów, gwarancji stanu flagi, mają być niezmienialne podczas wywołania żądania) w którym zarządzają za sceną. Ponadto biblioteka ASP.NET Core oferuje integracji out-of--box, łącznie z akcji kontrolera MVC, widoki, trasy i oprogramowania pośredniczącego.

[Dodawanie flag funkcji do aplikacji platformy ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) przewodniku Szybki Start przedstawiono różne sposoby, aby dodać flag funkcji w aplikacji ASP.NET Core. W tym samouczku wyjaśniono one bardziej szczegółowo. Zobacz [dokumentacją dotyczącą zarządzania funkcji platformy ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410) pełną dokumentację.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * W części klucza aplikacji do sterowania dostępności funkcji, należy dodać flag funkcji.
> * Integracja z konfiguracji aplikacji, gdy jej używać do zarządzania flag funkcji.

## <a name="setup"></a>Konfiguracja

Menedżer funkcji platformy .NET Core `IFeatureManager` pobiera flagi funkcji z systemu macierzystego konfiguracji struktury. W rezultacie, można zdefiniować flag funkcji aplikacji przy użyciu dowolnego źródła konfiguracji, które obsługuje platformy .NET Core, w tym lokalnym *appsettings.json* pliku zmienne lub zmienne środowiskowe. Funkcja Menedżera opiera się na iniekcji zależności platformy .NET Core. Można zarejestrować usługi zarządzania funkcji przy użyciu standardowych konwencji.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Menedżer funkcji pobiera flagi funkcji w sekcji "FeatureManagement" dane konfiguracji platformy .NET Core domyślnie. Poniższy przykład informuje czytać z innej sekcji o nazwie "MyFeatureFlags" zamiast niego.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Jeśli używasz filtru flagi funkcji należy uwzględnić dodatkowe biblioteki i zarejestruj go. Poniższy przykład pokazuje, jak zastosować filtr wbudowanej funkcji o nazwie **PercentageFilter "**.

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

W celu efektywnego prowadzenia działalności, należy zachować flag funkcji spoza aplikacji i zarządzaj nimi osobno. Dzięki temu można zmodyfikować stany flagi w dowolnym momencie i zastosowaniem w aplikacji natychmiast tych zmian. Konfiguracja aplikacji zapewnia, scentralizowanym miejscem do organizowania i kontrolowanie Twojej funkcji flagi za pośrednictwem dedykowanego interfejsu użytkownika portalu i dostarcza flagi do aplikacji bezpośrednio za pomocą klienta platformy .NET Core bibliotek. Najprostszym sposobem łączenia aplikacji platformy ASP.NET Core z konfiguracji aplikacji jest za pośrednictwem dostawcy konfiguracji `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Można użyć tego pakietu NuGet w kodzie, dodając następujące polecenie, aby *Program.cs* pliku:

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

Wartości z flag funkcji powinny ulegać zmianie. Domyślnie Menedżer funkcji spowoduje odświeżenie wartości flag funkcji co 30 sekund. Można użyć różnych interwału sondowania w `options.UseFeatureFlags()` wywołanie powyżej.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każdy flagi funkcji ma dwie części: nazwy i listy jeden lub więcej filtrów, które są używane do oceny, czy stan funkcji jest *na* (oznacza to, gdy jej wartość jest `True`). Filtr definiuje przypadek użycia, dla której funkcja powinna być włączona. Jeśli flaga funkcji ma wiele filtrów, lista filtrów, o ile w kolejności, dopóki jeden z filtrów określa, czy funkcja powinna być włączona. W tym momencie jest uznawany za flagą funkcji *na* i wszelkie pozostałe wyniki zastosowania filtru są pomijane. Jeśli żaden filtr wskazuje, że funkcja powinna być włączona, Flaga funkcji jest *poza*.

Funkcja obsługuje Menedżera *appsettings.json* jako źródło konfiguracji dla flag funkcji. Poniższy przykład pokazuje, jak skonfigurować flagi funkcji w pliku json.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Zgodnie z Konwencją `FeatureManagement` część niniejszego dokumentu json jest używana do ustawienia flagi funkcji. Powyższy przykład przedstawia trzy flagi funkcji z ich filtrami zdefiniowanymi w *EnabledFor* właściwości:

* **Funkcjaa** jest *na*.
* **FeatureB** jest *poza*.
* **FeatureC** Określa filtr o nazwie *procent* z *parametry* właściwości. *Wartość procentowa* znajduje się przykład można skonfigurować filtr i określa prawdopodobieństwo 50% **FeatureC** flagę, aby być *na*.

## <a name="referencing"></a>Odwoływanie się do

Chociaż nie jest wymagane, flagi funkcji powinien być zdefiniowany jako `enum` zmienne, aby odwołanie łatwo w kodzie.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>Sprawdzania flagi funkcji

Podstawowy wzorzec funkcji zarządzania jest najpierw sprawdzić, jeśli jest ustawiona flaga funkcji *na* , a następnie wykonaj akcje zamknięte, jeśli tak jest rzeczywiście.

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

We wzorcu ASP.NET Core MVC, Menedżer funkcji `IFeatureManager` jest możliwy za pośrednictwem iniekcji zależności.

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-action"></a>Akcja kontrolera

W kontrolerów MVC `Feature` atrybut może służyć do kontroli, czy klasa całego kontrolera lub konkretnej akcji jest włączony. Następujące `HomeController` wymaga kontrolera *Funkcjaa* jako *na* przed można wykonać żadnych działań, które zawiera.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Następujące `Index` powyższej akcji wymaga *Funkcjaa* jako *na* zanim będzie można go uruchomić.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Gdy MVC kontroler lub akcję jest zablokowane, ponieważ jest flagi funkcji kontrolowania *poza*, zarejestrowany `IDisabledFeatureHandler` jest wywoływana. Wartość domyślna `IDisabledFeatureHandler` zwraca kod stanu 404 do klienta bez treści odpowiedzi.

## <a name="view"></a>Widok

W widokach MVC `<feature>` tagu może zostać użyty do renderowania zawartości, w oparciu o włączenie flagi funkcji lub nie.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>Filtr MVC

Filtry MVC można skonfigurować w taki sposób, że są aktywowane, na podstawie stanu flagi funkcji. Następujące dodaje filtr MVC o nazwie `SomeMvcFilter`. Ten filtr jest wyzwalany w MVC potoku tylko wtedy, gdy *Funkcjaa* jest włączona.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="route"></a>Trasa

Trasy mogą być udostępniane dynamicznie na podstawie flagi funkcji. Następujące dodaje trasę, która ustawia `Beta` jako domyślny kontroler, tylko wtedy, gdy *Funkcjaa* jest włączona.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Oprogramowanie pośredniczące

Flagi funkcji może służyć do dodawania aplikacji gałęzi i oprogramowanie pośredniczące warunkowo. Następujące operacje wstawiania składnik oprogramowania pośredniczącego w żądaniu potoku tylko wtedy, gdy *Funkcjaa* jest włączona.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Powoduje to skompilowanie poza możliwości bardziej ogólnym, gałęzi całej aplikacji, w oparciu o flagi funkcji.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrożyć flagi funkcji w aplikacji ASP.NET Core przy użyciu `Microsoft.FeatureManagement` bibliotek. Zobacz następujące zasoby, aby uzyskać więcej informacji dotyczących obsługi różnych funkcji zarządzania w ASP.NET Core i konfiguracji aplikacji.

* [Flagi funkcji platformy ASP.NET Core przykładowego kodu]()
* [Dokumentacja Microsoft.FeatureManagement]()
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
