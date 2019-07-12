---
title: Włączanie rozszerzenia Snapshot Debugger dla aplikacji .NET w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Włączanie rozszerzenia Snapshot Debugger dla aplikacji .NET w usłudze Azure App Service
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 3e8ce3c2eff7b1f7184bb37f141e62563d4fe714
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612683"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Włączanie rozszerzenia Snapshot Debugger dla aplikacji .NET w usłudze Azure App Service

Rozszerzenie Snapshot Debugger obecnie działa w przypadku aplikacji ASP.NET i ASP.NET Core, które są uruchomione w usłudze Azure App Service w planach usługi Windows.

## <a id="installation"></a> Włączanie rozszerzenia Snapshot Debugger
Aby włączyć rozszerzenie Snapshot Debugger dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, poniżej przedstawiono instrukcje umożliwiające rozszerzenie Snapshot Debugger innych obsługiwanych platform:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Zestawy skalowania maszyn wirtualnych i maszyn wirtualnych platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalnych maszyn wirtualnych lub fizycznych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Jeśli używasz wersji zapoznawczej platformy .NET Core, wykonaj instrukcje dotyczące [Włączanie rozszerzenia Snapshot Debugger dla innych środowisk](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) najpierw obejmujący [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet pakietu z aplikacją, a następnie wypełnij resztę z poniższymi instrukcjami. 

Application Insights Snapshot Debugger jest wstępnie zainstalowany jako część środowiska uruchomieniowego usług aplikacji, ale należy włączyć je w celu get migawek dla aplikacji usługi app Service. Po wdrożeniu aplikacji, nawet wtedy, gdy zestaw SDK usługi Application Insights wprowadzono w kodzie źródłowym, postępuj zgodnie z instrukcjami poniżej, aby włączyć rozszerzenie snapshot debugger.

1. Przejdź do **App Services** okienko w witrynie Azure portal.
2. Przejdź do **Ustawienia > Application Insights** okienka.

   ![Włączanie usługi App Insights w portalu usługi App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Albo postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób lub wybierz istniejący zasób usługi App Insights w celu monitorowania aplikacji. Upewnij się, oba przełączniki dla rozszerzenia Snapshot Debugger są również **na**.

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

4. Rozszerzenie Snapshot Debugger jest teraz włączony, za pomocą ustawienia aplikacji usługi aplikacji.

    ![Ustawienia aplikacji dla rozszerzenia Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Wyłącz rozszerzenia Snapshot Debugger

Wykonaj te same czynności, jak w przypadku **Włączanie rozszerzenia Snapshot Debugger**, ale przełączyć oba przełączniki dla rozszerzenia Snapshot Debugger do **poza**.
Zaleca się, że masz rozszerzenia Snapshot Debugger włączone na wszystkie Twoje aplikacje działają nawet pod dużym diagnostyka wyjątków aplikacji.

## <a name="next-steps"></a>Następne kroki

- Generowanie ruchu do aplikacji, które mogą wyzwalać wyjątek. Poczekaj 10 do 15 minut dla migawek do wysłania do wystąpienia usługi Application Insights.
- Zobacz [migawek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów rozszerzenia Snapshot Debugger, zobacz [Rozwiązywanie problemów z rozszerzenia Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

