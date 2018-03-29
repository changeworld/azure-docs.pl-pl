---
title: Inicjowanie rozszerzanie alerty z usługą OMS na platformie Azure | Dokumentacja firmy Microsoft
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
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 76b7481223566f16a5da8c08d9d76f2bdb6b542a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="initiate-extending-alerts-from-oms-into-azure"></a>Inicjowanie rozszerzanie alerty z usługą OMS na platformie Azure
Począwszy od **23 kwietnia 2018**, wszystkich klientów przy użyciu alertów, które są skonfigurowane w [programu Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), zostanie rozszerzony na platformie Azure. Alerty, które zostały rozszerzone na platformie Azure zachowują się tak samo jak OMS. Możliwości monitorowania pozostaną nienaruszone. Rozszerzające alertach tworzonych w OMS Azure oferuje wiele korzyści. Aby uzyskać więcej informacji o zaletach i proces rozszerzenia alerty z usługą OMS na platformie Azure, zobacz [rozszerzyć alerty z OMS Azure](monitoring-alerts-extend.md).

Klienci chcą Przenieś alerty z usługą OMS na platformie Azure, to zrobić za pomocą jednej z opcji podane.

## <a name="option-1---using-oms-portal"></a>Opcja 1 — za pomocą portalu OMS
Aby zainicjować dobrowolnie rozszerzające alertach z usługą OMS na platformie Azure, wykonaj kroki wymienione poniżej.

1. Na stronie Przegląd Operations Management Suite (OMS) przejdź do ustawień, a następnie sekcja alerty. Kliknij przycisk z etykietą "Rozszerzyć do platformy Azure", jako wyróżniane na poniższej ilustracji.

    ![Strona Ustawienia alertu OMS z opcją Rozszerz](./media/monitor-alerts-extend/ExtendInto.png)

2. Po kliknięciu przycisku kroku 3 kreatora zostanie wyświetlona, z pierwszym krokiem udostępnia szczegółowe informacje o procesie. Kliknij przycisk Dalej, aby kontynuować.

    ![Rozszerzanie alerty z usługą OMS na platformie Azure — krok 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. W drugim kroku system wyświetli podsumowanie proponowanej zmiany przez listę odpowiednich [grupy akcji](monitoring-action-groups.md), alertów w OMS. Jeśli podobne akcje są widoczne w więcej niż jeden alert — system zaproponuje do skojarzenia z wszystkich z nich grupy jednej akcji.  Grupa działań proponowana, postępuj zgodnie z konwencją nazewnictwa: *WorkspaceName_AG_ #Number*. Aby kontynuować, kliknij przycisk Dalej.
Przykład ekranu poniżej.

    ![Rozszerzanie alerty z usługą OMS na platformie Azure — krok 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. W ostatnim kroku kreatora możesz poprosić OMS można zaplanować rozszerzanie wszystkie alerty na platformie Azure — tworzenie nowych grup akcji i kojarzenie ich z alertami, jak pokazano wcześniej ekranu. Aby kontynuować wybierz kliknij przycisk Zakończ i upewnij się, w wierszu polecenia, aby zainicjować proces. Opcjonalnie klientów można też podać adresy e-mail, które chciałby OMS o wysłanie raportu na zakończenie przetwarzania.

    ![Rozszerzanie alerty z usługą OMS na platformie Azure — krok 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Po zakończeniu pracy Kreatora kontroli powróci do strony Ustawienia alertu, a opcja "Rozszerzyć do platformy Azure" zostanie usunięta. W tle OMS zaplanowane alerty w OMS na platformie Azure; to może zająć trochę czasu i po rozpoczęciu operacji krótki okres alertów w OMS nie będą dostępne do modyfikacji. Bieżący stan zostanie wyświetlony za pośrednictwem transparent i jeśli adresów e-mail miejsce pod warunkiem podczas wykonywania kroku 4, następnie zostaną one informację, gdy proces w tle pomyślnie rozszerza wszystkie alerty na platformie Azure. 

6. Alerty będą nadal wyświetlane w OMS, nawet po ich pomyślnie pobrać rozszerzony na platformie Azure.

    ![Po rozszerzeniu alerty w OMS na platformie Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Opcja 2 — przy użyciu interfejsu API
Dla klientów, którzy programowe sterowanie lub zautomatyzować proces rozszerzania alerty w OMS na platformie Azure; Firma Microsoft oferuje nowe AlertsVersion interfejsu API w ramach analizy dzienników.

Interfejs API AlertsVersion analizy dziennika jest RESTful i jest dostępny za pośrednictwem interfejsu REST API usługi Azure Resource Manager. W tym dokumencie można znaleźć przykłady którym dostęp do interfejsu API z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager. Korzystanie z ARMClient i programu PowerShell jest jedną z wielu opcji, aby uzyskać dostępu do interfejsu API. Interfejs API dane wyjściowe obejmują wyniki w formacie JSON programowane stosowanie użycia wyników na wiele sposobów.

Używając GET interfejsu API, co można uzyskać w wyniku podsumowanie proponowanej zmiany jako lista odpowiednie [grupy akcji](monitoring-action-groups.md) dla alertów w OMS, w formacie JSON formatowania. Jeśli podobne akcje są widoczne w więcej niż jeden alert — zaproponuje systemu do utworzenia skojarzyć z nimi wszystkimi grupy jednej akcji.  Grupa działań proponowana, postępuj zgodnie z konwencją nazewnictwa: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> Uzyskiwanie alertów w OMS pobierania rozszerzonych na platformie Azure nie spowoduje wywołanie interfejsu API. Tylko zapewni odpowiedzi podsumowania proponowanych zmian. Aby upewnić się, te zmiany należy zrobić, aby rozszerzyć alerty na platformie Azure, POST Wywołaj należy do interfejsu API.

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

Aby rozpocząć planowanie rozszerzania alertów w OMS na platformie Azure, zainicjować POST do interfejsu API. Wykonanie tego polecenia/call potwierdza użytkownika zamiaru jak również akceptacji alerty w OMS rozszerzony do platformy Azure i wprowadzić zmiany, jak wskazano w odpowiedzi GET wywołanie interfejsu API. Użytkownik może opcjonalnie możesz podać listę adresów e-mail, do których OMS będzie poczty raportów, pomyślnie zakończenie procesu zaplanowane tła rozszerzania alertów w OMS na platformie Azure.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Wynik rozszerzanie OMS alerty na platformie Azure, mogą się różnić od podsumowanie udostępniane przez GET - na koncie zmiany wykonane w systemie. Po zaplanowane, alerty w OMS będzie tymczasowo niedostępna do edycji/modyfikacji - podczas można tworzyć nowe alerty. 

Jeśli WPIS zakończy się pomyślnie, zwraca odpowiedź 200 OK wraz z programem:
```json
{
    "version": 2
}
```
Wskazującą, czy alerty zostały rozszerzone na platformie Azure, jak wskazano w wersji 2. Ta wersja jest tylko do sprawdzania, czy alerty zostały rozszerzone na platformie Azure i nie ma wpływu wykorzystania z [interfejsu API Search analizy dziennika](../log-analytics/log-analytics-api-alerts.md). Po alerty zostały rozszerzone pomyślnie na platformie Azure, wszyscy użytkownicy skojarzone z rolami administratora i współautor w obszarze roboczym otrzyma wiadomość e-mail z szczegółowe informacje o zmianach gotowe.


I na koniec, jeśli wszystkie alerty w obszarze roboczym określony wcześniej zaplanowane na platformie Azure — odpowiedź POST będzie 403 Zabroniony.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o nowe [zgłaszać alerty Azure](monitoring-overview-unified-alerts.md).
* Dowiedz się więcej o [rejestrowania alertów w alertach Azure](monitor-alerts-unified-log.md).
