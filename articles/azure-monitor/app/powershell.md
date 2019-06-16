---
title: Automatyzacja usługi Azure Application Insights przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Automatyzowanie tworzenia zasobu, alerty i dostępności testów w programie PowerShell przy użyciu szablonu usługi Azure Resource Manager.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mbullwin
ms.openlocfilehash: 07d52544b584adb02cc60790b7cb63c8aee1e366
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514477"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Tworzenie zasobów usługi Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule pokazano, jak zautomatyzować tworzenie i aktualizowanie [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) zasoby automatycznie przy użyciu usługi Azure Resource Management. Użytkownik może na przykład zrobić jako część procesu kompilacji. Wraz z podstawowy zasób usługi Application Insights, można utworzyć [testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md), skonfiguruj [alerty](../../azure-monitor/app/alerts.md)ustaw [ceny schemat](pricing.md)i tworzenie innych zasobów platformy Azure .

Kluczem do tworzenia tych zasobów jest szablonów JSON dla [usługi Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). Mówiąc, jest procedurą: pobieranie definicji JSON istniejących zasobów. parametryzacja określone wartości, takich jak nazwy; a następnie uruchom szablon zawsze wtedy, gdy chcesz utworzyć nowy zasób. Można spakować ze sobą kilka zasobów, do ich utworzenia w jednym go — na przykład monitorowanie aplikacji za pomocą testy dostępności, alerty i magazynu na potrzeby eksportu ciągłego. Istnieją pewne precyzyjnie do niektórych parameterizations, które wyjaśnimy, w tym miejscu.

## <a name="one-time-setup"></a>Jednorazowej konfiguracji
Jeśli nie znasz programu PowerShell z subskrypcją platformy Azure przed:

Instalowanie modułu Azure Powershell na komputerze, na którym chcesz uruchomić skrypty:

1. Zainstaluj [Instalatora platformy sieci Web firmy Microsoft (w wersji 5 lub nowszej)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Należy zainstalować program Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager
Utwórz nowy plik JSON — nazwiemy to `template1.json` w tym przykładzie. Skopiuj tę zawartość do niego:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                    "description": "1 = Per GB (Basic), 2 = Per Node (Enterprise)"
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
1. W programie PowerShell Zaloguj się do platformy Azure:
   
    `Connect-AzAccount`
2. Uruchom polecenie następująco:
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` jest to grupa, której chcesz tworzyć nowe zasoby.
   * `-TemplateFile` musi być wcześniejsza niż parametry niestandardowe.
   * `-appName` Nazwa zasobu do utworzenia.

Możesz dodać inne parametry - ich opisy znajdują się w sekcji Parametry szablonu.

## <a name="to-get-the-instrumentation-key"></a>Aby uzyskać klucz Instrumentacji
Po utworzeniu zasobu aplikacji, musisz wykonać klucz Instrumentacji: 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Ustaw plan cenowy

Możesz ustawić [plan cenowy](pricing.md).

Aby utworzyć zasób aplikacji usługi z planu cen w przedsiębiorstwie, przy użyciu powyższego szablonu:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|Plan|
|---|---|
|1|Podstawowa|
|2|Enterprise|

* Jeśli chcesz użyć domyślny plan cenowy podstawowa, można pominąć zasób CurrentBillingFeatures z szablonu.
* Jeśli chcesz zmienić plan cenowy, po utworzeniu zasobu składnik, można użyć szablonu, które pomija zasobu "microsoft.insights/components". Ponadto, Pomiń `dependsOn` węzła z rozliczeń zasobów. 

Aby sprawdzić, zaktualizowana cena planu, Przyjrzyj się **użycie i szacowane koszty strony** bloku w przeglądarce. **Odśwież widok w przeglądarce** się upewnić, że widoczny będzie najnowszy stan.



## <a name="add-a-metric-alert"></a>Dodaj alert metryki

Aby skonfigurować alert metryki, w tym samym czasie jako zasób aplikacji, należy scalić kod do pliku szablonu:

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

Po wywołaniu tego szablonu możesz opcjonalnie dodać tego parametru:

    `-responseTime 2`

Można oczywiście parametryzuj innych pól. 

Aby dowiedzieć się, nazwy typów i szczegółów konfiguracji innych reguł alertów, ręcznie Utwórz regułę, a następnie sprawdź w [usługi Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Dodaj test dostępności

W tym przykładzie jest testu ping (do testowania z pojedynczą stroną).  

**Istnieją dwie części** w test dostępności: badanie i alert, który informuje o awarii.

Scal następujący kod do pliku szablonu, która zostanie utworzona aplikacja.

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
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
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

Odnajdywanie kodów dla inne lokalizacji testu lub aby zautomatyzować tworzenie bardziej złożonych testów sieci web, ręcznie Utwórz przykładowy, a następnie zdefiniować parametry kodu z [usługi Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Dodaj więcej zasobów

Aby zautomatyzować tworzenie innych zasobów dowolnego rodzaju, Utwórz przykładowy ręcznie, a następnie skopiuj i sparametryzuj jego kod z [usługi Azure Resource Manager](https://resources.azure.com/). 

1. Otwórz [usługi Azure Resource Manager](https://resources.azure.com/). Przejdź w dół za pośrednictwem `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, aby dany zasób aplikacji. 
   
    ![Nawigacja w Eksploratorze zasobów platformy Azure](./media/powershell/01.png)
   
    *Składniki* są podstawowe zasoby usługi Application Insights w celu wyświetlania aplikacji. Istnieją osobne zasoby skojarzone reguły alertów i testy sieci web dostępności.
2. Skopiuj dane JSON składnika do odpowiedniego miejsca w `template1.json`.
3. Usuń te właściwości:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otwórz w sekcjach testów internetowych i alertrules, a następnie skopiuj kod JSON dla poszczególnych elementów do szablonu. (Nie należy kopiować z testów internetowych lub alertrules węzłów: Przejdź do elementów w ramach nich.)
   
    Każdy test sieci web ma skojarzone reguły alertu, więc trzeba skopiować obu z nich.
   
    Może również obejmować alertów dotyczących metryk. [Nazwy metryk](powershell-alerts.md#metric-names).
5. Wstaw ten wiersz w poszczególnych zasobów:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Definiowanie parametrów szablonu
Masz teraz zastąpić konkretne nazwy parametrów. Aby [parametryzacji szablonu](../../azure-resource-manager/resource-group-authoring-templates.md), pisania wyrażeń przy użyciu [zestaw funkcji pomocnika](../../azure-resource-manager/resource-group-template-functions.md). 

Nie można sparametryzować tylko część ciągu, więc `concat()` do tworzenia ciągów.

Poniżej przedstawiono przykłady podstawienia, które należy wprowadzić. Istnieje kilka wystąpień każdego podstawienia. Możesz potrzebować innych użytkowników w szablonie. Te przykłady użycia parametry i zmienne, zdefiniowanego w górnej części szablonu.

| find | Zamień |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (małe litery) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Usuń identyfikator Guid i identyfikator. |

### <a name="set-dependencies-between-the-resources"></a>Definiowanie zależności między zasobami
Azure należy skonfigurować zasoby w kolejności strict. Aby upewnić się, że jeden Instalator ukończy przed rozpoczęciem następnego, Dodaj wiersze zależności:

* W zasobie testów dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* W zasobie alertu testu dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Kolejne kroki
Inne artykuły, automatyzacji:

* [Utwórz zasób usługi Application Insights](powershell-script-create-resource.md) — szybkie metody bez przy użyciu szablonu.
* [Konfigurowanie alertów](powershell-alerts.md)
* [Tworzenie testów sieci web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Wysyłanie Diagnostyki Azure do usługi Application Insights](powershell-azure-diagnostics.md)
* [Wdrażanie na platformie Azure z usługi GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Utwórz adnotacji dotyczących wersji](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
