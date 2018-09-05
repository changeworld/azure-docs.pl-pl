---
title: Tworzenie aplikacji internetowej środowiska C# ASP.NET Framework na platformie Azure | Microsoft Docs
description: Dowiedz się, jak uruchamiać aplikacje internetowe w usłudze Azure App Service, wdrażając przykładową aplikację internetową ASP.NET w języku C#.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/29/2018
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: f6f443dfe637e68b0e1e13f7c3b9e718ae48a85d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191309"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Tworzenie aplikacji internetowej środowiska ASP.NET Framework na platformie Azure

Usługa [Azure Web Apps](app-service-web-overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostowaną w Internecie.  Ten przewodnik Szybki start zawiera informacje dotyczące sposobu wdrożenia pierwszej aplikacji internetowej ASP.NET w usłudze Azure App Service. Po zakończeniu będzie istnieć grupa zasobów składająca się z planu usługi App Service i aplikacji internetowej platformy Azure wraz z wdrożoną aplikacją internetową.

![](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

Zainstaluj program <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**.

Jeśli program Visual Studio został już zainstalowany, dodaj obciążenia w programie Visual Studio, klikając kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

W programie Visual Studio utwórz nowy projekt, wybierając pozycję **Plik > Nowy > Projekt**. 

W oknie dialogowym **Nowy projekt** kliknij pozycję **Visual C# &gt; Internet &gt; Aplikacja internetowa ASP.NET (program .NET Framework)**.

Nadaj aplikacji nazwę _myFirstAzureWebApp_, a następnie kliknij przycisk **OK**.
   
![Okno dialogowe Nowy projekt](./media/app-service-web-get-started-dotnet-framework/new-project.png)

Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej platformy ASP.NET. Do celów tego przewodnika Szybki start wybierz szablon **MVC** i upewnij się, że uwierzytelnianie jest ustawione na wartość **Bez uwierzytelniania**.
      
Kliknij przycisk **OK**.

![Okno dialogowe Nowy projekt ASP.NET](./media/app-service-web-get-started-dotnet-framework/select-mvc-template.png)

Z menu wybierz pozycję **Debuguj > Uruchom bez debugowania**, aby lokalnie uruchomić aplikację internetową.

![Uruchamianie aplikacji lokalnie](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp**, a następnie wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-get-started-dotnet-framework/solution-explorer-publish.png)

Upewnij się, że pozycja **Microsoft Azure App Service** jest zaznaczona, a następnie kliknij przycisk **Publikuj**.

![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-dotnet-framework/publish-to-app-service.png)

Spowoduje to otwarcie okna dialogowego **Tworzenie usługi App Service**, które ułatwia tworzenie wszystkich zasobów platformy Azure niezbędnych do uruchomienia aplikacji internetowej ASP.NET na platformie Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

W oknie dialogowym **Tworzenie usługi App Service** wybierz pozycję **Dodaj konto** i zaloguj się do swojej subskrypcji platformy Azure. Po zalogowaniu wybierz z listy rozwijanej konto zawierające odpowiednią subskrypcję.

> [!NOTE]
> Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
>
>
   
![Logowanie do platformy Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj grupie zasobów nazwę **myResourceGroup**, a następnie kliknij przycisk **OK**.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Obok pozycji **Plan usługi App Service** wybierz pozycję **Nowy**. 

W oknie dialogowym **Konfiguruj plan usługi App Service** użyj ustawień znajdujących się w tabeli poniżej zrzutu ekranu.

![Tworzenie planu usługi App Service](./media/app-service-web-get-started-dotnet-framework/configure-app-service-plan.png)

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| myAppServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Europa Zachodnia | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

Kliknij przycisk **OK**.

## <a name="create-and-publish-the-web-app"></a>Tworzenie i publikowanie aplikacji internetowej

W polu **Nazwa aplikacji internetowej** wpisz unikatową nazwę aplikacji (dozwolone znaki to `a-z`, `0-9` i `-`) lub zaakceptuj automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji internetowej.

Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

![Konfigurowanie nazwy aplikacji internetowej](./media/app-service-web-get-started-dotnet-framework/web-app-name.png)

Po zakończeniu działania kreatora aplikacja internetowa ASP.NET zostanie opublikowana na platformie Azure, a następnie uruchomiona w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Nazwa aplikacji internetowej określona w [kroku tworzenia i publikowania](#create-and-publish-the-web-app) jest używana jako prefiks adresu URL w formacie `http://<app_name>.azurewebsites.net`.

Gratulacje, Twoja aplikacja internetowa ASP.NET działa w usłudze Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Za pomocą **Eksploratora rozwiązań** otwórz plik _Views\Home\Index.cshtml_.

Znajdź tag HTML `<div class="jumbotron">` u góry i zastąp cały element następującym kodem:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

Na stronie publikowania wybierz pozycję **Publikuj**.
![Strona publikowania podsumowania programu Visual Studio](./media/app-service-web-get-started-dotnet-framework/publish-summary-page.png)

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać aplikacją internetową.

W lewym menu wybierz pozycję **App Services**, a następnie wybierz nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji internetowej platformy Azure](./media/app-service-web-get-started-dotnet-framework/access-portal.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Blok usługi App Service w witrynie Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-blade.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

## <a name="video"></a>Połączenia wideo

Obejrzyj wideo, aby zobaczyć, jak działa ten przewodnik Szybki start, a następnie wykonaj kroki samodzielnie, aby opublikować swoją pierwszą aplikację .NET na platformie Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Platforma .ASP.NET z usługą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
