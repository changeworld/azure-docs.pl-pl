---
title: Konfigurowanie aplikacji Linux ASP.NET Core
description: Dowiedz się, jak skonfigurować wstępnie utworzony kontener ASP.NET Core dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255910"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurowanie aplikacji linux ASP.NET Core dla usługi Azure App Service

ASP.NET aplikacje Core muszą być wdrażane jako skompilowane pliki binarne. Narzędzie do publikowania programu Visual Studio tworzy rozwiązanie, a następnie wdraża bezpośrednio skompilowane pliki binarne, podczas gdy aparat wdrażania usługi App Service najpierw wdraża repozytorium kodu, a następnie kompiluje pliki binarne.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla ASP.NET core deweloperów, którzy używają wbudowanego kontenera systemu Linux w usłudze App Service. Jeśli nigdy nie korzystałeś z usługi Azure App Service, wykonaj najpierw [ASP.NET Core Szybki start](quickstart-dotnetcore.md) i ASP.NET Core za pomocą [samouczka bazy danych SQL.](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>Pokaż wersję .NET Core

Aby wyświetlić bieżącą wersję rdzenia .NET, uruchom następujące polecenie w [połocie Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje .NET Core, uruchom następujące polecenie w [usłudze Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Ustawianie wersji .NET Core

Uruchom następujące polecenie w [usłudze Cloud Shell,](https://shell.azure.com) aby ustawić wersję .NET Core na 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Dostosowywanie automatyzacji kompilacji

Jeśli wdrożysz aplikację przy użyciu pakietów Git lub zip z włączoną automatyzacją kompilacji, kroki automatyzacji kompilacji usługi App Service w następującej kolejności:

1. Uruchom skrypt niestandardowy, jeśli jest określony przez `PRE_BUILD_SCRIPT_PATH`plik .
1. Uruchom, `dotnet restore` aby przywrócić zależności NuGet.
1. Uruchom, `dotnet publish` aby utworzyć plik binarny dla produkcji.
1. Uruchom skrypt niestandardowy, jeśli jest określony przez `POST_BUILD_SCRIPT_PATH`plik .

`PRE_BUILD_COMMAND`i `POST_BUILD_COMMAND` są zmienne środowiskowe, które są domyślnie puste. Aby uruchomić polecenia przed kompilacją, zdefiniuj `PRE_BUILD_COMMAND`plik . Aby uruchomić polecenia po kompilacji, zdefiniuj `POST_BUILD_COMMAND`plik .

Poniższy przykład określa dwie zmienne do serii poleceń, oddzielone przecinkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Aby uzyskać dodatkowe zmienne środowiskowe w celu dostosowania automatyzacji kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Aby uzyskać więcej informacji na temat sposobu działania i kompilacji usługi App Service ASP.NET aplikacji Core w systemie Linux, zobacz [dokumentację Oryx: Jak aplikacje .NET Core są wykrywane i komploatowane](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp w dowolnej klasie przy użyciu wzorca iniekcji zależności zależności standardowej ASP.NET:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Jeśli skonfigurujesz ustawienie aplikacji o tej samej nazwie w usłudze App Service i w *appsettings.json*, na przykład wartość usługi app service ma pierwszeństwo przed wartością *appsettings.json.* Wartość local *appsettings.json* umożliwia debugowanie aplikacji lokalnie, ale wartość usługi App Service umożliwia uruchamianie aplikacji w produkcie z ustawieniami produkcji. Parametry połączenia działają w ten sam sposób. W ten sposób można zachować wpisy tajne aplikacji poza repozytorium kodu i uzyskać dostęp do odpowiednich wartości bez zmiany kodu.

## <a name="get-detailed-exceptions-page"></a>Pobierz stronę szczegółowych wyjątków

Gdy aplikacja ASP.NET generuje wyjątek w debugerze programu Visual Studio, przeglądarka wyświetla szczegółową stronę wyjątku, ale w usłudze App Service ta strona jest zastępowana ogólnym błędem **HTTP 500** lub **wystąpił błąd podczas przetwarzania żądania.** . Aby wyświetlić stronę szczegółowych wyjątków `ASPNETCORE_ENVIRONMENT` w usłudze App Service, dodaj ustawienie aplikacji do aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >aplikacji Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi wiedzieć, czy żądania użytkownika są szyfrowane, czy nie, skonfiguruj oprogramowanie pośredniczące nagłówków przesyłanych dalej w *Startup.cs:*

- Skonfiguruj oprogramowanie pośredniczące za pomocą `X-Forwarded-For` [forwardedHeadersOptions,](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) aby przesłać dalej i `X-Forwarded-Proto` nagłówki w `Startup.ConfigureServices`pliku .
- Dodaj zakresy prywatnych adresów IP do znanych sieci, aby oprogramowanie pośredniczące ufać modułowi równoważenia obciążenia usługi App Service.
- Wywołaj [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metody `Startup.Configure` przed wywołaniem innych programów pośredniczących.

Łącząc wszystkie trzy elementy, kod wygląda następująco:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Wdrażanie rozwiązań wieloprojektowych

Podczas wdrażania repozytorium ASP.NET do aparatu wdrażania z plikiem *csproj* w katalogu głównym aparat wdraża projekt. Podczas wdrażania repozytorium ASP.NET z plikiem *.sln* w katalogu głównym aparat wybiera pierwszą witrynę sieci Web lub projekt aplikacji sieci Web, który zostanie odnajduje jako aplikację usługi App Service. Aparat może nie wybierać odpowiedniego projektu.

Aby wdrożyć rozwiązanie wieloprojektowe, można określić projekt do użycia w usłudze App Service na dwa różne sposoby:

### <a name="using-deployment-file"></a>Korzystanie z pliku .deployment

Dodaj plik *.deployment* do katalogu głównego repozytorium i dodaj następujący kod:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Korzystanie z ustawień aplikacji

W <a target="_blank" href="https://shell.azure.com">usłudze Azure Cloud Shell</a>dodaj ustawienie aplikacji do aplikacji usługi App Service, uruchamiając następujące polecenie interfejsu wiersza polecenia. * \<Zastąp>nazwy aplikacji, * * \<>nazwy grupy zasobów *i * \<>nazwy projektu* odpowiednimi wartościami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET podstawowa aplikacja z bazą danych SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania dotyczące usługi aplikacji Linux](app-service-linux-faq.md)
