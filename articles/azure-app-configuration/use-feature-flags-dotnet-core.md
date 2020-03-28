---
title: Samouczek dotyczący używania flag funkcji w aplikacji .NET Core | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak zaimplementować flagi funkcji w aplikacjach .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 3182961f928a9befc5a55fb6d58e22c74ba81089
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473442"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Samouczek: Używanie flag funkcji w aplikacji ASP.NET Core

Biblioteki zarządzania funkcjami .NET Core zapewniają idiomatyczną obsługę implementowania flag funkcji w aplikacji .NET lub ASP.NET Core. Biblioteki te umożliwiają deklaratywnie dodać flagi funkcji do kodu, dzięki `if` czemu nie trzeba pisać wszystkie instrukcje dla nich ręcznie.

Biblioteki zarządzania funkcjami zarządzają również cyklami życia flagi funkcji za kulisami. Na przykład biblioteki odświeżyć i buforować stany flagi lub zagwarantować stan flagi, aby być niezmienne podczas wywołania żądania. Ponadto biblioteka ASP.NET Core oferuje gotowe do użycia integracje, w tym akcje kontrolera MVC, widoki, trasy i oprogramowanie pośredniczące.

Dodawanie [flag funkcji do ASP.NET Core aplikacji Szybki start](./quickstart-feature-flag-aspnet-core.md) pokazuje kilka sposobów, aby dodać flagi funkcji w aplikacji ASP.NET Core. W tym samouczku opisano te metody bardziej szczegółowo. Aby uzyskać pełne odniesienie, zobacz [ASP.NET dokumentację zarządzania funkcjami Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj flagi obiektów w kluczowych częściach aplikacji, aby kontrolować dostępność funkcji.
> * Integracja z konfiguracją aplikacji, gdy używasz go do zarządzania flagami funkcji.

## <a name="set-up-feature-management"></a>Konfigurowanie zarządzania funkcjami

Dodaj odwołanie do `Microsoft.FeatureManagement` pakietu NuGet, aby korzystać z menedżera funkcji .NET Core.
    
Menedżer `IFeatureManager` funkcji .NET Core pobiera flagi funkcji z natywnego systemu konfiguracji struktury. W rezultacie można zdefiniować flagi funkcji aplikacji przy użyciu dowolnego źródła konfiguracji, które obsługuje .NET Core, w tym lokalnego pliku *appsettings.json* lub zmiennych środowiskowych. `IFeatureManager`opiera się na iniekcji zależności .NET Core. Usługi zarządzania funkcjami można zarejestrować przy użyciu standardowych konwencji:

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

Domyślnie menedżer funkcji pobiera flagi funkcji `"FeatureManagement"` z sekcji danych konfiguracyjnych .NET Core. Poniższy przykład nakazuje menedżerowi funkcji odczytywanie z innej sekcji o nazwie: `"MyFeatureFlags"`

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

Jeśli używasz filtrów w flagach funkcji, musisz dołączyć dodatkową bibliotekę i zarejestrować ją. W poniższym przykładzie pokazano, jak używać `PercentageFilter`wbudowanego filtru funkcji o nazwie:

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

Zaleca się, aby zachować flagi funkcji poza aplikacją i zarządzać nimi oddzielnie. W ten sposób można zmodyfikować stany flagi w dowolnym momencie i te zmiany zaczynają obowiązywać w aplikacji od razu. Konfiguracja aplikacji zapewnia scentralizowane miejsce do organizowania i kontrolowania wszystkich flag funkcji za pośrednictwem dedykowanego interfejsu użytkownika portalu. Konfiguracja aplikacji dostarcza również flagi do aplikacji bezpośrednio za pośrednictwem bibliotek klienta .NET Core.

Najprostszym sposobem połączenia aplikacji ASP.NET Core z konfiguracją aplikacji `Microsoft.Azure.AppConfiguration.AspNetCore`jest dostawca konfiguracji . Wykonaj następujące kroki, aby użyć tego pakietu NuGet.

1. Otwórz *plik Program.cs* i dodaj następujący kod.

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

2. Otwórz *Startup.cs* i zaktualizuj `Configure` metodę, aby dodać oprogramowanie pośredniczące, aby umożliwić odświeżanie wartości flagi funkcji w cyklicznym odstępie czasu, podczas gdy aplikacja sieci web ASP.NET Core nadal odbiera żądania.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Oczekuje się, że wartości flag charakterystycznych będą się zmieniać wraz z czasem. Domyślnie wartości flagi funkcji są buforowane przez okres 30 sekund, więc operacja odświeżania wyzwalana po odebraniu żądania przez oprogramowanie pośredniczące nie zaktualizuje wartości, dopóki nie wygaśnie wartość buforowana. Poniższy kod pokazuje, jak zmienić czas wygaśnięcia pamięci podręcznej lub interwał sondowania do 5 minut w `options.UseFeatureFlags()` wywołaniu.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklaracja flagi funkcji

Każda flaga elementu składa się z dwóch części: nazwy i listy co najmniej jednego *on* filtru, które są używane `True`do oceny, czy stan obiektu jest włączony (czyli gdy jest jego wartość ). Filtr definiuje przypadek użycia, gdy funkcja powinna być włączona.

Gdy flaga elementu ma wiele filtrów, lista filtrów jest przesuwana w kolejności, dopóki jeden z filtrów nie określi, że funkcja powinna być włączona. W tym momencie flaga funkcji jest *wliczone*, a wszystkie pozostałe wyniki filtru są pomijane. Jeśli żaden filtr nie wskazuje, że funkcja powinna być włączona, flaga obiektu jest *wyłączona*.

Menedżer funkcji obsługuje *appsettings.json* jako źródło konfiguracji dla flag funkcji. W poniższym przykładzie pokazano, jak skonfigurować flagi funkcji w pliku JSON:

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

Zgodnie z `FeatureManagement` konwencją sekcja tego dokumentu JSON jest używana dla ustawień flagi funkcji. W poprzednim przykładzie przedstawiono trzy flagi `EnabledFor` operacji z ich filtrów zdefiniowanych we właściwości:

* `FeatureA`jest *włączony*.
* `FeatureB`jest *wyłączony*.
* `FeatureC`określa filtr nazwany `Percentage` właściwością. `Parameters` `Percentage`jest konfigurowalnym filtrem. W tym `Percentage` przykładzie określa 50-procentowe `FeatureC` prawdopodobieństwo, że flaga ma być *wł..*

## <a name="feature-flag-references"></a>Odwołania do flag charakterystycznych

Aby można było łatwo odwoływać się do flag `enum` funkcji w kodzie, należy zdefiniować je jako zmienne:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Sprawdzanie flagi funkcji

Podstawowy wzorzec zarządzania operacjami polega na tym, aby najpierw sprawdzić, czy flaga obiektu jest ustawiona *na .* Jeśli tak, menedżer funkcji następnie uruchamia akcje, które zawiera funkcja. Przykład:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

W ASP.NET Core MVC można uzyskać dostęp `IFeatureManager` do menedżera funkcji za pomocą iniekcji zależności:

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

## <a name="controller-actions"></a>Akcje kontrolera

W kontrolerach MVC można `FeatureGate` użyć atrybutu do kontrolowania, czy klasa całego kontrolera lub określonej akcji jest włączona. Następujący `HomeController` kontroler `FeatureA` wymaga *włączonej,* zanim będzie można wykonać dowolną akcję, która zawiera klasę kontrolera:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Aby `Index` można `FeatureA` było *on* uruchomić, należy włączyć następującą akcję:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Gdy kontroler MVC lub akcja jest zablokowana, ponieważ flaga funkcji sterującej jest *wyłączona,* wywoływany jest zarejestrowany `IDisabledFeaturesHandler` interfejs. Domyślny `IDisabledFeaturesHandler` interfejs zwraca kod stanu 404 do klienta bez treści odpowiedzi.

## <a name="mvc-views"></a>Widoki MVC

W widokach MVC można `<feature>` użyć znacznika do renderowania zawartości na podstawie tego, czy flaga funkcji jest włączona:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Aby wyświetlić zawartość alternatywną, gdy `negate` wymagania nie są spełnione, można użyć atrybutu.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Znacznik `<feature>` obiektu może również służyć do pokazywalania zawartości, jeśli są włączone dowolne lub wszystkie funkcje na liście.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtry MVC

Można skonfigurować filtry MVC tak, aby były aktywowane na podstawie stanu flagi funkcji. Poniższy kod dodaje filtr `SomeMvcFilter`MVC o nazwie . Ten filtr jest wyzwalany w potoku MVC tylko wtedy, gdy `FeatureA` jest włączona. Ta funkcja jest `IAsyncActionFilter`ograniczona do . 

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

## <a name="middleware"></a>Oprogramowanie pośredniczące

Flagom funkcji można również warunkowo dodawać gałęzie aplikacji i oprogramowanie pośredniczące. Poniższy kod wstawia składnik oprogramowania pośredniczącego w potoku żądań tylko wtedy, gdy `FeatureA` jest włączony:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ten kod tworzy poza bardziej ogólne możliwości rozgałęzienia całej aplikacji na podstawie flagi funkcji:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak zaimplementować flagi `Microsoft.FeatureManagement` funkcji w aplikacji ASP.NET Core przy użyciu bibliotek. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcjami w ASP.NET Core i konfiguracji aplikacji, zobacz następujące zasoby:

* [Przykładowy kod flagi funkcji ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentacja enia funkcją Microsoft.Feature](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Zarządzanie flagami funkcji](./manage-feature-flags.md)
