---
title: Interpretowanie schemat dziennika inspekcji usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Opisz schemat dziennika inspekcji usługi Azure AD do użycia w usłudze Azure Monitor (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239934"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Interpretowanie schematu dzienników inspekcji usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza)

W tym artykule opisano schemat dziennika inspekcji usługi Azure AD w usłudze Azure Monitor. Każdy wpis osobny dziennik jest przechowywany jako tekst w formacie obiektu blob JSON, jak pokazano w poniższych dwóch przykładach. 

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

| Nazwa pola | Opis |
|------------|-------------|
| time       | Data i godzina (UTC) |
| operationName | Nazwa operacji |
| operationVersion | Wersja interfejsu API REST zażądane przez klienta |
| category | Obecnie *inspekcji* jest jedyną wartość obsługiwaną |
| Identyfikator dzierżawy | Identyfikator Guid dzierżawy skojarzonej z dziennikami aplikacji |
| resultType | Wynik operacji, może być *Powodzenie* lub *awarii* |
| resultSignature |  Jest to niezamapowane, a w tym polu można bezpiecznie zignorować. | 
| resultDescription | Dodatkowy opis wyniku, jeśli taki jest dostępny | 
| durationMs |  Jest to niezamapowane, a w tym polu można bezpiecznie zignorować. |
| callerIpAddress | Adres IP klienta, który zgłosił żądanie | 
| correlationId | Opcjonalny identyfikator Guid przekazany przez klienta. To może pomóc korelowanie operacji po stronie klienta przy użyciu operacji po stronie serwera i jest przydatne, gdy śledzenie dzienników, które rozciągają się między usługami. |
| identity | Tożsamość z tokenu, który został przedstawiony podczas przesyłania żądania. Może być konto użytkownika, konto system lub usługę podmiotu zabezpieczeń. |
| poziom | Typ komunikatu. W przypadku dzienników inspekcji jest zawsze *informacyjny* |
| location | Lokalizacja centrum danych |
| properties | Wyświetla listę obsługiwanych właściwości związane z dziennika inspekcji. Aby uzyskać więcej informacji zobacz pod tabelą. | 


| Nazwa właściwości | Opis |
|---------------|-------------|
| AuditEventCategory | Typ zdarzenia inspekcji. Może być *Zarządzanie użytkownikami*, *Zarządzanie aplikacjami* itp.|
| Typ tożsamości | *Aplikacja* lub *użytkownika* |
| Typ operacji | Może być *Dodaj*, *aktualizacji*, *Usuń* lub *innych* |
| Typ zasobu docelowego | Określa typ zasobu docelowego, że operacja została wykonana w. Może być *aplikacji*, *użytkownika*, *roli*, *zasad* | 
| Nazwa zasobu docelowego | Nazwa zasobu docelowego. Na przykład może to być nazwa aplikacji, nazwy roli, główną nazwę użytkownika lub główną nazwę usługi |
| additionalTargets | Wyświetla wszelkie dodatkowe właściwości dla określonych operacji. Na przykład operacji aktualizacji, stare wartości i nowe wartości są wyświetlane w obszarze *targetUpdatedProperties* | 

## <a name="next-steps"></a>Kolejne kroki

* [Interpretowanie dzienników logowania schematu w usłudze Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Często zadawane pytania i znane problemy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)