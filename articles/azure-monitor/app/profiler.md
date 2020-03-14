---
title: Profilowanie aplikacji Azure App Service na żywo za pomocą Application Insights | Microsoft Docs
description: Profilowanie aplikacji na żywo na Azure App Service z Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275778"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilowanie aplikacji Azure App Service na żywo za pomocą Application Insights

Program Profiler można uruchomić na ASP.NET i ASP.NET Core aplikacji, które są uruchomione w Azure App Service za pomocą warstwy usługi Basic lub nowszej. Włączenie profilera w systemie Linux jest obecnie możliwe tylko za pośrednictwem [tej metody](profiler-aspnetcore-linux.md).

## <a id="installation"></a>Włącz Profiler dla swojej aplikacji
Aby włączyć program Profiler dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, poniżej znajdują się instrukcje dotyczące włączania profilera na innych obsługiwanych platformach:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric aplikacji](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler jest wstępnie zainstalowany w ramach środowiska uruchomieniowego App Services. W poniższych krokach pokazano, jak włączyć ją dla App Service. Wykonaj następujące kroki, nawet jeśli zestaw SDK usługi App Insights został uwzględniony w aplikacji w czasie kompilacji.

1. Włącz ustawienie "Always On" dla usługi App Service. Możesz zaktualizować ustawienie na stronie Konfiguracja App Service w obszarze Ustawienia ogólne.
1. Przejdź do okienka **App Services** w Azure Portal.
1. Przejdź do okna **ustawienia > Application Insights** .

   ![Włączanie usługi App Insights w portalu usługi App Services](./media/profiler/AppInsights-AppServices.png)

1. Postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób, lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że profiler jest **włączony**. Jeśli zasób Application Insights znajduje się w innej subskrypcji z App Service, nie można użyć tej strony do skonfigurowania Application Insights. Można to zrobić ręcznie, tworząc ręcznie niezbędne ustawienia aplikacji. [Następna sekcja zawiera instrukcje dotyczące ręcznego włączania profilera.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

1. Profiler jest teraz włączony, za pomocą ustawienia aplikacji usługi aplikacji.

    ![Ustawienia aplikacji dla Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ręczne włączanie profilera lub Azure Resource Manager
Application Insights Profiler można włączyć, tworząc ustawienia aplikacji dla Azure App Service. Na stronie z opisanymi powyżej opcjami tworzone są te ustawienia aplikacji. Można jednak zautomatyzować tworzenie tych ustawień przy użyciu szablonu lub innych metod. Te ustawienia będą również działały, jeśli zasób Application Insights jest w innej subskrypcji z Azure App Service.
Oto ustawienia, które są konieczne do włączenia profilera:

|Ustawienia aplikacji    | Wartość    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey zasobów Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Można ustawić te wartości przy użyciu [szablonów Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Ręczne włączanie profilera dla innych chmur

Jeśli chcesz włączyć Profiler dla innych chmur, możesz użyć poniższych ustawień aplikacji.

|Ustawienia aplikacji    | Wartości dla instytucji rządowych USA| Chmura Chińska |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Wyłącz Profiler

Aby zatrzymać lub uruchomić ponownie Profiler dla wystąpienia pojedynczej aplikacji, w obszarze **zadania sieci Web**przejdź do zasobu aplikacji. Aby usunąć program Profiler, przejdź do pozycji **rozszerzenia**.

![Wyłącz Profiler dla zadania w sieci web][disable-profiler-webjob]

Zaleca się, aby na wszystkich Twoich aplikacjach był włączony Profiler, aby odnajdywał jakiekolwiek problemy z wydajnością tak szybko, jak to możliwe.

Pliki profilera można usuwać, gdy jest używany program webdeploy do wdrażania zmian w aplikacji sieci Web. Można zapobiec usunięciu przez wykluczenie folderu App_Data podczas wdrażania. 


## <a name="next-steps"></a>Następne kroki

* [Praca z Application Insights w programie Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
