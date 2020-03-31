---
title: Schemat dziennika logowania w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Opis schematu dziennika logowania usługi Azure AD do użycia w usłudze Azure Monitor
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
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748665"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretowanie schematu dzienników logowania usługi Azure AD w usłudze Azure Monitor

W tym artykule opisano schemat dziennika logowania usługi Azure Active Directory (Azure AD) w usłudze Azure Monitor. Większość informacji, które są związane z logowania jest pod właściwości *atrybut* `records` obiektu.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Opisy pól

| Nazwa pola | Opis |
|------------|-------------|
| Time | Data i godzina w czasie UTC. |
| ResourceId | Ta wartość jest niezamapowana i można bezpiecznie zignorować to pole.  |
| OperationName | W przypadku logowania ta wartość jest zawsze *działaniem logowania.* |
| OperacjaVersion | Wersja interfejsu API REST, która jest wymagana przez klienta. |
| Kategoria | W przypadku logowania ta wartość jest zawsze *SignIn*. | 
| TenantId | Identyfikator GUID dzierżawy, który jest skojarzony z dziennikami. |
| Resulttype | Wynikiem operacji logowania może być *powodzenie* lub *niepowodzenie*. | 
| ResultSignature | Zawiera kod błędu, jeśli istnieje, dla operacji logowania. |
| Opis wyników | Zawiera opis błędu dla operacji logowania. |
| ryzykoDetail | ryzykoDetail | Podaje "przyczynę" określonego stanu ryzykownego użytkownika, logowania lub wykrywania ryzyka. Możliwe `none`wartości to: `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised`, `unknownFutureValue`, , , , , . Wartość `none` oznacza, że do tej pory nie wykonano żadnej akcji na użytkowniku lub loguch. <br>**Uwaga:** Szczegóły dotyczące tej właściwości wymagają licencji usługi Azure AD Premium P2. Inne licencje zwracają `hidden`wartość . |
| riskEventTypes (Typy ryzyka) | riskEventTypes (Typy ryzyka) | Typy wykrywania ryzyka skojarzone z logowaniem. Możliwe `unlikelyTravel`wartości to: `anonymizedIPAddress` `maliciousIPAddress`, `unfamiliarFeatures` `malwareInfectedIPAddress`, `suspiciousIPAddress` `leakedCredentials`, `investigationsThreatIntelligence` `generic`, `unknownFutureValue`, , , , , i . |
| riskLevelAggregated | riskPoziom | Zagregowany poziom ryzyka. Możliwe wartości `none`to: `low` `medium`, `high` `hidden`, `unknownFutureValue`, , i . Wartość `hidden` oznacza, że użytkownik lub logowanie nie zostało włączone dla usługi Azure AD Identity Protection. **Uwaga:** Szczegóły dotyczące tej właściwości są dostępne tylko dla klientów usługi Azure AD Premium P2. Wszyscy pozostali klienci `hidden`zostaną zwróconi. |
| riskLevelDuringSignIn | riskPoziom | Poziom ryzyka podczas logowania. Możliwe wartości `none`to: `low` `medium`, `high` `hidden`, `unknownFutureValue`, , i . Wartość `hidden` oznacza, że użytkownik lub logowanie nie zostało włączone dla usługi Azure AD Identity Protection. **Uwaga:** Szczegóły dotyczące tej właściwości są dostępne tylko dla klientów usługi Azure AD Premium P2. Wszyscy pozostali klienci `hidden`zostaną zwróconi. |
| riskPaństwo | riskPaństwo | Raportuje stan ryzykownego użytkownika, logowania lub wykrywania ryzyka. Możliwe `none`wartości to: `confirmedSafe` `remediated`, `dismissed` `atRisk`, `confirmedCompromised` `unknownFutureValue`, , , , . |
| Czas trwaniaMs |  Ta wartość jest niezamapowana i można bezpiecznie zignorować to pole. |
| CallerIpAddress (Adres callerIpAddress) | Adres IP klienta, który złożył żądanie. | 
| CorrelationId | Opcjonalny identyfikator GUID, który jest przekazywany przez klienta. Ta wartość może pomóc skorelować operacje po stronie klienta z operacjami po stronie serwera i jest przydatna podczas śledzenia dzienników obejmujących usługi. |
| Tożsamość | Tożsamość z tokenu, który został przedstawiony podczas żądania. Może to być konto użytkownika, konto systemowe lub jednostki usługi. |
| Poziom | Zawiera typ wiadomości. W przypadku audytu jest to zawsze *informacje.* |
| Lokalizacja | Zapewnia lokalizację działania logowania. |
| Właściwości | Wyświetla listę wszystkich właściwości, które są skojarzone z logowania. Aby uzyskać więcej informacji, zobacz [Microsoft Graph API Reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Ten schemat używa tych samych nazw atrybutów jako zasobu logowania, aby uzyskać czytelność.

## <a name="next-steps"></a>Następne kroki

* [Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)
