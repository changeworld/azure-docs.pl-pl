---
title: O wysokiej gęstości hosting w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: O wysokiej gęstości hosting w usłudze Azure App Service
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.openlocfilehash: 8bdf62ce70929c583b59b91e5f1e7c520376120b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735816"
---
# <a name="high-density-hosting-on-azure-app-service"></a>O wysokiej gęstości hosting w usłudze Azure App Service
Korzystając z usługi aplikacji, aplikacja jest całkowicie niezależna od pojemności w dwóch pojęcia:

* **Aplikacja:** reprezentuje aplikacji i jego konfiguracja środowiska uruchomieniowego. Na przykład zawiera wersji platformy .NET, które powinny zostać załadowane środowisko uruchomieniowe, ustawienia aplikacji.
* **Plan usługi aplikacji:** określa właściwości pojemności, zestaw dostępnych funkcji i lokalizację aplikacji. Na przykład właściwości mogą być duże maszyny (4 rdzenie), cztery wystąpienia, funkcji Premium w wschodnie stany USA.

Aplikacja jest zawsze połączony plan usługi aplikacji, ale plan usługi aplikacji umożliwiają zdolności do co najmniej jednej aplikacji.

W związku z tym platformy zapewnia elastyczność do izolowania tylko jednej aplikacji lub ma wiele aplikacji udostępnianie zasobów za pomocą udostępniania plan usługi aplikacji.

Jednak gdy wiele aplikacji udostępnić plan usługi aplikacji, wystąpienia, aplikacja działa na każde wystąpienie tego planu usługi aplikacji.

## <a name="per-app-scaling"></a>Na skalowanie aplikacji
*Na skalowanie aplikacji* jest funkcją, która może być włączone na poziomie planu usługi aplikacji i następnie używane na aplikację.

Każdej aplikacji odbierającej skalowanie aplikacji niezależnie od planu usług aplikacji, który go obsługuje. W ten sposób plan usługi aplikacji mogą być skalowane do 10 wystąpień, ale można ustawić aplikację do używania pięciu tylko.

   >[!NOTE]
   >Dla programu app skalowanie, jest dostępna tylko w przypadku **standardowe**, **Premium**, **Premium V2** i **izolowany** planów usługi App Service dla jednostki SKU
   >

### <a name="per-app-scaling-using-powershell"></a>Każdej aplikacji odbierającej za pomocą programu PowerShell

Tworzenie planu, który został skonfigurowany jako *na skalowanie aplikacji* planu przez przekazywanie ```-perSiteScaling $true``` atrybutu ```New-AzureRmAppServicePlan``` apletu polecenia

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Jeśli chcesz zaktualizować istniejący plan usługi aplikacji, aby użyć tej funkcji: 

- Pobierz planu docelowego ```Get-AzureRmAppServicePlan```
- Modyfikowanie właściwości lokalnie ```$newASP.PerSiteScaling = $true```
- przesyłanie zmian do platformy azure ```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Na poziomie aplikacji skonfiguruj liczbę wystąpień używanych przez aplikację w planie usługi aplikacji.

W poniższym przykładzie aplikacji może zawierać maksymalnie dwa wystąpienia, niezależnie od tego, jak wiele wystąpień podstawowy plan usługi aplikacji może obsłużyć limit.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp. SiteConfig.NumberOfWorkers różni się od $newapp. MaxNumberOfWorkers. Każdej aplikacji odbierającej używa $newapp. SiteConfig.NumberOfWorkers, aby określić właściwości skalowania aplikacji.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Na skalowanie aplikacji przy użyciu usługi Azure Resource Manager

Następujące *szablonu usługi Azure Resource Manager* tworzy:

- Plan usługi aplikacji, która jest skalowana w poziomie do 10 wystąpień
- Aplikacja, która jest skonfigurowana do skalowania do maksymalnie pięć wystąpień.

Plan usługi aplikacji jest ustawienie **PerSiteScaling** właściwości na wartość true ```"perSiteScaling": true```. Aplikacja jest ustawienie **liczba pracowników** na potrzeby 5 ```"properties": { "numberOfWorkers": "5" }```.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Zalecana konfiguracja o wysokiej gęstości hostingu
Na skalowanie aplikacji jest funkcją, która jest włączona w globalnej regiony platformy Azure i środowiska usługi App Service. Jednak strategii zalecane jest użycie środowiska usługi App Service korzystają z ich zaawansowane funkcje i większe pule o pojemności.  

Wykonaj następujące kroki, aby skonfigurować o wysokiej gęstości hosting dla aplikacji:

1. Konfigurowanie środowiska usługi aplikacji i wybierz polecenie puli procesów roboczych, który jest przeznaczony do obsługi scenariusza wysokiej gęstości.
1. Utwórz jeden plan usługi aplikacji i skalować ją do korzystania z dostępnej pojemności w puli procesów roboczych.
1. Ustaw flagę PerSiteScaling true na plan usługi aplikacji.
1. Nowe aplikacje są tworzone i przypisane do tego planu usługi aplikacji z **numberOfWorkers** ustawioną właściwość **1**. Za pomocą tej konfiguracji daje najwyższy gęstość możliwe w tej puli procesów roboczych.
1. Liczba procesów roboczych można skonfigurować niezależnie każdej aplikacji można przyznać dodatkowe zasoby, zgodnie z potrzebami. Na przykład:
    - Można ustawić aplikacji bardzo obciążonym **numberOfWorkers** do **3** ma więcej możliwości przetwarzania dla danej aplikacji. 
    - Ustawiał niskiego użycia aplikacji **numberOfWorkers** do **1**.

## <a name="next-steps"></a>Następne kroki

- [Szczegółowe omówienie planów usługi aplikacji Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Wprowadzenie do usługi App Service Environment](app-service-app-service-environment-intro.md)
