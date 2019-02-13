---
title: Hosting o dużej gęstości za pomocą aplikacji skalowanie — w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Hosting o dużej gęstości w usłudze Azure App Service
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
ms.custom: seodec18
ms.openlocfilehash: 08d6d0c31e1cff799e952c50bae3446e41477aba
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104573"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosting o dużej gęstości w usłudze Azure App Service przy użyciu skalowania dla aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Domyślnie, skalować aplikacje usługi App Service, skalując [planu usługi App Service](overview-hosting-plans.md) działają one na. Gdy wiele aplikacji są uruchamiane w tym samym planie usługi App Service, każdego wystąpienia skalowanego uruchamia wszystkie aplikacje w planie.

Aby umożliwić *skalowania dla aplikacji* na usługi App Service plan poziom. Zostanie przeprowadzone skalowanie aplikacji niezależnie od planu usługi App Service, który ją obsługuje. W ten sposób można skalować do 10 wystąpień planu usługi App Service, ale można ustawić aplikację do używania tylko pięciu.

> [!NOTE]
> Skalowanie aplikacji jest dostępna tylko w przypadku **standardowa**, **Premium**, **warstwa Premium V2** i **izolowany** warstw cenowych.
>

## <a name="per-app-scaling-using-powershell"></a>Dla aplikacji, skalowanie przy użyciu programu PowerShell

Tworzenie planu z poszczególnych aplikacji skalowania, przekazując ```-PerSiteScaling $true``` parametr ```New-AzAppServicePlan``` polecenia cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Włącz Skalowanie z istniejący Plan usługi App Service przez przekazanie aplikacji `-PerSiteScaling $true` parametr ```Set-AzAppServicePlan``` polecenia cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na poziomie aplikacji skonfiguruj liczbę wystąpień, które aplikacja może używać w ramach planu usługi App Service.

W poniższym przykładzie aplikacja jest ograniczona do dwóch wystąpień, niezależnie od tego, ile wystąpień podstawowy plan usługi app service, skaluje się.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` różni się od `$newapp.MaxNumberOfWorkers`. Używa skalowania dla aplikacji `$newapp.SiteConfig.NumberOfWorkers` do określania cech skalowania aplikacji.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalowanie poszczególnych aplikacji, za pomocą usługi Azure Resource Manager

Tworzy następującego szablonu usługi Azure Resource Manager:

- Plan usługi App Service, która będzie skalowana w poziomie do 10 wystąpień
- Aplikacja, która jest skonfigurowana do skalowania do maksymalnie pięć wystąpień.

Plan usługi App Service jest ustawienie **PerSiteScaling** właściwości na wartość true `"perSiteScaling": true`. Aplikacja jest ustawienie **liczba procesów roboczych** służące do 5 `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="recommended-configuration-for-high-density-hosting"></a>Zalecana konfiguracja hosting o dużej gęstości
Na skalowanie aplikacji to funkcja, która jest włączona w obu globalnych regionów platformy Azure i [środowisk usługi App Service](environment/app-service-app-service-environment-intro.md). Jednak zalecaną strategią jest użycie środowisk usługi App Service, aby móc korzystać z ich zaawansowane funkcje i większe pule o pojemności.  

Wykonaj następujące kroki, aby skonfigurować o wysokiej gęstości hosting dla aplikacji:

1. Skonfiguruj środowisko usługi App Service i wybierz pulę procesów roboczych przeznaczonych do scenariusza hostingu w sieci o wysokiej gęstości.
2. Utwórz jeden plan usługi App Service i skalować ją na korzystanie z całej dostępnej pojemności puli procesów roboczych.
3. Ustaw `PerSiteScaling` flagi na wartość true w planie usługi App Service.
4. Nowe aplikacje zostały utworzone i przypisane do tego planu usługi App Service przy użyciu **numberOfWorkers** właściwością **1**. Za pomocą tej konfiguracji daje najwyższy gęstość można w tej puli procesów roboczych.
5. Liczba procesów roboczych, które można niezależnie konfigurować na aplikację, aby udzielić dodatkowych zasobów, zgodnie z potrzebami. Na przykład:
    - Można ustawić aplikacji obciążonym **numberOfWorkers** do **3** zapewnienie większej pojemności przetwarzania dla danej aplikacji. 
    - Ustawiał niskiego użycia aplikacje **numberOfWorkers** do **1**.

## <a name="next-steps"></a>Następne kroki

- [Szczegółowe omówienie planów usługi Azure App Service](overview-hosting-plans.md)
- [Wprowadzenie do usługi App Service Environment](environment/app-service-app-service-environment-intro.md)
