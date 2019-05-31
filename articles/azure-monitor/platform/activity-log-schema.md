---
title: Usługa Azure schemat zdarzeń dziennika aktywności
description: Zrozumienie schematu zdarzeń dla danych emitowane do dziennika aktywności
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ba5e0f696f54f46fb14086b542dc3b2e64155975
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244931"
---
# <a name="azure-activity-log-event-schema"></a>Usługa Azure schemat zdarzeń dziennika aktywności
**Dziennika aktywności platformy Azure** jest dziennika, który zapewnia wgląd w poziom subskrypcji zdarzeń, które wystąpiły na platformie Azure. W tym artykule opisano schemat zdarzeń dla każdej kategorii danych. Schemat danych różni się zależnie od tego, podczas odczytu danych w portalu, programu PowerShell, interfejsu wiersza polecenia, lub bezpośrednio za pośrednictwem interfejsu API REST i [przesyłania strumieniowego danych w magazynie lub Event Hubs za pomocą profilu dziennika](activity-log-export.md). Poniższe przykłady pokazują schematu jako udostępniane za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST. Mapowanie tych właściwości w celu [Azure diagnostyczne dzienniki schematu](diagnostic-logs-schema.md) znajduje się na końcu tego artykułu.

## <a name="administrative"></a>Administracyjne
Ta kategoria zawiera rekord wszystkich tworzenia, aktualizowania, usuwania i akcji operacje wykonywane przy użyciu usługi Resource Manager. Typy zdarzeń, które powinny zostać wyświetlone tej kategorii należą "Tworzenie maszyny wirtualnej" i "Usuń sieciową grupę zabezpieczeń" każdej akcji podjętej przez użytkownika lub aplikacji przy użyciu usługi Resource Manager ma formę operacji na określonego typu zasobu. W przypadku typu operacji zapisu, usuń lub akcję, rekordy początkowego i powodzenie lub niepowodzenie tej operacji są rejestrowane w kategorii administracyjnej. Kategoria administracyjna także wszelkie zmiany do kontroli dostępu opartej na rolach w ramach subskrypcji.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| Autoryzacja |Obiekt blob RBAC właściwości zdarzenia. Zazwyczaj zawiera właściwości "action", "roli" i "scope". |
| Obiekt wywołujący |Adres e-mail użytkownika, który wykonał operację, oświadczenia nazwy UPN lub nazwy SPN oświadczenia na podstawie dostępności. |
| kanały |Jeden z następujących wartości: "Admin", "Operacja" |
| oświadczenia |Token JWT używane przez usługi Active Directory do uwierzytelniania użytkownika lub aplikacji do wykonania tej operacji w usłudze Resource Manager. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które mają identyfikator korelacji należą do tej samej akcji uber. |
| description |Statyczny tekst opisu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia. |
| eventName | Przyjazna nazwa zdarzenia administracyjne. |
| category | Zawsze "administracyjne" |
| httpRequest |Obiekt blob opisujące żądania Http. Zwykle obejmuje "clientRequestId", "clientIpAddress" i "method" (metoda HTTP. Adapterem, umieść). |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie" i "Informacyjne" |
| resourceGroupName |Nazwa grupy zasobów zasób objęty wpływem. |
| resourceProviderName |Nazwa dostawcy zasobów dla zasób objęty wpływem |
| Typ zasobu | Typ zasobu, który miała wpływ zdarzenie administracyjne. |
| resourceId |Identyfikator zasobu zasób objęty wpływem. |
| operationId |Identyfikator GUID współużytkowane przez zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (Słownik), opisujący szczegóły zdarzenia. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: Pracę w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus |Zwykle kod stanu HTTP REST odpowiednich wywołań, ale mogą również obejmować inne parametry opisujące podstanu, takie jak te wartości typowych: OK (kod stanu HTTP: 200), utworzone (kod stanu HTTP: 201) zaakceptowane (kod stanu HTTP: 202), żadnej zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404) konflikt (kod stanu HTTP: 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503), limit czasu bramy (kod stanu HTTP: 504). |
| eventTimestamp |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="service-health"></a>Kondycja usługi
Ta kategoria zawiera rekord wszelkie zdarzenia kondycji usługi, które miały miejsce w systemie Azure. Jest przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii, "SQL Azure w regionie wschodnie stany USA występuje Przestój." Zdarzenia usługi Service health są dostępne w pięciu odmian: Czynności, wspomagana odzyskiwania, zdarzenia, konserwacji, informacje lub zabezpieczeń i są wyświetlane tylko w przypadku zasobów w subskrypcji, która będzie mieć wpływ na zdarzenie.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Zapoznaj się [usługi powiadomień dotyczących kondycji](./../../azure-monitor/platform/service-notifications.md) artykuł dotyczący dokumentacji dotyczącej wartości we właściwościach.

## <a name="resource-health"></a>Kondycja zasobów
Ta kategoria zawiera rekord wszystkie zdarzenia dotyczące kondycji zasobów, które wystąpiły z zasobami platformy Azure. Przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii jest "Zmieniono na niedostępny stan kondycji maszyny wirtualnej." Zdarzenia dotyczące kondycji zasobów może reprezentować jedną z czterech stanów kondycji: Dostępne, niedostępne, obniżonej wydajności i nieznany. Ponadto zdarzenia dotyczące kondycji zasobów można sklasyfikować jako są inicjowane platformy lub Zainicjowanie przez użytkownika.

### <a name="sample-event"></a>Zdarzenie próbkowania

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| kanały | Zawsze "Admin, operacji" |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Opis statyczny tekst alertu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia alertów. |
| category | Zawsze "ResourceHealth" |
| eventTimestamp |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie", "Informacyjne" i "Pełne" |
| operationId |Identyfikator GUID współużytkowane przez zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| resourceGroupName |Nazwa grupy zasobów, która zawiera zasób. |
| resourceProviderName |Zawsze "Microsoft.Resourcehealth/healthevent/action". |
| Typ zasobu | Typ zasobu, który miała wpływ zdarzenie kondycji zasobu. |
| resourceId | Nazwa Identyfikatora zasobu dla zasobu, których to dotyczy. |
| status |Ciąg opisujący stan zdarzenie kondycji. Możliwe wartości to: Aktywne, rozwiązane w toku, aktualizowane. |
| subStatus | Zazwyczaj wartość null dla alertów. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| properties |Zestaw `<Key, Value>` pary (Słownik), opisujący szczegóły zdarzenia.|
| Properties.Title | Przyjazny dla użytkownika ciąg opisujący stan kondycji zasobu. |
| properties.details | Przyjazny dla użytkownika ciąg, który opisuje dalsze szczegółowe informacje o zdarzeniu. |
| properties.currentHealthStatus | Bieżący stan kondycji zasobu. Jeden z następujących wartości: "Dostępne", "Niedostępne", "Negatywny wpływ na dostępność" i "Nieznany". |
| properties.previousHealthStatus | Poprzedni stan kondycji zasobu. Jeden z następujących wartości: "Dostępne", "Niedostępne", "Negatywny wpływ na dostępność" i "Nieznany". |
| Properties.Type | Opis typu zdarzenia kondycji zasobów. |
| Properties.cause | Opis przyczyn zdarzenia kondycji zasobów. "UserInitiated" i "PlatformInitiated". |


## <a name="alert"></a>Alerty
Ta kategoria zawiera rekord wszystkich aktywacje alertów platformy Azure. Jest przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii, "procent użycia procesora CPU na myVM została ponad 80 dla ostatnich 5 minut." Z różnych systemów Azure ma koncepcji alertów — możesz zdefiniować regułę jakieś i Otrzymuj powiadomienie, gdy warunki zgodne z tą regułą. Każdym obsługiwanym typem alertów platformy Azure "aktywuje," lub warunki są spełnione, aby wygenerować powiadomienie, rekord aktywacji są również wypychane do tej kategorii dziennika aktywności.

### <a name="sample-event"></a>Zdarzenie próbkowania

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| Obiekt wywołujący | Zawsze Microsoft.Insights/alertRules |
| kanały | Zawsze "Admin, operacji" |
| oświadczenia | Obiekt blob JSON z typem nazwy SPN (główna nazwa usługi) lub zasobu, aparat alertów. |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Opis statyczny tekst alertu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia alertów. |
| category | Zawsze "Alert" |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie" i "Informacyjne" |
| resourceGroupName |Nazwa grupy zasobów zasób objęty wpływem przypadku alertu dotyczącego metryki. Inne typy alertów jest nazwa grupy zasobów, która zawiera samego alertu. |
| resourceProviderName |Nazwa dostawcy zasobów zasób objęty wpływem przypadku alertu dotyczącego metryki. Inne typy alertów jest nazwa dostawcy zasobów dla samego alertu. |
| resourceId | Nazwa Identyfikatora zasobu zasób objęty wpływem przypadku alertu dotyczącego metryki. Inne typy alertów jest identyfikator zasobu alertu samego zasobu. |
| operationId |Identyfikator GUID współużytkowane przez zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (Słownik), opisujący szczegóły zdarzenia. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: Pracę w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus | Zazwyczaj wartość null dla alertów. |
| eventTimestamp |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

### <a name="properties-field-per-alert-type"></a>Pole właściwości według typu alertu
Pole właściwości będzie zawierać różne wartości w zależności od źródła zdarzeń alertów. Dwóch dostawców na typowych zdarzeń alertów są alerty dziennika aktywności i alertów dotyczących metryk.

#### <a name="properties-for-activity-log-alerts"></a>Właściwości alertów dziennika aktywności
| Nazwa elementu | Opis |
| --- | --- |
| properties.subscriptionId | Identyfikator subskrypcji z zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony. |
| properties.eventDataId | Identyfikator danych zdarzenia ze zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony. |
| properties.resourceGroup | Grupa zasobów, od zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony. |
| properties.resourceId | Identyfikator zasobu z zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony. |
| properties.eventTimestamp | Sygnatura czasowa zdarzenia zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony. |
| properties.operationName | Nazwa operacji z zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony. |
| properties.status | Stan zdarzenia dziennika aktywności, który spowodował tę regułę alertu dziennika aktywności zostanie uaktywniony.|

#### <a name="properties-for-metric-alerts"></a>Właściwości alertów dotyczących metryk
| Nazwa elementu | Opis |
| --- | --- |
| properties.RuleUri | Identyfikator zasobu sam regułę alertu metryki. |
| properties.RuleName | Nazwa reguły alertów metryk. |
| properties.RuleDescription | Opis regułę alertu metryki (zgodnie z definicją w regule alertu). |
| properties.Threshold | Wartość progowa, używana podczas obliczania metryki reguły alertu. |
| properties.WindowSizeInMinutes | Rozmiar okna używana podczas obliczania metryki reguły alertu. |
| properties.Aggregation | Typ agregacji, zdefiniowane w regule alertu metryki. |
| properties.Operator | Operator warunkowy używana podczas obliczania metryki reguły alertu. |
| properties.MetricName | Nazwa metryki metryki używana podczas obliczania metryki reguły alertu. |
| properties.MetricUnit | Metryki jednostkę Metryka używana podczas obliczania metryki reguły alertu. |

## <a name="autoscale"></a>Automatyczne skalowanie
Ta kategoria zawiera rekord wszystkie zdarzenia związane z działaniem aparat skalowania automatycznego na podstawie żadnych ustawień automatycznego skalowania, zdefiniowanych w ramach subskrypcji. Przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii jest "Skalowania automatycznego skalowania w górę akcja nie powiodła się". Przy użyciu skalowania automatycznego, możesz automatycznie skalować w poziomie lub skalowanie liczby wystąpień w obsługiwany typ zasobu na podstawie czasu dzień danych i/lub obciążenia (metryk) przy użyciu ustawienia automatycznego skalowania. Jeśli warunki są spełnione, aby skalowania w górę lub dół początkowego i zakończyło się pomyślnie lub zakończone niepowodzeniem zdarzenia będą rejestrowane w tej kategorii.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| Obiekt wywołujący | Always Microsoft.Insights/autoscaleSettings |
| kanały | Zawsze "Admin, operacji" |
| oświadczenia | Obiekt blob JSON z typem nazwy SPN (główna nazwa usługi) lub zasobu, aparat skalowania automatycznego. |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Statyczny tekst opisu zdarzenia skalowania automatycznego. |
| eventDataId |Unikatowy identyfikator zdarzenia skalowania automatycznego. |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie" i "Informacyjne" |
| resourceGroupName |Nazwa grupy zasobów ustawienia automatycznego skalowania. |
| resourceProviderName |Nazwa dostawcy zasobów ustawienia automatycznego skalowania. |
| resourceId |Identyfikator zasobu Ustawienia skalowania automatycznego. |
| operationId |Identyfikator GUID współużytkowane przez zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (Słownik), opisujący szczegóły zdarzenia. |
| właściwości. Opis elementu | Szczegółowy opis czynności była wykonywane przez aparat skalowania automatycznego. |
| properties.ResourceName | Identyfikator zasobu, zasób objęty wpływem (zasobu, na którym została wykonana akcji skalowania) |
| properties.OldInstancesCount | Liczba wystąpień, zanim akcji skalowania automatycznego weszło w życie. |
| properties.NewInstancesCount | Liczba wystąpień po wykonaniu akcji skalowania automatycznego weszło w życie. |
| properties.LastScaleActionTime | Sygnatura czasowa wystąpienia akcji skalowania automatycznego. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: Pracę w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus | Zazwyczaj wartość null dla skalowania automatycznego. |
| eventTimestamp |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="security"></a>Bezpieczeństwo
Ta kategoria zawiera rekord wszystkie alerty wygenerowane przez usługę Azure Security Center. Przykładem typu zdarzenia, które powinny zostać wyświetlone tej kategorii są ""podejrzane podwójne rozszerzenie pliku wykonywane.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| kanały | Zawsze "Operacja" |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Tekst statyczny opis zdarzenia zabezpieczeń. |
| eventDataId |Unikatowy identyfikator zdarzenia zabezpieczeń. |
| eventName |Przyjazna nazwa zdarzenia zabezpieczeń. |
| category | Zawsze "zabezpieczenia" |
| id |Unikatowy identyfikator zdarzenia zabezpieczeń. |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie" lub "Informacyjny" |
| resourceGroupName |Nazwa grupy zasobów dla zasobu. |
| resourceProviderName |Nazwa dostawcy zasobów usługi Azure Security Center. Zawsze "Microsoft.Security". |
| Typ zasobu |Typ zasobu, który wygenerował zdarzenie zabezpieczeń, takie jak "Microsoft.Security/locations/alerts" |
| resourceId |Identyfikator zasobu alertu zabezpieczeń. |
| operationId |Identyfikator GUID współużytkowane przez zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (Słownik), opisujący szczegóły zdarzenia. Te właściwości różnią się w zależności od rodzaju alertu zabezpieczeń. Zobacz [na tej stronie](../../security-center/security-center-alerts-type.md) opis typów alertów, które pochodzą z usługi Security Center. |
| properties.Severity |Poziom ważności. Możliwe wartości to "High", "Średnie" lub "Niska". |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: Pracę w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus | Zazwyczaj wartość null dla zdarzeń dotyczących zabezpieczeń. |
| eventTimestamp |Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="recommendation"></a>Zalecenie
Ta kategoria zawiera rekord nowych zaleceń, które są generowane dla usług. Przykład zalecenie byłoby "skorzystaj z zestawów dostępności dla zwiększenia odporności na uszkodzenia." Istnieją cztery typy zdarzeń zalecenia, które mogą być generowane: Wysokiej dostępności, wydajności, bezpieczeństwa i optymalizacji kosztów. 

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| kanały | Zawsze "Operacja" |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Statyczny tekst opisu zdarzenia rekomendacji |
| eventDataId | Unikatowy identyfikator zdarzenia rekomendacji. |
| category | Zawsze "Recommendation" |
| id |Unikatowy identyfikator zdarzenia rekomendacji. |
| poziom |Poziom zdarzenia. Jeden z następujących wartości: "Krytyczne", "Error", "Ostrzeżenie" lub "Informacyjny" |
| operationName |Nazwa operacji.  Zawsze "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Nazwa grupy zasobów dla zasobu. |
| resourceProviderName |Nazwa dostawcy zasobu dla zasobu, który dotyczy tego zalecenia, takie jak "MICROSOFT.COMPUTE" |
| Typ zasobu |Nazwa typu zasobu dla zasobu, który dotyczy tego zalecenia, takie jak "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |Identyfikator zasobu zasobów, z których zalecenie ma zastosowanie do |
| status | Zawsze "aktywny" |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| properties |Zestaw `<Key, Value>` pary (Słownik), opisujący szczegóły zalecenia.|
| properties.recommendationSchemaVersion| Opublikowana wersja schematu właściwości zalecenie we wpisie dziennika aktywności |
| properties.recommendationCategory | Kategoria zalecenia. Możliwe wartości to "Wysokiej dostępności", "Performance", "Zabezpieczenia" i "Kosztów" |
| properties.recommendationImpact| Wpływ rekomendacji. Możliwe wartości to "Wysoka", "Średnie", "Niska" |
| properties.recommendationRisk| Ryzyko zalecenia. Możliwe wartości to "Error", "Ostrzeżenie", "None" |

## <a name="policy"></a>Zasady

Ta kategoria zawiera rekordy wszystkich efekt działania operacji wykonywanych przez [usługi Azure Policy](../../governance/policy/overview.md). Przykłady typów zdarzeń, które powinny zostać wyświetlone tej kategorii _inspekcji_ i _Odmów_. Każdej akcji podjętej przez zasady są modelowane jako operacja dotycząca zasobu.

### <a name="sample-policy-event"></a>Przykładowe zasady zdarzeń

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Opisy właściwości zdarzenia zasad

| Nazwa elementu | Opis |
| --- | --- |
| Autoryzacja | Tablica RBAC właściwości zdarzenia. Dla nowych zasobów jest to akcja i zakres żądania, która wyzwoliła oceny. Dla istniejących zasobów akcja jest "Microsoft.Resources/checkPolicyCompliance/read". |
| Obiekt wywołujący | Dla nowych zasobów, tożsamość, która zainicjowała wdrożenia. Dla istniejących zasobów, identyfikator GUID RP szczegółowych informacji programu Microsoft Azure zasad. |
| kanały | Zdarzenia dotyczące zasad można użyć tylko kanału "Działania". |
| oświadczenia | Token JWT używane przez usługi Active Directory do uwierzytelniania użytkownika lub aplikacji do wykonania tej operacji w usłudze Resource Manager. |
| correlationId | Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które mają identyfikator korelacji należą do tej samej akcji uber. |
| description | To pole jest puste w przypadku zdarzenia dotyczące zasad. |
| eventDataId | Unikatowy identyfikator zdarzenia. |
| eventName | "BeginRequest" lub "EndRequest". "BeginRequest" jest używana opóźnionego oceny auditIfNotExists i deployIfNotExists i po rozpoczęciu wdrażania szablonu w efekt deployIfNotExists. Wszystkie inne operacje dotyczące zwrotu "EndRequest". |
| category | Deklaruje zdarzenia dziennika aktywności jako należący do "Policy". |
| eventTimestamp | Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadające zdarzenie. |
| id | Unikatowy identyfikator zdarzenia dla określonego zasobu. |
| poziom | Poziom zdarzenia. Inspekcja korzysta z "Ostrzeżenie" i Odmów używa "Error". Błąd auditIfNotExists lub deployIfNotExists może generować "Ostrzeżenie" lub "Error", w zależności od ważności. Inne zdarzenia zasad, użyj "Informacyjne". |
| operationId | Identyfikator GUID współużytkowane przez zdarzenia, które odpowiadają jednej operacji. |
| operationName | Nazwa operacji i bezpośrednio skorelowany efektu zasad. |
| resourceGroupName | Nazwa grupy zasobów ocenianą zasobów. |
| resourceProviderName | Nazwa dostawcy zasobów ocenianą zasobów. |
| Typ zasobu | Dla nowych zasobów jest typu obliczania. Dla istniejących zasobów zwraca "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | Identyfikator zasobu ocenianą zasobów. |
| status | Ciąg opisujący stan wyniku oceny zasad. Większość oceny zasad zwrócić "Powodzenie", ale efektu odrzucenia zwraca "Niepowodzenie". Błędy w auditIfNotExists lub deployIfNotExists również zwracać "Niepowodzenie". |
| subStatus | Pole jest puste dla zdarzeń dotyczących zasad. |
| submissionTimestamp | Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań. |
| subscriptionId | Identyfikator subskrypcji platformy Azure. |
| properties.isComplianceCheck | Zwraca wartość "False", podczas wdrażania nowego zasobu lub aktualizacji istniejącego zasobu właściwości Menedżera zasobów. Wszystkie inne [wyzwalaczy oceny](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) spowodować "True". |
| properties.resourceLocation | Region platformy Azure zasobu oceniane. |
| Properties.ancestors | Przecinkami lista grup zarządzania nadrzędnego uporządkowane od bezpośrednią lokacją nadrzędną do położony nadrzędnych. |
| properties.policies | Zawiera szczegółowe informacje o definicji zasad, przypisanie, wpływ i parametry, które ta ocena zasad jest wynikiem. |
| relatedEvents | To pole jest puste w przypadku zdarzenia dotyczące zasad. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mapowanie schematu dzienników diagnostycznych

Podczas przesyłania strumieniowego dziennika aktywności platformy Azure na koncie magazynu lub przestrzeni nazw usługi Event Hubs, danych następuje [Azure diagnostyczne dzienniki schematu](./diagnostic-logs-schema.md). Poniżej przedstawiono Mapowanie właściwości w schemacie powyżej schematu dzienników diagnostycznych:

| Właściwości schematu dzienników diagnostycznych | Właściwości schematu interfejsu API REST dziennika aktywności | Uwagi |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | Identyfikator subskrypcji, resourceType, resourceGroupName są dedukowane z resourceId. |
| operationName | operationName.value |  |
| category | Część nazwy operacji | Podgrupach typ operacji — "Zapis" / "Delete" / "Action" |
| resultType | status.Value | |
| resultSignature | Substatus.Value | |
| resultDescription | description |  |
| durationMs | ND | Zawsze 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | oświadczenia i właściwości autoryzacji |  |
| Poziom | Poziom |  |
| location | ND | Lokalizacja przetwarzania zdarzenia. *Nie jest to lokalizacja zasobu, ale raczej, gdy zdarzenie zostało przetworzone. Tej właściwości zostanie usunięta w przyszłej aktualizacji.* |
| Właściwości | properties.eventProperties |  |
| properties.eventCategory | category | Jeśli nie ma properties.eventCategory, kategoria jest "Administracyjne" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o dzienniku aktywności](activity-logs-overview.md)
* [Eksportuj Dziennik aktywności do usługi Azure Storage lub usługi Event Hubs](activity-log-export.md)

