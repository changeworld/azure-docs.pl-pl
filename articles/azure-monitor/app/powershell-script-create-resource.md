---
title: Skrypt programu PowerShell służący do tworzenia zasobu Application Insights | Microsoft Docs
description: Automatyzowanie tworzenia zasobów Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820689"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Skrypt programu PowerShell do tworzenia zasobu usługi Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Jeśli chcesz monitorować nową aplikację lub nową wersję aplikacji — za pomocą [usługi Azure Application Insights](https://azure.microsoft.com/services/application-insights/)skonfigurujesz nowy zasób w Microsoft Azure. Ten zasób to miejsce, w którym dane telemetryczne z aplikacji są analizowane i wyświetlane. 

Możesz zautomatyzować tworzenie nowego zasobu przy użyciu programu PowerShell.

Na przykład w przypadku tworzenia aplikacji urządzenia przenośnego jest to możliwe, ponieważ w dowolnym momencie będzie można korzystać z kilku opublikowanych wersji aplikacji. Nie chcesz uzyskać danych telemetrycznych z różnych wersji. Dlatego proces kompilacji umożliwia utworzenie nowego zasobu dla każdej kompilacji.

> [!NOTE]
> Jeśli chcesz utworzyć zestaw zasobów wszystko w tym samym czasie, rozważ [utworzenie zasobów przy użyciu szablonu platformy Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skrypt służący do tworzenia zasobów Application Insights
Zobacz odpowiednie specyfikacje poleceń cmdlet:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Skrypt programu PowerShell*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Co zrobić z iKey
Każdy zasób jest identyfikowany przez swój klucz Instrumentacji (iKey). IKey jest wyjściem skryptu tworzenia zasobów. Skrypt kompilacji powinien udostępnić Application Insights iKey zestaw SDK osadzony w aplikacji.

Istnieją dwa sposoby udostępnienia iKey dla zestawu SDK:

* W [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Lub w [kodzie inicjalizacji](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Zobacz także
* [Tworzenie zasobów testowania Application Insights i sieci Web z szablonów](powershell.md)
* [Konfigurowanie monitorowania diagnostyki platformy Azure przy użyciu programu PowerShell](powershell-azure-diagnostics.md) 
* [Ustawianie alertów przy użyciu programu PowerShell](powershell-alerts.md)

