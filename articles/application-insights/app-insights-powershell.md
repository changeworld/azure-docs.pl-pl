---
title: Automatyzacji Azure Application Insights przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Zautomatyzować tworzenie testów dostępności zasobów i alertu w programie PowerShell przy użyciu szablonu usługi Azure Resource Manager.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 46ba4ce992640e8a6d171ab839dd7cdb24e0b404
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
#  <a name="create-application-insights-resources-using-powershell"></a>Tworzenie zasobów usługi Application Insights przy użyciu programu PowerShell
W tym artykule przedstawiono sposób tworzenia i aktualizacji [usługi Application Insights](app-insights-overview.md) zasobów automatycznie przy użyciu usługi Azure Resource Management. Użytkownik może na przykład zrobić jako część procesu kompilacji. Wraz z podstawowy zasób usługi Application Insights, można utworzyć [testów sieci web dostępności](app-insights-monitor-web-app-availability.md), skonfiguruj [alerty](app-insights-alerts.md)ustaw [cennik schemat](app-insights-pricing.md)i Utwórz innych zasobów platformy Azure.

Kluczem do tworzenia tych zasobów jest szablony JSON dla [usługi Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md). Mówiąc, jest procedurą: pobieranie definicji JSON istniejących zasobów. parametryzacja niektórych wartości, takich jak nazwy; a następnie uruchom szablon, aby utworzyć nowy zasób. Tworzyć pakiet kilku zasobów ze sobą, można je utworzyć w jednym przejdź — na przykład monitor aplikacji z testów dostępności, alerty i magazynu dla Eksport ciągły. Brak niektórych precyzyjnie niektórych parameterizations, które wyjaśniamy w tym miejscu.

## <a name="one-time-setup"></a>Jednorazowej konfiguracji
Jeśli nie użyto programu PowerShell z subskrypcją platformy Azure przed:

Zainstaluj moduł Azure Powershell na komputerze, na którym chcesz uruchomić skrypty:

1. Zainstaluj [Instalatora platformy sieci Web firmy Microsoft (w wersji 5 lub nowszej)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Użyć go do zainstalowania programu Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager
Utwórz nowy plik JSON — teraz wywołać ją `template1.json` w tym przykładzie. Skopiuj zawartość do niego:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
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
                    "description": "1 = Basic, 2 = Enterprise"
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
                "defaultValue": 24,
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
                    "ApplicationId": "[parameters('appName')]"
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



## <a name="create-application-insights-resources"></a>Tworzenie zasobów usługi Application Insights
1. W programie PowerShell logowanie do platformy Azure:
   
    `Login-AzureRmAccount`
2. Uruchom polecenie następująco:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` jest to grupa, której chcesz utworzyć nowe zasoby.
   * `-TemplateFile` musi być wcześniejsza niż parametry niestandardowe.
   * `-appName` Nazwa zasobu do utworzenia.

Można dodać inne parametry - opisami znajdują się w sekcji parametrów szablonu.

## <a name="to-get-the-instrumentation-key"></a>Aby uzyskać klucz Instrumentacji
Po utworzeniu zasobu aplikacji, należy klucza Instrumentacji: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Ustawienie planu cen

Można ustawić [planu cen](app-insights-pricing.md).

Aby utworzyć zasobów aplikacji z planem cen przedsiębiorstwa przy użyciu szablonu powyżej:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|plan|
|---|---|
|1|Podstawowa|
|2|Enterprise|

* Jeśli chcesz użyć domyślnego planu ceny podstawowej, można pominąć zasobów CurrentBillingFeatures z szablonu.
* Jeśli chcesz zmienić planu cen po utworzeniu zasobu składnik, można użyć szablonu, który umożliwia pominięcie zasobu "microsoft.insights/components". Ponadto Pomiń `dependsOn` węzła z zasobu rozliczeń. 

Aby sprawdzić, zaktualizowana cena planu, obejrzyj **strona szacowanych kosztów i użycia** bloku w przeglądarce. **Odśwież widok w przeglądarce** się upewnić, że widoczny będzie najnowszy stan.



## <a name="add-a-metric-alert"></a>Dodawanie metryki alertu

Aby skonfigurować alert metryki jednocześnie zasobu aplikacji, należy scalić kod podobny do tego pliku szablonu:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Po wywołaniu tego szablonu można opcjonalnie dodawać tego parametru:

    `-responseTime 2`

Oczywiście można parametryzacja innych pól. 

Aby znaleźć nazwy typu i szczegóły konfiguracji innych reguł alertów, ręcznie utworzyć regułę, a następnie sprawdzić, w [usługi Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Dodaj test dostępności

W tym przykładzie jest dla testów ping (do testowania z pojedynczą stroną).  

**Można wyróżnić dwie części** w test dostępności: badanie i alert, który informuje o awarii.

Scal następujący kod w pliku szablonu, który tworzy aplikację.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Odnajdywanie kodów dla innych lokalizacji testu lub zautomatyzować tworzenie bardziej złożonych testów sieci web, Utwórz przykład ręcznie, a następnie parametryzacja kod z [usługi Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Dodaj więcej zasobów

Aby zautomatyzować tworzenie jakichkolwiek innych zasobów, Utwórz przykład ręcznie, a następnie skopiuj i parametryzacja jego kod z [usługi Azure Resource Manager](https://resources.azure.com/). 

1. Otwórz [usługa Azure Resource Manager](https://resources.azure.com/). Przejdź w dół za pośrednictwem `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, zasobu aplikacji. 
   
    ![Nawigacja w Eksploratorze zasobów platformy Azure](./media/app-insights-powershell/01.png)
   
    *Składniki* podstawowych zasobów usługi Application Insights do wyświetlania aplikacji. Istnieją oddzielne zasoby skojarzonych reguł alertów i dostępności testów sieci web.
2. Skopiuj kod JSON składnika w odpowiednim miejscu w `template1.json`.
3. Usuń następujące właściwości:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otwórz w sekcjach webtests i alertrules i skopiuj kod JSON dla poszczególnych elementów do szablonu. (Nie należy kopiować z węzłów webtests lub alertrules: Przejdź do elementów pod nimi.)
   
    Każdy test sieci web ma skojarzone reguły alertu, dlatego należy skopiować obu z nich.
   
    Możesz również uwzględnić alerty dotyczące metryk. [Nazwy metryki](app-insights-powershell-alerts.md#metric-names).
5. Wstaw ten wiersz w każdym z zasobów:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametryzacja szablonu
Teraz należy zastąpić konkretne nazwy parametrów. Aby [parametryzacja szablonu](../azure-resource-manager/resource-group-authoring-templates.md), zapisu przy użyciu wyrażenia [zestaw funkcji pomocnika](../azure-resource-manager/resource-group-template-functions.md). 

Nie można sparametryzować tylko część ciągu, więc `concat()` do tworzenia ciągów.

Poniżej przedstawiono przykłady podstawień, które należy wprowadzić. Istnieje kilka wystąpień każdej podstawienia. W szablonie, mogą wymagać innych użytkowników. Poniższe przykłady użycia parametry i zmienne, zdefiniowanego w górnej części szablonu.

| Znajdź | Zamień |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (małe litery) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Usuń identyfikator Guid i identyfikator. |

### <a name="set-dependencies-between-the-resources"></a>Definiowanie zależności między zasobami
Azure należy skonfigurować zasoby w kolejności strict. Aby upewnić się, że jeden Instalator ukończy przed rozpoczęciem następnego, Dodaj linii zależności:

* W zasobie testu dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* W zasobie alertów dla testu dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Kolejne kroki
Inne artykuły automatyzacji:

* [Tworzenie zasobu usługi Application Insights](app-insights-powershell-script-create-resource.md) — szybkie metody bez przy użyciu szablonu.
* [Konfigurowanie alertów](app-insights-powershell-alerts.md)
* [Tworzenie testów sieci web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Wysyłanie Diagnostyki Azure do usługi Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Wdrażanie na platformie Azure z usługi GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Tworzenie wersji adnotacji](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

