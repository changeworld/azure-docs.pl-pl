---
title: Rozszerzanie alertów z usługi Log Analytics na platformie Azure Government Cloud
description: W tym artykule opisano, narzędzi i interfejsów API za pomocą którego można rozszerzyć alerty z usługą Log Analytics do alertów platformy Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60431178"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Rozszerzanie alertów z usługi Log Analytics do alertów platformy Azure
Funkcja alertów w portalu pakietu OMS jest zastępowany przez alerty platformy Azure w chmurze Azure Government. W ramach tego przejścia alerty, które pierwotnie skonfigurowane w usłudze Log Analytics zostanie rozszerzony na platformę Azure. Jeśli nie chcesz poczekać na ich automatycznie przeniesione na platformę Azure, możesz zainicjować proces:

- Ręcznie z portalu Operations Management Suite. 
- Programowe korzystając z interfejsu API AlertsVersion.  

> [!NOTE]
> Firma Microsoft automatycznie rozszerzy alerty utworzone w portalu wystąpień platformy Azure dla instytucji rządowych w pakiecie OMS usługi Log Analytics do alertów platformy Azure, począwszy od 1 marca 2019 r w sposób systematyczny. Jeśli masz problemy z tworzeniem [grup akcji](../../azure-monitor/platform/action-groups.md), użyj [te czynności zaradczych](alerts-extend-tool.md#troubleshooting) można pobrać grup akcji utworzone automatycznie. W portalu Azure dla instytucji rządowych OMS, można użyć tych kroków do 15 marca 2019 r.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opcja 1: Inicjuj z portalu Operations Management Suite
Poniżej opisano sposób rozszerzyć alerty dla obszaru roboczego z portalu Operations Management Suite w chmurze Azure dla instytucji rządowych.  

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. W okienku subskrypcji usługi Log Analytics wybierz obszar roboczy, a następnie wybierz **portalu pakietu OMS** kafelka.
![W okienku subskrypcji zrzut ekranu usługi Log Analytics, za pomocą portalu pakietu OMS kafelka wyróżnione](media/alerts-extend-tool/azure-portal-01.png) 
3. Po użytkownik zostanie przekierowany do portalu Operations Management Suite, wybierz **ustawienia** ikony.
![Zrzut ekranu z pakietu Operations Management Suite portalu z wyróżnioną ikonę ustawienia](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Z **ustawienia** wybierz opcję **alerty**.  
5. Wybierz **rozszerzone na platformę Azure**.
![Zrzut ekranu z pakietu Operations Management Suite ustawienia alertów strony portalu, za pomocą Rozszerz do platformy Azure z wyróżnioną pozycją](media/alerts-extend-tool/ExtendInto.png)
6. Wyświetlany jest Kreator trzech krokach **alerty** okienka. Zapoznaj się z omówieniem, a następnie wybierz pozycję **dalej**.
![Zrzut ekranu przedstawiający kreatora w kroku 1](media/alerts-extend-tool/ExtendStep1.png)  
7. W drugim kroku zostanie wyświetlone podsumowanie proponowanych zmian i listę odpowiednich [grup akcji](../../azure-monitor/platform/action-groups.md) dla alertów. Jeśli podobnych działań są widoczne w więcej niż jeden alert, proponowanych przez kreatora skojarzyć grupy pojedynczej akcji z wszystkich z nich.  Konwencja nazewnictwa jest następująca: *WorkspaceName_AG_ #Number*. Aby kontynuować, wybierz **dalej**.
![Zrzut ekranu przedstawiający krok 2 Kreatora](media/alerts-extend-tool/ExtendStep2.png)  
8. W ostatnim kroku kreatora wybierz **Zakończ**i upewnij się, gdy zostanie wyświetlony monit, aby zainicjować proces. Opcjonalnie można podać adres e-mail, dzięki czemu otrzymasz powiadomienie, gdy proces zostanie zakończony i wszystkich alertów została pomyślnie przeniesiona do alertów platformy Azure.
![Zrzut ekranu przedstawiający krok 3 kreatora](media/alerts-extend-tool/ExtendStep3.png)

Po zakończeniu działania kreatora, na **ustawienia alertów** strona, możliwość rozszerzanie alertów do platformy Azure zostanie usunięta. W tle alerty zostaną przeniesione na platformę Azure, a to może zająć trochę czasu. Podczas operacji nie możesz wprowadzać zmian do alertów z portalu Operations Management Suite. Można wyświetlić bieżący stan z Baner w górnej części portalu. Jeśli adres e-mail podany wcześniej, otrzymasz wiadomość e-mail, gdy proces zostanie pomyślnie zakończony.  


Alerty nadal będą wyświetlane w portalu Operations Management Suite, nawet w przypadku, po pomyślnym przeniesieniu ich do platformy Azure.
![Strona Ustawienia alertów portal zrzut ekranu z pakietu Operations Management Suite](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opcja 2: Użyj interfejs API AlertsVersion
Aby rozszerzyć alerty z usługą Log Analytics do alertów platformy Azure za pomocą dowolnego klienta, który można wywołać interfejs API REST, można użyć interfejsu API AlertsVersion usługi Log Analytics. Możesz uzyskać dostęp interfejsu API z poziomu programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia typu open source. Może zapewniać dane wyjściowe wyniki w formacie JSON.  

Aby korzystać z interfejsu API, należy najpierw utworzyć żądanie GET. Ocenia i zwraca podsumowanie proponowanych zmian przed podjęciem próby faktycznie rozszerzone na platformę Azure za pomocą żądania POST. Wynikiem jest lista alertów i listę proponowanych [grup akcji](../../azure-monitor/platform/action-groups.md), w formacie JSON. Jeśli podobnych działań są widoczne w więcej niż jeden alert, usługa proponuje skojarzyć wszystkich z nich z grupą jednej akcji. Konwencja nazewnictwa jest następująca: *WorkspaceName_AG_ #Number*.

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
                            "serviceUri": "https://test.com"
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

Ta odpowiedź wskazuje, że alerty zostały pomyślnie rozszerzone do alertów platformy Azure. Właściwość wersja jest tylko do sprawdzania, czy alerty zostały rozszerzone do platformy Azure i nie są powiązane do [interfejsu API wyszukiwania usługi Log Analytics](../../azure-monitor/platform/api-alerts.md). Gdy alerty zostały pomyślnie rozszerzone na platformę Azure, dowolny adresy e-mail pod warunkiem wpis żądania są wysyłane raportu. Jeśli wszystkie alerty w obszarze roboczym określonego są już zaplanowane być rozszerzony, odpowiedzi na żądania POST jest, czy próba zostało zabronione (kod stanu 403). Aby wyświetlić wszelkie komunikaty o błędach lub zrozumieć ten proces jest zatrzymany, możesz przesłać żądanie GET. W przypadku komunikatu o błędzie jest zwracany, wraz z informacjami podsumowania.

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
                            "serviceUri": "https://test.com"
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

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Podczas procesu rozszerzania alertów, problemów może uniemożliwić systemu tworzenia wymaganych [grup akcji](../../azure-monitor/platform/action-groups.md). W takiej sytuacji zobaczysz komunikat o błędzie na banerze w **alertu** sekcji portalu Operations Management Suite lub GET wywołać gotowe do interfejsu API.

> [!IMPORTANT]
> W przypadku opartego na chmurze platformy Azure Government użytkownicy portalu pakietu OMS nie wykonaj następujące kroki korygowania przed 15 marca 2019 r, alerty zostaną wykonane na platformie Azure, ale nie będą uruchamiane w dowolnej akcji powiadamiania. Aby otrzymywać powiadomienia o alertach, należy ręcznie edytować własnych reguł alertów na platformie Azure i dodać [grupy akcji](../../azure-monitor/platform/action-groups.md)

Poniżej przedstawiono kroki korygowania dla każdego błędu:
- **Błąd: Zakres blokady występuje na poziomie grupy zasobów/subskrypcji dla operacji zapisu**:   ![Zrzut ekranu przedstawiający pakietu Operations Management Suite ustawienia alertów stronie portalu, zakres blokady komunikat o błędzie wyróżniony](media/alerts-extend-tool/ErrorScopeLock.png)

    Gdy zakres blokady jest włączona, ta funkcja ogranicza wszelkie nowe zmiany w grupie subskrypcji lub zasobu, która zawiera obszar roboczy usługi Log Analytics (Operations Management Suite). System nie mógł rozszerzyć alertów do platformy Azure i utworzyć niezbędnych grup akcji.
    
    Aby rozwiązać problem, Usuń *tylko do odczytu* blokadę dla Twojej subskrypcji lub grupy zasobów, która zawiera obszar roboczy. Można to zrobić za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API. Aby dowiedzieć się więcej, zobacz [użycia blokady zasobów](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    Po naprawieniu błędu wykonując kroki przedstawione w artykule, pakiet Operations Management Suite rozszerza alertów do platformy Azure w ramach następnego dnia zaplanowane uruchomienie. Nie trzeba podejmować żadnych dalszych akcji lub zainicjować niczego.

- **Błąd: Ma zasady na poziomie grupy zasobów/subskrypcji**:   ![Zrzut ekranu przedstawiający pakietu Operations Management Suite ustawienia alertów stronie portalu, zasady komunikat o błędzie wyróżniony](media/alerts-extend-tool/ErrorPolicy.png)

    Gdy [usługi Azure Policy](../../governance/policy/overview.md) jest stosowany, ogranicza nowego zasobu w grupie subskrypcji lub zasobu, która zawiera obszar roboczy usługi Log Analytics (Operations Management Suite). System nie mógł rozszerzyć alertów do platformy Azure i utworzyć niezbędnych grup akcji.
    
    Aby rozwiązać problem, Edytuj zasady, które powoduje *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* błąd, który uniemożliwia tworzenie nowych zasobów w Twojej subskrypcji lub grupy zasobów, która zawiera obszar roboczy. Można to zrobić za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API. Można przeprowadzać ich inspekcje działania, aby znaleźć odpowiednie zasady, który jest przyczyną błędu. Aby dowiedzieć się więcej, zobacz [wyświetlanie dzienników aktywności do inspekcji akcji](../../azure-resource-manager/resource-group-audit.md). 
    
    Po naprawieniu błędu wykonując kroki przedstawione w artykule, pakiet Operations Management Suite rozszerza alertów do platformy Azure w ramach następnego dnia zaplanowane uruchomienie. Nie trzeba podejmować żadnych dalszych akcji lub zainicjować niczego.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowym [środowisko Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
* Dowiedz się więcej o [alerty dzienników w alertach Azure](alerts-unified-log.md).

