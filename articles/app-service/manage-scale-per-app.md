---
title: Skalowanie dla aplikacji dla hostingu o wysokiej gęstości
description: Skaluj aplikacje niezależnie od planów usługi App Service i optymalizuj skalowane w poziomie wystąpienia w planie.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672348"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosting o wysokiej gęstości w usłudze Azure App Service przy użyciu skalowania dla aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Korzystając z usługi App Service, możesz skalować aplikacje, skalując [plan usługi app service,](overview-hosting-plans.md) na którym działają. Gdy wiele aplikacji są uruchamiane w tym samym planie usługi App Service, każde wystąpienie skalowane w poziomie uruchamia wszystkie aplikacje w planie.

*Skalowanie dla aplikacji* można włączyć na poziomie planu usługi aplikacji, aby umożliwić skalowanie aplikacji niezależnie od planu usługi app, który go obsługuje. W ten sposób plan usługi app service można skalować do 10 wystąpień, ale aplikację można ustawić do użycia tylko pięć.

> [!NOTE]
> Skalowanie dla aplikacji jest dostępne tylko dla warstw **cenowych Standard,** **Premium,** **Premium V2** i **Isolated.**
>

Aplikacje są przydzielane do planu usługi aplikacji dostępne przy użyciu podejścia najlepszego wysiłku dla równomiernej dystrybucji między wystąpieniami. Chociaż dystrybucja parzyste nie jest gwarantowana, platforma upewnij się, że dwa wystąpienia tej samej aplikacji nie będą hostowane w tym samym wystąpieniu planu usługi App Service.

Platforma nie opiera się na metryki, aby zdecydować o alokacji pracownika. Aplikacje są równoważenia tylko wtedy, gdy wystąpienia są dodawane lub usuwane z planu usługi app service.

## <a name="per-app-scaling-using-powershell"></a>Skalowanie aplikacji przy użyciu programu PowerShell

Utwórz plan ze skalowaniem dla aplikacji, przekazując ```-PerSiteScaling $true``` parametr do polecenia ```New-AzAppServicePlan``` cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Włącz skalowanie dla aplikacji z istniejącym planem `-PerSiteScaling $true` usługi aplikacji, przekazując parametr do polecenia ```Set-AzAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na poziomie aplikacji skonfiguruj liczbę wystąpień, których aplikacja może używać w planie usługi app service.

W poniższym przykładzie aplikacja jest ograniczona do dwóch wystąpień, niezależnie od tego, ile wystąpień podstawowy plan usługi aplikacji skaluje się w poziomie.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`różni się `$newapp.MaxNumberOfWorkers`od . Skalowanie dla aplikacji `$newapp.SiteConfig.NumberOfWorkers` służy do określania charakterystyki skali aplikacji.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalowanie dla aplikacji przy użyciu usługi Azure Resource Manager

Tworzy następujący szablon usługi Azure Resource Manager:

- Plan usługi aplikacji skalowany w poziomie do 10 wystąpień
- aplikacja, która jest skonfigurowana do skalowania do maksymalnie pięciu wystąpień.

Plan usługi aplikacji jest ustawienie **PerSiteScaling** właściwość true `"perSiteScaling": true`. Aplikacja ustawia **liczbę pracowników** do użycia na `"properties": { "numberOfWorkers": "5" }`5 .

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Zalecana konfiguracja hostingu o dużej gęstości

Skalowanie dla aplikacji jest funkcją, która jest włączona zarówno w globalnych regionach platformy Azure, jak i [w środowiskach usługi app service.](environment/app-service-app-service-environment-intro.md) Jednak zalecana strategia jest użycie środowiska usługi app service, aby skorzystać z ich zaawansowanych funkcji i większą pojemność planu usługi App Service.  

Wykonaj następujące kroki, aby skonfigurować hosting o wysokiej gęstości dla aplikacji:

1. Wyznacz plan usługi app service jako plan o wysokiej gęstości i skaluj go w poziomie do żądanej pojemności.
1. Ustaw `PerSiteScaling` flagę na true w planie usługi app service.
1. Nowe aplikacje są tworzone i przypisywane do tego planu usługi App Service z **numberOfWorkers** właściwość ustawiona na **1**.
   - Użycie tej konfiguracji daje najwyższą możliwą gęstość.
1. Liczba pracowników można skonfigurować niezależnie dla aplikacji, aby udzielić dodatkowych zasobów w razie potrzeby. Przykład:
   - Aplikacja o wysokim użyciu można ustawić **numberOfWorkers** do **3,** aby mieć większą zdolność przetwarzania dla tej aplikacji.
   - Aplikacje o niskim użyciu ustawiłyby **liczbęPracownic na** **1**.

## <a name="next-steps"></a>Następne kroki

- [Szczegółowe omówienie planów usługi Azure App Service](overview-hosting-plans.md)
- [Wprowadzenie do usługi App Service Environment](environment/app-service-app-service-environment-intro.md)
