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
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, sureshja
ms.openlocfilehash: 0783c9885ec47bdd8c33c296e975547391900139
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505739"
---
# <a name="azure-active-directory-application-manifest"></a>Manifest aplikacji w usłudze Azure Active Directory
Aplikacje, które integrują się z usługą Azure AD, musi być zarejestrowany z dzierżawą usługi Azure AD. Tę aplikację można skonfigurować przy użyciu manifest aplikacji (w ramach bloku usługi Azure AD) w [witryny Azure portal](https://portal.azure.com).

## <a name="manifest-reference"></a>Odwołanie do manifestu

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Klucz  |Typ wartości |Przykładowa wartość  |Opis  |
|---------|---------|---------|---------|
|appID     |  Ciąg identyfikatora       |""|  Unikatowy identyfikator dla aplikacji, która jest przypisana do aplikacji przez usługę Azure AD.|
|appRoles     |    Typ tablicy     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Kolekcja ról, które aplikacja może deklarować. Te role można przypisać do użytkowników, grup lub jednostki usługi.|
|availableToOtherTenants|wartość logiczna|`true`|Jeśli ta wartość jest równa true, aplikacja jest dostępna do innych dzierżaw. Jeśli ma wartość false, aplikacja jest dostępna tylko dla dzierżawy jest zarejestrowany w. Aby uzyskać więcej informacji, zobacz: [jak zalogować dowolnego użytkownika usługi Azure Active Directory (AD), za pomocą wzorca aplikacji wielodostępnych](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |ciąg         |`MyRegisteredApp`         |Nazwa wyświetlana aplikacji. |
|errorURL     |ciąg         |`http://MyRegisteredAppError`         |Adres URL dla błędów w aplikacji. |
|groupMembershipClaims     |    ciąg     |    `1`     |   Maska bitów, który konfiguruje oświadczenia "groups" wystawiony w użytkownika lub token dostępu OAuth 2.0, który oczekuje, że aplikacja. Wartości maski bitów to: 0: Brak, 1: grupy zabezpieczeń i role usługi Azure AD, 2: zarezerwowane i 4: zarezerwowane. Ustawienie maski bitów na 7 otrzyma wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, które zalogowany użytkownik jest członkiem. |
|optionalClaims     |  ciąg       |     `null`    |    [Opcjonalnych oświadczeń](active-directory-optional-claims.md) zwrócone w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji. |
|acceptMappedClaims    |      wartość logiczna   | `true`        |    Jeśli ta wartość jest równa true, umożliwia mapowanie oświadczeń aplikacji na używanie bez określenia niestandardowego klucza podpisywania.|
|Strona główna     |  ciąg       |`http://MyRegistererdApp`         |    Adres URL do strony głównej aplikacji. |
|identifierUris     |  Tablica ciągów       | `http://MyRegistererdApp`        |   URI(s) zdefiniowanych przez użytkownika, który jednoznacznie zidentyfikować aplikacji sieci Web w swojej dzierżawie usługi Azure AD, lub w zweryfikowanej domenie niestandardowej, jeśli aplikacja wielodostępna. |
|keyCredentials     |   Typ tablicy      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Ta właściwość zawiera odwołania do przypisanych aplikacji poświadczeń, oparte na ciągach wspólne klucze tajne i certyfikaty X.509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient zamiast, jako zasób). |
|knownClientApplications     |     Typ tablicy    |    [identyfikator guid]     |     Wartość służy do tworzenia pakietów zgody, jeśli masz rozwiązanie, które zawiera dwa elementy, aplikacja kliencka i niestandardowych aplikacji interfejsu API sieci web. Jeśli wprowadzasz identyfikator aplikacji klienta do tej wartości, użytkownik będzie miał tylko do zgody raz do aplikacji klienckiej. Usługa Azure AD będzie wiedzieć, że wyrażanie zgody dla klienta oznacza, że niejawnie wyrażanie zgody na interfejs API sieci web i automatycznie przydzieli nazwy główne usług dla klienta i interfejsu API sieci web w tym samym czasie. Zarówno klient, jak i aplikacji interfejsu API sieci web musi być zarejestrowany w ramach tej samej dzierżawy.|
|logoutUrl     |   ciąg      |     `http://MyRegisteredAppLogout`    |   Adres URL wylogowania aplikacji. |
|oauth2AllowImplicitFlow     |   wartość logiczna      |  `false`       |       Określa, czy ta aplikacja sieci web może żądać tokenów niejawny przepływ OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana do aplikacje korzystające z przeglądarki, takich jak aplikacji jednostronicowej Javascript. |
|oauth2AllowUrlPathMatching     |   wartość logiczna      |  `false`       |   Określa, czy, jako część żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalał na dopasowywania ścieżek identyfikatora URI przekierowania względem adresów URL odpowiedzi aplikacji. Wartość domyślna to false. |
|oauth2Permissions     | Typ tablicy         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Kolekcja zakresów uprawnień OAuth 2.0, które sieci web aplikacji interfejsu API (zasobu) uwidacznia aplikacjom klienckim. Te zakresy uprawnień, może przyznawać aplikacjom klienckim podczas wyrażania zgody. |
|oauth2RequiredPostResponse     | wartość logiczna        |    `false`     |      Określa, czy, jako część żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalać na żądania POST, w przeciwieństwie do żądania GET. Wartość domyślna to false, która określa, czy może być tylko żądania GET. 
|Identyfikator obiektu     | Ciąg identyfikatora        |     ""    |    Unikatowy identyfikator aplikacji w katalogu. Ten identyfikator nie jest to identyfikator używany do identyfikowania aplikacji w każdej transakcji protokołu. Użytkownik jest do odwoływania się do obiektu w katalogu zapytania.|
|passwordCredentials     | Typ tablicy        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Zobacz opis właściwości keyCredentials. |
|publicClient     |  wartość logiczna       |      `false`   | Określa, czy aplikacja jest klientem publicznym (takich jak zainstalowana aplikacja uruchamiana na urządzeniu przenośnym). Wartość domyślna to false. |
|supportsConvergence     |  wartość logiczna       |   `false`      | Nie można edytować tej właściwości. Zaakceptuj wartość domyślną. |
|adresów URL odpowiedzi     |  Tablica ciągów       |   `http://localhost`     |  Ta właściwość wielu wartości przechowuje listę wartości redirect_uri zarejestrowanych, które usługi Azure AD będzie akceptować jako miejsca docelowe, gdy zwracany jest tokenów. |
|requiredResourceAccess     |     Typ tablicy    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Określa zasoby, które ta aplikacja wymaga dostępu do i z zestawem zakresy uprawnień uwierzytelniania OAuth i ról aplikacji, których potrzebuje pod każdym z tych zasobów. Ten poprzedzający konfigurację dostępu do wymaganych zasobów stacji środowisko zgody.|
|resourceAppId     |    Ciąg identyfikatora     |  ""      |   Unikatowy identyfikator aplikacji wymaga dostępu do zasobu. Ta wartość powinna być równa appId zadeklarowanych w aplikacji docelowej zasobu. |
|resourceAccess     |  Typ tablicy       | Zobacz przykładową wartość dla właściwości requiredResourceAccess. |   Lista zakresów uprawnień OAuth 2.0 i ról aplikacji, których aplikacja wymaga z określonego zasobu (zawiera identyfikator i typ wartości określone zasoby)        |
|samlMetadataUrl    |ciąg| `http://MyRegisteredAppSAMLMetadata` |Adres URL metadanych SAML aplikacji.| 

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat relacji między obiekty aplikacji i nazwy głównej usługi aplikacji, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure AD][AAD-APP-OBJECTS].
* Zobacz [słownik deweloperów usługi Azure AD] [ AAD-DEVELOPER-GLOSSARY] uzyskać definicje niektórych podstawowe pojęcia dla deweloperów usługi Azure Active Directory (AD).

Aby przekazać opinię, ułatwiające analizy i połącz kształt naszej zawartości, należy użyć w poniższej sekcji komentarzy.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

