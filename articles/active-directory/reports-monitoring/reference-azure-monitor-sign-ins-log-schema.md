---
title: Schemat rejestrowania w dzienniku usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Opisz logowania usługi Azure AD w schemacie dziennika do użycia w usłudze Azure Monitor (wersja zapoznawcza)
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78ce1de5b5b9ff46efcc9e7faed9aa147b53211a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439060"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor-preview"></a>Interpretowanie schematu dzienniki logowania w usłudze Azure AD w usłudze Azure Monitor (wersja zapoznawcza)

W tym artykule opisano schemat rejestrowania w dzienniku usługi Azure Active Directory (Azure AD) w usłudze Azure Monitor. Większość danych, który jest powiązany z logowania znajduje się w obszarze *właściwości* atrybut `records` obiektu.

```json
{ 
    "records": [ 
    { 
        "time": "2018-05-16T16:09:58.4634578Z", 
        "resourceId": null, 
        "operationName": "Sign-in activity", 
        "operationVersion": "1.0", 
        "category": "SignIn", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "50140", 
        "resultSignature": "None", 
        "resultDescription": "Other", 
        "durationMs": 0, 
        "callerIpAddress": "167.220.0.158", 
        "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
        "identity": "arvind harinder", 
        "Level": 4, 
        "location": "US", 
        "properties": { 
            "id": "0782c515-08b6-4029-a65c-29d9a3d20800", 
            "createdDateTime": "2018-05-16T16:09:58.4634578+00:00", 
            "userDisplayName": "Arvind Harinder", 
            "userPrincipalName": "ah@wingtiptoysonline.onmicrosoft.com", 
            "userId": "5b9f356d-9592-42fd-9ec4-d70963909534", 
            "appId": "c44b4083-3bb0-49c1-b47d-974e53cbdf3c", 
            "appDisplayName": "Azure Portal", 
            "ipAddress": "167.220.0.158", 
            "status": { 
                "errorCode": 50140, 
                "failureReason": "Other" 
            }, 
            "clientAppUsed": "Browser", 
            "deviceDetail": { 
                "operatingSystem": "Windows 10", 
                "browser": "Chrome 66.0.3359" 
            }, 
            "location": { 
                "city": "Sammamish", 
                "state": "Washington", 
                "countryOrRegion": "US", 
                "geoCoordinates": { 
                    "latitude": 47.66630935668945, 
                    "longitude": -122.09821319580078 
                } 
            }, 
            "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
            "conditionalAccessStatus": 2, 
            "conditionalAccessPolicies": [ 
            { 
                "id": "de7e60eb-ed89-4d73-8205-2227def6b7c9", 
                "displayName": "[billg] SharePoint limited access policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "7412a2d8-cbb1-4f1c-96cf-8410b4b8b37b", 
                "displayName": "[BillG] AIP MFA Policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "727ed8ea-059d-4d8f-aba5-c1dc500e8b06", 
                "displayName": "[billg] mfa for mail", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "6701123a-b4c6-48af-8565-565c8bf7cabc", 
                "displayName": "Medium signin risk block", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "fbafa2da-cf7f-4ec3-83cf-281188e53f76", 
                "displayName": "Require MFA for admins [Ignite talk] ", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "15339054-709d-4e06-a9ec-342bf043ea56", 
                "displayName": "Enhanced proofing for Azure portal [Ignite talk]", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "2ff9436f-bc72-4ce6-b17e-e7e51153146e", 
                "displayName": "[calebb] AIP policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "46ab586b-9447-4847-a889-e60705d96e56", 
                "displayName": "Test policy, OR", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "ceb6e17e-a5d0-4b3a-a150-6c2be2d5b0e9", 
                "displayName": "mm policy with Duo", 
                "enforcedGrantControls": [ 
                    "Require Duo Mfa" 
                ], 
            "enforcedSessionControls": [], 
            "result": 2 
            }, 
            ], 
            "isRisky": false 
            } 
        } 
    } 
```

## <a name="field-descriptions"></a>Opisy pól

| Nazwa pola | Opis |
|------------|-------------|
| Time | Data i godzina w formacie UTC. |
| ResourceId | Ta wartość jest niezamapowany. Ponadto można bezpiecznie zignorować to pole.  |
| OperationName | Do logowania, ta wartość jest zawsze *logowań*. |
| OperationVersion | Wersja interfejsu API REST, które są wymagane przez klienta. |
| Kategoria | Do logowania, ta wartość jest zawsze *SignIn*. | 
| TenantId | Identyfikator GUID, który jest skojarzony z dziennikami dzierżawy. |
| ResultType | Wynik operacji logowania może być *Powodzenie* lub *błąd*. | 
| ResultSignature | Zawiera kod błędu dla operacji logowania. |
| ResultDescription | Zawiera opis błędu dla operacji logowania. |
| durationMs |  Ta wartość jest niezamapowany. Ponadto można bezpiecznie zignorować to pole.|
| CallerIpAddress | Adres IP klienta, który zgłosił żądanie. | 
| CorrelationId | Opcjonalny identyfikator GUID, który jest przekazywany przez klienta. Ta wartość może pomóc korelowanie operacji po stronie klienta przy użyciu operacji po stronie serwera, a jest to przydatne, gdy podczas śledzenia dzienników, które rozciągają się usług. |
| Tożsamość | Tożsamość z tokenu, który został przedstawiony podczas zgłosił żądanie. Można go, konto użytkownika, konto systemowe lub jednostki usługi. |
| Poziom | Zawiera typ komunikatu. W przypadku inspekcji, jest zawsze *komunikat o charakterze informacyjnym*. |
| Lokalizacja | Określa lokalizację aktywności logowania. |
| Właściwości | Wyświetla listę wszystkich właściwości, które są skojarzone z logowania. Aby uzyskać więcej informacji, zobacz [Microsoft Graph API Reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Ten schemat używa tych samych nazw atrybutu jako zasób logowania dla czytelności.

## <a name="next-steps"></a>Kolejne kroki

* [Interpret audit logs schema in Azure Monitor (Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor)](reference-azure-monitor-audit-log-schema.md)
* [Więcej informacji na temat dzienniki diagnostyczne platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
