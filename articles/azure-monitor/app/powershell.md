---
title: Automatyzacja usługi Azure Application Insights za pomocą programu PowerShell | Dokumenty firmy Microsoft
description: Automatyzacja tworzenia zasobów, alertów i testów dostępności w programie PowerShell i zarządzania nimi przy użyciu szablonu usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275882"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Zarządzanie zasobami usługi Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule pokazano, jak zautomatyzować tworzenie i aktualizowanie zasobów [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) automatycznie przy użyciu usługi Azure Resource Management. Można, na przykład, zrobić to w ramach procesu kompilacji. Wraz z podstawowym zasobem usługi Application Insights można tworzyć [testy sieci Web dostępności,](../../azure-monitor/app/monitor-web-app-availability.md) [konfigurowanie alertów, ustawianie](../../azure-monitor/app/alerts.md) [schematu cenowego](pricing.md)i tworzenie innych zasobów platformy Azure.

Kluczem do utworzenia tych zasobów są szablony JSON dla [usługi Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md). Podstawowa procedura to: pobierz definicje JSON istniejących zasobów; parametryzacja niektórych wartości, takich jak nazwy; a następnie uruchom szablon, gdy chcesz utworzyć nowy zasób. Można spakować kilka zasobów razem, aby utworzyć je wszystkie za jednym zamachem — na przykład monitor aplikacji z testów dostępności, alerty i magazynu do ciągłego eksportu. Istnieją pewne subtelności do niektórych parametryzacji, które wyjaśnimy tutaj.

## <a name="one-time-setup"></a>Konfiguracja jednorazowa
Jeśli nie używałeś programu PowerShell z subskrypcją platformy Azure przed:

Zainstaluj moduł programu Azure Powershell na komputerze, na którym chcesz uruchomić skrypty:

1. Zainstaluj [Instalator microsoft web platform (v5 lub nowszy)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Użyj go do zainstalowania programu Microsoft Azure Powershell.

Oprócz używania szablonów Menedżera zasobów istnieje bogaty zestaw [poleceń cmdlet programu PowerShell usługi Application Insights,](https://docs.microsoft.com/powershell/module/az.applicationinsights)które ułatwiają programową konfigurację zasobów usługi Application Insights. Możliwości włączane przez polecenia cmdlet obejmują:

* Tworzenie i usuwanie zasobów usługi Application Insights
* Pobierz listy zasobów usługi Application Insights i ich właściwości
* Tworzenie ciągłego eksportu i zarządzanie nim
* Tworzenie kluczy aplikacji i zarządzanie nimi
* Ustawianie dziennego limitu
* Ustawianie planu cenowego

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Tworzenie zasobów usługi Application Insights przy użyciu polecenia cmdlet programu PowerShell

Poniżej opisano, jak utworzyć nowy zasób usługi Application Insights w centrum danych usługi Azure East US przy użyciu polecenia cmdlet [New-AzApplicationInsights:](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Tworzenie zasobów usługi Application Insights przy użyciu szablonu Menedżera zasobów

Poniżej opisano, jak utworzyć nowy zasób usługi Application Insights przy użyciu szablonu Menedżera zasobów.

### <a name="create-the-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Utwórz nowy plik .json - `template1.json` nazwijmy go w tym przykładzie. Skopiuj do niej tę zawartość:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Tworzenie nowego zasobu usługi Application Insights za pomocą szablonu Menedżera zasobów

1. W programie PowerShell zaloguj się na platformie Azure przy użyciu`$Connect-AzAccount`
2. Ustaw kontekst na subskrypcję za pomocą`Set-AzContext "<subscription ID>"`
2. Uruchom nowe wdrożenie, aby utworzyć nowy zasób usługi Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`to grupa, w której chcesz utworzyć nowe zasoby.
   * `-TemplateFile`musi wystąpić przed parametrami niestandardowymi.
   * `-appName`Nazwa zasobu do utworzenia.

Możesz dodać inne parametry - ich opisy znajdziesz w sekcji parametrów szablonu.

## <a name="get-the-instrumentation-key"></a>Pobierz klucz oprzyrządowania

Po utworzeniu zasobu aplikacji, należy klucz instrumentacji: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Aby wyświetlić listę wielu innych właściwości zasobu usługi Application Insights, należy użyć:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Dodatkowe właściwości są dostępne za pośrednictwem poleceń cmdlet:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Szczegółowe parametry tych poleceń cmdlet można znaleźć w [szczegółowej dokumentacji.](https://docs.microsoft.com/powershell/module/az.applicationinsights)  

## <a name="set-the-data-retention"></a>Ustawianie przechowywania danych 

Aby uzyskać bieżące przechowywanie danych dla zasobu usługi Application Insights, można użyć narzędzia OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Dowiedz się więcej o ARMClient z artykułów [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Oto przykład użycia `ARMClient`, aby uzyskać bieżącą retencję:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Aby ustawić retencję, polecenie jest podobne PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Aby ustawić przechowywanie danych na 365 dni przy użyciu powyższego szablonu, uruchom:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Poniższy skrypt może również służyć do zmiany przechowywania. Skopiuj `Set-ApplicationInsightsRetention.ps1`ten skrypt, aby zapisać jako .

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Ten skrypt może być następnie użyty jako:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Ustawianie dziennej czapki

Aby uzyskać właściwości daily cap, użyj polecenia cmdlet [Set-AzApplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Aby ustawić właściwości dziennego limitu, użyj tego samego polecenia cmdlet. Na przykład, aby ustawić limit na 300 GB/dzień,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Można również użyć [ARMClient,](https://github.com/projectkudu/ARMClient) aby uzyskać i ustawić dzienne parametry limitu.  Aby uzyskać bieżące wartości, należy użyć:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Ustawianie czasu dziennego resetowania limitu

Aby ustawić dzienny czas resetowania limitu, można użyć [programu ARMClient](https://github.com/projectkudu/ARMClient). Oto przykład użycia `ARMClient`, aby ustawić czas resetowania na nową godzinę (w tym przykładzie 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Ustawianie planu cenowego 

Aby uzyskać bieżący plan cenowy, użyj polecenia cmdlet [Set-AzApplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan)

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Aby ustawić plan cenowy, użyj tego `-PricingPlan` samego polecenia cmdlet z określonymi:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Plan cenowy można również ustawić dla istniejącego zasobu usługi Application Insights przy użyciu powyższego szablonu `dependsOn` Menedżera zasobów, pomijając zasób "microsoft.insights/components" i węzeł z zasobu rozliczeniowego. Na przykład, aby ustawić go na plan na GB (dawniej nazywany planem podstawowym), uruchom:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

Jest `priceCode` zdefiniowany jako:

|kod cenowy|plan|
|---|---|
|1|Na GB (dawniej nazwany plan podstawowy)|
|2|Na węzeł (dawniej nazwa planu Enterprise)|

Na koniec można użyć [ARMClient,](https://github.com/projectkudu/ARMClient) aby uzyskać i ustawić plany cenowe i parametry dziennego limitu.  Aby uzyskać bieżące wartości, należy użyć:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

I można ustawić wszystkie te parametry za pomocą:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Spowoduje to ustawienie dziennego limitu na 200 GB /dzień, skonfigurowanie dziennego czasu resetowania limitu do 12:00 UTC, wysyłanie wiadomości e-mail zarówno po osiągnięciu limitu, jak i osiągnięciu poziomu ostrzeżenia, a także ustawienie progu ostrzegawczego na 90% limitu.  

## <a name="add-a-metric-alert"></a>Dodawanie alertu metrycznego

Aby zautomatyzować tworzenie alertów metryk, zapoznaj się z [artykułem szablonu alertów metryk](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Dodawanie testu dostępności

Aby zautomatyzować testy dostępności, zapoznaj się z [artykułem szablonu alertów metryk](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Dodawanie większej ilości zasobów

Aby zautomatyzować tworzenie dowolnego innego zasobu dowolnego rodzaju, utwórz przykład ręcznie, a następnie skopiuj i sparametryzuj jego kod z [usługi Azure Resource Manager](https://resources.azure.com/). 

1. Otwórz [usługę Azure Resource Manager](https://resources.azure.com/). Przejdź w `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`dół , do zasobu aplikacji. 
   
    ![Nawigacja w Eksploratorze zasobów platformy Azure](./media/powershell/01.png)
   
    *Składniki* są podstawowymi zasobami usługi Application Insights do wyświetlania aplikacji. Istnieją oddzielne zasoby dla skojarzonych reguł alertów i testów sieci web dostępności.
2. Skopiuj JSON komponentu `template1.json`w odpowiednie miejsce w .
3. Usuń te właściwości:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otwórz `webtests` sekcje i `alertrules` skopiuj json dla poszczególnych elementów do szablonu. (Nie kopiuj `webtests` `alertrules` z węzłów lub: przejdź do elementów pod nimi.)
   
    Każdy test sieci web ma skojarzoną regułę alertu, więc musisz skopiować oba z nich.
   
    Można również dołączyć alerty dotyczące metryk. [Nazwy metryki](powershell-alerts.md#metric-names).
5. Wstaw ten wiersz w każdym zasobie:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametryzacja szablonu
Teraz musisz zastąpić konkretne nazwy parametrami. Aby [sparametryzować szablon,](../../azure-resource-manager/templates/template-syntax.md)należy pisać wyrażenia przy użyciu [zestawu funkcji pomocnika](../../azure-resource-manager/templates/template-functions.md). 

Nie można sparametryzować tylko część ciągu, więc należy użyć `concat()` do tworzenia ciągów.

Oto przykłady podstawień, które chcesz wprowadzić. Istnieje kilka wystąpień każdego podstawienia. W szablonie mogą być potrzebne inne osoby. W tych przykładach użyto parametrów i zmiennych zdefiniowanych w górnej części szablonu.

| find | zastąp na |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`(małe litery) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Ustawianie zależności między zasobami
Platforma Azure powinna skonfigurować zasoby w ścisłej kolejności. Aby upewnić się, że jedna konfiguracja zostanie ukończona przed rozpoczęciem następnego, dodaj wiersze zależności:

* W zasób testowy dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* W zasobie alertu dla testu dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Następne kroki
Inne artykuły automatyzacji:

* [Tworzenie zasobu usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) — szybka metoda bez użycia szablonu.
* [Konfigurowanie alertów](powershell-alerts.md)
* [Utwórz testy sieci Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Wysyłanie Diagnostyki Azure do usługi Application Insights](powershell-azure-diagnostics.md)
* [Wdrażanie na platformie Azure z usługi GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Tworzenie adnotacji wydania](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)