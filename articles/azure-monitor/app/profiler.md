---
title: Aplikacje usługi Azure App Service na żywo profiluje z usługą Application Insights | Dokumenty firmy Microsoft
description: Profiluj aplikacje na żywo w usłudze Azure App Service za pomocą aplikacji Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275778"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Aplikacje usługi Azure App Service na żywo profiluje z usługą Application Insights

Profiler można uruchomić w aplikacjach ASP.NET i ASP.NET Core, które są uruchomione w usłudze Azure App Service przy użyciu warstwy usługi Basic lub wyższej. Włączenie Profilera w systemie Linux jest obecnie możliwe tylko za pomocą [tej metody](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>Włącz profiler dla swojej aplikacji
Aby włączyć profiler dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, oto instrukcje dotyczące włączania programu Profiler na innych obsługiwanych platformach:
* [Usługi w chmurze](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje sieci szkieletowej usług](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Profiler usługi Application Insights jest wstępnie zainstalowany jako część środowiska wykonawczego usług aplikacji. Poniższe kroki pokażą, jak włączyć go dla usługi app service. Wykonaj następujące kroki, nawet jeśli masz włączone App Insights SDK w aplikacji w czasie kompilacji.

1. Włącz ustawienie "Zawsze włączone" dla usługi aplikacji. Ustawienie można zaktualizować na stronie Konfiguracja usługi app service w obszarze Ustawienia ogólne.
1. Przejdź do okienka **Usługi aplikacji** w witrynie Azure portal.
1. Przejdź do **okienka Ustawienia > aplikacji.**

   ![Włączanie statystyk aplikacji w portalu usług app services](./media/profiler/AppInsights-AppServices.png)

1. Postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że profiler jest **włączony**. Jeśli zasób usługi Application Insights znajduje się w innej subskrypcji niż usługa App Service, nie można użyć tej strony do skonfigurowania usługi Application Insights. Nadal można to zrobić ręcznie, tworząc niezbędne ustawienia aplikacji ręcznie. [Następna sekcja zawiera instrukcje dotyczące ręcznego włączania profilera.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Dodawanie rozszerzenia witryny usługi App Insights][Enablement UI]

1. Profiler jest teraz włączony przy użyciu ustawienia aplikacji usługi App Services.

    ![Ustawienie aplikacji dla profilera][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Włącz profiler ręcznie lub za pomocą usługi Azure Resource Manager
Profiler usługi Application Insights można włączyć, tworząc ustawienia aplikacji dla usługi Azure App Service. Strona z opcjami pokazanymi powyżej tworzy te ustawienia aplikacji dla Ciebie. Ale można zautomatyzować tworzenie tych ustawień za pomocą szablonu lub w inny sposób. Te ustawienia będą również działać, jeśli zasób usługi Application Insights znajduje się w innej subskrypcji niż usługa Azure App Service.
Oto ustawienia potrzebne do włączenia profilera:

|Ustawienia aplikacji    | Wartość    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Aplikacja iKey dla zasobu usługi Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Wartości te można ustawić za pomocą [szablonów usługi Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), Azure [Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Ręczne włączanie profilera dla innych chmur

Jeśli chcesz włączyć profiler dla innych chmur, możesz użyć poniższych ustawień aplikacji.

|Ustawienia aplikacji    | Wartości rządu USA| Chińska chmura |   
|---------------|---------------------|-------------|
|AplikacjaInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|Punkt widokowy aplikacji | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Wyłącz profiler

Aby zatrzymać lub ponownie uruchomić profiler dla wystąpienia poszczególnych aplikacji, w obszarze **Zadania sieci Web**przejdź do zasobu aplikacji. Aby usunąć profiler, przejdź do **rozszerzenia**.

![Wyłącz profiler dla zadania sieci Web][disable-profiler-webjob]

Firma Microsoft zaleca, aby mieć profiler włączone we wszystkich aplikacjach, aby odkryć wszelkie problemy z wydajnością tak wcześnie, jak to możliwe.

Pliki profilera można usunąć podczas korzystania z funkcji WebDeploy w celu wdrożenia zmian w aplikacji sieci web. Można zapobiec usunięciu, wykluczając usunięcie folderu App_Data podczas wdrażania. 


## <a name="next-steps"></a>Następne kroki

* [Praca z usługą Application Insights w programie Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
