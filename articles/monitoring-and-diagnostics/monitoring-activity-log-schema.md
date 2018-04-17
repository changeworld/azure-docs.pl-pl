---
title: Schemat zdarzenia dziennika aktywności platformy Azure | Dokumentacja firmy Microsoft
description: Zrozumienie schematu zdarzeń do dziennika aktywności danych
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: dukek
ms.openlocfilehash: 4264bfd733f586dcdabdee8f29494bfffd9a7a76
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-activity-log-event-schema"></a>Schematu zdarzeń dziennika aktywności platformy Azure
**Dziennika aktywności platformy Azure** jest dziennika, który zapewnia wgląd w wszelkie zdarzenia poziomu subskrypcji, które wystąpiły na platformie Azure. W tym artykule opisano schematu zdarzeń na kategorię danych.

## <a name="administrative"></a>Administracyjne
Ta kategoria zawiera rekord wszystkich utworzyć, update, delete i akcji operacje wykonywane za pomocą Menedżera zasobów. Typy zdarzeń, które można zobaczyć w tej kategorii należą "Utwórz maszynę wirtualną" i "Usuń sieciową grupę zabezpieczeń" co akcję wykonywaną przez użytkownika lub aplikacji przy użyciu usługi Resource Manager ma formę operację określonego typu zasobu. W przypadku typu operacji zapisu, usuń lub działania, rekordy start i powodzenie lub niepowodzenie tej operacji są rejestrowane w kategorii administracyjnej. Kategoria administracyjna także wszystkie zmiany do kontroli dostępu opartej na rolach w ramach subskrypcji.

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
| Autoryzacji |Obiekt typu blob RBAC właściwości zdarzenia. Zwykle zawiera właściwości "Akcja" i "rola" 'scope'. |
| element wywołujący |Adres e-mail użytkownika, który wykonał operację, oświadczenia UPN lub nazwy SPN oświadczenia na podstawie dostępności. |
| kanały |Jedną z następujących wartości: "Administrator", "Operacji" |
| Oświadczenia |Token JWT używanych przez usługi Active Directory do uwierzytelniania użytkownika lub aplikacji do wykonania tej operacji w Menedżera zasobów. |
| correlationId |Zazwyczaj identyfikator GUID w postaci ciągu. Zdarzenia, które mają correlationId należą do tego samego działania pełny. |
| description |Statyczny tekst opisu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia. |
| httpRequest |Obiekt typu blob opisujące żądania Http. Obejmuje zazwyczaj "clientRequestId", "clientIpAddress" i "method" (metoda HTTP. For example, PUT). |
| poziom |Poziom zdarzenia. Jedną z następujących wartości: "Krytyczne", "Błąd", "Ostrzeżenie", "Informacyjny" i "Pełne" |
| resourceGroupName |Nazwa grupy zasobów dla zasobu ryzyko. |
| resourceProviderName |Nazwa dostawcy zasobu dla zasobu wpływ na |
| resourceId |Identyfikator zasobu ryzyko zasobu. |
| operationId |Identyfikator GUID współdzielenia zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (słowniku) opisujący szczegóły zdarzenia. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: pracy w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus |Zazwyczaj wywołań REST odpowiedni kod stanu HTTP, ale można również uwzględnić inne parametry opisujące podstanu, takich jak te wartości typowych: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), akceptowane (kod stanu HTTP: 202), nie zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404), konflikt (kod stanu HTTP : 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503), limit czasu bramy (kod stanu HTTP: 504). |
| eventTimestamp |Sygnatura czasowa po zdarzeniu został wygenerowany przez usługę Azure przetwarzania żądania odpowiednie zdarzenie. |
| submissionTimestamp |Znacznik czasu w momencie zdarzenia stały się dostępne na potrzeby zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="service-health"></a>Kondycja usługi
Ta kategoria zawiera rekord określone zdarzenia kondycji usługi, które wystąpiły na platformie Azure. Przykładem typu zdarzenia, które widać w tej kategorii jest "Azure SQL w wschodnie stany USA występuje Przestój." Zdarzenia kondycji usługi są dostępne w pięciu odmian: wymagana akcja, wspierana odzyskiwania, zdarzenie, konserwacji, informacje lub zabezpieczeń i są wyświetlane tylko, jeśli zasób w subskrypcji, która może wpływać na zdarzenia.

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
Zapoznaj się [usługi powiadomień o kondycji](./monitoring-service-notifications.md) artykułu dokumentacji o wartości właściwości.

## <a name="alert"></a>Alerty
Ta kategoria zawiera rekord wszystkich aktywacji Azure alertów. Przykładem typu zdarzenia, które widać w tej kategorii jest "procent użycia procesora CPU na myVM została ponad 80 dla ostatnich 5 minut." Z różnymi systemami Azure ma alertów koncepcji — można zdefiniować regułę jakiegoś i otrzymasz powiadomienie, gdy warunki reguły są zgodne. Zawsze Azure obsługiwanego typu alertu "aktywuje," lub warunki są spełnione, aby wygenerować powiadomienie, rekord aktywacji jest również przypisany do tej kategorii dziennik aktywności.

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
| element wywołujący | Zawsze Microsoft.Insights/alertRules |
| kanały | Zawsze "Admin, operację" |
| Oświadczenia | Obiektu blob JSON z typem głównej nazwy usługi (nazwy głównej usługi) lub zasób, aparat alertów. |
| correlationId | Identyfikator GUID w postaci ciągu. |
| description |Tekst statyczny opis alertu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia alertu. |
| poziom |Poziom zdarzenia. Jedną z następujących wartości: "Krytyczne", "Błąd", "Ostrzeżenie", "Informacyjny" i "Pełne" |
| resourceGroupName |Nazwa grupy zasobów dla wpływ na zasobu, jeśli jest to alert metryki. Inne typy alertów jest to nazwa grupy zasobów, która zawiera samego alertu. |
| resourceProviderName |Nazwa dostawcy zasobów wpływ na zasobu, jeśli jest to alert metryki. Inne typy alertów jest to nazwa dostawcy zasobów dla samego alertu. |
| resourceId | Nazwa identyfikatora zasobu wpływ na zasobu, jeśli jest to alert metryki. Inne typy alertów jest to identyfikator zasobu alertu zasobu sam. |
| operationId |Identyfikator GUID współdzielenia zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (słowniku) opisujący szczegóły zdarzenia. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: pracy w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus | Zazwyczaj o wartości null dla alertów. |
| eventTimestamp |Sygnatura czasowa po zdarzeniu został wygenerowany przez usługę Azure przetwarzania żądania odpowiednie zdarzenie. |
| submissionTimestamp |Znacznik czasu w momencie zdarzenia stały się dostępne na potrzeby zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

### <a name="properties-field-per-alert-type"></a>Pole właściwości typu alertu
Pole właściwości będzie zawierać różne wartości w zależności od źródła zdarzeń alertów. Dwóch dostawców typowych zdarzeń alertów są alerty dziennika aktywności i metryki alerty.

#### <a name="properties-for-activity-log-alerts"></a>Właściwości alertów dziennik aktywności
| Nazwa elementu | Opis |
| --- | --- |
| properties.subscriptionId | Identyfikator subskrypcji z dziennika zdarzeń działania, który spowodował tę regułę alertów dziennika aktywności do aktywacji. |
| properties.eventDataId | Identyfikator danych zdarzenia z dziennika zdarzeń działania, który spowodował tę regułę alertów dziennika aktywności do aktywacji. |
| properties.resourceGroup | Grupy zasobów z dziennika zdarzeń działania, który spowodował tę regułę alertów dziennika aktywności do aktywacji. |
| properties.resourceId | Identyfikator zasobu z dziennika zdarzeń działania, który spowodował tę regułę alertów dziennika aktywności do aktywacji. |
| properties.eventTimestamp | Sygnatura czasowa zdarzenia dziennika zdarzeń działania, który spowodował tę regułę alertów dziennika aktywności do aktywacji. |
| properties.operationName | Nazwa operacji z dziennika zdarzeń działania, który spowodował tę regułę alertów dziennika aktywności do aktywacji. |
| properties.status | Stan działania dziennika zdarzeń, który spowodował tę regułę alertów dziennika aktywności do aktywacji.|

#### <a name="properties-for-metric-alerts"></a>Właściwości alertów metryki
| Nazwa elementu | Opis |
| --- | --- |
| properties.RuleUri | Identyfikator zasobu metryki reguły alertu samej siebie. |
| properties.RuleName | Nazwa metryki reguły alertów. |
| właściwości. RuleDescription | Opis metryki reguły alertu (zgodnie z definicją w regule alertu). |
| właściwości. Próg | Wartość progowa, używana podczas obliczania metryki reguły alertów. |
| properties.WindowSizeInMinutes | Rozmiar okna używana podczas obliczania metryki reguły alertów. |
| properties.Aggregation | Typ agregacji zdefiniowane w regule alertu metryki. |
| właściwości. Operator | Operator warunkowy używana podczas obliczania metryki reguły alertów. |
| properties.MetricName | Nazwa metryki metryki używana podczas obliczania metryki reguły alertów. |
| properties.MetricUnit | Metryki jednostka Metryka używana podczas obliczania metryki reguły alertów. |

## <a name="autoscale"></a>Automatyczne skalowanie
Ta kategoria zawiera rekord wszystkie zdarzenia związane z operacji skalowania automatycznego aparatu oparte na ustawienia skalowania automatycznego zdefiniowanych w ramach subskrypcji. Przykładem typu zdarzenia, które widać w tej kategorii jest "Skalowania automatycznego skalowania w górę akcja nie powiodła się". Przy użyciu automatycznego skalowania, można automatycznie skalować w poziomie lub skalowanie liczby wystąpień w typie zasobów obsługiwanych na podstawie czasu dnia i/lub obciążenia () dane przy użyciu ustawienia skalowania automatycznego. Jeśli warunki są spełnione, aby skalowania w górę lub w dół, uruchamiania i zakończyło się pomyślnie lub niepowodzeniem zdarzenia będą rejestrowane w tej kategorii.

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
| element wywołujący | Always Microsoft.Insights/autoscaleSettings |
| kanały | Zawsze "Admin, operację" |
| Oświadczenia | Obiektu blob JSON z typem głównej nazwy usługi (nazwy głównej usługi) lub zasobów, aparatu skalowania automatycznego. |
| correlationId | Identyfikator GUID w postaci ciągu. |
| description |Opis zdarzenia skalowania automatycznego tekst statyczny. |
| eventDataId |Unikatowy identyfikator zdarzenia automatycznego skalowania. |
| poziom |Poziom zdarzenia. Jedną z następujących wartości: "Krytyczne", "Błąd", "Ostrzeżenie", "Informacyjny" i "Pełne" |
| resourceGroupName |Nazwa grupy zasobów w ustawieniu skalowania automatycznego. |
| resourceProviderName |Nazwa dostawcy zasobów w ustawieniu skalowania automatycznego. |
| resourceId |Identyfikator zasobu w ustawieniu skalowania automatycznego. |
| operationId |Identyfikator GUID współdzielenia zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (słowniku) opisujący szczegóły zdarzenia. |
| właściwości. Opis elementu | Szczegółowy opis wykonywanych czynności został aparat skalowania automatycznego. |
| properties.ResourceName | Identyfikator zasobu ryzyko zasobu (zasobu, na którym wykonywana akcji skalowania) |
| właściwości. OldInstancesCount | Liczba wystąpień przed akcji skalowania automatycznego weszły w życie. |
| properties.NewInstancesCount | Liczba wystąpień po akcji skalowania automatycznego weszły w życie. |
| properties.LastScaleActionTime | Sygnatura czasowa wystąpienia akcji skalowania automatycznego. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: pracy w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus | Zazwyczaj o wartości null dla automatycznego skalowania. |
| eventTimestamp |Sygnatura czasowa po zdarzeniu został wygenerowany przez usługę Azure przetwarzania żądania odpowiednie zdarzenie. |
| submissionTimestamp |Znacznik czasu w momencie zdarzenia stały się dostępne na potrzeby zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="security"></a>Bezpieczeństwo
Ta kategoria zawiera rekord wszystkie alerty wygenerowane przez Centrum zabezpieczeń Azure. Przykładem typu zdarzenia, które widać w tej kategorii jest "podejrzane podwójne rozszerzenie pliku wykonywane".

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
| kanały | Zawsze "operacji" |
| correlationId | Identyfikator GUID w postaci ciągu. |
| description |Statyczny tekst opisu zdarzenia zabezpieczeń. |
| eventDataId |Unikatowy identyfikator zdarzenia zabezpieczeń. |
| EventName |Przyjazna nazwa zdarzeń zabezpieczeń. |
| id |Identyfikator unikatowy zasób zdarzeń zabezpieczeń. |
| poziom |Poziom zdarzenia. Jedną z następujących wartości: "Krytyczne", "Błąd", "Ostrzeżenie", "Informacyjny" lub "Pełne" |
| resourceGroupName |Nazwa grupy zasobów dla zasobu. |
| resourceProviderName |Nazwa dostawcy zasobów Centrum zabezpieczeń Azure. Zawsze "Microsoft.Security". |
| Typ zasobu |Typ zasobu, który wygenerował zdarzenie zabezpieczeń, takich jak "Microsoft.Security/locations/alerts" |
| resourceId |Identyfikator zasobu alertu zabezpieczeń. |
| operationId |Identyfikator GUID współdzielenia zdarzenia, które odpowiadają jednej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw `<Key, Value>` pary (słowniku) opisujący szczegóły zdarzenia. Te właściwości będą się różnić w zależności od rodzaju alertu zabezpieczeń. Zobacz [tej strony](../security-center/security-center-alerts-type.md) opis typów alertów, które pochodzą z Centrum zabezpieczeń. |
| właściwości. Ważność |Poziom ważności. Możliwe wartości to "High", "Średni" lub "Low". |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: pracy w toku, zakończyło się pomyślnie, nie powiodło się, aktywne, rozwiązane. |
| subStatus | Zazwyczaj o wartości null dla zdarzeń zabezpieczeń. |
| eventTimestamp |Sygnatura czasowa po zdarzeniu został wygenerowany przez usługę Azure przetwarzania żądania odpowiednie zdarzenie. |
| submissionTimestamp |Znacznik czasu w momencie zdarzenia stały się dostępne na potrzeby zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o dziennik aktywności (dawniej dzienników inspekcji)](monitoring-overview-activity-logs.md)
* [Strumień dziennika aktywności platformy Azure do usługi Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
