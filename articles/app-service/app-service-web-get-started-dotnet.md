---
title: Tworzenie aplikacji internetowej platformy .NET Core w języku C# — Azure App Service | Microsoft Docs
description: Dowiedz się, jak uruchamiać aplikacje internetowe w usłudze Azure App Service, wdrażając przykładową aplikację internetową ASP.NET Core języka C#.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2762a81c9f366ad4a5d0d6d87cf04490fc10b23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60854636"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](./containers/quickstart-dotnetcore.md). 
>

Usługa [Azure App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie.  Ten przewodnik Szybki start zawiera informacje dotyczące sposobu wdrożenia pierwszej aplikacji internetowej ASP.NET Core w usłudze Azure App Service. Po zakończeniu będzie istnieć grupa zasobów składająca się z planu usługi App Service i aplikacji usługi App Service wraz z wdrożoną aplikacją internetową.

![](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, zainstaluj program <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> z pakietem roboczym **Programowanie dla platformy ASP.NET i sieci Web**.

Jeśli masz już zainstalowany program Visual Studio 2017:

- Zainstaluj najnowsze aktualizacje w programie Visual Studio, klikając pozycje **Pomoc** > **Sprawdź aktualizacje**.
- Dodaj pakiet roboczy, klikając pozycje **Narzędzia** > **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

W programie Visual Studio utwórz nowy projekt, wybierając pozycję **Plik > Nowy > Projekt**. 

W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C# &gt; Internet &gt; Aplikacja internetowa platformy ASP.NET Core**.

Nadaj aplikacji nazwę _myFirstAzureWebApp_, a następnie kliknij przycisk **OK**.
   
![Okno dialogowe Nowy projekt](./media/app-service-web-get-started-dotnet/new-project.png)

Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej ASP.NET Core. Do celów tego przewodnika Szybki start wybierz szablon **Aplikacja internetowa** i upewnij się, że uwierzytelnianie jest ustawione na wartość **Bez uwierzytelniania** i nie jest wybrana żadna inna opcja.
      
Kliknij przycisk **OK**.

![Okno dialogowe Nowy projekt ASP.NET](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

Z menu wybierz pozycję **Debuguj > Uruchom bez debugowania**, aby lokalnie uruchomić aplikację internetową.

![Uruchamianie aplikacji lokalnie](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="launch-the-publish-wizard"></a>Uruchamianie kreatora publikacji

W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp**, a następnie wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-get-started-dotnet/right-click-publish.png)

Kreator publikowania jest uruchamiany automatycznie. Wybierz pozycje **App Service** > **Publikuj**, aby otworzyć okno dialogowe **Utwórz usługę App Service**.

![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

W oknie dialogowym **Tworzenie usługi App Service** kliknij pozycję **Dodaj konto**, a następnie zaloguj się do swojej subskrypcji platformy Azure. Jeśli logowanie zostało już wykonane, wybierz żądane konto z listy rozwijanej.

> [!NOTE]
> Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
>
   
![Logowanie do platformy Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj grupie zasobów nazwę **myResourceGroup**, a następnie kliknij przycisk **OK**.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**. 

W oknie dialogowym **Konfiguruj plan hostingu** użyj ustawień znajdujących się w tabeli poniżej zrzutu ekranu.

![Tworzenie planu usługi App Service](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| myAppServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Europa Zachodnia | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

Kliknij przycisk **OK**.

## <a name="create-and-publish-the-web-app"></a>Tworzenie i publikowanie aplikacji internetowej

W polu **Nazwa aplikacji** wpisz unikatową nazwę aplikacji (dozwolone znaki to `a-z`, `0-9` i `-`) lub zaakceptuj automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

![Konfigurowanie nazwy aplikacji](./media/app-service-web-get-started-dotnet/web-app-name.png)

Po zakończeniu działania kreatora aplikacja internetowa ASP.NET Core zostanie opublikowana na platformie Azure, a następnie uruchomiona w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Nazwa aplikacji określona w [kroku tworzenia i publikowania](#create-and-publish-the-web-app) jest używana jako prefiks adresu URL w formacie `http://<app_name>.azurewebsites.net`.

Gratulacje, Twoja aplikacja internetowa ASP.NET Core działa w usłudze Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Za pomocą **Eksploratora rozwiązań** otwórz plik _Pages/Index.cshtml_.

Zastąp dwa tagi `<div>` następującym kodem:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

Na stronie publikowania podsumowania wybierz pozycję **Publikuj**.
![Strona publikowania podsumowania programu Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page.png)

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać aplikacją internetową.

W lewym menu wybierz pozycję **App Services**, a następnie wybierz nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-dotnet/access-portal.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Blok usługi App Service w witrynie Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](app-service-web-tutorial-dotnetcore-sqldb.md)
