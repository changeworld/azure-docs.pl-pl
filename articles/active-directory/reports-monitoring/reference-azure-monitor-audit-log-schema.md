---
title: Interpretowanie schemat dziennika inspekcji usługi Azure Active Directory w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Opisz schemat dziennika inspekcji usługi Azure AD do użycia w usłudze Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437077"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretowanie schematu dzienników inspekcji usługi Azure AD w usłudze Azure Monitor (wersja zapoznawcza)

W tym artykule opisano schemat dziennika inspekcji usługi Azure Active Directory (Azure AD) w usłudze Azure Monitor. Każdy wpis osobny dziennik jest przechowywane jako tekst i sformatowane jako obiekt blob JSON, jak pokazano w poniższych dwóch przykładach: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Opisy pól i właściwości

| Nazwa pola | Opis |
|------------|-------------|
| time       | Data i godzina (UTC). |
| operationName | Nazwa operacji. |
| operationVersion | Wersja interfejsu API REST, które są wymagane przez klienta. |
| category | Obecnie *inspekcji* jest to jedyna obsługiwana wartość. |
| tenantId | Identyfikator GUID, który jest skojarzony z dziennikami dzierżawy. |
| resultType | Wynik operacji. Może to spowodować *Powodzenie* lub *błąd*. |
| resultSignature |  To pole jest niezamapowany. Ponadto można zignorować. | 
| resultDescription | Dodatkowy opis wyniku, gdzie są dostępne. | 
| durationMs |  To pole jest niezamapowany. Ponadto można zignorować. |
| callerIpAddress | Adres IP klienta, który zgłosił żądanie. | 
| correlationId | Opcjonalny identyfikator GUID, który jest przekazywany przez klienta. Może ono pomóc korelowanie operacji po stronie klienta przy użyciu operacji po stronie serwera i jest to przydatne, gdy podczas śledzenia dzienników, które rozciągają się usług. |
| identity | Tożsamość z tokenu, który został przedstawiony podczas zgłosił żądanie. Tożsamość może być konto użytkownika, konto systemowe lub jednostki usługi. |
| poziom | Typ komunikatu. Dzienniki inspekcji poziom jest zawsze *komunikat o charakterze informacyjnym*. |
| location | Lokalizacja centrum danych. |
| properties | Wyświetla listę obsługiwanych właściwości, które są powiązane z dziennika inspekcji. Aby uzyskać więcej informacji zobacz następną tabelę. | 

<br>

| Nazwa właściwości | Opis |
|---------------|-------------|
| AuditEventCategory | Typ zdarzenia inspekcji. Może być *Zarządzanie użytkownikami*, *Zarządzanie aplikacjami*, lub innego typu.|
| Typ tożsamości | Typ może być *aplikacji* lub *użytkownika*. |
| Typ operacji | Typ może być *Dodaj*, *aktualizacji*, *Usuń*. lub *innych*. |
| Typ zasobu docelowego | Określa typ zasobu docelowego, że operacja została wykonana w. Typ może być *aplikacji*, *użytkownika*, *roli*, *zasad* | 
| Nazwa zasobu docelowego | Nazwa zasobu docelowego. Może być to nazwa aplikacji, nazwy roli, główną nazwę użytkownika lub główną nazwę usługi. |
| additionalTargets | Wyświetla wszelkie dodatkowe właściwości dla określonych operacji. Na przykład operacji aktualizacji, stare wartości i nowe wartości są wyświetlane w obszarze *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Kolejne kroki

* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)