---
title: Opis manifestu aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Szczegółowe pokrycia manifest aplikacji usługi Azure Active Directory, który reprezentuje konfiguracji tożsamości aplikacji w dzierżawie usługi Azure AD i jest używana do przyspieszania OAuth autoryzacji, środowisko zgody i inne.
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
ms.openlocfilehash: 7448a6c37df2c0bbffbebf23d211e3ace8d12edc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317399"
---
# <a name="azure-active-directory-application-manifest"></a>Manifest aplikacji w usłudze Azure Active Directory
Aplikacje do zintegrowania z usługą Azure AD musi być zarejestrowana w dzierżawie usługi Azure AD. Tę aplikację można skonfigurować przy użyciu manifest aplikacji (w ramach bloku usługi Azure AD) w [portalu Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Odwołanie do manifestu

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Klucz  |Typ wartości |Przykładowa wartość  |Opis  |
|---------|---------|---------|---------|
|appID     |  Ciąg identyfikatora       |""|  Unikatowy identyfikator dla aplikacji, która jest przypisany do aplikacji przez usługę Azure AD.|
|appRoles     |    Typ tablicy     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Kolekcja ról, które mogą zadeklarować aplikacji. Te role można przypisać do użytkowników, grupy lub podmiotów zabezpieczeń usługi.|
|availableToOtherTenants|wartość logiczna|`true`|Jeśli ta wartość jest równa true, aplikacja jest dostępna na innych dzierżawców. Jeśli ma wartość false, aplikacja jest dostępna tylko dla dzierżawcy jest zarejestrowany w. Aby uzyskać więcej informacji, zobacz: [jak zarejestrować każdy użytkownik usługi Azure Active Directory (AD) przy użyciu wzorca wielodostępnych aplikacji](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |ciąg         |`MyRegisteredApp`         |Nazwa wyświetlana dla aplikacji. |
|errorURL     |ciąg         |`http://MyRegisteredAppError`         |Adres URL błędów występujących w aplikacji. |
|groupMembershipClaims     |    ciąg     |    `1`     |   Maska bitowa konfigurującego wystawione oświadczenie "grupy" użytkownika lub tokenu dostępu protokołu OAuth 2.0, który oczekuje aplikacji. Wartości maski to: 0: Brak 1: grup zabezpieczeń i role usługi Azure AD, 2: zarezerwowane i 4: zastrzeżone. Ustawienie maski bitów 7 otrzyma wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, które zalogowany użytkownik jest członkiem. |
|optionalClaims     |  ciąg       |     `null`    |    [Opcjonalnych oświadczeń](active-directory-optional-claims.md) zwrócony w tokenie przez usługę tokenu zabezpieczającego dla określonej aplikacji. |
|acceptMappedClaims    |      wartość logiczna   | `true`        |    Jeśli ta wartość jest ustawiana na wartość true pozwala aplikacji na używanie oświadczeń mapowania bez określania niestandardowych klucza podpisywania.|
|Strona główna     |  ciąg       |`http://MyRegistererdApp`         |    Adres URL do strony głównej aplikacji. |
|identifierUris     |  Tablica ciągów       | `http://MyRegistererdApp`        |   URI(s) zdefiniowane przez użytkownika, które jednoznacznie zidentyfikować aplikacji sieci Web w swojej dzierżawy usługi Azure AD lub w zweryfikowanej domeny niestandardowej, gdy aplikacja jest wielodostępnej. |
|keyCredentials     |   Typ tablicy      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Ta właściwość zawiera odwołania do poświadczeń przypisane aplikacji, na podstawie ciągu wspólne klucze tajne i certyfikaty X.509. Te poświadczenia są używane podczas żądania tokenów dostępu (Jeśli aplikacja działa jako klient raczej który jako zasób). |
|knownClientApplications     |     Typ tablicy    |    [identyfikator guid]     |     Wartość służy do zgody wiązanie, jeśli rozwiązanie, który zawiera dwie części, aplikacji klienckiej i niestandardowych aplikacji interfejsu API sieci web. Po wprowadzeniu identyfikator aplikacji klienta w tej wartości, użytkownik będzie miał tylko do zgody raz do aplikacji klienckiej. Usługi Azure AD będzie wiadomo, że zgodę klienta oznacza zgodę niejawnie interfejsu API sieci web i będzie automatycznie obsługiwał nazwy główne usług zarówno dla klienta, jak i interfejs API sieci web w tym samym czasie. Zarówno klient, jak i aplikacji interfejsu API sieci web musi być zarejestrowana w ramach tej samej dzierżawy.|
|logoutUrl     |   ciąg      |     `http://MyRegisteredAppLogout`    |   Adres URL wylogowania w aplikacji. |
|oauth2AllowImplicitFlow     |   wartość logiczna      |  `false`       |       Określa, czy ta aplikacja sieci web może żądać OAuth2.0 przepływu niejawnego tokenów. Wartość domyślna to false. Ta flaga jest wykorzystywana dla aplikacji przeglądarki, takich jak aplikacje jednej strony Javascript. |
|oauth2AllowUrlPathMatching     |   wartość logiczna      |  `false`       |   Określa, czy w ramach żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalał na ścieżkę dopasowywania identyfikatora URI przekierowania dla aplikacji replyUrls. Wartość domyślna to false. |
|oauth2Permissions     | Typ tablicy         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Kolekcja zakresy uprawnień OAuth 2.0, które ujawnia aplikacji interfejsu API (zasobów) w sieci web dla aplikacji klienckich. Te zakresy uprawnień można udzielić aplikacji klienckich podczas ich zgodę. |
|oauth2RequiredPostResponse     | wartość logiczna        |    `false`     |      Określa, czy w ramach żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalał na żądania POST, w przeciwieństwie do żądania GET. Wartość domyślna to false, która określa, czy mogą być tylko żądania GET. 
|Identyfikator obiektu     | Ciąg identyfikatora        |     ""    |    Unikatowy identyfikator dla aplikacji w katalogu. Ten identyfikator nie jest identyfikator używany do identyfikowania aplikacji w każdej transakcji protokołu. Odwołanie do obiektu w katalogu zapytania jest użytkownika.|
|passwordCredentials     | Typ tablicy        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Zobacz opis właściwości keyCredentials. |
|publicClient     |  wartość logiczna       |      `false`   | Określa, czy aplikacja ma publicznego klienta (na przykład zainstalowanych aplikacji uruchomionej na urządzeniu przenośnym). Wartość domyślna to false. |
|supportsConvergence     |  wartość logiczna       |   `false`      | Tej właściwości nie powinny być edytowane. Zaakceptuj wartość domyślną. |
|replyUrls     |  Tablica ciągów       |   `http://localhost`     |  Ta właściwość wielowartościowe przechowuje listę zarejestrowanych redirect_uri wartości, które będzie akceptować usługi Azure AD jako miejsca docelowe, zwracając tokenów. |
|requiredResourceAccess     |     Typ tablicy    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Określa zasoby, które ta aplikacja wymaga dostępu do i z zestawem zakresy uprawnień uwierzytelniania OAuth i role aplikacji, które należy w każdej z tych zasobów. Tej wstępnej konfiguracji dostępu do wymaganych zasobów stacji środowisko zgody.|
|resourceAppId     |    Ciąg identyfikatora     |  ""      |   Unikatowy identyfikator dla aplikacji wymaga dostępu do zasobu. Ta wartość powinna być równa appId zadeklarowana w aplikacji docelowej zasobów. |
|resourceAccess     |  Typ tablicy       | Zobacz przykład wartość właściwości requiredResourceAccess. |   Lista OAuth2.0 zakresy uprawnień i ról aplikacji, które wymaga aplikacji z określonego zasobu (zawiera identyfikator i typ wartości określonych zasobów)        |
|samlMetadataUrl    |ciąg| `http://MyRegisteredAppSAMLMetadata` |Adres URL metadanych SAML aplikacji.| 

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na relacje między obiektów aplikacji i nazwę główną usługi dla aplikacji, zobacz [aplikacji i usług obiektów principal w usłudze Azure AD][AAD-APP-OBJECTS].
* Zobacz [słownik dewelopera usługi Azure AD] [ AAD-DEVELOPER-GLOSSARY] definicje niektóre podstawowe koncepcje dewelopera usługi Azure Active Directory (AD).

Następująca sekcja komentarze służy do przekazywania opinii, ułatwiająca uściślić i kształtu zawartość.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
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

