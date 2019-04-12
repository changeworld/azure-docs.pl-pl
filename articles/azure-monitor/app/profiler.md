---
title: Profilowanie na żywo aplikacji usługi Azure App Service za pomocą usługi Application Insights | Dokumentacja firmy Microsoft
description: Profil aplikacji na żywo w usłudze Azure App Service za pomocą Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493743"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilowanie na żywo aplikacji usługi Azure App Service za pomocą usługi Application Insights

Możesz uruchomić Profiler w ASP.NET i ASP.NET Core aplikacje działające w usłudze Azure App Service w warstwie usług w warstwie podstawowa lub wyższej. Włączanie Profiler w systemie Linux jest obecnie możliwe tylko za pośrednictwem [ta metoda](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Włącz Profiler do aplikacji
Aby włączyć Profiler dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli korzystasz z innego typu usługi platformy Azure, w tym miejscu znajdują się instrukcje dotyczące włączania Profiler na innych platformach obsługiwanych:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje usługi Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler jest wstępnie zainstalowany jako część środowiska uruchomieniowego usługi aplikacji. Poniższe kroki pokazują jak go włączyć dla usługi App Service. Wykonaj następujące kroki, nawet wtedy, gdy zostały dołączone do zestawu SDK usługi Insights aplikacji w aplikacji w czasie kompilacji.

1. Włącz ustawienie "Zawsze włączone" dla usługi app service. Możesz zaktualizować ustawienia na stronie konfiguracji usługi App Service w obszarze Ustawienia ogólne.
1. Przejdź do **App Services** okienko w witrynie Azure portal.
1. Przejdź do **Ustawienia > Application Insights** okienka.

   ![Włączanie usługi App Insights w portalu usługi App Services](./media/profiler/AppInsights-AppServices.png)

1. Albo postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób lub wybierz istniejący zasób usługi App Insights w celu monitorowania aplikacji. Upewnij się, Profiler jest również **na**. Jeśli zasób usługi Application Insights znajduje się w innej subskrypcji z usługi App Service, ta strona nie służy do konfigurowania usługi Application Insights. Możesz nadal zrobić to ręcznie chociaż przez tworzenie ustawień aplikacji konieczne ręcznie. [Następna sekcja zawiera instrukcje dotyczące Ręczne włączanie Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

1. Profiler jest teraz włączony, za pomocą ustawienia aplikacji usługi aplikacji.

    ![Ustawienia aplikacji dla Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Włącz Profiler, ręcznie lub za pomocą usługi Azure Resource Manager
Application Insights Profiler można włączyć poprzez utworzenie ustawienia aplikacji dla usługi Azure App Service. Strony z opcjami powyżej tworzy następujące ustawienia aplikacji. Jednak aby zautomatyzować tworzenie tych ustawień, korzystając z szablonu lub w inny sposób. Te ustawienia również będzie działać, jeśli zasób usługi Application Insights znajduje się w innej subskrypcji z usługi Azure App Service.
Poniżej przedstawiono ustawienia wymagane do włączenia profilera:

|Ustawienia aplikacji    | Wartość    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | klucz Instrumentacji zasobu usługi Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Możesz ustawić te wartości przy użyciu [szablonów usługi Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [programu Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Wyłącz Profiler

Aby zatrzymać lub ponownie uruchomić Profiler dla wystąpienia poszczególnych aplikacji, w obszarze **zadania Web Job**, przejdź do zasobu aplikacji. Aby usunąć Profiler, przejdź do **rozszerzenia**.

![Wyłącz Profiler dla zadania w sieci web][disable-profiler-webjob]

Zaleca się, że masz Profiler włączone na wszystkich Twoich aplikacji do wykrywania problemów z wydajnością tak szybko, jak to możliwe.

Pliki przez Profiler można usunąć, gdy za pomocą usługi WebDeploy Aby wdrożyć zmiany dla aplikacji sieci web. Usuwanie może uniemożliwić wykluczając App_Data folder przed usunięciem podczas wdrażania. 


## <a name="next-steps"></a>Kolejne kroki

* [Praca z usługą Application Insights w programie Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
