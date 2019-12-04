---
title: Informacje o schemacie i wyrażeniach niestandardowych usługi Azure AD
description: W tym temacie opisano schemat usługi Azure AD, atrybuty, które są przesyłane przez agenta aprowizacji i wyrażenia niestandardowe.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794499"
---
# <a name="understanding-the-azure-ad-schema"></a>Informacje o schemacie usługi Azure AD
Obiekt w usłudze Azure AD, podobnie jak każdy katalog, to programowa konstrukcja danych wysokiego poziomu, która reprezentuje takie elementy jak użytkownicy, grupy i kontakty.  Gdy tworzysz nowego użytkownika lub kontakt w usłudze Azure AD, tworzysz nowe wystąpienie tego obiektu.  Te wystąpienia można rozróżnić w zależności od ich właściwości.

Właściwości w usłudze Azure AD to elementy odpowiedzialne za przechowywanie informacji o wystąpieniu obiektu w usłudze Azure AD.  

Schemat usługi Azure AD definiuje reguły, dla których właściwości mogą być używane w danym wpisie, rodzaje wartości, które mogą mieć te właściwości, oraz sposób, w jaki użytkownicy mogą współdziałać z tymi wartościami. 

Usługa Azure AD ma dwa typy właściwości.  Właściwości są następujące:
- **Wbudowane właściwości** — właściwości, które są wstępnie zdefiniowane przez schemat usługi Azure AD.  Te właściwości zapewniają różne zastosowania i mogą być niedostępne.
- **Rozszerzenia katalogów** — właściwości, które są udostępniane, aby można było dostosować usługę Azure AD do własnych potrzeb.  Na przykład jeśli rozszerzono Active Directory lokalne z określonym atrybutem i chcesz przepływać ten atrybut, możesz użyć jednej z podanych właściwości niestandardowych. 

## <a name="attributes-and-expressions"></a>Atrybuty i wyrażenia
Gdy do usługi Azure AD jest inicjowany obiekt, taki jak użytkownik, zostanie utworzone nowe wystąpienie obiektu użytkownika.  To tworzenie obejmuje właściwości tego obiektu, które są również znane jako atrybuty.  Początkowo nowo utworzony obiekt będzie miał atrybuty ustawione na wartości, które są określone przez reguły synchronizacji.  Te atrybuty są następnie aktualne za pośrednictwem agenta aprowizacji w chmurze.

![](media/concept-attributes/attribute1.png)

Na przykład jeśli użytkownik jest częścią działu marketingu, jego atrybut działu usługi Azure AD będzie początkowo tworzony podczas aprowizacji, a następnie wartość zostanie ustawiona na marketing.  Jednak następnie sześć miesięcy później zmienią się na sprzedaż.  Atrybut lokalnego działu usługi AD został zmieniony na sprzedaż.  Ta zmiana zostanie następnie przesynchronizowana do usługi Azure AD i odzwierciedlona w obiekcie użytkownika usługi Azure AD.

Synchronizacja atrybutów może być bezpośrednia, gdzie wartość w usłudze Azure AD jest ustawiona bezpośrednio na wartość atrybutu lokalnego.  Lub może istnieć wyrażenie programistyczne obsługujące tę synchronizację.  Konieczne jest wyrażenie programistyczne w przypadkach, gdy konieczne jest wykonanie pewnych logiki lub określenia w celu wypełnienia wartości.

Na przykład jeśli mam atrybut poczty ("john.smith@contoso.com") i potrzebuję części "@contoso.com" i przepływam tylko na wartość "Jan. Smith", można użyć podobnej do tego:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (poczta): "john.smith@contoso.com"
* **Dane wyjściowe**: "Jan. Kowalski"

Aby uzyskać dodatkowe informacje na temat pisania wyrażeń niestandardowych i składni, zobacz [Pisanie wyrażeń do mapowania atrybutów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

Poniżej wymieniono typowe atrybuty i sposób ich synchronizacji z usługą Azure AD.


|Lokalna usługa Active Directory|Typ mapowania|Azure AD|
|-----|-----|-----|
|CN|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Wyrażenie|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|proxyAddress|

## <a name="viewing-the-schema"></a>Wyświetlanie schematu
Aby wyświetlić schemat i sprawdzić go, wykonaj następujące czynności:

1.  Przejdź do [Eksploratora grafów](https://developer.microsoft.com/graph/graph-explorer).
2.  Zaloguj się przy użyciu konta administratora globalnego
3.  Po lewej stronie kliknij pozycję **Modyfikuj uprawnienia** i upewnij się, że jest wyrażana zgoda na **katalog. ReadWrite. All** .
4.  Uruchom następujące zapytanie: https://graph.microsoft.com/beta/serviceprincipals/.  To zapytanie zwróci listę jednostek usługi.
5.  Znajdź "appDisplayName": "Active Directory do Azure Active Directory aprowizacji" i zanotuj wartość "ID:".
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
6. Zastąp wartość {Service Principal ID} wartością i uruchom następujące zapytanie: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Znajdź sekcję "ID": "AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976" i zanotuj "ID:".
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
8. Teraz uruchom następujące zapytanie: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    Przykład: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Zastąp wartości {Service Principal ID} i {AD2ADD ID} wartościami.

9. To zapytanie zwróci schemat.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
