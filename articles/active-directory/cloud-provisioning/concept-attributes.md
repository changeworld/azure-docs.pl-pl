---
title: Opis schematu usługi Azure AD i wyrażeń niestandardowych
description: W tym artykule opisano schemat usługi Azure AD, atrybuty, które agent inicjowania obsługi administracyjnej przepływa i wyrażenia niestandardowe.
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
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299333"
---
# <a name="understand-the-azure-ad-schema"></a>Opis schematu usługi Azure AD
Obiekt w usłudze Azure Active Directory (Azure AD), podobnie jak w każdym katalogu, jest programową konstrukcją danych wysokiego poziomu, która reprezentuje takie rzeczy jak użytkownicy, grupy i kontakty. Podczas tworzenia nowego użytkownika lub kontaktu w usłudze Azure AD, tworzysz nowe wystąpienie tego obiektu. Te wystąpienia mogą być zróżnicowane na podstawie ich właściwości.

Właściwości w usłudze Azure AD są elementy odpowiedzialne za przechowywanie informacji o wystąpieniu obiektu w usłudze Azure AD.

Schemat usługi Azure AD definiuje reguły, dla których właściwości mogą być używane we wpisie, rodzaje wartości, które mogą mieć te właściwości i jak użytkownicy mogą wchodzić w interakcje z tymi wartościami. 

Usługa Azure AD ma dwa typy właściwości:
- **Właściwości wbudowane:** właściwości, które są wstępnie zdefiniowane przez schemat usługi Azure AD. Te właściwości zapewniają różne zastosowania i może lub nie mogą być dostępne.
- **Rozszerzenia katalogów:** Właściwości, które są dostarczane, dzięki czemu można dostosować usługę Azure AD do własnego użytku. Jeśli na przykład lokalna usługa Active Directory została rozszerzona o określony atrybut i chcesz go przepłynąć, można użyć jednej z podanych właściwości niestandardowych. 

## <a name="attributes-and-expressions"></a>Atrybuty i wyrażenia
Gdy obiekt, taki jak użytkownik jest aprowizny do usługi Azure AD, tworzone jest nowe wystąpienie obiektu użytkownika. To tworzenie zawiera właściwości tego obiektu, które są również znane jako atrybuty. Początkowo nowo utworzony obiekt ma swoje atrybuty ustawione na wartości, które są określane przez reguły synchronizacji. Te atrybuty są następnie aktualizowane za pośrednictwem agenta inicjowania obsługi administracyjnej w chmurze.

![Inicjowanie obsługi administracyjnej obiektów](media/concept-attributes/attribute1.png)

Na przykład użytkownik może być częścią działu marketingu. Ich atrybut działu usługi Azure AD jest początkowo tworzony, gdy są one aprowizowane, a wartość jest ustawiona na Marketing. Sześć miesięcy później, jeśli zmienią się na Sprzedaż, ich lokalny atrybut działu Active Directory zostanie zmieniony na Sprzedaż. Ta zmiana synchronizuje się z usługą Azure AD i jest odzwierciedlana w obiekcie użytkownika usługi Azure AD.

Synchronizacja atrybutów może być bezpośrednia, gdzie wartość w usłudze Azure AD jest bezpośrednio ustawiona na wartość atrybutu lokalnego. Lub wyrażenie programowe może obsługiwać synchronizacji. Wyrażenie programowe jest potrzebne w przypadkach, gdy niektóre logiki lub determinacji muszą być wykonane w celu wypełniania wartości.

Na przykład, jeśli masz atrybutjohn.smith@contoso.commail " " i@contoso.compotrzebne do usuwania " " " część i przepływu tylko wartość "john.smith", należy użyć czegoś takiego:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Przykładowe wejście/wyjście:** <br>

* **WEJŚCIE** (poczta):john.smith@contoso.com" "
* **WYJŚCIE:**"john.smith"

Aby uzyskać więcej informacji na temat pisania wyrażeń niestandardowych i składni, zobacz [Pisanie wyrażeń mapowań atrybutów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

W poniższej tabeli wymieniono typowe atrybuty i sposób ich synchronizacji z usługą Azure AD.


|Lokalna usługa Active Directory|Typ mapowania|Azure AD|
|-----|-----|-----|
|Cn|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Wyrażenie|givenName|
|Objectguid|Direct|źródłoAnchorBinary|  
|Userprincipalname|Direct|userPrincipalName|
|ProxyAdress (Adres)|Direct|Proxyaddress|

## <a name="view-the-schema"></a>Wyświetlanie schematu
> [!WARNING]
> Konfiguracja inicjowania obsługi administracyjnej w chmurze tworzy jednostkę usługi. Podmiot zabezpieczeń usługi jest widoczny w witrynie Azure portal. Nie należy modyfikować mapowania atrybutów przy użyciu środowiska jednostkowego usługi w witrynie Azure portal.  To nie jest obsługiwane.

Aby wyświetlić schemat i zweryfikować go, wykonaj następujące kroki.

1.  Przejdź do [Programu Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
1.  Zaloguj się za pomocą konta administratora globalnego.
1.  Po lewej stronie wybierz **pozycję modyfikuj uprawnienia** i upewnij się, że **directory.ReadWrite.All** jest *wyrażana zgodę.*
1.  Uruchom kwerendę `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`. Ta kwerenda zwraca filtrowane listy podmiotów usługi.
1.  Zlokalizuj `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` `"id"`i zanotuj wartość dla .
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
1. Zamień `{Service Principal id}` na swoją wartość `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`i uruchom kwerendę .
1. Zlokalizuj `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` `"id"`i zanotuj wartość dla .
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
1. Teraz uruchom `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`kwerendę .
 
    Przykład: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Zastąp `{Service Principal Id}` i `{AD2ADD Provisioning Id}` wartości.

1. Ta kwerenda zwraca schemat.

   ![Zwrócony schemat](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Następne kroki

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
