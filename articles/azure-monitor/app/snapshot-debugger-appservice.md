---
title: Włącz debuger migawek dla aplikacji platformy .NET w usłudze Azure App Service | Dokumenty firmy Microsoft
description: Włącz debuger migawek dla aplikacji platformy .NET w usłudze Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298275"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Włącz debuger migawek dla aplikacji platformy .NET w usłudze Azure App Service

Debuger migawek obecnie działa dla ASP.NET i ASP.NET podstawowych aplikacji, które są uruchomione w usłudze Azure App Service w planach usług systemu Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Włączanie debugera migawek
Aby włączyć debuger migawek dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, oto instrukcje dotyczące włączania debugera migawek na innych obsługiwanych platformach:
* [usług Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi sieci szkieletowej usług Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Jeśli używasz wersji zapoznawczej .NET Core, postępuj zgodnie z instrukcjami [dotyczącymi Włączanie debugera migawek dla innych środowisk,](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) aby najpierw dołączyć pakiet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet z aplikacją, a następnie wykonaj pozostałe instrukcje poniżej. 

Debuger migawek usługi Application Insights jest wstępnie zainstalowany jako część środowiska wykonawczego usług App Services, ale musisz go włączyć, aby uzyskać migawki dla aplikacji usługi App Service. Po wdrożeniu aplikacji, nawet jeśli zostały uwzględnione SDK usługi Application Insights w kodzie źródłowym, wykonaj poniższe kroki, aby włączyć debuger migawki.

1. Przejdź do okienka **Usługi aplikacji** w witrynie Azure portal.
2. Przejdź do **okienka Ustawienia > aplikacji.**

   ![Włączanie statystyk aplikacji w portalu usług app services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że oba przełączniki debugera migawki są **włączone**.

   ![Dodawanie rozszerzenia witryny usługi App Insights][Enablement UI]

4. Debuger migawek jest teraz włączony przy użyciu ustawienia aplikacji usługi App Services.

    ![Ustawienie aplikacji dla debugera migawek][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Wyłącz debuger migawki

Wykonaj te same kroki, co w przypadku **włączania debugera migawek,** ale przełącz oba przełączniki debugera migawek na **Wyłączone**.
Zaleca się, że debuger migawek włączone we wszystkich aplikacjach, aby ułatwić diagnostykę wyjątków aplikacji.

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

W przypadku usługi Azure App Service można ustawić ustawienia aplikacji w szablonie usługi Azure Resource Manager, aby włączyć debuger migawek i profiler. Dodaj zasób konfiguracowy, który zawiera ustawienia aplikacji jako zasób podrzędny witryny sieci Web:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Następne kroki

- Generowanie ruchu do aplikacji, która może wyzwolić wyjątek. Następnie poczekaj od 10 do 15 minut, aby migawki zostały wysłane do wystąpienia usługi Application Insights.
- Zobacz [migawki](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów z debugerem migawek, zobacz [Rozwiązywanie problemów z debugerem migawek](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

