---
title: 'Szybki Start: uruchamianie aplikacji ASP.NET Core systemu Linux'
description: Zacznij korzystać z aplikacji systemu Linux na Azure App Service, wdrażając pierwszą aplikację ASP.NET Core do kontenera systemu Linux w App Service.
keywords: azure app service, aplikacja internetowa, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: seodec18
ms.openlocfilehash: ea9449a89bcfbdb1378f0cceb31a07a288661bf7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687557"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Tworzenie aplikacji platformy ASP.NET Core w usłudze App Service dla systemu Linux

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Linux. Aby wdrożyć aplikację w usłudze App Service w systemie _Windows_, zobacz [Tworzenie aplikacji ASP.NET Core na platformie Azure](../app-service-web-get-started-dotnet.md).
>

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. Ten przewodnik szybkiego startu pokazuje, jak utworzyć aplikację platformy [.NET Core](https://docs.microsoft.com/aspnet/core/) w usłudze App Service w systemie Linux. Aplikację możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie kodu platformy .NET Core w aplikacji.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Kroki podane w tym artykule możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://www.microsoft.com/net/core/" target="_blank">Zainstaluj program .NET Core</a>

## <a name="create-the-app-locally"></a>Lokalne tworzenie aplikacji

W oknie terminala na komputerze utwórz katalog o nazwie `hellodotnetcore` i zmień bieżący katalog na niego.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Utwórz nową aplikację .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. 

Przywróć pakiety NuGet i uruchom aplikację.

```bash
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `http://localhost:5000`.

Na stronie zostanie wyświetlony komunikat **Hello World** z przykładowej aplikacji.

![Test przy użyciu przeglądarki](media/quickstart-dotnetcore/dotnet-browse-local.png)

W oknie terminalu naciśnij kombinację klawiszy **Ctrl + C**, aby zamknąć serwer sieci Web. Inicjowanie repozytorium Git dla projektu platformy .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Przejdź do nowo utworzonej aplikacji. Zastąp _ciąg&lt;App-name >_ nazwą swojej aplikacji.

```bash
http://<app-name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja:

![Pusta strona aplikacji](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

Przykładowy kod .NET Core jest uruchamiany w usłudze App Service dla systemu Linux z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację platformy .NET Core w usłudze App Service w systemie Linux.

## <a name="update-and-redeploy-the-code"></a>Aktualizowanie i ponowne wdrażanie kodu

W katalogu lokalnym otwórz plik _Startup.cs_. Wprowadź niewielką zmianę w tekście wywołania metody `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Zatwierdź zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po zakończeniu wdrożenia przejdź z powrotem do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie kliknij przycisk Odśwież.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-dotnetcore/portal-app-service-list.png)

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)
