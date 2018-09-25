---
title: Rozszerzanie alertów z Analytcs dziennika do platformy Azure
description: W tym artykule opisano, narzędzi i interfejsów API za pomocą którego można rozszerzyć alerty z usługą Log Analytics do alertów platformy Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: d70eecb6a5d6bafbfa6507dbe8b1bcb1cad67191
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990250"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Rozszerzanie alertów z usługi Log Analytics do alertów platformy Azure
Funkcja alertów w usłudze Azure Log Analytics jest zastępowany przez alerty platformy Azure. W ramach tego przejścia alerty, które pierwotnie skonfigurowane w usłudze Log Analytics zostanie rozszerzony na platformę Azure. Jeśli nie chcesz poczekać na ich automatycznie przeniesione na platformę Azure, możesz zainicjować proces:

- Ręcznie z portalu Operations Management Suite. 
- Programowe korzystając z interfejsu API AlertsVersion.  

> [!NOTE]
> Firma Microsoft automatycznie rozszerzy alerty utworzone w wystąpieniach chmury publicznej usługi Log Analytics do alertów platformy Azure od 14 maja 2018 r. w serii cyklicznych do momentu zakończenia. Jeśli masz problemy z tworzeniem [grup akcji](monitoring-action-groups.md), użyj [te czynności zaradczych](monitoring-alerts-extend-tool.md#troubleshooting) można pobrać grup akcji utworzone automatycznie. Te kroki można użyć do 5 lipca 2018 r. *Nie dotyczy rządu platformy Azure i użytkownicy usługi Log Analytics w chmurze Soveriegn*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opcja 1: Zainicjuj z portalu Operations Management Suite
Poniżej opisano sposób rozszerzyć alerty dla obszaru roboczego z portalu Operations Management Suite.  

1. W witrynie Azure portal wybierz **wszystkich usług**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. W okienku subskrypcji usługi Log Analytics wybierz obszar roboczy, a następnie wybierz **portalu pakietu OMS** kafelka.
![W okienku subskrypcji zrzut ekranu usługi Log Analytics, za pomocą portalu pakietu OMS kafelka wyróżnione](./media/monitor-alerts-extend/azure-portal-01.png) 
3. Po użytkownik zostanie przekierowany do portalu Operations Management Suite, wybierz **ustawienia** ikony.
![Zrzut ekranu z pakietu Operations Management Suite portalu z wyróżnioną ikonę ustawienia](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Z **ustawienia** wybierz opcję **alerty**.  
5. Wybierz **rozszerzone na platformę Azure**.
![Zrzut ekranu z pakietu Operations Management Suite ustawienia alertów strony portalu, za pomocą Rozszerz do platformy Azure z wyróżnioną pozycją](./media/monitor-alerts-extend/ExtendInto.png)
6. Wyświetlany jest Kreator trzech krokach **alerty** okienka. Zapoznaj się z omówieniem, a następnie wybierz pozycję **dalej**.
![Zrzut ekranu przedstawiający kreatora w kroku 1](./media/monitor-alerts-extend/ExtendStep1.png)  
7. W drugim kroku zostanie wyświetlone podsumowanie proponowanych zmian i listę odpowiednich [grup akcji](monitoring-action-groups.md) dla alertów. Jeśli podobnych działań są widoczne w więcej niż jeden alert, proponowanych przez kreatora skojarzyć grupy pojedynczej akcji z wszystkich z nich.  Konwencja nazewnictwa jest następująca: *WorkspaceName_AG_ #Number*. Aby kontynuować, wybierz **dalej**.
![Zrzut ekranu przedstawiający krok 2 Kreatora](./media/monitor-alerts-extend/ExtendStep2.png)  
8. W ostatnim kroku kreatora wybierz **Zakończ**i upewnij się, gdy zostanie wyświetlony monit, aby zainicjować proces. Opcjonalnie można podać adres e-mail, dzięki czemu otrzymasz powiadomienie, gdy proces zostanie zakończony i wszystkich alertów została pomyślnie przeniesiona do alertów platformy Azure.
![Zrzut ekranu przedstawiający krok 3 kreatora](./media/monitor-alerts-extend/ExtendStep3.png)

Po zakończeniu działania kreatora, na **ustawienia alertów** strona, możliwość rozszerzanie alertów do platformy Azure zostanie usunięta. W tle alerty zostaną przeniesione na platformę Azure, a to może zająć trochę czasu. Podczas operacji nie możesz wprowadzać zmian do alertów z portalu Operations Management Suite. Można wyświetlić bieżący stan z Baner w górnej części portalu. Jeśli adres e-mail podany wcześniej, otrzymasz wiadomość e-mail, gdy proces zostanie pomyślnie zakończony.  


Alerty nadal będą wyświetlane w portalu Operations Management Suite, nawet w przypadku, po pomyślnym przeniesieniu ich do platformy Azure.
![Strona Ustawienia alertów portal zrzut ekranu z pakietu Operations Management Suite](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opcja 2: Użycie interfejs API AlertsVersion
Aby rozszerzyć alerty z usługą Log Analytics do alertów platformy Azure za pomocą dowolnego klienta, który można wywołać interfejs API REST, można użyć interfejsu API AlertsVersion usługi Log Analytics. Możesz uzyskać dostęp interfejsu API z poziomu programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia typu open source. Może zapewniać dane wyjściowe wyniki w formacie JSON.  

Aby korzystać z interfejsu API, należy najpierw utworzyć żądanie GET. Ocenia i zwraca podsumowanie proponowanych zmian przed podjęciem próby faktycznie rozszerzone na platformę Azure za pomocą żądania POST. Wynikiem jest lista alertów i listę proponowanych [grup akcji](monitoring-action-groups.md), w formacie JSON. Jeśli podobnych działań są widoczne w więcej niż jeden alert, usługa proponuje skojarzyć wszystkich z nich z grupą jednej akcji. Konwencja nazewnictwa jest następująca: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli żądanie GET zakończy się pomyślnie, kod stanu HTTP 200 został zwrócony, wraz z listy alertów, a proponowane grup akcji danych JSON. Poniżej zamieszczono przykładową odpowiedź:

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
Jeśli określony obszar roboczy nie ma żadnych reguł alertów, które zostały zdefiniowane, dane JSON zwróci następujące czynności:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Jeśli wszystkie reguły alertów w określony obszar roboczy już zostały rozszerzone na platformę Azure, jest odpowiedź na żądanie GET:

```json
{
    "version": 2
}
```

Aby zainicjować migracji alertów do platformy Azure, zainicjować odpowiedzi na WPIS. W odpowiedzi na WPIS potwierdza swoje przeznaczenie, a także akceptacji, mieć alerty rozszerzonej z usługą Log Analytics do alertów platformy Azure. Zaplanowano działania i alerty są przetwarzane, jak wskazano w oparciu o wyniki podczas wykonywania odpowiedzi GET wcześniej. Opcjonalnie możesz podać listę adresów e-mail, do których usługi Log Analytics wysyła raport po pomyślnym zakończeniu proces w tle zaplanowanej migracji alertów. Można użyć w poniższym przykładzie żądania:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Wynik alerty Migrowanie do alertów platformy Azure może zależeć od podsumowanie podał odpowiedź GET. Podczas planowania alerty w usłudze Log Analytics są tymczasowo niedostępne do modyfikacji w portalu Operations Management Suite. Można jednak utworzyć nowe alerty. 

Jeśli żądanie POST zakończy się pomyślnie, zwraca stan HTTP 200 OK, wraz z następującą odpowiedź:

```json
{
    "version": 2
}
```

Ta odpowiedź wskazuje, że alerty zostały pomyślnie rozszerzone do alertów platformy Azure. Właściwość wersja jest tylko do sprawdzania, czy alerty zostały rozszerzone do platformy Azure i nie są powiązane do [interfejsu API wyszukiwania usługi Log Analytics](../log-analytics/log-analytics-api-alerts.md). Gdy alerty zostały pomyślnie rozszerzone na platformę Azure, dowolny adresy e-mail pod warunkiem wpis żądania są wysyłane raportu. Jeśli wszystkie alerty w obszarze roboczym określonego są już zaplanowane być rozszerzony, odpowiedzi na żądania POST jest, czy próba zostało zabronione (kod stanu 403). Aby wyświetlić wszelkie komunikaty o błędach lub zrozumieć ten proces jest zatrzymany, możesz przesłać żądanie GET. W przypadku komunikatu o błędzie jest zwracany, wraz z informacjami podsumowania.

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


## <a name="option-3-use-a-custom-powershell-script"></a>Opcja 3: Użyj niestandardowego skryptu programu PowerShell
 Microsoft nie pomyślnie rozszerzony alertów z portalu Operations Management Suite na platformę Azure, należy ręcznie do 5 lipca 2018 r. Dwie opcje ręcznego rozszerzenia zostały omówione w poprzednich sekcjach.

Po 5 lipca 2018 r. wszystkie alerty z portalu Operations Management Suite zostały rozszerzone na platformę Azure. Użytkownicy, którzy nie wykonać [czynności niezbędne zaradczych zasugerowanych](#troubleshooting) zostanie skojarzony alerty bez wyzwalania, akcji lub powiadomienia z powodu braku [grup akcji](monitoring-action-groups.md). 

Aby utworzyć [grup akcji](monitoring-action-groups.md) alertów ręcznie w usłudze Log Analytics, użyj następującego skryptu przykładowego:
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


### <a name="about-the-custom-powershell-script"></a>Temat niestandardowego skryptu programu PowerShell 
Poniżej przedstawiono ważne informacje dotyczące za pomocą skryptu:
- Warunkiem wstępnym jest instalacja [ARMclient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager.
- Aby uruchomić skrypt, musi mieć rolę współautora lub właściciela w subskrypcji platformy Azure.
- Należy podać następujące parametry:
    - $subscriptionId: identyfikator subskrypcji platformy Azure skojarzone z obszarem roboczym Operations Management Suite Log Analytics.
    - $resourceGroup: Grupa zasobów platformy Azure dla obszaru roboczego Operations Management Suite Log Analytics.
    - $workspaceName: Nazwa obszaru roboczego Operations Management Suite Log Analytics.

### <a name="output-of-the-custom-powershell-script"></a>Dane wyjściowe niestandardowego skryptu programu PowerShell
Skrypt jest pełny i generuje czynności, jak działa: 
- Wyświetla podsumowanie, które zawiera informacje o istniejące alerty programu Operations Management Suite Log Analytics w obszarze roboczym. Podsumowanie zawiera również informacje dotyczące grup akcji platformy Azure, który ma zostać utworzony dla akcji skojarzonych z nimi. 
- Monit hypervreplicavolumesize rozszerzenia, lub Zamknij po obejrzeniu podsumowania.
- Jeśli przejdziesz dalej z rozszerzeniem, tworzenia nowych grup akcji platformy Azure, a wszystkie istniejące alerty są skojarzone z nimi. 
- Skrypt zakończy pracę, wyświetlając komunikat "Pełną rozszerzenia!" W razie błędów pośredniego skrypt wyświetla kolejne błędy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Podczas procesu rozszerzania alertów, problemów może uniemożliwić systemu tworzenia wymaganych [grup akcji](monitoring-action-groups.md). W takiej sytuacji zobaczysz komunikat o błędzie na banerze w **alertu** sekcji portalu Operations Management Suite lub GET wywołać gotowe do interfejsu API.

> [!IMPORTANT]
> W przypadku chmury publicznej platformy Azure na podstawie użytkowników usługi Log Analytics nie wykonaj następujące kroki korygowania przed 5 lipca 2018 r., alerty zostaną wykonane na platformie Azure, ale nie będą uruchamiane w dowolnej akcji powiadomienia. Aby otrzymywać powiadomienia o alertach, należy ręcznie edytować i Dodaj [grup akcji](monitoring-action-groups.md), lub użyj kroku [niestandardowego skryptu programu PowerShell](#option-3---using-custom-powershell-script).

Poniżej przedstawiono kroki korygowania dla każdego błędu:
- **Błąd: Blokada zakres jest obecny na poziomie grupy zasobów/subskrypcji dla operacji zapisu**: ![zrzut ekranu przedstawiający pakietu Operations Management Suite ustawienia alertów stronie portalu, zakres blokady komunikat o błędzie wyróżniony](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Gdy zakres blokady jest włączona, ta funkcja ogranicza wszelkie nowe zmiany w grupie subskrypcji lub zasobu, która zawiera obszar roboczy usługi Log Analytics (Operations Management Suite). System nie mógł rozszerzyć alertów do platformy Azure i utworzyć niezbędnych grup akcji.
    
    Aby rozwiązać problem, Usuń *tylko do odczytu* blokadę dla Twojej subskrypcji lub grupy zasobów, która zawiera obszar roboczy. Można to zrobić za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API. Aby dowiedzieć się więcej, zobacz [użycia blokady zasobów](../azure-resource-manager/resource-group-lock-resources.md). 
    
    Po naprawieniu błędu wykonując kroki przedstawione w artykule, pakiet Operations Management Suite rozszerza alertów do platformy Azure w ramach następnego dnia zaplanowane uruchomienie. Nie trzeba podejmować żadnych dalszych akcji lub zainicjować niczego.

- **Błąd: Zasady są obecne na poziomie grupy zasobów/subskrypcji**: ![zrzut ekranu przedstawiający pakietu Operations Management Suite ustawienia alertów stronie portalu, zasady komunikat o błędzie wyróżniony](./media/monitor-alerts-extend/ErrorPolicy.png)

    Gdy [usługi Azure Policy](../azure-policy/azure-policy-introduction.md) jest stosowany, ogranicza nowego zasobu w grupie subskrypcji lub zasobu, która zawiera obszar roboczy usługi Log Analytics (Operations Management Suite). System nie mógł rozszerzyć alertów do platformy Azure i utworzyć niezbędnych grup akcji.
    
    Aby rozwiązać problem, Edytuj zasady, które powoduje *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* błąd, który uniemożliwia tworzenie nowych zasobów w Twojej subskrypcji lub grupy zasobów, która zawiera obszar roboczy. Można to zrobić za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API. Można przeprowadzać ich inspekcje działania, aby znaleźć odpowiednie zasady, który jest przyczyną błędu. Aby dowiedzieć się więcej, zobacz [wyświetlanie dzienników aktywności do inspekcji akcji](../azure-resource-manager/resource-group-audit.md). 
    
    Po naprawieniu błędu wykonując kroki przedstawione w artykule, pakiet Operations Management Suite rozszerza alertów do platformy Azure w ramach następnego dnia zaplanowane uruchomienie. Nie trzeba podejmować żadnych dalszych akcji lub zainicjować niczego.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowym [środowisko Azure Alerts](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [alerty dzienników w alertach Azure](monitor-alerts-unified-log.md).
