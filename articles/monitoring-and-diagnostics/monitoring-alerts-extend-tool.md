---
title: Jak rozszerzyć alerty z Analytcs dziennika na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano narzędzia i interfejs API za pomocą którego można rozszerzyć alerty z Analiza dzienników Azure alerty.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: vinagara
ms.openlocfilehash: 0dce6e6772b4efea90df2e095ac0041641d99061
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763531"
---
# <a name="how-to-extend-alerts-from-log-analytics-into-azure-alerts"></a>Jak rozszerzyć alerty z analizy dzienników do alertów Azure
Alerty w analizy dzienników jest zastępowany Dzięki alertom w usłudze Azure i w ramach tego przejścia, alerty, które skonfigurowano w analizy dzienników zostanie rozszerzony na platformie Azure.  Jeśli nie chcesz czekać na ich automatycznie przeniesione do platformy Azure, możesz zainicjować proces po jednej z opcji:

1. Ręcznie w portalu OMS 
2. Programowo przy użyciu interfejsu API AlertsVersion  

> [!NOTE]
> Firma Microsoft automatycznie rozszerzy alerty utworzone w Analiza dzienników Azure alertów, uruchamianie na **14 maja 2018** etapami aż do zakończenia. Od tego dnia Microsoft rozpocznie się do planowania migracji alerty na platformie Azure, a podczas to przejście alertami można zarządzać z portalu OMS i portalu Azure. Ten proces jest bezpieczna i nie interruptive.  

## <a name="option-1---initiate-from-the-oms-portal"></a>Opcja 1 — inicjowanie z portalu OMS
W poniższych krokach opisano, jak rozszerzyć alerty w obszarze roboczym z portalu OMS.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. W okienku subskrypcje analizy dzienników, wybierz obszar roboczy, a następnie wybierz **portalu OMS** kafelka.<br><br> ![Przycisk Wyszukiwanie w dziennikach](./media/monitor-alerts-extend/azure-portal-01.png)<br><br> 
3. Po nastąpi przekierowanie do portalu OMS, kliknij Kafelek ustawienia po prawej stronie górnej części strony.<br><br> ![Portalu OMS opcji ustawień](./media/monitor-alerts-extend/oms-portal-settings-option.png)<br><br> 
4. Z **ustawienia** wybierz pozycję **alerty**.  
5. Kliknij przycisk **Rozszerz na platformie Azure**.<br><br> ![Strony portalu ustawienia alertu OMS z opcją Rozszerz](./media/monitor-alerts-extend/ExtendInto.png)
6. Kreator jest wyświetlone w okienku pierwszy trzy kroki udostępnienie omówienia procesu.  Kliknij przycisk **dalej** aby kontynuować.<br><br> ![Rozszerzyć alertów z usługi Analiza dzienników Azure — krok 1](./media/monitor-alerts-extend/ExtendStep1.png)  
7. W drugim kroku przedstawiono podsumowanie proponowanych zmian listę odpowiednich [grupy akcji](monitoring-action-groups.md) alertów. Jeśli podobne akcje są widoczne w więcej niż jeden alert, usługa zaproponuje do skojarzenia z wszystkich z nich grupy jednej akcji.  Grupa działań proponowana, postępuj zgodnie z konwencją nazewnictwa: *WorkspaceName_AG_ #Number*. Aby kontynuować, kliknij przycisk **dalej**.<br><br> ![rozszerzyć alertów z usługi Analiza dzienników Azure — krok 2](./media/monitor-alerts-extend/ExtendStep2.png)  
8. W ostatnim kroku kreatora, kliknij przycisk **Zakończ** i upewnij się, gdy zostanie wyświetlony monit, aby zainicjować proces.  Opcjonalnie można określić adresy e-mail, co użytkownik jest powiadamiany o zakończeniu procesu i wszystkie alerty została pomyślnie przeniesiona do alertów Azure.<br><br> ![Rozszerzyć alertów z usługi Analiza dzienników Azure — krok 3](./media/monitor-alerts-extend/ExtendStep3.png)

Po zakończeniu działania kreatora, zwróć uwagę na **ustawienia alertu** strona, która opcję, aby rozszerzyć alerty do platformy Azure zostanie usunięta.  W tle alerty zostaną przeniesione na platformie Azure, a to może zająć trochę czasu.  Podczas operacji nie można wprowadzać zmiany do alertów w portalu OMS.  Bieżący stan będą wyświetlane z banerze w górnej części portalu, a jeśli wcześniej podany adres e-mail otrzymasz wiadomość e-mail po pomyślnym zakończeniu procesu.  


Alerty nadal wyświetlane w portalu OMS, nawet po zostały przeniesione do platformy Azure.<br><br> ![Po przeniesieniu alertów w Analiza dzienników Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-the-alertsversion-api"></a>Opcja 2 — przy użyciu AlertsVersion interfejsu API
Interfejs API AlertsVersion analizy dziennika umożliwia rozszerzanie alerty z analizy dzienników do alertów Azure za pomocą dowolnego klienta, który można wywołać interfejsu API REST. Są dostępne przy użyciu programu PowerShell [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager. Korzystanie z ARMClient i programu PowerShell jest jedną z wielu opcji, aby uzyskać dostępu do interfejsu API.  Korzystanie z interfejsu API będzie zapisuje wyniki w formacie JSON.  

Aby korzystać z interfejsu API, należy najpierw utworzyć żądania GET, który przetwarza i zwraca podsumowanie proponowanych zmian przed przystąpieniem do faktycznie rozszerzany także na platformie Azure przy użyciu żądania POST. Lista wyników alertami i listę proponowanych [grupy akcji](monitoring-action-groups.md) w formacie JSON.  Jeśli podobne akcje są widoczne w więcej niż jeden alert, usługa zaproponuje kojarzenie wszystkich z nich z grupą jednej akcji.  Grupy akcji proponowane postępuj zgodnie z konwencją nazewnictwa: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli żądanie GET zakończy się pomyślnie, wraz z listy alertów i grup proponowanego działania w formacie JSON dane zwracany jest kod stanu HTTP 200. Poniżej przedstawiono przykład odpowiedzi:

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
Jeśli określony obszar roboczy nie ma żadnych reguł alertów zdefiniowane, wraz ze stanem HTTP 200 OK kod operacji GET dane JSON zwraca:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Jeśli wszystkie reguły alertu w obszarze roboczym określonego zostały rozszerzone na platformie Azure — odpowiedzi na żądanie GET jest:

```json
{
    "version": 2
}
```

Aby zainicjować migrację alerty na platformie Azure, inicjują odpowiedź POST. Odpowiedź POST potwierdza z celem, a także akceptacji mają alerty z analizy dzienników dla Auzre alertów.  Działanie zostało zaplanowane i alerty są przetwarzane wskazane oparte na wynikach podczas wcześniej wykonywanej odpowiedzi GET.  Opcjonalnie można udostępnić listę adresów e-mail, do których Log Analytics będzie poczty raportu po pomyślnym zakończeniu tła zaplanowanego procesu migracji alerty.  Jest to wykonywane przy użyciu następującego żądania:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Powoduje Migrowanie alerty do alertów Azure mogą różnić w zależności od podsumowanie podał odpowiedź GET.  Jeśli zaplanowane, alerty w analizy dzienników będzie tymczasowo niedostępna do edycji/modyfikacji w portalu OMS.  Można jednak tworzyć nowe alerty. 

Jeśli żądanie POST zakończy się pomyślnie, zwraca stan HTTP 200 OK wraz z następującą odpowiedź:

```json
{
    "version": 2
}
```

Odpowiedź wskazuje, że alerty zostały pomyślnie rozszerzone do alertów Azure. Właściwość wersja jest tylko do sprawdzania, czy alerty zostały rozszerzone na platformie Azure i nie są powiązane do [interfejsu API Search analizy dziennika](../log-analytics/log-analytics-api-alerts.md). Po alerty zostały rozszerzone pomyślnie na platformie Azure, każdej wiadomości e-mail, adresów, pod warunkiem przy użyciu metody POST żądania są wysyłane raport zawierający szczegółowe informacje o zmianach wykonywane.  Jeśli zaplanowano wszystkie alerty w obszarze roboczym określonego już być rozszerzony, odpowiedź na żądanie POST jest 403 stan kod znaczenie, które nie zostało zabronione. Aby wyświetlić wszelkie komunikaty o błędach lub zrozumieć, jeśli proces jest zablokowana, można przesłać żądania GET i komunikat o błędzie, jeśli istnieje, zostanie zwrócony oraz podsumowanie informacji.

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


## <a name="option-3---using-custom-powershell-script"></a>Opcja 3 - przy użyciu niestandardowego skryptu PowerShell
 Po 14 maja 2018 — jeśli Microsoft nie pomyślnie rozszerzony alerty z portalu OMS Azure; następnie do **5 lipca 2018** — użytkownik może ręcznie wykonaj te same czynności za pomocą [opcja 1 — za pomocą interfejsu GUI](#option-1---initiate-from-the-oms-portal) lub [opcja 2 — za pośrednictwem interfejsu API](#option-2---using-the-alertsversion-api).

Po **5 lipca 2018** — wszystkie alerty z portalu OMS zostanie rozszerzony na platformie Azure. Użytkownicy, którzy nie wykonać [kroki niezbędne korygowania sugerowane](#troubleshooting), zostaną skojarzone ich alertów uruchomiona bez wyzwalania, akcji lub powiadomień z powodu braku [grupy akcji](monitoring-action-groups.md). 

Aby ręcznie utworzyć [grupy akcji](monitoring-action-groups.md) dla alertów w analizy dzienników, użytkownicy mogą używać Poniższy przykładowy skrypt.
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

    
        # ARM call to create action group
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


**Przy użyciu niestandardowego skryptu PowerShell** 
- Wstępnie wymagane jest instalacja [ARMclient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager
- Użytkownik uruchamiający wspomnianej skryptu musi mieć rolę współautora lub właściciela w subskrypcji platformy Azure
- Parametry skryptu są następujące:
    - $subscriptionId: identyfikator subskrypcji platformy Azure skojarzone z obszarem roboczym pakietu OMS/LA
    - $resourceGroup: grupy zasobów platformy Azure gdzie znajduje się obszar roboczy OMS/LA
    - $workspaceName: Nazwa obszaru roboczego OMS/LA

**Dane wyjściowe skryptu programu PowerShell niestandardowego** skrypt jest pełne i dane wyjściowe obejmują czynności podczas wykonywania. 
- Wyświetli podsumowanie, która zawiera informacje o istniejących OMS/LA alerty w obszarze roboczym i grup Azure akcji, który ma zostać utworzony dla akcji skojarzonych z nimi. 
- Przejdź dalej z rozszerzeniem lub zamknąć po obejrzeniu Podsumowanie użytkownika pojawi się monit.
- Monity użytkownika do korzystania z rozszerzeniem, zostaną utworzone nowe grupy Azure akcji, a wszystkie istniejące alerty zostaną skojarzone z nimi. 
- Skrypt zakończy pracę w celu, wyświetlając komunikat "ukończyć rozszerzenia!." W przypadku niepowodzenia pośredniego pojawi się kolejne błędy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Podczas rozszerzania alerty z usługą OMS na platformie Azure, może być okazjonalne problem, który uniemożliwia tworzenie niezbędne system [grupy akcji](monitoring-action-groups.md). W takich przypadkach komunikat o błędzie będzie wyświetlana w portalu OMS za pośrednictwem Baner w sekcji alertów i wywołanie GET gotowe do interfejsu API.

> [!WARNING]
> Jeśli użytkownik nie przyjmuje czynności korygujące precribed poniżej przed **5 lipca 2018** —, a następnie alerty zostaną uruchomione na platformie Azure, ale bez wyzwalania, akcji ani powiadomień. Aby otrzymywać powiadomienia dla alertów, użytkowników należy ręcznie edytować i dodać [grupy akcji](monitoring-action-groups.md) lub użyj [niestandardowego skryptu PowerShell](#option-3---using-custom-powershell-script) podanego powyżej.

Poniżej przedstawiono procedurę korygowania dla każdego błędu:
1. **Błąd: Zakres blokady jest obecna na poziomie grupy zasobów subskrypcji dla operacji zapisu**: ![strony portalu ustawienia alertu OMS komunikat o błędzie ScopeLock](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Gdy zakres blokady jest włączona, ograniczanie żadnych nowych zmian w subskrypcji lub grupy zasobów zawierającej obszaru roboczego analizy dzienników (OMS); system nie może rozszerzyć alerty (Kopiuj) na platformie Azure i tworzenie grup niezbędnych działań.
    
    b. Aby rozwiązać, należy usunąć *tylko do odczytu* blokady w grupie zasobów lub subskrypcji zawierający obszar roboczy; przy użyciu portalu Azure, programu Powershell, interfejsu wiersza polecenia Azure lub interfejsu API. Aby dowiedzieć się więcej, Wyświetl artykuł na [użycia zasobów blokady](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Po rozpoznaniu zgodnie z harmonogramem kroki przedstawione w artykule, OMS rozszerzenie alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie; bez konieczności żadnej akcji ani inicjowania.

2. **Błąd: Zasady są obecne na poziomie grupy zasobów subskrypcji**: ![strony portalu ustawienia alertu OMS komunikat o błędzie zasad](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Gdy [zasad usługi Azure](../azure-policy/azure-policy-introduction.md) jest stosowana, ograniczanie wszystkich nowych zasobów w subskrypcji lub grupy zasobów zawierającej obszaru roboczego analizy dzienników (OMS); system nie może rozszerzyć alerty (Kopiuj) na platformie Azure i tworzenie grup niezbędnych działań.
    
    b. Aby rozwiązać, należy edytować zasady powodują *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* błąd, który uniemożliwia tworzenie nowych zasobów w grupie zasobów lub subskrypcji zawierający obszar roboczy. Przy użyciu portalu Azure, programu Powershell, interfejsu wiersza polecenia Azure lub interfejsu API; można przeprowadzić inspekcję działań, aby znaleźć odpowiednie zasady, powoduje błąd. Aby dowiedzieć się więcej, Wyświetl artykuł na [wyświetlanie działania dzienników inspekcji akcje](../azure-resource-manager/resource-group-audit.md). 
    
    c. Po rozpoznaniu zgodnie z harmonogramem kroki przedstawione w artykule, OMS rozszerzenie alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie; bez konieczności żadnej akcji ani inicjowania.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowe [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
