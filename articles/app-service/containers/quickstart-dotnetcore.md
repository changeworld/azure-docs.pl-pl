---
title: Tworzenie aplikacji internetowej .NET Core i wdrażanie jej w usłudze App Service w systemie Linux | Microsoft Docs
description: Wdróż swoją pierwszą aplikację Hello World na platformie .NET Core w usłudze App Service w systemie Linux w ciągu kilku minut.
keywords: azure app service, aplikacja internetowa, dotnet, core, linux, oss
services: app-service
documentationCenter: ''
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 04/11/2018
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 89973581d7c6aa99ab671d5bd04f258898e9235e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Linux. Aby wdrożyć aplikację w usłudze App Service w systemie _Windows_, zobacz [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../app-service-web-get-started-dotnet.md).
>

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. Ten przewodnik szybkiego startu pokazuje, jak utworzyć aplikację platformy [.NET Core](https://docs.microsoft.com/aspnet/core/) w usłudze App Service w systemie Linux. Aplikację internetową możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie kodu platformy .NET Core w aplikacji internetowej.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Kroki podane w tym artykule możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://www.microsoft.com/net/core/" target="_blank">Zainstaluj program .NET Core</a>

## <a name="create-the-app-locally"></a>Lokalne tworzenie aplikacji

W oknie terminala na komputerze utwórz katalog o nazwie `hellodotnetcore` i zmień bieżący katalog na niego.

```bash
md hellodotnetcore
cd hellodotnetcore
```

Utwórz nową aplikację internetową platformy .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Przywróć pakiety NuGet i uruchom aplikację.

```bash
dotnet restore
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

## <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Przejdź do nowo utworzonej aplikacji internetowej. Zastąp ciąg _&lt;nazwa aplikacji>_ nazwą swojej aplikacji internetowej.

```bash
http://<app name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Pusta strona aplikacji internetowej](media/quickstart-dotnetcore/dotnet-browse-created.png)

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
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

Przykładowy kod platformy .NET Core jest uruchamiany w aplikacji internetowej z wbudowanym obrazem.

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

## <a name="manage-your-new-azure-web-app"></a>Zarządzanie nową aplikacją sieci Web platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją internetową.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji sieci Web platformy Azure](./media/quickstart-dotnetcore/portal-app-service-list.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej platformy .NET Core i bazy danych SQL w usłudze Azure App Service w systemie Linux](tutorial-dotnetcore-sqldb-app.md)
