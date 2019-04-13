---
title: Konfigurowanie aplikacji platformy ASP.NET Core — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania aplikacji ASP.NET Core pracę w usłudze Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551115"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurowanie systemu Linux aplikacji ASP.NET Core dla usługi Azure App Service

Aplikacje platformy ASP.NET Core, musi zostać wdrożony jako skompilowane pliki binarne. Narzędzie do publikowania programu Visual Studio kompiluje rozwiązanie, a następnie wdraża skompilowane pliki binarne bezpośrednio w aparacie wdrażania usługi App Service najpierw wdraża repozytorium kodu, a następnie kompiluje pliki binarne.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dotyczące platformy ASP.NET Core programistów, którzy korzystają z wbudowanych kontenera systemu Linux w usłudze App Service. Jeśli nie znasz usługi Azure App Service, postępuj zgodnie z [Szybki Start platformy ASP.NET Core](quickstart-dotnetcore.md) i [platformy ASP.NET Core z usługą SQL Database](tutorial-dotnetcore-sqldb-app.md) pierwszy.

## <a name="show-net-core-version"></a>Pokaż wersji programu .NET Core

Aby wyświetlić bieżącą wersję platformy .NET Core, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje platformy .NET Core, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Ustaw wersję .NET Core

Uruchom następujące polecenie [Cloud Shell](https://shell.azure.com) można ustawić wersji platformy .NET Core do wersji 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [określić ustawienia aplikacji](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) poza kodu aplikacji. Następnie można z nich korzystać przy użyciu standardowego wzorca ASP.NET:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Jeśli skonfigurujesz to ustawienie aplikacji o takiej samej nazwie w usłudze App Service, a w *Web.config*, wartość usługi App Service ma pierwszeństwo przed wartością pliku Web.config. Wartość Web.config umożliwia debugowanie aplikacji w środowisku lokalnym, ale wartość usługi App Service umożliwia przebieg aplikacji w produkcie przy użyciu ustawień w środowisku produkcyjnym. Parametry połączenia działają w taki sam sposób. W ten sposób można przechowywać klucze tajne aplikacji poza repozytorium kodu i uzyskać dostęp do odpowiedniej wartości bez wprowadzania zmian w kodzie.

## <a name="get-detailed-exceptions-page"></a>Strona szczegółowe wyjątki

Aplikacja ASP.NET generuje wyjątek w debugerze programu Visual Studio, w przeglądarce pojawi się Strona szczegółowe wyjątku, ale w usłudze App Service tę stronę zastępuje ogólnego **HTTP 500** błąd lub **błąd wystąpił podczas przetwarzanie żądania.** Komunikat. Aby wyświetlić stronę szczegółowe wyjątek w usłudze App Service, należy dodać `ASPNETCORE_ENVIRONMENT` ustawienia aplikacji na aplikację, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi znać, jeśli użytkownik zażąda są zaszyfrowane, czy nie, należy skonfigurować oprogramowanie pośredniczące nagłówki przekazywane w *Startup.cs*:

- Konfigurowanie oprogramowania pośredniczącego z [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) do przekazywania `X-Forwarded-For` i `X-Forwarded-Proto` nagłówków w `Startup.ConfigureServices`.
- Dodaj zakresy prywatnych adresów IP do znane sieci, tak aby oprogramowanie pośredniczące mogą ufać modułu równoważenia obciążenia usługi App Service.
- Wywoływanie [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) method in Class metoda `Startup.Configure` przed wywołaniem innych middlewares.

Zestawiania wszystkich trzech elementów, Twój kod wygląda podobnie jak w poniższym przykładzie:

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

Aby uzyskać więcej informacji, zobacz [Konfigurowanie platformy ASP.NET Core pracować z serwerów proxy i moduły równoważenia obciążenia](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Wdrażanie rozwiązań dotyczących wielu projektów

Podczas wdrażania repozytorium programu ASP.NET w aparacie wdrażania za pomocą *.csproj* pliku w katalogu głównym, aparat wdroży projekt. Podczas wdrażania repozytorium platformy ASP.NET przy użyciu *.sln* pliku w katalogu głównym, aparat wybiera pierwszej witryny sieci Web lub projektu aplikacji sieci Web znajdzie jako aplikację usługi App Service. Jest to aparat nie wybrać projekt, który ma.

Aby wdrożyć rozwiązania wielu projektów, można określić projektu do użycia w usłudze App Service na dwa sposoby:

### <a name="using-deployment-file"></a>Przy użyciu pliku .deployment

Dodaj *.deployment* pliku w folderze głównym repozytorium i Dodaj następujący kod:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Przy użyciu ustawień aplikacji

W <a target="_blank" href="https://shell.azure.com">usługi Azure Cloud Shell</a>, Dodaj ustawienie aplikacji do aplikacji usługi app Service, uruchamiając następujące polecenie interfejsu wiersza polecenia. Zastąp  *\<Nazwa aplikacji >*,  *\<Nazwa grupy zasobów >*, i  *\<Nazwa projektu >* odpowiednimi wartościami .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja platformy ASP.NET Core z bazą danych SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)