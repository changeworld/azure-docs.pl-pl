---
title: Skrypt programu PowerShell, aby utworzyć zasób usługi Application Insights | Dokumentacja firmy Microsoft
description: Automatyczne tworzenie zasobów usługi Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 3fe6a89073da731332a91ece40a6ea6f667d150a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "54003515"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Skrypt programu PowerShell do tworzenia zasobu usługi Application Insights


Gdy zachodzi potrzeba monitorowania nowej aplikacji — lub nową wersję aplikacji — przy użyciu [usługi Azure Application Insights](https://azure.microsoft.com/services/application-insights/), konfigurowanie nowego zasobu w systemie Microsoft Azure. Ten zasób jest, gdzie analizowane i wyświetlane dane telemetryczne z Twojej aplikacji. 

Aby zautomatyzować tworzenie nowego zasobu, należy za pomocą programu PowerShell.

Na przykład jeśli tworzysz aplikacji urządzenia przenośnego jest prawdopodobieństwo, że w dowolnym momencie i będzie kilka wersji opublikowanej aplikacji używanych przez klientów. Nie chcesz uzyskać wyniki dane telemetryczne z różnych wersji zamienione. Dlatego możesz uzyskać proces kompilacji, aby utworzyć nowy zasób dla każdej kompilacji.

> [!NOTE]
> Jeśli chcesz utworzyć zestaw zasobów, wszystko w tym samym czasie, należy wziąć pod uwagę [tworzenia zasobów przy użyciu szablonu platformy Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skrypt, aby utworzyć zasób usługi Application Insights
Zobacz specyfikacje odpowiednie polecenia cmdlet:

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

## <a name="what-to-do-with-the-ikey"></a>Co należy zrobić klucz Instrumentacji
Każdy zasób jest identyfikowany za pomocą klucza Instrumentacji (klucz Instrumentacji). Klucz instrumentacji, są to dane wyjściowe skryptu tworzenia zasobu. Skrypt kompilacji powinien zapewnić, że klucz Instrumentacji do zestawu SDK usługi Application Insights osadzone w aplikacji.

Istnieją dwa sposoby, aby udostępnić klucz Instrumentacji do zestawu SDK:

* W [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*klucz Instrumentacji*`</instrumentationkey>`
* Lub [kod inicjujący](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*klucz Instrumentacji*`";`

## <a name="see-also"></a>Zobacz także
* [Tworzenie usługi Application Insights i zasoby testu sieci web za pomocą szablonów](powershell.md)
* [Konfigurowanie monitorowania diagnostyki platformy Azure przy użyciu programu PowerShell](powershell-azure-diagnostics.md) 
* [Ustawianie alertów przy użyciu programu PowerShell](powershell-alerts.md)

