---
title: Skrypt programu PowerShell, aby utworzyć zasobu usługi Application Insights | Dokumentacja firmy Microsoft
description: Zautomatyzować tworzenie zasobów usługi Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: d06b44246b694c0d2a83503ecd1ae0cedfadd9ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Skrypt programu PowerShell do tworzenia zasobu usługi Application Insights


Jeśli chcesz monitorować nową aplikację — lub nowej wersji aplikacji — z [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), konfigurowanie nowego zasobu w systemie Microsoft Azure. Ten zasób jest, gdzie przeanalizowane i wyświetlić dane telemetryczne z aplikacji. 

Tworzenie nowego zasobu można zautomatyzować za pomocą programu PowerShell.

Na przykład jeśli tworzysz aplikacji urządzenia przenośnego jest prawdopodobne, że w dowolnym momencie, będą kilka wersji opublikowanej aplikacji używany przez klientów. Nie chcesz uzyskać wyniki dane telemetryczne z różnych wersji zamienione. Dlatego możesz uzyskać procesu kompilacji, aby utworzyć nowy zasób dla każdej kompilacji.

> [!NOTE]
> Jeśli chcesz utworzyć zestaw zasobów, wszystko na tym samym czasie, należy wziąć pod uwagę [tworzenie zasobów przy użyciu szablonu Azure](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skrypt w celu utworzenia zasobu usługi Application Insights
Zobacz specyfikacji odpowiednie polecenie cmdlet:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Skrypt programu PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

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


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Co należy zrobić iKey
Każdy zasób jest identyfikowane za pomocą klucza Instrumentacji (iKey). IKey jest dane wyjściowe skryptu tworzenia zasobu. Skrypt kompilacji powinien zapewnić iKey do zestawu SDK usługi Application Insights osadzonego w aplikacji.

Istnieją dwa sposoby, aby udostępnić iKey zestawu SDK:

* W [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Lub [kod inicjujący](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Zobacz także
* [Tworzenie usługi Application Insights i zasobów testu sieci web za pomocą szablonów](app-insights-powershell.md)
* [Konfigurowanie monitorowania diagnostyki Azure przy użyciu programu PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Ustaw alerty za pomocą programu PowerShell](app-insights-powershell-alerts.md)

