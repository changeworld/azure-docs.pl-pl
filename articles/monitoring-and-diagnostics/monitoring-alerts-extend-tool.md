---
title: Rozszerzanie alerty z Analytcs dziennika na platformie Azure
description: W tym artykule opisano narzędzia i interfejs API za pomocą którego można rozszerzyć alerty z Analiza dzienników Azure alerty.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 21ba95a7b3efff177afe63d22da3f6ba9848ded2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301035"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Rozszerzona z usługi Analiza dzienników Azure alerty Alerty
Funkcja alerty w Azure Log Analytics jest zastępowany przez Azure alerty. W ramach tego przejścia alerty, które pierwotnie skonfigurowane w analizy dzienników zostanie rozszerzony na platformie Azure. Jeśli nie chcesz czekać na ich automatycznie przeniesione do platformy Azure, możesz zainicjować proces:

- Ręcznie w portalu usługi Operations Management Suite. 
- Programowo, za pomocą interfejsu API AlertsVersion.  

> [!NOTE]
> Microsoft spowoduje automatyczne przedłużenie alertach tworzonych w Analiza dzienników Azure alerty, począwszy od na 14 maja 2018 cykl aż do zakończenia. Harmonogramy Microsoft migracji alerty Azure i w trakcie tego przejścia, alertami można zarządzać z portalu usługi Operations Management Suite i portalu Azure. Ten proces nie jest destrukcyjnego lub interruptive.  

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opcja 1: Inicjowanie z portalu usługi Operations Management Suite
W poniższych krokach opisano, jak rozszerzyć alerty w obszarze roboczym z portalu usługi Operations Management Suite.  

1. W portalu Azure wybierz **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. W okienku subskrypcje analizy dzienników, wybierz obszar roboczy, a następnie wybierz **portalu OMS** kafelka.
![Zrzut ekranu analizy dzienników okienku subskrypcji z wyróżnionym kafelka portalu OMS](./media/monitor-alerts-extend/azure-portal-01.png) 
3. Po nastąpi przekierowanie do portalu usługi Operations Management Suite, wybierz **ustawienia** ikony.
![Zrzut ekranu Operations Management Suite portalu, z wyróżnioną pozycją ikonę ustawień](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Z **ustawienia** wybierz pozycję **alerty**.  
5. Wybierz **rozszerzona Azure**.
![Zrzut ekranu Operations Management Suite ustawienia alertu strony portalu, z Rozszerz na platformie Azure wyróżnione](./media/monitor-alerts-extend/ExtendInto.png)
6. Wyświetlany jest Kreator trzech etapów **alerty** okienka. Zapoznanie się z omówieniem, a następnie wybierz **dalej**.
![Zrzut ekranu przedstawiający kroku 1 Kreatora](./media/monitor-alerts-extend/ExtendStep1.png)  
7. W drugim kroku zobaczysz podsumowanie proponowanych zmian listę odpowiednich [grupy akcji](monitoring-action-groups.md) alertów. Jeśli podobne akcje są widoczne w więcej niż jeden alert, Kreator zamierza Skojarz grupę jednej akcji z wszystkich z nich.  Konwencja nazewnictwa jest następująca: *WorkspaceName_AG_ #Number*. Aby kontynuować, wybierz **dalej**.
![Zrzut ekranu przedstawiający krok 2 Kreatora](./media/monitor-alerts-extend/ExtendStep2.png)  
8. W ostatnim kroku kreatora wybierz **Zakończ**i upewnij się, gdy zostanie wyświetlony monit, aby zainicjować proces. Opcjonalnie można określić adres e-mail, co użytkownik jest powiadamiany o zakończeniu procesu i wszystkie alerty została pomyślnie przeniesiona do alertów Azure.
![Zrzut ekranu przedstawiający krok 3 kreatora](./media/monitor-alerts-extend/ExtendStep3.png)

Po zakończeniu działania kreatora, na **ustawienia alertu** strona, opcję, aby rozszerzyć alerty Azure zostanie usunięta. W tle alerty zostaną przeniesione na platformie Azure, a to może zająć trochę czasu. Podczas operacji nie można wprowadzić zmian na alerty z portalu usługi Operations Management Suite. Widać przy bieżącym stanie z banerze w górnej części portalu. Jeśli wcześniej została podana adres e-mail, otrzymasz wiadomość e-mail po procesie zostało pomyślnie ukończone.  


Alerty nadal wyświetlane w portalu usługi Operations Management Suite, nawet po zostały przeniesione do platformy Azure.
![Strona Ustawienia alertu portal zrzut ekranu Operations Management Suite](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opcja 2: Użycie AlertsVersion interfejsu API
Interfejs API AlertsVersion analizy dziennika umożliwia rozszerzanie alerty z analizy dzienników do alertów Azure za pomocą dowolnego klienta, który można wywołać interfejsu API REST. Interfejs API mogą korzystać z programu PowerShell, za pomocą [ARMClient](https://github.com/projectkudu/ARMClient), narzędzia wiersza polecenia open source. Można zapisuje wyniki w formacie JSON.  

Aby korzystać z interfejsu API, należy najpierw utworzyć żądanie GET. Ocenia i zwraca podsumowanie proponowanych zmian przed przystąpieniem do faktycznie rozszerzyć na platformie Azure przy użyciu żądania POST. Wyniki listy alertów i listę proponowanych [grupy akcji](monitoring-action-groups.md), w formacie JSON. Jeśli podobne akcje są widoczne w więcej niż jeden alert, usługa zamierza je wszystkie powiązania z grupą jednej akcji. Konwencja nazewnictwa jest następująca: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli żądanie GET zakończy się pomyślnie, kod stanu HTTP 200 jest zwracany, oraz listę alertów i proponowane grupy akcji w danych JSON. Poniżej przedstawiono przykład odpowiedzi:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Jeśli określony obszar roboczy nie ma żadnych reguł alertów zdefiniowane, dane JSON zwraca następujące czynności:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Jeśli wszystkie reguły alertu w obszarze roboczym określonego już zostały rozszerzone na platformie Azure, odpowiedź na żądanie GET jest:

```json
{
    "version": 2
}
```

Aby zainicjować migrację alerty na platformie Azure, inicjują odpowiedź POST. Odpowiedź POST potwierdza z celem, a także akceptacji, mieć alerty rozszerzony Analiza dzienników Azure alerty. Działanie zostało zaplanowane i alerty są przetwarzane wskazane, na podstawie wyników podczas wcześniej wykonywanej odpowiedzi GET. Opcjonalnie można udostępnić listę adresów e-mail, do których analizy dzienników wysyła raport po pomyślnym zakończeniu tła zaplanowanego procesu migracji alerty. Można użyć w poniższym przykładzie żądania:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Wynik Migrowanie alerty do alertów Azure może różnić w zależności od podsumowanie podał odpowiedź GET. Podczas planowania alerty w analizy dzienników są tymczasowo niedostępne do modyfikacji w portalu usługi Operations Management Suite. Można jednak utworzyć nowe alerty. 

Jeśli żądanie POST zakończy się pomyślnie, zwraca stan HTTP 200 OK wraz z następującą odpowiedź:

```json
{
    "version": 2
}
```

Odpowiedź wskazuje, że alerty zostały pomyślnie rozszerzone do alertów Azure. Właściwość wersja jest tylko do sprawdzania, czy alerty zostały rozszerzone na platformie Azure i nie są powiązane do [interfejsu API Search analizy dziennika](../log-analytics/log-analytics-api-alerts.md). Gdy alerty zostały rozszerzone pomyślnie na platformie Azure, wszelkie adresy e-mail pod warunkiem przy użyciu metody POST żądania są wysyłane raportu. Jeśli wszystkie alerty w obszarze roboczym określonego zaplanowano już zostać rozszerzony, odpowiedzi na żądanie POST jest, że próba zostało zabronione (kod stanu 403). Aby wyświetlić wszelkie komunikaty o błędach lub zrozumieć, jeśli proces jest zablokowana, można przesłać żądanie GET. W przypadku komunikat o błędzie, jest zwracany, wraz z informacjami podsumowania.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3-use-a-custom-powershell-script"></a>Opcja 3: Użyj niestandardowego skryptu PowerShell
 Jeśli Microsoft nie pomyślnie rozszerzony alerty z portalu usługi Operations Management Suite do platformy Azure, możesz to zrobić ręcznie do 5 lipca 2018. Dwie opcje ręcznego rozszerzenia zostały omówione w poprzednich sekcjach.

Po 5 lipca 2018 wszystkie alerty z portalu usługi Operations Management Suite zostały rozszerzone na platformie Azure. Użytkownicy, którzy nie wykonać [kroki niezbędne korygowania sugerowane](#troubleshooting) zostaną skojarzone ich alertów uruchomiona bez wyzwalania, akcji lub powiadomień z powodu braku [grupy akcji](monitoring-action-groups.md). 

Aby utworzyć [grupy akcji](monitoring-action-groups.md) alertów ręcznie w analizy dzienników, użyj następującego skryptu przykładowego:
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>O niestandardowego skryptu PowerShell 
Poniżej przedstawiono ważne informacje o używaniu skryptu:
- Warunkiem wstępnym jest instalacja [ARMclient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager.
- Aby uruchomić skrypt, musi mieć rolę współautora lub właściciela w subskrypcji platformy Azure.
- Należy podać następujące parametry:
    - $subscriptionId: identyfikator subskrypcji platformy Azure skojarzone z obszaru roboczego Operations Management Suite Log Analytics.
    - $resourceGroup: Grupa zasobów Azure dla obszaru roboczego Operations Management Suite Log Analytics.
    - $workspaceName: Nazwa obszaru roboczego Operations Management Suite Log Analytics.

### <a name="output-of-the-custom-powershell-script"></a>Dane wyjściowe skryptu programu PowerShell niestandardowego
Skrypt jest pełne i wyprowadza kroki podczas jego działania: 
- Wyświetla podsumowanie, który zawiera informacje dotyczące istniejącego Operations Management Suite Log Analytics alerty w obszarze roboczym. Podsumowanie zawiera również informacje dotyczące grup Azure akcji ma zostać utworzony dla akcji skojarzonych z nimi. 
- Zostanie wyświetlony monit Przejdź dalej z rozszerzeniem, lub Zamknij po wyświetlania podsumowania.
- Jeśli hypervreplicavolumesize z rozszerzeniem, tworzenia nowych grup Azure akcji, a wszystkie istniejące alerty są skojarzone z nimi. 
- Skrypt zakończy pracę, wyświetlając komunikat "Rozszerzenie pełną!" W przypadku niepowodzenia pośredniego skrypt wyświetli kolejne błędy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
W trakcie rozszerzania alerty problemy mogą uniemożliwić systemu tworzenia wymaganych [grupy akcji](monitoring-action-groups.md). W takich przypadkach zostanie wyświetlony komunikat o błędzie na transparencie w **alertu** sekcji portalu Operations Management Suite lub GET wywołać gotowe do interfejsu API.

> [!IMPORTANT]
> Jeśli przed 5 lipca 2018 nie wykonać następujące kroki korygujące alerty będą działać na platformie Azure, ale nie zostanie wyzwolony żadnych akcji lub powiadomienia. Aby otrzymywać powiadomienia dla alertów, należy ręcznie edytować i dodać [grupy akcji](monitoring-action-groups.md), lub użyj poprzedniego [niestandardowego skryptu PowerShell](#option-3---using-custom-powershell-script).

Poniżej przedstawiono kroki korygujące dla każdego błędu:
- **Błąd: Zakres blokady jest obecna na poziomie grupy zasobów subskrypcji dla operacji zapisu**: ![zrzut ekranu przedstawiający Operations Management Suite ustawienia alertu strony portalu, zakres blokady komunikat o błędzie wyróżnione](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Gdy zakres blokady jest włączona, funkcja ogranicza wszelkie nowe zmiany w grupie zasobów lub subskrypcji, która zawiera obszar roboczy analizy dzienników (Operations Management Suite). System nie może rozszerzyć alerty na platformie Azure i tworzenie grup niezbędnych działań.
    
    Aby rozwiązać, należy usunąć *tylko do odczytu* blokady w grupie zasobów lub subskrypcji zawierający obszar roboczy. Można to zrobić za pomocą portalu Azure, programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API. Aby dowiedzieć się więcej, zobacz [użycia zasobów blokady](../azure-resource-manager/resource-group-lock-resources.md). 
    
    Jeśli rozwiązania problemu, wykonaj kroki przedstawione w artykule Operations Management Suite rozszerza alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie. Nie trzeba podejmować dalszych działań lub zainicjować żadnych czynności.

- **Błąd: Zasady są obecne na poziomie grupy zasobów subskrypcji**: ![zrzut ekranu strony Operations Management Suite portalu ustawienia alertów, zasad komunikat o błędzie wyróżnione](./media/monitor-alerts-extend/ErrorPolicy.png)

    Gdy [zasadami Azure](../azure-policy/azure-policy-introduction.md) jest stosowane, ogranicza wszystkich nowych zasobów w grupie zasobów lub subskrypcji, która zawiera obszar roboczy analizy dzienników (Operations Management Suite). System nie może rozszerzyć alerty na platformie Azure i tworzenie grup niezbędnych działań.
    
    Aby rozwiązać, edytować zasad, która powoduje *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* błąd, który uniemożliwia tworzenie nowych zasobów w grupie zasobów lub subskrypcji zawierający obszar roboczy. Można to zrobić za pomocą portalu Azure, programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API. Można przeprowadzić inspekcję działań, aby znaleźć odpowiednie zasady, która powoduje błąd. Aby dowiedzieć się więcej, zobacz [wyświetlanie działania dzienników inspekcji akcje](../azure-resource-manager/resource-group-audit.md). 
    
    Jeśli rozwiązania problemu, wykonaj kroki przedstawione w artykule Operations Management Suite rozszerza alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie. Nie trzeba podejmować dalszych działań lub zainicjować żadnych czynności.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowe [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
