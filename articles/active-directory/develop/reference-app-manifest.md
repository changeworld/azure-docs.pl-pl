---
title: Opis manifestu aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Szczegółowe pokrycie manifestu aplikacji usługi Azure Active Directory, reprezentuje konfigurację tożsamości aplikacji w dzierżawie usługi Azure AD, która jest używana do przyspieszania OAuth autoryzacji, środowisko wyrażania zgody i nie tylko.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127024"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikacji w usłudze Azure Active Directory

Aplikacje, które integrują się z usługą Azure Active Directory (Azure AD) musi być zarejestrowany z dzierżawą usługi Azure AD. Można skonfigurować aplikację w [witryny Azure portal](https://portal.azure.com) , wybierając **usługi Azure Active Directory**, wybierając aplikację, którą chcesz skonfigurować, a następnie wybierając **manifestu**.

## <a name="manifest-reference"></a>Odwołanie do manifestu

> [!NOTE]
> Jeśli nie można wyświetlić opisy, zmaksymalizuj okno przeglądarki lub przewijania/przesunięcia, aby wyświetlić opisy.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Klucz  | Typ wartości | Przykładowa wartość | Opis  |
|---------|---------|---------|---------|
| `appID` | Ciąg identyfikatora | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Określa unikatowy identyfikator aplikacji, który jest przypisany do aplikacji przez usługę Azure AD. |
| `appRoles` | Typ tablicy | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Określa kolekcję ról, które aplikacja może deklarować. Te role można przypisać do użytkowników, grup lub jednostki usługi. |
| `availableToOtherTenants`| wartość logiczna | `true` | Jeśli ta wartość jest równa true, aplikacja jest dostępna do innych dzierżaw. Jeśli ma wartość false, aplikacja jest dostępna tylko dla dzierżawy jest zarejestrowany w. Aby uzyskać więcej informacji, zobacz [jak: logowanie dowolnego użytkownika usługi Azure AD za pomocą wzorca aplikacji wielodostępnych](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | ciąg | `MyRegisteredApp` | Nazwa wyświetlana aplikacji. |
| `errorURL` | ciąg | `http://MyRegisteredAppError` | Adres URL dla błędów w aplikacji. |
| `groupMembershipClaims` | ciąg | `1` | Maska bitów, który konfiguruje `groups` oświadczenia wydane w użytkownika lub token dostępu OAuth 2.0, który oczekuje, że aplikacja. Wartości maski bitów to:<br>0: Brak<br>1: grupy zabezpieczeń i role usługi Azure AD<br>2: zarezerwowane<br>4: zarezerwowane<br>Ustawienie maski bitów na 7 otrzyma wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, które zalogowany użytkownik jest członkiem. |
| `optionalClaims` | ciąg | `null` | Opcjonalne oświadczenia zwrócone w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji. Aby uzyskać więcej informacji, zobacz [opcjonalnych oświadczeń](active-directory-optional-claims.md). |
| `acceptMappedClaims` | wartość logiczna | `true` | Jeśli ta wartość jest równa `true`, umożliwia aplikacji korzystanie z oświadczeń mapowanie bez określenia niestandardowego klucza podpisywania. |
| `homepage` | ciąg | `http://MyRegisteredApp` | Określa adres URL do strony głównej aplikacji. |
| `informationalUrls` | ciąg | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Określa łącza do aplikacji warunków usługi i zasady zachowania poufności. Warunki usługi i zasady zachowania poufności są udostępniane użytkownikom za pośrednictwem środowiska zgody użytkownika. Aby uzyskać więcej informacji, zobacz [porady: Dodawanie warunków usługi i zasady zachowania poufności dla zarejestrowane aplikacje usługi Azure AD](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Tablica ciągów | `http://MyRegistererdApp` | URI(s) zdefiniowanych przez użytkownika, który jednoznacznie zidentyfikować aplikacji sieci Web w swojej dzierżawie usługi Azure AD, lub w zweryfikowanej domenie niestandardowej, jeśli aplikacja jest wielodostępne. |
| `keyCredentials` | Typ tablicy | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Zawiera odwołania do przypisanych aplikacji poświadczeń, oparte na ciągach wspólne klucze tajne i certyfikaty X.509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient zamiast, jako zasób). |
| `knownClientApplications` | Typ tablicy | `[GUID]` | Używany do tworzenia pakietów zgody, jeśli masz rozwiązanie, które zawiera dwie części: aplikacji klienckiej i aplikacji sieci web niestandardowego interfejsu API. Jeśli wprowadzasz identyfikator aplikacji klienckiej na tę wartość, użytkownik będzie miał tylko do zgody raz do aplikacji klienckich. Usługa Azure AD będzie wiedzieć, że wyrażanie zgody dla klienta oznacza, że niejawnie wyrażanie zgody na interfejs API sieci web i automatycznie przydzieli nazwy główne usług dla klienta i interfejsu API sieci web w tym samym czasie. Zarówno klient, jak i aplikacji interfejsu API sieci web musi być zarejestrowany w ramach tej samej dzierżawy. |
| `logoutUrl` | ciąg | `http://MyRegisteredAppLogout` | Adres URL wylogować się z aplikacji. |
| `oauth2AllowImplicitFlow` | wartość logiczna | `false` | Określa, czy ta aplikacja sieci web może żądać tokenów niejawny przepływ OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana do aplikacje korzystające z przeglądarki, takich jak aplikacji jednostronicowej Javascript. |
| `oauth2AllowUrlPathMatching` | wartość logiczna | `false` | Określa, czy, jako część żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalał na dopasowywania ścieżek identyfikatora URI przekierowania względem adresów URL odpowiedzi aplikacji. Wartość domyślna to false. |
| `oauth2Permissions` | Typ tablicy | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Określa kolekcję zakresów uprawnień OAuth 2.0, które aplikacja sieci web interfejsu API (zasobu) uwidacznia aplikacjom klienckim. Te zakresy uprawnień można udzielić aplikacji klienckich podczas wyrażania zgody. |
| `oauth2RequiredPostResponse` | wartość logiczna | `false` | Określa, czy, jako część żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalać na żądania POST, w przeciwieństwie do żądania GET. Wartość domyślna to false, która określa, czy może być tylko żądania GET. |
| `objectId` | Ciąg identyfikatora | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | Unikatowy identyfikator dla aplikacji w katalogu. Ten identyfikator nie jest to identyfikator używany do identyfikowania aplikacji w każdej transakcji protokołu. Służy do odwoływania się do obiektu w katalogu zapytania. |
| `parentalControlSettings` | ciąg | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` Określa krajach, w których aplikacja jest zablokowana dla osoby nieletnie.<br>`legalAgeGroupRule` Określa pełnoletności reguły grupy, która ma zastosowanie do użytkowników aplikacji. Można ustawić `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, lub `BlockMinors`.  |
| `passwordCredentials` | Typ tablicy | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | Zobacz opis `keyCredentials` właściwości. |
| `publicClient` | wartość logiczna | `false` | Określa, czy aplikacja jest klientem publicznym, takich jak zainstalowanych aplikacji uruchomionej na urządzeniu przenośnym. Wartość domyślna to false. |
| `replyUrls` | Tablica ciągów | `"http://localhost"` | Tej właściwości wielowartościowych listę wartości redirect_uri zarejestrowanych, które będzie akceptować usługi Azure AD jest przechowywana jako miejsc docelowych przy zwracaniu tokenów. |
| `requiredResourceAccess` | Typ tablicy | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | Za zgodą dynamiczne `requiredResourceAccess` dyski środowisko zgody administratora i środowisko zgody użytkownika dla użytkowników, którzy używają statycznych zgody. Jednak to nie podejmuj środowisko zgody użytkownika w przypadku ogólnych.<br>`resourceAppId` jest unikatowy identyfikator, dla których aplikacja wymaga dostępu do zasobu. Ta wartość powinna być równa appId zadeklarowanych w aplikacji do zasobu docelowego.<br>`resourceAccess` jest tablicą, który zawiera listę zakresów uprawnień OAuth 2.0 i ról aplikacji, których wymaga aplikacja z określonego zasobu. Zawiera `id` i `type` wartości określonych zasobów. |
| `samlMetadataUrl` | ciąg | `http://MyRegisteredAppSAMLMetadata` | Adres URL metadanych SAML dla aplikacji. |

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji dotyczących relacji między obiekty aplikacji i nazwy głównej usługi aplikacji, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure AD](app-objects-and-service-principals.md).
* Zobacz [słownik deweloperów usługi Azure AD](developer-glossary.md) uzyskać definicje niektórych podstawowe pojęcia dla deweloperów usługi Azure Active Directory (AD).

Aby przekazać opinię, ułatwiające analizy i połącz kształt naszej zawartości, należy użyć w poniższej sekcji komentarzy.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

