---
title: Samouczek dotyczący przy użyciu flag funkcji w aplikacji platformy .NET Core | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach platformy .NET Core.
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
ms.openlocfilehash: fc5215f71af45d3273da437fc796bf0d396ba3f9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393513"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Samouczek: Używanie flag funkcji w aplikacji platformy .NET Core

Biblioteki .NET Core funkcja zarządzania umożliwiają idiomatyczną Implementowanie flagi funkcji w aplikacji .NET lub ASP.NET Core. Te biblioteki pozwalają na deklaratywne dodać flag funkcji w kodzie, dzięki czemu nie trzeba napisać cały `if` instrukcje dla nich ręcznie.

Biblioteki zarządzania funkcji również zarządzać cyklów flagi funkcji w tle. Na przykład biblioteki Odśwież stany flagi w pamięci podręcznej i zagwarantować stanu flagi, mają być niezmienialne podczas wywołania żądania. Ponadto biblioteka ASP.NET Core oferuje integracji poza pole, a w tym akcji kontrolera MVC, widoki, trasy i oprogramowania pośredniczącego.

[Dodawanie flag funkcji do aplikacji platformy ASP.NET Core Przewodnik Szybki Start](./quickstart-feature-flag-aspnet-core.md) pokazano kilka sposobów, aby dodać flag funkcji w aplikacji ASP.NET Core. W tym samouczku wyjaśniono tych metod, które bardziej szczegółowo. Aby uzyskać pełną dokumentację, zobacz [dokumentacją dotyczącą zarządzania funkcji platformy ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * W części klucza aplikacji do sterowania dostępności funkcji, należy dodać flag funkcji.
> * Integracja z konfiguracji aplikacji, gdy jest używany do zarządzania flag funkcji.

## <a name="set-up-feature-management"></a>Konfigurowanie funkcji zarządzania

Menedżer funkcji platformy .NET Core `IFeatureManager` pobiera flagi funkcji z systemu macierzystego konfiguracji struktury. Co w efekcie można zdefiniować flag funkcji aplikacji przy użyciu dowolnego źródła konfiguracji, które obsługuje platformy .NET Core, w tym lokalnym *appsettings.json* pliku zmienne lub zmienne środowiskowe. `IFeatureManager` opiera się na iniekcji zależności platformy .NET Core. Funkcja usług zarządzania można zarejestrować za pomocą standardowych konwencji:

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

Domyślnie Menedżer funkcja pobiera flagi funkcji z `"FeatureManagement"` części danych konfiguracji platformy .NET Core. Poniższy przykład informuje Menedżera funkcji, które można odczytać z innej sekcji o nazwie `"MyFeatureFlags"` zamiast tego:

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

Użyj filtrów w swojej flag funkcji, należy uwzględnić dodatkowe biblioteki i zarejestrowanie go. Poniższy przykład pokazuje, jak zastosować filtr wbudowanej funkcji o nazwie `PercentageFilter`:

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

Zaleca się zachować flag funkcji spoza aplikacji i zarządzaj nimi osobno. Dzięki temu można zmodyfikować stany flagi w dowolnym momencie i te zmiany zaczną obowiązywać w aplikacji, następnie od razu. Konfiguracja aplikacji zapewnia scentralizowanym miejscem do organizowania i kontrolowanie swoje flag funkcji za pośrednictwem dedykowanego interfejsu użytkownika portalu. Konfiguracja aplikacji również możliwość flagi do aplikacji bezpośrednio za pomocą klienta platformy .NET Core bibliotek.

Najprostszym sposobem łączenia aplikacji platformy ASP.NET Core z konfiguracji aplikacji jest za pośrednictwem dostawcy konfiguracji `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Aby użyć tego pakietu NuGet, Dodaj następujący kod do *Program.cs* pliku:

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

Wartości z flag funkcji powinny ulegać zmianie. Domyślnie Menedżer funkcji odświeża wartości flag funkcji co 30 sekund. Poniższy kod pokazuje, jak zmienić interwał sondowania to 5 sekund w `options.UseFeatureFlags()` wywołania:

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każdy flagi funkcji ma dwie części: nazwy i listy jeden lub więcej filtrów, które są używane do oceny, czy stan funkcji jest *na* (oznacza to, gdy jej wartość jest `True`). Filtr definiuje przypadek użycia, gdy powinna być włączona funkcja.

Jeśli flaga funkcji ma wiele filtrów, lista filtrów, o ile w kolejności, dopóki jeden z filtrów okaże się, że funkcja powinna być włączona. W tym momencie Flaga funkcji jest *na*, a wszystkie pozostałe wyniki zastosowania filtru są pomijane. Jeśli żaden filtr wskazuje, funkcja powinna być włączona, Flaga funkcji jest *poza*.

Funkcja obsługuje Menedżera *appsettings.json* jako źródło konfiguracji dla flag funkcji. Poniższy przykład pokazuje, jak skonfigurować flagi funkcji w pliku JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
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

Zgodnie z Konwencją `FeatureManagement` część niniejszego dokumentu JSON jest używana do ustawienia flagi funkcji. Poprzedni przykład przedstawia trzy flagi funkcji z ich filtrami zdefiniowanymi w `EnabledFor` właściwości:

* `FeatureA` jest *na*.
* `FeatureB` jest *poza*.
* `FeatureC` Określa filtr o nazwie `Percentage` z `Parameters` właściwości. `Percentage` Czy można skonfigurować filtr. W tym przykładzie `Percentage` określa prawdopodobieństwo 50 procent `FeatureC` flagę, aby być *na*.

## <a name="feature-flag-references"></a>Odwołania flagi funkcji

Tak, aby łatwo można odwoływać się flagi funkcji w kodzie, należy zdefiniować je jako `enum` zmiennych:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Sprawdzanie flag funkcji

Podstawowy wzorzec funkcji zarządzania jest najpierw sprawdzić, jeśli jest ustawiona flaga funkcji *na*. Jeśli tak, Menedżer funkcji następnie uruchamia akcje, zawiera tę funkcję. Na przykład:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

W programie ASP.NET Core MVC uzyskujesz dostęp do Menedżera funkcji `IFeatureManager` za pomocą iniekcji zależności:

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

## <a name="controller-actions"></a>Akcji kontrolera

W kontrolerów MVC `Feature` atrybutów do kontroli, czy klasa całego kontrolera lub konkretnej akcji jest włączony. Następujące `HomeController` wymaga kontrolera `FeatureA` jako *na* przed można wykonać żadnych działań, które zawiera klasy kontrolera:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Następujące `Index` akcja wymaga `FeatureA` jako *na* zanim będzie można go uruchomić:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Gdy MVC kontroler lub akcję jest zablokowane, ponieważ jest flagi funkcji kontrolowania *poza*, zarejestrowany `IDisabledFeaturesHandler` nosi nazwę interfejsu. Wartość domyślna `IDisabledFeaturesHandler` interfejsu zwraca kod stanu 404 do klienta bez treści odpowiedzi.

## <a name="mvc-views"></a>Widoków MVC

W widokach MVC, można użyć `<feature>` tag do renderowania zawartości oparte na włączenie flagi funkcji:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC można skonfigurować tak, aby zostały aktywowane, na podstawie stanu flagi funkcji. Poniższy kod dodaje filtr MVC o nazwie `SomeMvcFilter`. Ten filtr jest wyzwalany w MVC potoku tylko wtedy, gdy `FeatureA` jest włączona.

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

## <a name="routes"></a>Trasy

Flagi funkcji umożliwia dynamiczne udostępnianie trasy. Poniższy kod dodaje trasy, który ustawia `Beta` jako domyślny kontroler tylko wtedy, gdy `FeatureA` jest włączona:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Oprogramowanie pośredniczące

Flagi funkcji umożliwia również warunkowo Dodaj gałęzie aplikacji i oprogramowania pośredniczącego. Poniższej wstawki kodu składnik oprogramowania pośredniczącego w żądaniu potoku tylko wtedy, gdy `FeatureA` jest włączona:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ten kod tworzy wyłączyć możliwość bardziej ogólny gałęzi całej aplikacji, w oparciu o flagi funkcji:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrożyć flagi funkcji w aplikacji ASP.NET Core przy użyciu `Microsoft.FeatureManagement` bibliotek. Aby uzyskać więcej informacji dotyczących obsługi różnych funkcji zarządzania w ASP.NET Core i konfiguracji aplikacji zobacz następujące zasoby:

* [Flagi funkcji platformy ASP.NET Core przykładowego kodu](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentacja Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
