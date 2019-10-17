---
title: Informacje na temat manifestu aplikacji Azure Active Directory | Microsoft Docs
description: Szczegółowy zakres manifestu aplikacji Azure Active Directory, który reprezentuje konfigurację tożsamości aplikacji w dzierżawie usługi Azure AD i służy do ułatwienia autoryzacji uwierzytelniania OAuth, środowiska zgody i nie tylko.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: e44c3e607f3d5a5ea8269b9885a4fec54000bb5f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389585"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory manifest aplikacji

Manifest aplikacji zawiera definicję wszystkich atrybutów obiektu aplikacji na platformie tożsamości firmy Microsoft. Służy również jako mechanizm aktualizowania obiektu aplikacji. Aby uzyskać więcej informacji na temat jednostki aplikacji i jej schematu, zobacz [dokumentację jednostki aplikacji interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Atrybuty aplikacji można skonfigurować za pomocą Azure Portal lub programowo przy użyciu [interfejsu API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) lub [programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Istnieje jednak kilka scenariuszy, w których należy edytować manifest aplikacji w celu skonfigurowania atrybutu aplikacji. Scenariusze obejmują:

* Jeśli aplikacja została zarejestrowana jako usługa Azure AD z wieloma dzierżawcami i osobistymi kontami Microsoft, nie można zmienić obsługiwanych kont Microsoft w interfejsie użytkownika. Zamiast tego należy użyć edytora manifestu aplikacji, aby zmienić obsługiwany typ konta.
* Jeśli konieczne jest zdefiniowanie uprawnień i ról obsługiwanych przez aplikację, należy zmodyfikować manifest aplikacji.

## <a name="configure-the-app-manifest"></a>Konfigurowanie manifestu aplikacji

Aby skonfigurować manifest aplikacji:

1. Zaloguj się [Azure Portal](https://portal.azure.com).
1. Wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, którą chcesz skonfigurować.
1. Na stronie **Przegląd** aplikacji wybierz sekcję **Manifest**. Zostanie otwarty Edytor manifestu oparty na sieci Web, który umożliwia edytowanie manifestu w portalu. Opcjonalnie możesz wybrać pozycję **Pobierz** , aby edytować manifest lokalnie, a następnie użyć opcji **Przekaż** , aby ponownie zastosować ją do aplikacji.

## <a name="manifest-reference"></a>Odwołanie do manifestu

> [!NOTE]
> Jeśli nie widzisz kolumny **przykładowej wartości** po **opisie**, zmaksymalizuj okno przeglądarki i przewiń/przesuń do momentu wyświetlenia kolumny **przykładowej wartości** .

| Klucz  | Typ wartości | Opis  | Przykładowa wartość |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Dopuszczający wartość null | Określa wersję tokenu dostępu oczekiwaną przez zasób. Spowoduje to zmianę wersji i formatu JWT generowanej niezależnie od punktu końcowego lub klienta użytego do żądania tokenu dostępu.<br/><br/>Używany punkt końcowy, wersja 1.0 lub 2.0, jest wybierany przez klienta i ma wpływ tylko na wersję id_tokens. Zasoby muszą jawnie skonfigurować `accesstokenAcceptedVersion`, aby wskazać obsługiwany format tokenu dostępu.<br/><br/>Możliwe wartości `accesstokenAcceptedVersion` to 1, 2 lub null. Jeśli wartość jest równa null, domyślnie jest to 1, która odpowiada punktowi końcowemu v 1.0. <br/><br/>Jeśli `signInAudience` jest `AzureADandPersonalMicrosoftAccount`, wartość musi być `2`  | `2` |
| `addIns` | Collection | Definiuje niestandardowe zachowanie używane przez usługę do wywoływania aplikacji w określonych kontekstach. Na przykład aplikacje, które mogą renderować strumienie plików, mogą ustawiać właściwość addIns dla swojej funkcji "FileHandler". Dzięki temu usługi, takie jak pakiet Office 365, będą wywoływały aplikację w kontekście dokumentu, nad którym pracuje użytkownik. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Wartość logiczna | Określa typ aplikacji rezerwowej. Usługa Azure AD domyślnie wnioskuje typ aplikacji z replyUrlsWithType. Istnieją pewne scenariusze, w których usługa Azure AD nie może określić typu aplikacji klienta (np. przepływu [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) , gdzie żądanie HTTP odbywa się bez przekierowania adresu URL). W tych przypadkach usługa Azure AD interpretuje typ aplikacji na podstawie wartości tej właściwości. Jeśli ta wartość jest równa true, typ aplikacji rezerwowej jest ustawiany jako klient publiczny, taki jak zainstalowana aplikacja uruchomiona na urządzeniu przenośnym. Wartość domyślna to false, co oznacza, że rezerwowy typ aplikacji to poufny klient, taki jak aplikacja sieci Web. | `false` |
| `availableToOtherTenants` | Wartość logiczna | ma wartość true, jeśli aplikacja jest udostępniana innym dzierżawcom; w przeciwnym razie false. <br><br> _Uwaga: jest to możliwe tylko w Rejestracje aplikacji (starsza wersja) środowiska. Zastąpione `signInAudience` w środowisku [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `appId` | Ciąg | Określa unikatowy identyfikator aplikacji przypisanej do aplikacji przez usługę Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Collection | Określa kolekcję ról, które może deklarować aplikacja. Role te można przypisać do użytkowników, grup lub podmiotów usługi. Aby uzyskać więcej przykładów i informacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Ciąg | Nazwa wyświetlana aplikacji. <br><br> _Uwaga: jest to możliwe tylko w Rejestracje aplikacji (starsza wersja) środowiska. Zastąpione `name` w środowisku [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"MyRegisteredApp"` |
| `errorUrl` | Ciąg | Ich. | |
| `groupMembershipClaims` | Ciąg | Konfiguruje dla `groups` roszczeń wystawionych w tokenie dostępu użytkownika lub OAuth 2,0, którego oczekuje aplikacja. Aby ustawić ten atrybut, należy użyć jednej z następujących prawidłowych wartości ciągu:<br/><br/>- `"None"`<br/>-  @ no__t-1 (dla grup zabezpieczeń i ról usługi Azure AD)<br/>-  @ no__t-1 (spowoduje to wyświetlenie wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról katalogu usługi Azure AD, do których należy użytkownik zalogowany). | `"SecurityGroup"` |
| `homepage` | Ciąg | Adres URL strony głównej aplikacji. <br><br> _Uwaga: jest to możliwe tylko w Rejestracje aplikacji (starsza wersja) środowiska. Zastąpione `signInUrl` w środowisku [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"https://MyRegisteredApp"` |
| `objectId` | Ciąg | Unikatowy identyfikator aplikacji w katalogu. <br><br> _Uwaga: jest to możliwe tylko w Rejestracje aplikacji (starsza wersja) środowiska. Zastąpione `id` w środowisku [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Ciąg | Opcjonalne oświadczenia zwracane w tokenie przez usługę tokenu zabezpieczającego dla tej konkretnej aplikacji.<br>W tej chwili aplikacje obsługujące zarówno konta osobiste, jak i usługi Azure AD (zarejestrowane za pomocą portalu rejestracji aplikacji) nie mogą używać oświadczeń opcjonalnych. Jednak aplikacje zarejestrowane dla tylko usługi Azure AD przy użyciu punktu końcowego v 2.0 mogą uzyskać opcjonalne oświadczenia, które zażądały w manifeście. Aby uzyskać więcej informacji, zobacz [opcjonalne oświadczenia](active-directory-optional-claims.md). | `null` |
| `id` | Ciąg | Unikatowy identyfikator aplikacji w katalogu. Ten identyfikator nie jest identyfikatorem używanym do identyfikowania aplikacji w żadnej transakcji protokołu. Służy do odwoływania się do obiektu w zapytaniach w katalogu. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Tablica ciągów | Zdefiniowane przez użytkownika identyfikatory URI, które w unikatowy sposób identyfikują aplikację sieci Web w ramach dzierżawy usługi Azure AD lub w ramach zweryfikowanej domeny niestandardowej, jeśli aplikacja ma wiele dzierżawców. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Ciąg | Określa linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji. Warunki użytkowania usługi i zasady zachowania poufności informacji są udostępniane użytkownikom za pomocą funkcji wyrażania zgody użytkownika. Aby uzyskać więcej informacji, zobacz [jak: Dodawanie warunków użytkowania i zasad zachowania poufności informacji dla zarejestrowanych aplikacji usługi Azure AD](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Collection | Zawiera odwołania do poświadczeń przypisanych do aplikacji, wspólnych haseł opartych na ciągach oraz certyfikatów X. 509. Te poświadczenia są używane podczas żądania tokenów dostępu (gdy aplikacja działa jako klient, a nie jako zasób). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Tablica ciągów | Używane do grupowania wyrazów, jeśli masz rozwiązanie, które zawiera dwie części: aplikację kliencką i niestandardową aplikację interfejsu API sieci Web. Jeśli wprowadzisz identyfikator appID aplikacji klienckiej do tej wartości, użytkownik będzie musiał tylko raz wyrazić zgodę na aplikację kliencką. Usługa Azure AD będzie wiedzieć, że zgoda na klienta oznacza niejawne przesłanie do internetowego interfejsu API i automatycznie udostępnia jednostki usługi zarówno dla klienta, jak i interfejsu API sieci Web. Zarówno klient, jak i aplikacja internetowego interfejsu API muszą być zarejestrowani w tej samej dzierżawie. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Ciąg | Wartość tylko do odczytu wskazująca adres URL usługi CDN na logo, które zostało przekazane w portalu. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Ciąg | Adres URL do wylogowania z aplikacji. | `"https://MyRegisteredAppLogout"` |
| `name` | Ciąg | Nazwa wyświetlana aplikacji. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Wartość logiczna | Określa, czy ta aplikacja sieci Web może żądać niejawnych tokenów dostępu protokołu OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana w przypadku aplikacji opartych na przeglądarce, takich jak aplikacje jednostronicowe języka JavaScript. Aby dowiedzieć się więcej, wprowadź `OAuth 2.0 implicit grant flow` w spisie treści i zapoznaj się z tematami dotyczącymi niejawnego przepływu. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Wartość logiczna | Określa, czy ta aplikacja sieci Web może żądać niejawnych tokenów identyfikatora przepływu OAuth 2.0. Wartość domyślna to false. Ta flaga jest używana w przypadku aplikacji opartych na przeglądarce, takich jak aplikacje jednostronicowe języka JavaScript. | `false` |
| `oauth2Permissions` | Collection | Określa kolekcję zakresów uprawnień OAuth 2,0, które aplikacja Web API (zasób) uwidacznia dla aplikacji klienckich. Te zakresy uprawnień mogą być przyznawane aplikacjom klienckim podczas wyrażania zgody. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Wartość logiczna | Określa, czy w ramach żądania tokenu OAuth 2,0 usługa Azure AD będzie zezwalać na żądania POST, w przeciwieństwie do żądań GET. Wartość domyślna to false, co oznacza, że dozwolone są tylko żądania GET. | `false` |
| `parentalControlSettings` | Ciąg | `countriesBlockedForMinors` określa kraje, w których aplikacja jest blokowana dla małoletnich.<br>`legalAgeGroupRule` Określa regułę grupy wieku prawnego, która ma zastosowanie do użytkowników aplikacji. Można ustawić na `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` lub `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Collection | Zobacz opis właściwości `keyCredentials`. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Collection | Wyświetla listę aplikacji i żądanych uprawnień dla niejawnej zgody. Wymaga, aby administrator wyraził zgodę na aplikację. preAuthorizedApplications nie wymaga, aby użytkownik wyrażał zgodę na żądane uprawnienia. Uprawnienia wymienione w preAuthorizedApplications nie wymagają zgody użytkownika. Jednak wszelkie dodatkowe żądane uprawnienia, które nie są wymienione w preAuthorizedApplications, wymagają zgody użytkownika. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Wartość logiczna | Określa, czy ta aplikacja jest klientem publicznym (na przykład zainstalowaną aplikacją uruchomioną na urządzeniu przenośnym). <br><br> _Uwaga: jest to możliwe tylko w Rejestracje aplikacji (starsza wersja) środowiska. Zastąpione `allowPublicClient` w środowisku [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `publisherDomain` | Ciąg | Zweryfikowana domena wydawcy dla aplikacji. Tylko do odczytu. | https://www.contoso.com |
| `replyUrls` | Tablica ciągów | Ta właściwość wielowartościowa zawiera listę zarejestrowanych wartości redirect_uri, które usługa Azure AD będzie akceptować jako miejsca docelowe podczas zwracania tokenów. <br><br> _Uwaga: jest to możliwe tylko w Rejestracje aplikacji (starsza wersja) środowiska. Zastąpione `replyUrlsWithType` w środowisku [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) ._ | |
| `replyUrlsWithType` | Collection | Ta właściwość wielowartościowa zawiera listę zarejestrowanych wartości redirect_uri, które usługa Azure AD będzie akceptować jako miejsca docelowe podczas zwracania tokenów. Każda wartość identyfikatora URI powinna zawierać skojarzoną wartość typu aplikacji. Obsługiwane wartości typu: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Dowiedz się więcej o [ograniczeniach replyUrl i ograniczeniach](https://docs.microsoft.com/azure/active-directory/develop/reply-url). | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Collection | W przypadku zgody na korzystanie z opcji "`requiredResourceAccess`" są obsługiwane w przypadku użytkowników, którzy korzystają z zgody statycznej. Nie dotyczy to jednak środowiska zgody użytkownika na ogólny przypadek.<br>`resourceAppId` jest unikatowym identyfikatorem zasobu, do którego aplikacja wymaga dostępu. Ta wartość powinna być taka sama jak identyfikator appId zadeklarowany w docelowej aplikacji zasobów.<br>`resourceAccess` jest tablicą zawierającą zakresy uprawnień OAuth 2.0 i role aplikacji wymagane przez aplikację od określonego zasobu. Zawiera wartości `id` i `type` dla określonych zasobów. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | Ciąg | Adres URL metadanych SAML dla aplikacji. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Ciąg | Określa adres URL strony głównej aplikacji. | `https://MyRegisteredApp` |
| `signInAudience` | Ciąg | Określa, jakie konta Microsoft są obsługiwane przez bieżącą aplikację. Obsługiwane są następujące wartości:<ul><li>**AzureADMyOrg** — użytkownicy z kontem służbowym firmy Microsoft w dzierżawie usługi Azure AD w organizacji (tj. pojedynczym dzierżawcą)</li><li>**AzureADMultipleOrgs** — użytkownicy z kontem służbowym firmy Microsoft w ramach dzierżawy usługi Azure AD w organizacji (tj. wielu dzierżawców)</li> <li>**AzureADandPersonalMicrosoftAccount** — użytkownicy z konto Microsoft osobistym lub kontem służbowym w ramach dzierżawy usługi Azure AD w organizacji</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Tablica ciągów | Niestandardowe ciągi, których można użyć do kategoryzowania i identyfikowania aplikacji. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Typowe problemy

### <a name="manifest-limits"></a>Limity manifestu

Manifest aplikacji ma wiele atrybutów, które są określane jako kolekcje; na przykład approles, poświadczenia, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess i oauth2Permissions. W ramach kompletnego manifestu aplikacji dla dowolnej aplikacji łączna liczba wpisów we wszystkich łączonych kolekcjach została ograniczona o 1200. Jeśli masz już 100 identyfikatorów URI przekierowania określonych w manifeście aplikacji, pozostało tylko z 1100 pozostałymi wpisami do użycia we wszystkich innych kolekcjach, które składają się na manifest.

> [!NOTE]
> W przypadku próby dodania więcej niż 1200 wpisów w manifeście aplikacji może zostać wyświetlony komunikat o błędzie **"nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: rozmiar manifestu przekroczył swój limit. Zmniejsz liczbę wartości i ponów próbę wykonania żądania ".**

### <a name="unsupported-attributes"></a>Nieobsługiwane atrybuty

Manifest aplikacji reprezentuje schemat modelu bazowego aplikacji w usłudze Azure AD. W miarę rozwoju podstawowego schematu Edytor manifestu zostanie zaktualizowany w celu odzwierciedlenia nowego schematu od czasu do czasu. W związku z tym można zauważyć, że nowe atrybuty pojawiają się w manifeście aplikacji. W rzadkich przypadkach może być zauważalna zmiana składni lub semantyki w istniejących atrybutach lub znalezienie wcześniej nieobsługiwanego atrybutu. Na przykład zobaczysz nowe atrybuty w [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) , które są znane z inną nazwą w środowisku rejestracje aplikacji (starsza wersja).


| Rejestracje aplikacji (starsza wersja)| Rejestracje aplikacji           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Opisy tych atrybutów znajdują się w sekcji [Dokumentacja manifestu](#manifest-reference) .

Podczas próby przekazania wcześniej pobranego manifestu może zostać wyświetlony jeden z następujących błędów. Prawdopodobnie jest to spowodowane tym, że Edytor manifestu obsługuje teraz nowszą wersję schematu, która nie jest zgodna z tą, którą próbujesz przekazać.

- "**Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: nieprawidłowy identyfikator obiektu "undefined". [].** "
- "**Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: co najmniej jedna podana wartość właściwości jest nieprawidłowa. [].** "
- "**Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: nie można ustawić availableToOtherTenants w tej wersji interfejsu API na potrzeby aktualizacji. [].** "
- "**Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: aktualizacje właściwości "replyUrls" są niedozwolone w przypadku tej aplikacji. Zamiast tego użyj właściwości "replyUrlsWithType". [].** "
- "**Nie można zaktualizować aplikacji xxxxxx. Szczegóły błędu: nie znaleziono wartości bez nazwy typu i nie jest dostępny żaden oczekiwany typ. Gdy model jest określony, każda wartość w ładunku musi mieć typ, który może być określony w ładunku, jawnie przez obiekt wywołujący lub niejawnie wywnioskowany na podstawie wartości nadrzędnej. []** "

Gdy zobaczysz jeden z tych błędów, zalecamy wykonanie następujących czynności:

1. Edytuj atrybuty osobno w edytorze manifestu zamiast przekazywania wcześniej pobranego manifestu. Użyj tabeli [referencyjnej manifestu](#manifest-reference) , aby zrozumieć składnię i semantykę starych i nowych atrybutów, dzięki czemu możesz pomyślnie edytować interesujące Cię atrybuty. 
1. Jeśli przepływ pracy wymaga zapisania manifestów w repozytorium źródłowym na potrzeby późniejszego użycia, sugerujemy oparcie zapisanych manifestów w repozytorium z tym, co widzisz w **rejestracje aplikacji** środowisku.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat relacji między aplikacją aplikacji a obiektami głównymi usługi, zobacz temat [obiekty główne aplikacji i usługi w usłudze Azure AD](app-objects-and-service-principals.md).
* Zapoznaj się z [słownikiem dla deweloperów Microsoft Identity platform](developer-glossary.md) , aby zapoznać się z definicjami niektórych podstawowych koncepcji deweloperów platformy tożsamości firmy Microsoft.

Skorzystaj z następującej sekcji komentarzy, aby przekazać Opinie, które ułatwiają udoskonalenie i kształtowanie naszej zawartości.

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
