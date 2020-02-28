---
title: Włącz Snapshot Debugger dla aplikacji .NET w Azure App Service | Microsoft Docs
description: Włącz Snapshot Debugger dla aplikacji .NET w programie Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671430"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Włącz Snapshot Debugger dla aplikacji .NET w programie Azure App Service

Snapshot Debugger obecnie działa dla aplikacji ASP.NET i ASP.NET Core, które działają w Azure App Service planach usług systemu Windows.

## <a id="installation"></a>Włącz Snapshot Debugger
Aby włączyć Snapshot Debugger dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, poniżej przedstawiono instrukcje dotyczące włączania Snapshot Debugger na innych obsługiwanych platformach:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Jeśli używasz wersji zapoznawczej programu .NET Core, postępuj zgodnie z instrukcjami dotyczącymi [włączania Snapshot Debugger w innych środowiskach](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , aby uwzględnić pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) z aplikacją, a następnie wykonaj pozostałe instrukcje poniżej. 

Application Insights Snapshot Debugger jest wstępnie zainstalowana jako część środowiska uruchomieniowego App Services, ale musisz ją włączyć, aby uzyskać migawki dla aplikacji App Service. Po wdrożeniu aplikacji, nawet jeśli w kodzie źródłowym dołączono zestaw SDK Application Insights, wykonaj poniższe kroki, aby włączyć debuger migawek.

1. Przejdź do okienka **App Services** w Azure Portal.
2. Przejdź do okna **ustawienia > Application Insights** .

   ![Włączanie usługi App Insights w portalu usługi App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób, lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że oba przełączniki Snapshot Debugger są **włączone**.

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

4. Snapshot Debugger jest teraz włączona przy użyciu ustawienia aplikacji App Services.

    ![Ustawienie aplikacji dla Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Wyłącz Snapshot Debugger

Wykonaj te same czynności co w przypadku **Snapshot Debugger Włącz**, ale Przełącz oba przełączniki Snapshot Debugger na **wyłączone**.
Zalecamy, aby na wszystkich Twoich aplikacjach była włączona Snapshot Debugger, co ułatwia diagnostykę wyjątków aplikacji.

## <a name="next-steps"></a>Następne kroki

- Generuj ruch do aplikacji, która może wyzwolić wyjątek. Następnie odczekaj od 10 do 15 minut na wysłanie migawek do wystąpienia Application Insights.
- Zobacz [migawki](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w Azure Portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Snapshot Debugger, zobacz [Snapshot Debugger Rozwiązywanie problemów](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

