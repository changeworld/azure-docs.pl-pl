---
title: Jak rozszerzyć (Kopiuj) alerty z portalu OMS na platformie Azure | Dokumentacja firmy Microsoft
description: Narzędzia i interfejs API za pomocą którego rozszerzeniu alerty z usługą OMS do alertów Azure może odbywać się przez klientów dobrowolnie.
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
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 241ac027a0606f901f51d6a20b9a48a2cf7a9fcf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Jak rozszerzyć alerty (Kopiuj) z pakietu OMS na platformie Azure
Począwszy od **14 maja 2018**, wszystkich klientów przy użyciu alertów, które są skonfigurowane w [programu Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), zostanie rozszerzony na platformie Azure. Alerty, które zostały rozszerzone na platformie Azure zachowują się tak samo jak OMS. Możliwości monitorowania pozostaną nienaruszone. Rozszerzające alertach tworzonych w OMS Azure oferuje wiele korzyści. Aby uzyskać więcej informacji o zaletach i proces rozszerzenia alerty z usługą OMS na platformie Azure, zobacz [rozszerzyć alerty z OMS Azure](monitoring-alerts-extend.md).

> [!NOTE]
> Uruchamianie 14 maja 2018 - Microsoft rozpocznie się proces automatycznie rozszerzanie alerty na platformie Azure. Nie wszystkie obszary robocze i alertów zostanie rozszerzony w tym dniu; Zamiast tego Microsoft rozpocznie rozszerzyć alerty automatycznie w transzach w przyszłych tygodni. Dlatego alerty w portalu OMS zostanie nie auto rozszerzania na platformie Azure natychmiast na 14 maja 2018 a użytkownika można nadal ręcznie rozszerzenie alerty za pomocą opcji szczegóły poniżej.

Klienci chcą Przenieś alerty z usługą OMS na platformie Azure, to zrobić za pomocą jednej z opcji podane.

## <a name="option-1---using-oms-portal"></a>Opcja 1 — za pomocą portalu OMS
Aby zainicjować dobrowolnie rozszerzanie alerty z portalu OMS na platformie Azure, wykonaj kroki wymienione poniżej.

1. Na stronie Przegląd portalu OMS przejdź do ustawień, a następnie sekcja alerty. Kliknij przycisk z etykietą "Rozszerzyć do platformy Azure", jako wyróżniane na poniższej ilustracji.

    ![Strony portalu ustawienia alertu OMS z opcją Rozszerz](./media/monitor-alerts-extend/ExtendInto.png)

2. Po kliknięciu przycisku kroku 3 kreatora zostanie wyświetlona, z pierwszym krokiem udostępnia szczegółowe informacje o procesie. Kliknij przycisk Dalej, aby kontynuować.

    ![Rozszerzanie alerty z portalu OMS na platformie Azure — krok 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. W drugim kroku system wyświetli podsumowanie proponowanej zmiany przez listę odpowiednich [grupy akcji](monitoring-action-groups.md), alertów w portalu OMS. Jeśli podobne akcje są widoczne w więcej niż jeden alert — system zaproponuje do skojarzenia z wszystkich z nich grupy jednej akcji.  Grupa działań proponowana, postępuj zgodnie z konwencją nazewnictwa: *WorkspaceName_AG_ #Number*. Aby kontynuować, kliknij przycisk Dalej.
Przykład ekranu poniżej.

    ![Rozszerzanie alerty z portalu OMS na platformie Azure — krok 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. W ostatnim kroku kreatora możesz poprosić portalu OMS można zaplanować rozszerzanie wszystkie alerty na platformie Azure — tworzenie nowych grup akcji i kojarzenie ich z alertami, jak pokazano wcześniej ekranu. Aby kontynuować wybierz kliknij przycisk Zakończ i upewnij się, w wierszu polecenia, aby zainicjować proces. Opcjonalnie klientów można też podać adresy e-mail, które chciałby portalu OMS o wysłanie raportu na zakończenie przetwarzania.

    ![Rozszerzanie alerty z portalu OMS na platformie Azure — krok 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Po zakończeniu pracy Kreatora kontroli powróci do strony Ustawienia alertu, a opcja "Rozszerzyć do platformy Azure" zostanie usunięta. W tle portalu OMS zaplanowane alerty w analizy dzienników na platformie Azure; to może zająć trochę czasu i po rozpoczęciu operacji krótki okres alertów w portalu OMS nie będą dostępne do modyfikacji. Bieżący stan zostanie wyświetlony za pośrednictwem transparent i jeśli adresów e-mail miejsce pod warunkiem podczas wykonywania kroku 4, następnie zostaną one informację, gdy proces w tle pomyślnie rozszerza wszystkie alerty na platformie Azure. 

6. Alerty będą nadal wyświetlane w portalu OMS, nawet po ich pomyślnie pobrać rozszerzony na platformie Azure.

    ![Po rozszerzeniu alerty w portalu OMS na platformie Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Opcja 2 — przy użyciu interfejsu API
Dla klientów, którzy programowe sterowanie lub zautomatyzować proces rozszerzania alerty w portalu OMS na platformie Azure; Firma Microsoft oferuje nowe AlertsVersion interfejsu API w ramach analizy dzienników.

Interfejs API AlertsVersion analizy dziennika jest RESTful i jest dostępny za pośrednictwem interfejsu REST API usługi Azure Resource Manager. W tym dokumencie można znaleźć przykłady którym dostęp do interfejsu API z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager. Korzystanie z ARMClient i programu PowerShell jest jedną z wielu opcji, aby uzyskać dostępu do interfejsu API. Interfejs API dane wyjściowe obejmują wyniki w formacie JSON programowane stosowanie użycia wyników na wiele sposobów.

Używając GET interfejsu API, co można uzyskać w wyniku podsumowanie proponowanej zmiany jako lista odpowiednie [grupy akcji](monitoring-action-groups.md) alertów w portalu OMS w formacie JSON formatowania. Jeśli podobne akcje są widoczne w więcej niż jeden alert — zaproponuje systemu do utworzenia skojarzyć z nimi wszystkimi grupy jednej akcji.  Grupa działań proponowana, postępuj zgodnie z konwencją nazewnictwa: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> Uzyskiwanie alertów w portalu OMS pobierania rozszerzonych na platformie Azure nie spowoduje wywołanie interfejsu API. Tylko zapewni odpowiedzi podsumowania proponowanych zmian. Aby upewnić się, te zmiany należy zrobić, aby rozszerzyć alerty na platformie Azure, POST Wywołaj należy do interfejsu API.

W przypadku powodzenia wraz z odpowiedź 200 OK GET wywołanie interfejsu API, listę JSON alerty wraz z grupy proponowanych akcji można dostarczyć. Przykładowa odpowiedź poniżej:

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
W przypadku nie ma żadnych alertów w obszarze roboczym określony, wraz z odpowiedź 200 OK dla operacji GET JSON mogą być następujące:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Jeśli wszystkie alerty w obszarze roboczym określonego zostały rozszerzone na platformie Azure — odpowiedzi na wywołanie GET należy:
```json
{
    "version": 2
}
```

Aby rozpocząć planowanie rozszerzania alerty w portalu OMS na platformie Azure, zainicjować POST do interfejsu API. Wykonanie tego polecenia/call potwierdza użytkownika zamiaru jak również akceptacji alerty w portalu OMS rozszerzony do platformy Azure i wprowadzić zmiany, jak wskazano w odpowiedzi GET wywołanie interfejsu API. Użytkownik może opcjonalnie możesz podać listę adresów e-mail, do których OMS portalu będzie poczty raportów, pomyślnie zakończenie procesu zaplanowane tła rozszerzania alerty w portalu OMS na platformie Azure.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Wynik rozszerzanie OMS portal alerty na platformie Azure, mogą się różnić od podsumowanie udostępniane przez GET - na koncie zmiany wykonane w systemie. Po zaplanowane, alerty w portalu OMS będzie tymczasowo niedostępna do edycji/modyfikacji - podczas można tworzyć nowe alerty. 

Jeśli WPIS zakończy się pomyślnie, zwraca odpowiedź 200 OK wraz z programem:
```json
{
    "version": 2
}
```
Wskazującą, czy alerty zostały rozszerzone na platformie Azure, jak wskazano w wersji 2. Ta wersja jest tylko do sprawdzania, czy alerty zostały rozszerzone na platformie Azure i nie ma wpływu wykorzystania z [interfejsu API Search analizy dziennika](../log-analytics/log-analytics-api-alerts.md). Po alerty zostały rozszerzone pomyślnie na platformie Azure, wszystkie e-mail adresy podane podczas GET będą wysyłane raport zawierający szczegółowe informacje o zmianach gotowe.

I na koniec, jeśli wszystkie alerty w obszarze roboczym określonego już zaplanowane na platformie Azure — odpowiedź POST będzie 403 Zabroniony. Aby wyświetlić wszelkie komunikaty o błędach lub określić, czy rozszerzenie procesu jest zablokowana, użytkownik może robić GET i wywołanie komunikat, jeśli dowolne zostanie zwrócony oraz podsumowanie.

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

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Podczas rozszerzania alerty z usługą OMS na platformie Azure, może być okazjonalne problem, który uniemożliwia tworzenie niezbędne system [grupy akcji](monitoring-action-groups.md). W takich przypadkach komunikat o błędzie będzie wyświetlana w portalu OMS za pośrednictwem Baner w sekcji alertów i wywołanie GET gotowe do interfejsu API.

Poniżej przedstawiono procedurę korygowania dla każdego błędu:
1. **Błąd: Subskrypcji nie jest zarejestrowany do korzystania z przestrzeni nazw "w elemencie microsoft.insights"**: ![strony portalu ustawienia alertu OMS komunikat o błędzie rejestracji](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. Nie zarejestrowano subskrypcję skojarzoną z obszarem roboczym pakietu OMS — Aby używać funkcji monitorowania Azure (elemencie microsoft.insights); ze względu na których nie można rozszerzyć alerty do monitora Azure & alerty OMS.
    
    b. Aby rozwiązać, należy zarejestrować używany w elemencie microsoft.insights (Azure monitor & alerty) w ramach subskrypcji przy użyciu programu Powershell, interfejsu wiersza polecenia Azure lub portalu Azure. Aby dowiedzieć się więcej, Wyświetl artykuł na [naprawianie błędów w rejestracji dostawcy zasobów](../azure-resource-manager/resource-manager-register-provider-errors.md)
    
    c. Po rozpoznaniu zgodnie z harmonogramem kroki przedstawione w artykule, OMS rozszerzenie alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie; bez konieczności żadnej akcji ani inicjowania.
2. **Błąd: Zakres blokady jest obecna na poziomie grupy zasobów subskrypcji dla operacji zapisu**: ![strony portalu ustawienia alertu OMS komunikat o błędzie ScopeLock](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Gdy zakres blokady jest włączona, ograniczanie żadnych nowych zmian w subskrypcji lub grupy zasobów zawierającej obszaru roboczego analizy dzienników (OMS); system nie może rozszerzyć alerty (Kopiuj) na platformie Azure i tworzenie grup niezbędnych działań.
    
    b. Aby rozwiązać, należy usunąć *tylko do odczytu* blokady w grupie zasobów lub subskrypcji zawierający obszar roboczy; przy użyciu portalu Azure, programu Powershell, interfejsu wiersza polecenia Azure lub interfejsu API. Aby dowiedzieć się więcej, Wyświetl artykuł na [użycia zasobów blokady](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Po rozpoznaniu zgodnie z harmonogramem kroki przedstawione w artykule, OMS rozszerzenie alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie; bez konieczności żadnej akcji ani inicjowania.

3. **Błąd: Zasady są obecne na poziomie grupy zasobów subskrypcji**: ![strony portalu ustawienia alertu OMS komunikat o błędzie zasad](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Gdy [zasad usługi Azure](../azure-policy/azure-policy-introduction.md) jest stosowana, ograniczanie wszystkich nowych zasobów w subskrypcji lub grupy zasobów zawierającej obszaru roboczego analizy dzienników (OMS); system nie może rozszerzyć alerty (Kopiuj) na platformie Azure i tworzenie grup niezbędnych działań.
    
    b. Aby rozwiązać, należy edytować zasady powodują *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* błąd, który uniemożliwia tworzenie nowych zasobów w grupie zasobów lub subskrypcji zawierający obszar roboczy. Przy użyciu portalu Azure, programu Powershell, interfejsu wiersza polecenia Azure lub interfejsu API; można przeprowadzić inspekcję działań, aby znaleźć odpowiednie zasady, powoduje błąd. Aby dowiedzieć się więcej, Wyświetl artykuł na [wyświetlanie działania dzienników inspekcji akcje](../azure-resource-manager/resource-group-audit.md). 
    
    c. Po rozpoznaniu zgodnie z harmonogramem kroki przedstawione w artykule, OMS rozszerzenie alerty na platformie Azure w ramach następnego dnia zaplanowane uruchomienie; bez konieczności żadnej akcji ani inicjowania.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowe [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
