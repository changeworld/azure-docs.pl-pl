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
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 82a1dc293a019e4a48760ccbce830d067f2d620d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240942"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](./containers/quickstart-dotnetcore.md).
>

Usługa [Azure App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie.

Ten przewodnik Szybki start zawiera informacje dotyczące sposobu wdrożenia pierwszej aplikacji internetowej ASP.NET Core w usłudze Azure App Service. Po zakończeniu będzie istnieć grupa zasobów składająca się z planu usługi App Service i aplikacji usługi App Service wraz z wdrożoną aplikacją internetową.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> z obciążeniem **programowania ASP.NET i sieci Web** .

Jeśli zainstalowano już program Visual Studio 2019:

- Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc** > **Sprawdź dostępność aktualizacji**.
- Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Utwórz aplikację sieci Web ASP.NET Core, wykonując następujące czynności:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**Znajdź i wybierz **ASP.NET Core aplikacji sieci Web** dla C#, a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**Nadaj nazwę aplikacji _myFirstAzureWebApp_, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Na potrzeby tego przewodnika Szybki Start wybierz szablon **aplikacja sieci Web** . Upewnij się, że uwierzytelnianie jest ustawione na **Brak uwierzytelniania** i żadna inna opcja nie jest zaznaczona. Wybierz pozycję **Utwórz**.

   ![Wybierz ASP.NET Core Razor Pages dla tego samouczka](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej ASP.NET Core.

1. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   ![Uruchamianie aplikacji lokalnie](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**.

1. Wybierz **App Service** a następnie wybierz pozycję **Publikuj**.

   ![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. W **App Service utworzyć nowe**opcje zależą od tego, czy użytkownik jest zalogowany na platformie Azure, czy masz konto programu Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto** lub **Zaloguj** się, aby zalogować się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz odpowiednie konto.

   > [!NOTE]
   > Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
   >

   ![Logowanie do platformy Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. W obszarze **Grupa zasobów**wybierz pozycję **Nowy**.

1. W polu **Nazwa nowej grupy zasobów wprowadź nazwę** *zasobu* , a następnie wybierz **przycisk OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. W przypadku **planu hostingu**wybierz pozycję **Nowy**.

1. W oknie dialogowym **Konfigurowanie planu hostingu** wprowadź wartości z poniższej tabeli, a następnie wybierz przycisk **OK**.

   | Ustawienie | Sugerowana wartość | Opis |
   |-|-|-|
   |Plan usługi App Service| myAppServicePlan | Nazwa planu usługi App Service. |
   | Location | Europa Zachodnia | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | Size | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

   ![Tworzenie planu usługi App Service](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. W polu **Nazwa**wprowadź unikatową nazwę aplikacji, która zawiera tylko prawidłowe znaki to `a-z`, `A-Z` `0-9`, i `-`. Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

   ![Konfigurowanie nazwy aplikacji](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

Po zakończeniu działania kreatora aplikacja internetowa ASP.NET Core zostanie opublikowana na platformie Azure, a następnie uruchomiona w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Nazwa aplikacji określona na **App Service Utwórz nową** stronę jest używana jako prefiks adresu URL w formacie `http://<app_name>.azurewebsites.net`.

**Gratulacje!** Aplikacja sieci Web ASP.NET Core działa na żywo w Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

1. W **Eksplorator rozwiązań**w projekcie Otwórz **stronę** > **index. cshtml**.

1. Zastąp dwa tagi `<div>` następującym kodem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

1. Na stronie **Publikowanie** podsumowania wybierz pozycję **Publikuj**.

   ![Strona podsumowania publikowania programu Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

1. Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać aplikacją internetową.

1. W lewym menu wybierz pozycję **App Services**, a następnie wybierz nazwę swojej aplikacji platformy Azure.

   ![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

   Zostanie wyświetlona strona Omówienie aplikacji internetowej. W tym miejscu można wykonać podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

   ![App Service w Azure Portal](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

   Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](app-service-web-tutorial-dotnetcore-sqldb.md)
