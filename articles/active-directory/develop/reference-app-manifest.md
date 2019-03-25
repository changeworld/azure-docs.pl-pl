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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2750de16c71e7d678810316f281e28ca8c40553d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403157"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikacji w usłudze Azure Active Directory

Manifest aplikacji zawiera definicję wszystkie atrybuty obiektu aplikacji na platformie tożsamości firmy Microsoft. Służy również jako mechanizm służący do aktualizowania obiektu aplikacji. Aby uzyskać więcej informacji na temat jednostki Application i jej schematu, zobacz [dokumentację jednostki Application interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Pozwala ona skonfigurować atrybuty aplikacji za pośrednictwem witryny Azure portal lub programowo przy użyciu programu Microsoft Graph. Istnieją jednak sytuacje, w którym konieczne będzie Edytuj manifest aplikacji, aby skonfigurować aplikację atrybut. Scenariusze obejmują:

* Jeśli aplikacja jest zarejestrowana jako usługi Azure AD wielu dzierżawców i osobistymi kontami Microsoft, nie można zmienić obsługiwanych kont firmy Microsoft w interfejsie użytkownika. Zamiast tego należy podać edytorze manifestu aplikacji do zmianę typu konta obsługiwane.
* Jeśli musisz zdefiniować uprawnienia i role, które obsługuje aplikację, należy zmodyfikować manifest aplikacji.

## <a name="configure-the-app-manifest"></a>Konfigurowanie manifestu aplikacji

Aby skonfigurować manifest aplikacji:

1. Zaloguj się [witryny Azure portal](https://portal.azure.com).
1. Wybierz **usługi Azure Active Directory** usługi, a następnie wybierz **rejestracje aplikacji** lub **rejestracje aplikacji (wersja zapoznawcza)**.
1. Wybierz aplikację, którą chcesz skonfigurować.
1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Zostanie otwarty Edytor manifestu z opartej na sieci web, umożliwiając Edytuj manifest w portalu. Opcjonalnie można wybrać **Pobierz** Edytuj manifest lokalnie, a następnie użyć **przekazywanie** Aby ponownie zastosować go do aplikacji.

## <a name="manifest-reference"></a>Odwołanie do manifestu

> [!NOTE]
> Jeśli nie widzisz **Przykładowa wartość** kolumnę po **opis**, zmaksymalizuj okno przeglądarki i przewijania/przesunięcia, aż zobaczysz **Przykładowa wartość** kolumny.

| Klucz  | Typ wartości | Opis  | Przykładowa wartość |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 dopuszczającego wartość null | Określa wersję tokenu dostępu, oczekiwany przez zasób. Spowoduje to zmianę wersji i niezależny od punktu końcowego lub użytego do wysłania żądania tokenu dostępu klienta utworzone format tokenu JWT.<br/><br/>Użyty punkt końcowy, w wersji 1.0 lub 2.0, jest wybierany przez klienta i ma wpływ tylko na wersję id_tokens. Zasoby muszą jawnie skonfigurować `accesstokenAcceptedVersion` do wskazania format tokena dostępu obsługiwane.<br/><br/>Możliwe wartości dla `accesstokenAcceptedVersion` są 1, 2 lub wartość null. Jeśli ma wartość null, domyślnie jest równa 1, który odnosi się do endpoint w wersji 1.0. | `2` |
| `allowPublicClient` | wartość logiczna | Określa typ bazowy aplikacji. Usługa Azure AD wnioskuje typ aplikacji z replyUrlsWithType domyślnie. Brak niektórych scenariuszy, w którym usługi Azure AD nie może określić typ aplikacji klienckich (np. [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) przepływ, gdy żądanie HTTP jest możliwe bez Przekierowywanie adresu URL). W takich przypadkach usługi Azure AD będzie interpretować typ aplikacji, w oparciu o wartość tej właściwości. Jeśli ta wartość jest równa true typ bazowy aplikacji jest ustawiony jako publicznych klienta, takich jak zainstalowanych aplikacji uruchomionej na urządzeniu przenośnym. Wartość domyślna to false, co oznacza, że typ bazowy aplikacji jest poufne klienta, takie jak aplikacja sieci web. | `false` |
| `appId` | Ciąg identyfikatora | Określa unikatowy identyfikator aplikacji, który jest przypisany do aplikacji przez usługę Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Typ tablicy | Określa kolekcję ról, które aplikacja może deklarować. Te role można przypisać do użytkowników, grup lub jednostki usługi. Aby uzyskać więcej przykładów i informacje, zobacz [Dodaj role aplikacji w aplikacji i odbierać je w tokenie](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | string | Konfiguruje `groups` oświadczenia wydane w użytkownika lub token dostępu OAuth 2.0, który oczekuje, że aplikacja. Aby ustawić tego atrybutu, użyj jednej z następujących wartości prawidłowy ciąg:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (dla grup zabezpieczeń i role usługi Azure AD)<br/>- `"All"` (obejmuje programy wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, które zalogowany użytkownik jest członkiem. | `"SecurityGroup"` |
| `optionalClaims` | string | Opcjonalne oświadczenia zwrócone w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji.<br>W tej chwili aplikacji obsługujących konta osobiste i usługi Azure AD (zarejestrowane za pośrednictwem portalu rejestracji aplikacji) nie można użyć oświadczeń opcjonalne. Jednak aplikacje zarejestrowane dla właśnie Azure AD przy użyciu punktu końcowego v2.0 można uzyskać opcjonalnych oświadczenia, które są wymagane w manifeście. Aby uzyskać więcej informacji, zobacz [opcjonalnych oświadczeń](active-directory-optional-claims.md). | `null` |
| `id` | Ciąg identyfikatora | Unikatowy identyfikator dla aplikacji w katalogu. Ten identyfikator nie jest to identyfikator używany do identyfikowania aplikacji w każdej transakcji protokołu. Służy do odwoływania się do obiektu w katalogu zapytania. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Tablica ciągów | URI(s) zdefiniowanych przez użytkownika, który jednoznacznie zidentyfikować aplikacji sieci Web w swojej dzierżawie usługi Azure AD, lub w zweryfikowanej domenie niestandardowej, jeśli aplikacja jest wielodostępne. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | string | Określa łącza do aplikacji warunków usługi i zasady zachowania poufności. Warunki usługi i zasady zachowania poufności są udostępniane użytkownikom za pośrednictwem środowiska zgody użytkownika. Aby uzyskać więcej informacji, zobacz [jak: Dodawanie warunków usługi i zasady zachowania poufności dla zarejestrowane aplikacje usługi Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Typ tablicy | Zawiera odwołania do przypisanych aplikacji poświadczeń, oparte na ciągach wspólne klucze tajne i certyfikaty X.509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient zamiast, jako zasób). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Typ tablicy | Używany do tworzenia pakietów zgody, jeśli masz rozwiązanie, które zawiera dwie części: aplikacji klienckiej i aplikacji sieci web niestandardowego interfejsu API. Jeśli wprowadzasz identyfikator aplikacji klienckiej na tę wartość, użytkownik będzie miał tylko do zgody raz do aplikacji klienckich. Usługa Azure AD będzie wiedzieć, że wyrażanie zgody dla klienta oznacza, że niejawnie wyrażanie zgody na interfejs API sieci web i automatycznie przydzieli nazwy główne usług dla klienta i interfejsu API sieci web w tym samym czasie. Zarówno klient, jak i aplikacji interfejsu API sieci web musi być zarejestrowany w ramach tej samej dzierżawy. | `[GUID]` |
| `logoUrl` | string | Odczytać wartości, który wskazuje na adres URL usługi CDN, logo, który został przekazany w portalu. | `https://MyRegisteredAppLogo` |
| `logoutUrl` | string | Adres URL wylogować się z aplikacji. | `https://MyRegisteredAppLogout` |
| `name` | string | Nazwa wyświetlana aplikacji. | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | wartość logiczna | Określa, czy ta aplikacja sieci web może żądać tokenów dostępu niejawny przepływ OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana do aplikacje korzystające z przeglądarki, takich jak aplikacji jednostronicowej Javascript. Aby dowiedzieć się więcej, wprowadź `OAuth 2.0 implicit grant flow` w spisie treści i znajduje się w tematach dotyczących niejawny przepływ. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | wartość logiczna | Określa, czy ta aplikacja sieci web może żądać tokenów Identyfikatora niejawny przepływ OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana do aplikacje korzystające z przeglądarki, takich jak aplikacji jednostronicowej Javascript. | `false` |
| `oauth2Permissions` | Typ tablicy | Określa kolekcję zakresów uprawnień OAuth 2.0, które aplikacja sieci web interfejsu API (zasobu) uwidacznia aplikacjom klienckim. Te zakresy uprawnień można udzielić aplikacji klienckich podczas wyrażania zgody. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | wartość logiczna | Określa, czy, jako część żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalać na żądania POST, w przeciwieństwie do żądania GET. Wartość domyślna to false, która określa, czy może być tylko żądania GET. | `false` |
| `parentalControlSettings` | string | `countriesBlockedForMinors` Określa krajach, w których aplikacja jest zablokowana dla osoby nieletnie.<br>`legalAgeGroupRule` Określa pełnoletności reguły grupy, która ma zastosowanie do użytkowników aplikacji. Można ustawić `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, lub `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Typ tablicy | Zobacz opis `keyCredentials` właściwości. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Typ tablicy | Wyświetla listę aplikacji i żądanych uprawnień dla niejawnego zgody. Wymaga administratora zostały podane zgody na aplikację. preAuthorizedApplications nie wymagają użytkownika o zgodę na żądane uprawnienia. Uprawnienia na liście preAuthorizedApplications nie wymagają zgody użytkownika. Jednak wszelkie dodatkowe żądane uprawnienia niewymienionych w preAuthorizedApplications wymagają zgody użytkownika. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | Tablica ciągów | Tej właściwości wielowartościowych listę wartości redirect_uri zarejestrowanych, które będzie akceptować usługi Azure AD jest przechowywana jako miejsc docelowych przy zwracaniu tokenów. Każda wartość identyfikatora uri może zawierać wartość typu skojarzonej aplikacji. Obsługiwany typ wartości to: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Typ tablicy | Za zgodą dynamiczne `requiredResourceAccess` dyski środowisko zgody administratora i środowisko zgody użytkownika dla użytkowników, którzy używają statycznych zgody. Jednak to nie podejmuj środowisko zgody użytkownika w przypadku ogólnych.<br>`resourceAppId` jest unikatowy identyfikator, dla których aplikacja wymaga dostępu do zasobu. Ta wartość powinna być równa appId zadeklarowanych w aplikacji do zasobu docelowego.<br>`resourceAccess` jest tablicą, który zawiera listę zakresów uprawnień OAuth 2.0 i ról aplikacji, których wymaga aplikacja z określonego zasobu. Zawiera `id` i `type` wartości określonych zasobów. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | string | Adres URL metadanych SAML dla aplikacji. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | string | Określa adres URL do strony głównej aplikacji. | `https://MyRegisteredApp` |
| `signInAudience` | string | Określa konta Microsoft, które są obsługiwane dla bieżącej aplikacji. Obsługiwane są następujące wartości:<ul><li>**AzureADMyOrg** -użytkowników za pomocą programu Microsoft konto służbowe w dzierżawie usługi Azure AD w organizacji (np. pojedyncza dzierżawa)</li><li>**AzureADMultipleOrgs** -użytkowników za pomocą programu Microsoft konto służbowe w dzierżawie usługi Azure AD w organizacji (czyli z wieloma dzierżawami)</li> <li>**AzureADandPersonalMicrosoftAccount** -użytkowników za pomocą osobistego konta Microsoft lub konto służbowe lub szkolne w dzierżawie usługi Azure AD w organizacji</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Tablica ciągów | Niestandardowe ciągi, które mogą służyć do kategoryzowania i identyfikacji aplikacji. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |


## <a name="manifest-limits"></a>Limity manifestu
Manifest aplikacji ma wiele atrybutów, które są nazywane kolekcjami, na przykład approles keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess, oauth2Permissions itp. W manifeście kompletnej aplikacji dla dowolnej aplikacji łączna liczba wpisów we wszystkich kolekcjach połączone ma zostały ograniczone do 1200. Jeśli masz już 100 URI przekierowania określone w manifeście aplikacji znajdują się tylko po lewej stronie z pozostałymi 1100 wpisów, aby używać we wszystkich innych kolekcji połączone, które tworzą manifestu.

> [!NOTE]
> W przypadku, gdy użytkownik próbuje dodać więcej niż 1200 wpisy w manifeście aplikacji. Może wystąpić błąd **"nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Rozmiar manifestu przekroczył limit. Zmniejsz liczbę wartości, a następnie ponów żądanie.** "


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
