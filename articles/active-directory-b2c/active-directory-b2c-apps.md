---
title: Typy aplikacji, które mogą być używane w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat typów aplikacji, których można używać w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae2894f9f442bca1e6029b7e7d8e07824abf7fb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051728"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikacji, które mogą być używane w Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C obsługuje uwierzytelnianie w różnych architektur nowoczesnych aplikacji. Wszystkie one są oparte na standardowych protokołach branżowych [OAuth 2.0](active-directory-b2c-reference-protocols.md) lub [OpenID Connect](active-directory-b2c-reference-protocols.md). W tym dokumencie opisano typy aplikacji, które można tworzyć, niezależnie od języka lub platformy wolisz. Ułatwia on także zrozumienie ogólnych scenariuszy przed rozpoczęciem tworzenia aplikacji.

Każda aplikacja, która używa usługi Azure AD B2C musi być zarejestrowana w Twojej [dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md) przy użyciu [witryny Azure portal](https://portal.azure.com/). Proces rejestracji aplikacji służy do zbierania i przypisuje wartości, takich jak:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację.
* A **adres URL odpowiedzi** który może służyć do kierowania odpowiedzi z powrotem do aplikacji.

Każde żądanie, które są wysyłane do usługi Azure AD B2C Określa **przepływ użytkownika**, czyli zasady, które kontrolują zachowanie usługi Azure AD. Można również użyć tych punktów końcowych do utworzenia zestawu funkcji środowiska użytkownika, które można w znacznym stopniu dostosowywać. Firma Microsoft zapewnia zestaw przepływy użytkownika ułatwiające konfigurowanie typowych zasad, w tym tworzenia nowych kont i logowanie i edycję profilu. Ale można również utworzyć własne zasady niestandardowe. Jeśli nie znasz tych zasad, przed wykonaniem dalszych czynności zapoznaj się z informacjami na temat [rozszerzonej platformy zasad](active-directory-b2c-reference-policies.md) usługi Azure AD B2C.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja przekierowuje użytkownika do punktu końcowego v2.0 do wykonania [zasad](active-directory-b2c-reference-policies.md).
2. Użytkownik wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera token zabezpieczający z punktu końcowego v2.0.
4. Aplikacja używa tokenu zabezpieczającego uzyskać dostęp do chronionych informacji lub chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

Te kroki mogą różnić się nieco w zależności od typu aplikacji, które tworzysz.

## <a name="web-applications"></a>Aplikacje internetowe

Dla aplikacji sieci web (w tym .NET, PHP, Java, Ruby, Python i Node.js), które są hostowane na serwerze i dostępne za pośrednictwem przeglądarki, usługa Azure AD B2C obsługuje [OpenID Connect](active-directory-b2c-reference-protocols.md) dla użytkownika wszystkich środowisk. W implementacji usługi Azure AD B2C protokołu OpenID Connect aplikacji sieci web inicjuje środowisk pracy użytkownika, wysyłając żądania uwierzytelniania do usługi Azure AD. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający reprezentuje tożsamość użytkownika. Zawiera także informacje o użytkowniku w formie oświadczeń:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Dowiedz się więcej o typach tokenów i oświadczeń dostępnych dla aplikacji w [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

W aplikacji sieci web Każde wykonanie [zasad](active-directory-b2c-reference-policies.md) obejmuje następujące ogólne kroki:

1. Użytkownik przejdzie do aplikacji sieci web.
2. Aplikacja sieci web przekierowuje użytkownika do usługi Azure AD B2C wskazująca zasady wykonywania.
3. Użytkownik kończy zasad.
4. Usługa Azure AD B2C zwraca `id_token` do przeglądarki.
5. `id_token` Opublikowaniu identyfikator URI przekierowania.
6. `id_token` Jest weryfikowana, a plik cookie sesji jest ustawiona.
7. Strona bezpiecznych, jest zwracana do użytkownika.

Sprawdzenie tokenu `id_token` przy użyciu publicznego klucza podpisywania otrzymanego z usługi Azure AD jest wystarczające do zweryfikowania tożsamości użytkownika. Ten proces powoduje również ustawienie pliku cookie sesji, który może służyć do identyfikowania użytkownika w odpowiedzi na żądania następną stronę.

Aby wyświetlić tym scenariuszem w praktyce, wypróbuj jedną z próbek kodu logowania aplikacji sieci web w naszym [sekcji wprowadzenie](active-directory-b2c-overview.md).

Oprócz ułatwienia prostego logowania, aplikacja serwera sieci web może być konieczne również dostępu do usługi sieci web zaplecza. W takim przypadku aplikacja sieci web może wykonać nieco inny [przepływ protokołu OpenID Connect](active-directory-b2c-reference-oidc.md) i uzyskać tokeny przy użyciu kodów autoryzacji i tokenów odświeżania. Ten scenariusz jest opisany w [sekcji dotyczącej interfejsów API sieci Web](#web-apis) poniżej.

## <a name="web-apis"></a>Interfejsy API sieci Web

Za pomocą usługi Azure AD B2C do zabezpieczania usług sieci web, takie jak internetowy interfejs API RESTful aplikacji. Interfejsy API sieci Web mogą zabezpieczać swoje dane za pomocą protokołu OAuth 2.0, uwierzytelniając żądania przychodzące HTTP przy użyciu tokenów. Element wywołujący interfejs API sieci Web dołącza token w nagłówku autoryzacji żądania HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Interfejs API sieci Web może następnie użyć tego tokenu do zweryfikowania tożsamości elementu wywołującego interfejs API oraz do wyodrębnienia informacji o elemencie wywołującym z oświadczeń zakodowanych w tokenie. Aby uzyskać więcej informacji o typach tokenów i oświadczeń dostępnych dla aplikacji, zobacz [informacje o tokenach usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

Interfejs API sieci web może odbierać tokeny od wielu typów klientów, w tym aplikacji sieci web, pulpitu i aplikacje mobilne, aplikacje jednej strony, demonów po stronie serwera i innych interfejsów API sieci web. Oto przykład pełnego przepływu aplikacji sieci web, która wywołuje interfejs API sieci web:

1. Aplikacja sieci web wykonuje zasady, a następnie użytkownik kończy środowisko użytkownika.
2. Usługa Azure AD B2C zwraca (OpenID Connect) `id_token` i kodu autoryzacji do przeglądarki.
3. Wpisy przeglądarki `id_token` i Kod autoryzacji do identyfikatora URI przekierowania.
4. Serwer sieci web sprawdza poprawność `id_token` i ustawia plik cookie sesji.
5. Pyta, serwer sieci web usługi Azure AD B2C dla `access_token` dostarczając kod autoryzacji, identyfikator klienta aplikacji i poświadczeń klienta.
6. `access_token` i `refresh_token` są zwracane do serwera sieci web.
7. Internetowy interfejs API jest wywoływana z `access_token` w nagłówku autoryzacji.
8. Interfejs API sieci web sprawdza poprawność tokenu.
9. Zabezpieczanie danych jest zwracana do aplikacji sieci web.

Aby dowiedzieć się więcej o kodach autoryzacji, tokenach odświeżania i krokach uzyskiwania tokenów, zapoznaj się z informacjami na temat [protokołu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu usługi Azure AD B2C, zapoznaj się z samouczkami dotyczącymi interfejsu API sieci Web w [sekcji Wprowadzenie](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobilnych i aplikacji natywnych

Aplikacje, które są zainstalowane na urządzeniach, takich jak w przypadku aplikacji mobilnych i komputerowych często muszą uzyskać dostęp do usług zaplecza lub interfejsów API sieci web w imieniu użytkowników. Możesz dodać niestandardowe środowiska zarządzania tożsamością do aplikacji natywnych i bezpiecznie wywoływać usługi zaplecza przy użyciu usługi Azure AD B2C i [przepływ kodu autoryzacji OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

W tym przepływie aplikacja wykonuje [zasady](active-directory-b2c-reference-policies.md) i odbiera `authorization_code` z usługi Azure AD po wypełnieniu zasad przez użytkownika. `authorization_code` Reprezentuje aplikacji uprawnień do wywoływania usług zaplecza w imieniu użytkownika, który jest aktualnie zalogowany. Aplikacja może następnie wymienić `authorization_code` w tle dla `access_token` i `refresh_token`.  Aplikacja może używać `access_token` do uwierzytelniania internetowego interfejsu API zaplecza w żądaniach HTTP. Może również użyć tokenu `refresh_token` do pobrania nowego tokenu `access_token`, gdy wygaśnie stary.

## <a name="current-limitations"></a>Bieżące ograniczenia

### <a name="application-not-supported"></a>Aplikacja nie jest obsługiwane 

#### <a name="daemonsserver-side-applications"></a>Demony/po stronie serwera aplikacji

Aplikacje, które zawierają procesy długotrwałe lub niewymagające obecności użytkownika również potrzebują sposobu dostępu do zabezpieczonych zasobów takich jak interfejsy API sieci web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji (zamiast delegowanej tożsamości użytkownika) i za pomocą klienta OAuth 2.0 przepływ poświadczeń. Przepływ poświadczeń klienta nie jest taka sama jak w imieniu użytkownika — przepływ i w imieniu użytkownika — przepływ nie powinny być używane do uwierzytelniania serwera do serwera.

Mimo że przepływ poświadczeń klienta nie jest obecnie obsługiwane przez usługę Azure AD B2C, możesz skonfigurować przepływ poświadczeń klienta przy użyciu usługi Azure AD. Dzierżawy usługi Azure AD B2C udostępni niektóre funkcje usługi Azure AD enterprise dzierżawcy.  Przepływ poświadczeń klienta jest obsługiwana, korzystając z funkcji usługi Azure AD dla dzierżawy usługi Azure AD B2C. 

Aby skonfigurować przepływ poświadczeń klienta, zobacz [klienta OAuth 2.0 i usługi Azure Active Directory w wersji 2.0 credentials flow](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Po pomyślnym uwierzytelnieniu powoduje przyjęcie token sformatowane tak, aby może służyć przez usługę Azure AD zgodnie z opisem w [odwołania do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Łańcuchy interfejsu API sieci Web (przepływ „w imieniu”)

Wiele architektur obejmuje interfejs API sieci Web, który musi wywołać inny podrzędny interfejs API sieci Web, przy czym oba interfejsy są zabezpieczane przez usługę Azure AD B2C. Ten scenariusz jest często używany w klientach natywnych, którzy mają zaplecza interfejsu API sieci Web i wywołuje usługi online firmy Microsoft, takich jak interfejs API programu Graph usługi Azure AD.

Ten scenariusz obejmujący łańcuch interfejsów API sieci Web może być obsługiwany przy użyciu przyznania poświadczeń elementu nośnego OAuth 2.0 JWT, określanego również jako przepływ „w imieniu”.  Jednak przepływ „w imieniu” nie jest obecnie wdrażany w usłudze Azure AD B2C.

### <a name="faulted-apps"></a>Uszkodzone aplikacje

Nie należy edytować aplikacji usługi Azure AD B2C w następujący sposób:

- W innych portalach zarządzania aplikacjami, takie jak [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/).
- Korzystanie z interfejsu API programu Graph lub programu PowerShell.

Jeśli edytujesz aplikację usługi Azure AD B2C poza witryny Azure portal, stanie się uszkodzoną aplikacją i nie jest już możliwe za pomocą usługi Azure AD B2C. Usuń aplikację, a następnie utworzyć ją ponownie.

Aby usunąć aplikację, przejdź do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) i usuń ją tam. Aby aplikacja była widoczna, musisz być jej właścicielem (a nie tylko administratorem dzierżawy).

