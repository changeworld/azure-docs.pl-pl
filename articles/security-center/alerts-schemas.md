---
title: Schematy alertów usługi Azure Security Center
description: W tym artykule opisano różne schematy używane przez usługę Azure Security Center dla alertów zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062950"
---
# <a name="security-alerts-schemas"></a>Schematy alertów zabezpieczeń

Użytkownicy warstwy standardowej usługi Azure Security Center otrzymują alerty zabezpieczeń, gdy usługa Security Center wykryje zagrożenia dla swoich zasobów.

Te alerty zabezpieczeń można wyświetlić na stronach **ochrony przed zagrożeniami** usługi Azure Security Center lub za pomocą narzędzi zewnętrznych, takich jak:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) — siem natywna dla chmury firmy Microsoft. Łącznik wartownika pobiera alerty z usługi Azure Security Center i wysyła je do [obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) dla usługi Azure Sentinel.
- SIEMy innych firm — wysyłanie danych do [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)za pomocą narzędzi do [ciągłego eksportowania](continuous-export.md) usługi Security Center. Następnie zintegruj dane usługi Event Hub z siem innej firmy.
- [Interfejs API REST](https://docs.microsoft.com/rest/api/securitycenter/) — jeśli używasz interfejsu API REST do uzyskiwania dostępu do alertów, zobacz [dokumentację interfejsu API alertów online](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Jeśli używasz żadnych metod programowych do korzystania z alertów, musisz poprawny schemat, aby znaleźć pola, które są istotne dla Ciebie. Ponadto w przypadku eksportowania do Centrum zdarzeń lub próby wyzwolenia automatyzacji przepływu pracy z ogólnymi łącznikami HTTP, użyj schematów, aby poprawnie przeanalizować obiekty JSON.

>[!IMPORTANT]
> Schemat jest nieco inny dla każdego z tych scenariuszy, więc upewnij się, że wybierz odpowiednią kartę poniżej.


## <a name="the-schemas"></a>Schematy 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automatyzacja przepływu pracy i ciągłe eksportowanie do Centrum zdarzeń](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Przykładowy zestaw JSON dla alertów wysyłanych do aplikacji logiki, Centrum zdarzeń i siem innych firm

Poniżej znajdziesz schemat zdarzeń alertu przekazanych do:

- Wystąpienia aplikacji azure logiki skonfigurowane w automatyzacji przepływu pracy usługi Security Center
- Usługa Azure Event Hub przy użyciu funkcji ciągłego eksportowania usługi Security Center

Aby uzyskać więcej informacji na temat funkcji automatyzacji przepływu pracy, zobacz [Automatyzacja odpowiedzi na alerty i zalecenia](workflow-automation.md).
Aby uzyskać więcej informacji na temat ciągłego eksportu, zobacz [Eksportowanie alertów i zaleceń](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Obszary robocze usługi Azure Sentinel i Log Analytics](#tab/schema-sentinel)

Łącznik wartownika pobiera alerty z usługi Azure Security Center i wysyła je do obszaru roboczego analizy dzienników dla usługi Azure Sentinel. 

Aby utworzyć przypadek lub incydent wartownika przy użyciu alertów Usługi zabezpieczeń, potrzebny jest schemat dla alertów pokazanych poniżej. 

Aby uzyskać więcej informacji na temat usługi Azure Sentinel, zobacz [dokumentację](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Dziennik aktywności platformy Azure](#tab/schema-activitylog)

Inspekcje usługi Azure Security Center wygenerowały alerty zabezpieczeń jako zdarzenia w dzienniku aktywności platformy Azure.

Zdarzenia alertów zabezpieczeń można wyświetlić w dzienniku aktywności, wyszukując zdarzenie Aktywuj alert, jak pokazano na rysunku:

[![Przeszukiwanie dziennika aktywności w poszukiwaniu zdarzenia Aktywuj alert](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Przykładowy zestaw JSON dla alertów wysyłanych do dziennika aktywności platformy Azure

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Model danych schematu

|Pole|Opis|
|----|----|
|**Kanały**|Stała, "Operacja"|
|**Correlationid**|Identyfikator alertu usługi Azure Security Center|
|**Opis**|Opis wpisu|
|**identyfikator danych zdarzeń**|Zobacz correlationId|
|**Eventname**|Wartość i zlokalizowaneWalne podpola zawierają nazwę wyświetlaną alertu|
|**Kategorii**|Wartość i zlokalizowaneWalne podpola są stałe — "Zabezpieczenia"|
|**eventTimestamp**|Sygnatura czasowa UTC podczas generowania alertu|
|**Identyfikator**|W pełni kwalifikowany identyfikator alertu|
|**Poziom**|Stała, "Informacyjna"|
|**operationId**|Zobacz correlationId|
|**Operationname**|Pole wartości jest stałe — "Microsoft.Security/locations/alerts/activate/action", a zlokalizowaną wartością będzie "Aktywuj alert" (potencjalnie można zlokalizować par ustawień regionalnych użytkownika)|
|**nazwa grupy zasobów**|Będzie zawierać nazwę grupy zasobów|
|**nazwa zasobu**|Wartość i zlokalizowaneWartości wartości są stałe — "Microsoft.Security"|
|**resourceType**|Wartość i zlokalizowaneWalne podpola są stałe — "Microsoft.Security/locations/alerts"|
|**Resourceid**|W pełni kwalifikowany identyfikator zasobu platformy Azure|
|**Stan**|Wartość i zlokalizowaneWalne podpola są stałe - "Aktywne"|
|**Podstanu**|Wartość i zlokalizowaneWłasy wartości są puste|
|**submissionTimestamp**|Sygnatura czasowa UTC przesyłania zdarzeń do dziennika aktywności|
|**Subscriptionid**|Identyfikator subskrypcji zagrożonego zasobu|
|**Właściwości**|Torba JSON z dodatkowymi właściwościami odnoszącymi się do alertu. Mogą one ulec zmianie z jednego alertu na drugi, jednak we wszystkich alertach pojawią się następujące pola:<br>- nasilenie: Nasilenie ataku<br>- naruszone Entity: Nazwa zagrożonego zasobu<br>- kroki naprawczeSteps: Szereg kroków naprawczych, które należy podjąć<br>- intencja: Zamiar kill-chain alertu. Możliwe intencje są udokumentowane w [tabeli Intencje](alerts-reference.md#intentions)|
|**relatedEvents**|Stała - pusta tablica|
|||





### <a name="ms-graph-api"></a>[Interfejs API wykresu MS](#tab/schema-graphapi)

Microsoft Graph jest bramą do danych i analizy w usłudze Microsoft 365. Zapewnia ujednolicony model programowania, którego można użyć, aby uzyskać dostęp do ogromnej ilości danych w usłudze Office 365, Windows 10 i Enterprise Mobility + Security. Korzystaj z bogactwa danych w programie Microsoft Graph do tworzenia aplikacji dla organizacji i konsumentów, którzy wchodzą w interakcję z milionami użytkowników.

Schemat i reprezentacja JSON dla alertów zabezpieczeń wysyłanych do ms graph są dostępne w [dokumentacji programu Microsoft Graph](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano schematy używane przez narzędzia ochrony przed zagrożeniami usługi Azure Security Center podczas wysyłania informacji o alertach zabezpieczeń.

Aby uzyskać więcej informacji na temat sposobów uzyskiwania dostępu do alertów zabezpieczeń spoza Centrum zabezpieczeń, zobacz następujące strony:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) — natywny dla chmury SIEM firmy Microsoft
- [Usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) — w pełni zarządzana usługa pozyskiwania danych w czasie rzeczywistym przez firmę Microsoft
- [Funkcja ciągłego eksportu](continuous-export.md) centrum zabezpieczeń
- [Obszary robocze usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) — monitor platformy Azure przechowuje dane dziennika w obszarze roboczym usługi Log Analytics, kontenerze zawierającym informacje o danych i konfiguracji
