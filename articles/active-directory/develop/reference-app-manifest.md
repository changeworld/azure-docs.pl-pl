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
ms.date: 04/13/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b7a383eb8df8fcfd358bce226168e5fbeb42f7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009399"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikacji w usłudze Azure Active Directory

Manifest aplikacji zawiera definicję wszystkie atrybuty obiektu aplikacji na platformie tożsamości firmy Microsoft. Służy również jako mechanizm służący do aktualizowania obiektu aplikacji. Aby uzyskać więcej informacji dotyczących jednostek aplikacji i jego schematu, zobacz [dokumentacja jednostek aplikacji interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Pozwala ona skonfigurować atrybuty aplikacji za pośrednictwem platformy Azure portal lub programowo przy użyciu [interfejsu API REST](https://docs.microsoft.com/en-us/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) lub [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0#applications). Istnieją jednak sytuacje, w którym konieczne będzie Edytuj manifest aplikacji, aby skonfigurować aplikację atrybut. Scenariusze obejmują:

* Jeśli aplikacja jest zarejestrowana jako usługi Azure AD wielu dzierżawców i osobistymi kontami Microsoft, nie można zmienić obsługiwanych kont firmy Microsoft w interfejsie użytkownika. Zamiast tego należy podać edytorze manifestu aplikacji do zmianę typu konta obsługiwane.
* Jeśli musisz zdefiniować uprawnienia i role, które obsługuje aplikację, należy zmodyfikować manifest aplikacji.

## <a name="configure-the-app-manifest"></a>Konfigurowanie manifestu aplikacji

Aby skonfigurować manifest aplikacji:

1. Zaloguj się w [witryny Azure portal](https://portal.azure.com).
1. Wybierz **usługi Azure Active Directory** usługi, a następnie wybierz **rejestracje aplikacji** lub **rejestracje aplikacji (wersja zapoznawcza)**.
1. Wybierz aplikację, którą chcesz skonfigurować.
1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Zostanie otwarty Edytor manifestu z opartej na sieci web, umożliwiając Edytuj manifest w portalu. Opcjonalnie można wybrać **Pobierz** Edytuj manifest lokalnie, a następnie użyć **przekazywanie** Aby ponownie zastosować go do aplikacji.

## <a name="manifest-reference"></a>Odwołanie do manifestu

> [!NOTE]
> Jeśli nie widzisz **Przykładowa wartość** kolumnę po **opis**, zmaksymalizuj okno przeglądarki i przewijania/przesunięcia, aż zobaczysz **Przykładowa wartość** kolumny.

| Klucz  | Typ wartości | Opis  | Przykładowa wartość |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Int32 dopuszczającego wartość null | Określa wersję tokenu dostępu, oczekiwany przez zasób. Spowoduje to zmianę wersji i niezależny od punktu końcowego lub użytego do wysłania żądania tokenu dostępu klienta utworzone format tokenu JWT.<br/><br/>Użyty punkt końcowy, w wersji 1.0 lub 2.0, jest wybierany przez klienta i ma wpływ tylko na wersję id_tokens. Zasoby muszą jawnie skonfigurować `accesstokenAcceptedVersion` do wskazania format tokena dostępu obsługiwane.<br/><br/>Możliwe wartości dla `accesstokenAcceptedVersion` są 1, 2 lub wartość null. Jeśli ma wartość null, domyślnie jest równa 1, który odnosi się do endpoint w wersji 1.0. | `2` |
| `addIns` | Collection | Definiuje niestandardowe zachowanie, usługa korzystająca umożliwia wywołanie aplikacji w określonych kontekstach. Na przykład aplikacje, które można renderować strumieni plików mogą ustawiać właściwości dodatków dla jej funkcji "FileHandler". Umożliwi to usług, takich jak usługi Office 365 wywołanie aplikacji w kontekście dokument, który pracuje użytkownik. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Wartość logiczna | Określa typ bazowy aplikacji. Usługa Azure AD wnioskuje typ aplikacji z replyUrlsWithType domyślnie. Brak niektórych scenariuszy, w którym usługi Azure AD nie może określić typ aplikacji klienckich (np. [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) przepływ, gdy żądanie HTTP jest możliwe bez Przekierowywanie adresu URL). W takich przypadkach usługi Azure AD będzie interpretować typ aplikacji, w oparciu o wartość tej właściwości. Jeśli ta wartość jest równa true typ bazowy aplikacji jest ustawiony jako publicznych klienta, takich jak zainstalowanych aplikacji uruchomionej na urządzeniu przenośnym. Wartość domyślna to false, co oznacza, że typ bazowy aplikacji jest poufne klienta, takie jak aplikacja sieci web. | `false` |
| `availableToOtherTenants` | Wartość logiczna | wartość true, jeśli aplikacja jest udostępniana z innymi dzierżawami; w przeciwnym razie wartość false. <br><br> _Uwaga: Zastępuje to `signInAudience` w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) środowiska._ | |
| `appId` | String | Określa unikatowy identyfikator aplikacji, który jest przypisany do aplikacji przez usługę Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Collection | Określa kolekcję ról, które aplikacja może deklarować. Te role można przypisać do użytkowników, grup lub jednostki usługi. Aby uzyskać więcej przykładów i informacje, zobacz [Dodaj role aplikacji w aplikacji i odbierać je w tokenie](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | Nazwa wyświetlana aplikacji. <br><br> _Uwaga: Zastępuje to `name` w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) środowiska._ | `"MyRegisteredApp"` |
| `errorUrl` | String | Nieobsługiwane. | |
| `groupMembershipClaims` | String | Konfiguruje `groups` oświadczenia wydane w użytkownika lub token dostępu OAuth 2.0, który oczekuje, że aplikacja. Aby ustawić tego atrybutu, użyj jednej z następujących wartości prawidłowy ciąg:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (dla grup zabezpieczeń i role usługi Azure AD)<br/>- `"All"` (obejmuje programy wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, które zalogowany użytkownik jest członkiem. | `"SecurityGroup"` |
| `homepage` | String | Adres URL strony głównej aplikacji. <br><br> _Uwaga: Zastępuje to `signInUrl` w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) środowiska._ | `"https://MyRegisteredApp"` |
| `objectId` | String | Unikatowy identyfikator dla aplikacji w katalogu. <br><br> _Uwaga: Zastępuje to `id` w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) środowiska._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | Opcjonalne oświadczenia zwrócone w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji.<br>W tej chwili aplikacji obsługujących konta osobiste i usługi Azure AD (zarejestrowane za pośrednictwem portalu rejestracji aplikacji) nie można użyć oświadczeń opcjonalne. Jednak aplikacje zarejestrowane dla właśnie Azure AD przy użyciu punktu końcowego v2.0 można uzyskać opcjonalnych oświadczenia, które są wymagane w manifeście. Aby uzyskać więcej informacji, zobacz [opcjonalnych oświadczeń](active-directory-optional-claims.md). | `null` |
| `id` | String | Unikatowy identyfikator dla aplikacji w katalogu. Ten identyfikator nie jest to identyfikator używany do identyfikowania aplikacji w każdej transakcji protokołu. Służy do odwoływania się do obiektu w katalogu zapytania. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | URI(s) zdefiniowanych przez użytkownika, który jednoznacznie zidentyfikować aplikacji sieci Web w swojej dzierżawie usługi Azure AD, lub w zweryfikowanej domenie niestandardowej, jeśli aplikacja jest wielodostępne. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | Określa łącza do aplikacji warunków usługi i zasady zachowania poufności. Warunki usługi i zasady zachowania poufności są udostępniane użytkownikom za pośrednictwem środowiska zgody użytkownika. Aby uzyskać więcej informacji, zobacz [jak: Dodawanie warunków usługi i zasady zachowania poufności dla zarejestrowane aplikacje usługi Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Collection | Zawiera odwołania do przypisanych aplikacji poświadczeń, oparte na ciągach wspólne klucze tajne i certyfikaty X.509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient zamiast, jako zasób). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Używany do tworzenia pakietów zgody, jeśli masz rozwiązanie, które zawiera dwie części: aplikacji klienckiej i aplikacji sieci web niestandardowego interfejsu API. Jeśli wprowadzasz identyfikator aplikacji klienckiej na tę wartość, użytkownik będzie miał tylko do zgody raz do aplikacji klienckich. Usługa Azure AD będzie wiedzieć, że wyrażanie zgody dla klienta oznacza, że niejawnie wyrażanie zgody na interfejs API sieci web i automatycznie przydzieli nazwy główne usług dla klienta i interfejsu API sieci web w tym samym czasie. Zarówno klient, jak i aplikacji interfejsu API sieci web musi być zarejestrowany w ramach tej samej dzierżawy. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | Odczytać wartości, który wskazuje na adres URL usługi CDN, logo, który został przekazany w portalu. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | Adres URL wylogować się z aplikacji. | `"https://MyRegisteredAppLogout"` |
| `name` | String | Nazwa wyświetlana aplikacji. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Wartość logiczna | Określa, czy ta aplikacja sieci web może żądać tokenów dostępu niejawny przepływ OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana do aplikacje korzystające z przeglądarki, takich jak aplikacji jednostronicowej Javascript. Aby dowiedzieć się więcej, wprowadź `OAuth 2.0 implicit grant flow` w spisie treści i znajduje się w tematach dotyczących niejawny przepływ. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Wartość logiczna | Określa, czy ta aplikacja sieci web może żądać tokenów Identyfikatora niejawny przepływ OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana do aplikacje korzystające z przeglądarki, takich jak aplikacji jednostronicowej Javascript. | `false` |
| `oauth2Permissions` | Collection | Określa kolekcję zakresów uprawnień OAuth 2.0, które aplikacja sieci web interfejsu API (zasobu) uwidacznia aplikacjom klienckim. Te zakresy uprawnień można udzielić aplikacji klienckich podczas wyrażania zgody. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Wartość logiczna | Określa, czy, jako część żądania tokenu OAuth 2.0, usługi Azure AD będzie zezwalać na żądania POST, w przeciwieństwie do żądania GET. Wartość domyślna to false, która określa, czy może być tylko żądania GET. | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` Określa krajach, w których aplikacja jest zablokowana dla osoby nieletnie.<br>`legalAgeGroupRule` Określa pełnoletności reguły grupy, która ma zastosowanie do użytkowników aplikacji. Można ustawić `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, lub `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Collection | Zobacz opis `keyCredentials` właściwości. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Collection | Wyświetla listę aplikacji i żądanych uprawnień dla niejawnego zgody. Wymaga administratora zostały podane zgody na aplikację. preAuthorizedApplications nie wymagają użytkownika o zgodę na żądane uprawnienia. Uprawnienia na liście preAuthorizedApplications nie wymagają zgody użytkownika. Jednak wszelkie dodatkowe żądane uprawnienia niewymienionych w preAuthorizedApplications wymagają zgody użytkownika. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Wartość logiczna | Określa, czy ta aplikacja jest klientem publicznym (takich jak zainstalowana aplikacja uruchamiana na urządzeniu przenośnym). <br><br> _Uwaga: Zastępuje to `allowPublicClient` w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) środowiska._ | |
| `publisherDomain` | String | Domena wydawcy zweryfikowaną dla aplikacji. Tylko do odczytu. | https://www.contoso.com |
| `replyUrls` | Tablica ciągów | Tej właściwości wielowartościowych listę wartości redirect_uri zarejestrowanych, które będzie akceptować usługi Azure AD jest przechowywana jako miejsc docelowych przy zwracaniu tokenów. <br><br> _Uwaga: Zastępuje to `replyUrlsWithType` w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) środowiska._ | |
| `replyUrlsWithType` | Collection | Tej właściwości wielowartościowych listę wartości redirect_uri zarejestrowanych, które będzie akceptować usługi Azure AD jest przechowywana jako miejsc docelowych przy zwracaniu tokenów. Każda wartość identyfikatora uri może zawierać wartość typu skojarzonej aplikacji. Obsługiwany typ wartości to: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Collection | Za zgodą dynamiczne `requiredResourceAccess` dyski środowisko zgody administratora i środowisko zgody użytkownika dla użytkowników, którzy używają statycznych zgody. Jednak to nie podejmuj środowisko zgody użytkownika w przypadku ogólnych.<br>`resourceAppId` jest unikatowy identyfikator, dla których aplikacja wymaga dostępu do zasobu. Ta wartość powinna być równa appId zadeklarowanych w aplikacji do zasobu docelowego.<br>`resourceAccess` jest tablicą, który zawiera listę zakresów uprawnień OAuth 2.0 i ról aplikacji, których wymaga aplikacja z określonego zasobu. Zawiera `id` i `type` wartości określonych zasobów. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | Adres URL metadanych SAML dla aplikacji. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | Określa adres URL do strony głównej aplikacji. | `https://MyRegisteredApp` |
| `signInAudience` | String | Określa konta Microsoft, które są obsługiwane dla bieżącej aplikacji. Obsługiwane są następujące wartości:<ul><li>**AzureADMyOrg** -użytkowników za pomocą programu Microsoft konto służbowe w dzierżawie usługi Azure AD w organizacji (np. pojedyncza dzierżawa)</li><li>**AzureADMultipleOrgs** -użytkowników za pomocą programu Microsoft konto służbowe w dzierżawie usługi Azure AD w organizacji (czyli z wieloma dzierżawami)</li> <li>**AzureADandPersonalMicrosoftAccount** -użytkowników za pomocą osobistego konta Microsoft lub konto służbowe lub szkolne w dzierżawie usługi Azure AD w organizacji</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Niestandardowe ciągi, które mogą służyć do kategoryzowania i identyfikacji aplikacji. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Typowe problemy

### <a name="manifest-limits"></a>Limity manifestu

Manifest aplikacji ma wiele atrybutów, które są nazywane kolekcjami; na przykład approles, keycredentials, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess i oauth2Permissions. W manifeście kompletnej aplikacji dla dowolnej aplikacji łączna liczba wpisów we wszystkich kolekcjach połączone ma zostały ograniczone do 1200. Jeśli masz już 100 przekierowania URI określonego w manifeście aplikacji, możesz tylko po lewej stronie z 1100 pozostałe wpisy do użycia w innych kolekcjach w połączeniu, które tworzą manifestu.

> [!NOTE]
> W przypadku, gdy spróbujesz dodać więcej niż 1200 wpisy w manifeście aplikacji, zostanie wyświetlony błąd **"nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Rozmiar manifestu przekroczył limit. Zmniejsz liczbę wartości i ponów żądanie."**

### <a name="unsupported-attributes"></a>Nieobsługiwanych atrybutów

Manifest aplikacji reprezentuje schemat bazowego modelu aplikacji w usłudze Azure AD. Zgodnie z rozwojem schemat źródłowy w edytorze manifestu zostaną zaktualizowane do od czasu do czasu odzwierciedlenia nowego schematu. W rezultacie można zauważyć nowe atrybuty, które pojawią się w manifeście aplikacji. W rzadkich przypadkach można zauważyć zmiany składni lub semantyki w istniejących atrybutów lub może się okazać, atrybut, który występował wcześniej nie są już obsługiwane. Na przykład, zostanie wyświetlony nowych atrybutów w [rejestracje aplikacji (wersja zapoznawcza)](https://developer.microsoft.com/graph/blogs/new-app-registration/) których są znane pod inną nazwą, środowisko rejestracji (GA) aplikacji.


| Rejestracje aplikacji (GA)    | Rejestracje aplikacji (wersja zapoznawcza) |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Aby uzyskać opis tych atrybutów, zobacz [manifestu odwołanie](#manifest-reference) sekcji.

Podczas próby załadowania pobrane wcześniej manifestu, może zostać wyświetlony jeden z następujących błędów. Jest to prawdopodobnie, ponieważ edytor manifestu obsługuje teraz nowszą wersję schematu, który nie jest zgodna z ten, który próbujesz przekazać.

- "**Nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Nieprawidłowy identyfikator obiektu "undefined". [].** "
- "**Nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Określono co najmniej jednej wartości właściwości są nieprawidłowe. [].** "
- "**Nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Nie można ustawić availableToOtherTenants w tej wersji interfejsu api dla aktualizacji. [].** "
- "**Nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Aktualizacje do właściwości "adresów URL odpowiedzi" nie jest dozwolone dla tej aplikacji. Zamiast tego użyj właściwości "replyUrlsWithType". [].** "
- "**Nie można zaktualizować xxxxxx aplikacji. Szczegóły błędu: Znaleziono wartość bez nazwy typu, a nie oczekiwany typ jest dostępny. Jeśli model jest określony, każda wartość w ładunku musi mieć typ, który może zostać określony w ładunku, jawnie przez obiekt wywołujący albo niejawnie wnioskowany z wartości nadrzędnego. []**"

Gdy zostanie wyświetlony jeden z tych błędów, zalecamy wykonanie poniższych czynności:

1. Edytuj atrybuty indywidualnie w edytorze manifestu zamiast przekazywania pobrane wcześniej manifestu. Użyj [manifestu odwołanie](#manifest-reference) tabeli opisano składnia i semantyka starych i nowych atrybutów, dzięki czemu można pomyślnie edytować atrybuty interesuje Cię. 
1. Jeśli przepływ pracy wymaga zapisania manifesty w repozytorium źródłowym do użycia w przyszłości, sugerujemy zmienianie bazy zapisane manifesty w repozytorium każdy z nich zostanie wyświetlony w **rejestracje aplikacji (wersja zapoznawcza)** środowiska.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji dotyczących relacji między aplikacją i obiekty nazwy głównej usługi aplikacji, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure AD](app-objects-and-service-principals.md).
* Zobacz [słownik deweloperów platforma tożsamości firmy Microsoft](developer-glossary.md) uzyskać definicje niektórych podstawowe pojęcia dla deweloperów platformy tożsamości firmy Microsoft.

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
