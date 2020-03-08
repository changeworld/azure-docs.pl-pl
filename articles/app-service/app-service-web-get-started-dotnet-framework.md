---
title: 'Szybki Start: Tworzenie C# aplikacji ASP.NET'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service, C# wdrażając domyślny szablon aplikacji sieci Web ASP.NET z poziomu programu Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: f2e31723dc2761007994f652d9d6c28ab3f7d47d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388200"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Tworzenie aplikacji internetowej środowiska ASP.NET Framework na platformie Azure

Usługa [Azure App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie.

Ten przewodnik Szybki start zawiera informacje dotyczące sposobu wdrożenia pierwszej aplikacji internetowej ASP.NET w usłudze Azure App Service. Po zakończeniu będziesz mieć plan App Service. Będziesz również mieć aplikację App Service ze wdrożoną aplikacją sieci Web.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> z obciążeniem **programowania ASP.NET i sieci Web** .

Jeśli zainstalowano już program Visual Studio 2019:

- Zainstaluj najnowsze aktualizacje w programie Visual Studio, wybierając pozycję **pomoc** > **sprawdzić dostępność aktualizacji**.
- Dodaj obciążenie, wybierając pozycję **narzędzia** > **Pobierz narzędzia i funkcje**.

## Tworzenie aplikacji sieci Web ASP.NET<a name="create-and-publish-the-web-app"></a>

Utwórz aplikację internetową ASP.NET, wykonując następujące czynności:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

2. W obszarze **Utwórz nowy projekt**Znajdź i wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** , a następnie wybierz pozycję **dalej**.

3. W obszarze **Konfigurowanie nowego projektu**Nadaj nazwę aplikacji _myFirstAzureWebApp_, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej platformy ASP.NET. Na potrzeby tego przewodnika Szybki Start wybierz szablon **MVC** .

5. Upewnij się, że uwierzytelnianie jest ustawione na wartość **bez uwierzytelniania**. Wybierz pozycję **Utwórz**.

   ![Tworzenie aplikacji sieci Web ASP.NET](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Z menu programu Visual Studio wybierz kolejno opcje **debuguj** > **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   ![Uruchamianie aplikacji lokalnie](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Publikowanie aplikacji sieci Web<a name="launch-the-publish-wizard"></a>

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**.

1. Wybierz **App Service** a następnie zmień **profil tworzenia** do **opublikowania**.

   ![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. W **App Service utworzyć nowe**opcje zależą od tego, czy użytkownik jest zalogowany na platformie Azure, czy masz konto programu Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto** lub **Zaloguj** się, aby zalogować się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz odpowiednie konto.

   > [!NOTE]
   > Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
   >
   >

   ![Logowanie do platformy Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. W obszarze **Grupa zasobów**wybierz pozycję **Nowy**.

1. W polu **Nazwa nowej grupy zasobów wprowadź nazwę** *zasobu* , a następnie wybierz **przycisk OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. W obszarze **Plan hostingu**wybierz pozycję **Nowy**.

1. W oknie dialogowym **Konfigurowanie planu hostingu** wprowadź wartości z poniższej tabeli, a następnie wybierz przycisk **OK**.

   | Ustawienie | Sugerowana wartość | Opis |
   |-|-|-|
   |Plan usługi App Service| myAppServicePlan | Nazwa planu usługi App Service. |
   | Lokalizacja | Europa Zachodnia | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

   ![Tworzenie planu usługi App Service](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. W polu **Nazwa**wprowadź unikatową nazwę aplikacji, która zawiera tylko prawidłowe znaki to `a-z`, `A-Z`, `0-9`i `-`. Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

2. Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

   ![Konfigurowanie nazwy aplikacji](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Po zakończeniu działania kreatora aplikacja internetowa ASP.NET zostanie opublikowana na platformie Azure, a następnie uruchomiona w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Nazwa aplikacji określona na **App Service Utwórz nową** stronę jest używana jako prefiks adresu URL w formacie `http://<app_name>.azurewebsites.net`.

**Gratulacje!** Aplikacja sieci Web ASP.NET działa na żywo w Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

1. W **Eksplorator rozwiązań**w projekcie otwórz **Widok** > **Strona główna** > **index. cshtml**.

1. Znajdź tag HTML `<div class="jumbotron">` u góry i zastąp cały element następującym kodem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**. Następnie wybierz pozycję **Publikuj**.

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

1. Aby zarządzać aplikacją sieci Web, przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

   ![Wybieranie usługi App Services](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

   ![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Zostanie wyświetlona strona Omówienie aplikacji internetowej. W tym miejscu można wykonać podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

   ![Omówienie App Service w Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Platforma .ASP.NET z usługą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
