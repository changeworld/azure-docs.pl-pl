---
title: Profil działających aplikacji sieci web platformy Azure za pomocą usługi Application Insights | Dokumentacja firmy Microsoft
description: Profil aplikacji internetowych na żywo na platformie Azure za pomocą Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 2f726c9c70e6e46ad2e82e9d6f15dae2c9d3d008
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724693"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil działających aplikacji sieci web platformy Azure za pomocą usługi Application Insights

Profiler działa aktualnie w przypadku aplikacji sieci web ASP.NET i ASP.NET Core uruchomionych w aplikacji sieci Web. Podstawowa warstwę usługi lub nowszy jest wymagany, aby użyć Profiler.

## <a id="installation"></a> Włącz Profiler dla aplikacji sieci Web
Aby włączyć Profiler dla aplikacji sieci web, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, poniżej przedstawiono instrukcje dotyczące włączania Profiler na innych obsługiwanych platformach:
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje usługi Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler jest wstępnie zainstalowany jako część środowiska uruchomieniowego usług aplikacji, ale należy włączyć je w celu profile get dla aplikacji sieci Web platformy Azure. Po wdrożeniu aplikacji sieci Web, nawet wtedy, gdy zestaw SDK aplikacji usługi Insights wprowadzono w kodzie źródłowym, postępuj zgodnie z instrukcjami poniżej, aby włączyć program profilujący.

1. Przejdź do **App Services** okienko w witrynie Azure portal.
1. Przejdź do **Ustawienia > Monitorowanie** okienka.

   ![Włączanie usługi App Insights w portalu usługi App Services](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Albo postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób lub wybierz istniejący zasób usługi App Insights w celu monitorowania aplikacji sieci web. Upewnij się, Profiler jest również **na**.

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

1. Profiler jest teraz włączony, za pomocą ustawienia aplikacji usługi aplikacji.

    ![Ustawienia aplikacji dla Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Wyłącz Profiler

Aby zatrzymać lub ponownie uruchom program Profiler aplikację internetową poszczególne wystąpienia, w obszarze **zadania Web Job**, przejdź do zasobu aplikacji sieci Web. Aby usunąć Profiler, przejdź do **rozszerzenia**.

![Wyłącz Profiler dla zadania w sieci web][disable-profiler-webjob]

Zaleca się, że masz Profiler włączone na wszystkich Twoich aplikacji sieci web do wykrywania problemów z wydajnością tak szybko, jak to możliwe.

Jeśli używasz WebDeploy, aby wdrożyć zmiany dla aplikacji sieci web, upewnij się, że w folderze App_Data można wykluczyć z usuwana podczas wdrażania. W przeciwnym razie rozszerzenia Profiler pliki są usuwane przy następnym wdrożysz aplikację sieci web na platformie Azure.



## <a name="next-steps"></a>Kolejne kroki

* [Praca z usługą Application Insights w programie Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

