---
title: Typy aplikacji obsługiwane przez usługę Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o typach aplikacji, których można używać w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e8328db12bde531c2e27936c09247611ff1a3583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190147"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikacji, które mogą być używane w usłudze Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji. Wszystkie one są oparte na standardowych protokołach branżowych [OAuth 2.0](protocols-overview.md) lub [OpenID Connect](protocols-overview.md). W tym artykule opisano typy aplikacji, które można tworzyć, niezależnie od preferowanego języka lub platformy. Ułatwia on także zrozumienie ogólnych scenariuszy przed rozpoczęciem tworzenia aplikacji.

Każda aplikacja korzystająca z usługi Azure AD B2C musi być zarejestrowana w [dzierżawie usługi Azure AD B2C](tutorial-create-tenant.md) przy użyciu [portalu Azure.](https://portal.azure.com/) Proces rejestracji aplikacji zbiera i przypisuje wartości, takie jak:

* **Identyfikator aplikacji,** który jednoznacznie identyfikuje aplikację.
* **Adres URL odpowiedzi,** który może służyć do bezpośredniego odpowiedzi z powrotem do aplikacji.

Każde żądanie wysyłane do usługi Azure AD B2C określa **przepływ użytkownika** (wbudowane zasady) lub **zasady niestandardowe,** które sterują zachowaniem usługi Azure AD B2C. Oba typy zasad umożliwiają tworzenie wysoce konfigurowalny zestaw środowisk użytkownika.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja kieruje użytkownika do punktu końcowego w wersji 2.0 w celu wykonania [zasad](user-flow-overview.md).
2. Użytkownik wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja odbiera token zabezpieczający z punktu końcowego w wersji 2.0.
4. Aplikacja używa tokenu zabezpieczającego, aby uzyskać dostęp do chronionych informacji lub chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

Te kroki mogą się nieznacznie różnić w zależności od typu aplikacji, którą budujesz.

## <a name="web-applications"></a>Aplikacje internetowe

W przypadku aplikacji sieci web (w tym .NET, PHP, Java, Ruby, Python i Node.js), które są hostowane na serwerze i dostępne za pośrednictwem przeglądarki, usługa Azure AD B2C obsługuje [funkcję OpenID Connect](protocols-overview.md) dla wszystkich środowisk użytkownika. W implementacji usługi Azure AD B2C usługi OpenID Connect aplikacja sieci web inicjuje środowisko użytkownika, wystawiając żądania uwierzytelniania do usługi Azure AD. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający reprezentuje tożsamość użytkownika. Zawiera także informacje o użytkowniku w formie oświadczeń:

```json
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

Dowiedz się więcej o typach tokenów i oświadczeń dostępnych dla aplikacji w [odwołaniu do tokenu usługi Azure AD B2C.](tokens-overview.md)

W aplikacji sieci web każde wykonanie [zasad](user-flow-overview.md) wykonuje następujące kroki wysokiego poziomu:

1. Użytkownik przegląda do aplikacji sieci web.
2. Aplikacja sieci web przekierowuje użytkownika do usługi Azure AD B2C wskazując zasady do wykonania.
3. Użytkownik wypełnia zasady.
4. Usługa Azure AD B2C zwraca `id_token` do przeglądarki.
5. Jest `id_token` księgowy do identyfikatora URI przekierowania.
6. Jest `id_token` sprawdzany i plik cookie sesji jest ustawiony.
7. Bezpieczna strona jest zwracana do użytkownika.

Sprawdzenie tokenu `id_token` przy użyciu publicznego klucza podpisywania otrzymanego z usługi Azure AD jest wystarczające do zweryfikowania tożsamości użytkownika. Ten proces ustawia również plik cookie sesji, który może służyć do identyfikowania użytkownika w kolejnych żądaniach strony.

Aby zobaczyć ten scenariusz w akcji, wypróbuj jeden z przykładów kodu logowania aplikacji sieci web w sekcji [Wprowadzenie.](overview.md)

Oprócz ułatwienia prostego logowania aplikacja serwera sieci web może również wymagać dostępu do usługi sieci web zaplecza. W takim przypadku aplikacja sieci web może wykonać nieco inny [przepływ OpenID Connect](openid-connect.md) i uzyskać tokeny przy użyciu kodów autoryzacji i tokenów odświeżania. Ten scenariusz jest opisany w [sekcji dotyczącej interfejsów API sieci Web](#web-apis) poniżej.

## <a name="web-apis"></a>Interfejsy API sieci Web

Za pomocą usługi Azure AD B2C można zabezpieczyć usługi sieci web, takie jak interfejs API sieci Web restful aplikacji. Interfejsy API sieci Web mogą zabezpieczać swoje dane za pomocą protokołu OAuth 2.0, uwierzytelniając żądania przychodzące HTTP przy użyciu tokenów. Element wywołujący interfejs API sieci Web dołącza token w nagłówku autoryzacji żądania HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Interfejs API sieci Web może następnie użyć tego tokenu do zweryfikowania tożsamości elementu wywołującego interfejs API oraz do wyodrębnienia informacji o elemencie wywołującym z oświadczeń zakodowanych w tokenie. Aby uzyskać więcej informacji o typach tokenów i oświadczeń dostępnych dla aplikacji, zobacz [informacje o tokenach usługi Azure AD B2C](tokens-overview.md).

Internetowy interfejs API może odbierać tokeny od wielu typów klientów, w tym aplikacji sieci web, aplikacji komputerowych i mobilnych, aplikacji jednostronicowych, demonów po stronie serwera i innych interfejsów API sieci Web. Oto przykład pełnego przepływu dla aplikacji sieci web, która wywołuje internetowy interfejs API:

1. Aplikacja sieci web wykonuje zasady, a użytkownik kończy środowisko użytkownika.
2. Usługa Azure AD B2C zwraca `id_token` (OpenID Connect) i kod autoryzacji do przeglądarki.
3. Przeglądarka publikuje `id_token` kod autoryzacji i do identyfikatora URI przekierowania.
4. Serwer sieci web `id_token` sprawdza poprawność i ustawia plik cookie sesji.
5. Serwer sieci web prosi usługę Azure `access_token` AD B2C o podanie kodu autoryzacji, identyfikatora klienta aplikacji i poświadczeń klienta.
6. I `access_token` `refresh_token` są zwracane do serwera sieci web.
7. Internetowy interfejs API jest `access_token` wywoływany z nagłówkiem autoryzacji.
8. Internetowy interfejs API sprawdza poprawność tokenu.
9. Bezpieczne dane są zwracane do aplikacji sieci web.

Aby dowiedzieć się więcej o kodach autoryzacji, tokenach odświeżania i krokach uzyskiwania tokenów, zapoznaj się z informacjami na temat [protokołu OAuth 2.0](authorization-code-flow.md).

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu usługi Azure AD B2C, zapoznaj się z samouczkami dotyczącymi interfejsu API sieci Web w [sekcji Wprowadzenie](overview.md).

## <a name="mobile-and-native-applications"></a>Aplikacje mobilne i natywne

Aplikacje zainstalowane na urządzeniach, takich jak aplikacje mobilne i klasyczne, często wymagają dostępu do usług zaplecza lub internetowych interfejsów API w imieniu użytkowników. Niestandardowe środowiska zarządzania tożsamościami można dodać do aplikacji natywnych i bezpiecznie wywoływać usługi zaplecza przy użyciu usługi Azure AD B2C i [przepływu kodu autoryzacji OAuth 2.0.](authorization-code-flow.md)

W tym przepływie aplikacja [policies](user-flow-overview.md) wykonuje zasady `authorization_code` i odbiera z usługi Azure AD po zakończeniu zasad przez użytkownika. Reprezentuje `authorization_code` uprawnienie aplikacji do wywoływania usług zaplecza w imieniu użytkownika, który jest aktualnie zalogowany. Aplikacja może następnie `authorization_code` wymienić w `access_token` tle `refresh_token`dla i .  Aplikacja może używać `access_token` do uwierzytelniania do interfejsu API sieci web zaplecza w żądaniach HTTP. Może również użyć tokenu `refresh_token` do pobrania nowego tokenu `access_token`, gdy wygaśnie stary.

## <a name="current-limitations"></a>Bieżące ograniczenia

### <a name="unsupported-application-types"></a>Nieobsługiwane typy aplikacji

#### <a name="daemonsserver-side-applications"></a>Demony/aplikacje po stronie serwera

Aplikacje, które zawierają długotrwałe procesy lub które działają bez obecności użytkownika, również potrzebują sposobu dostępu do zabezpieczonych zasobów, takich jak internetowe interfejsy API. Te aplikacje mogą uwierzytelniać i uzyskać tokeny przy użyciu tożsamości aplikacji (a nie tożsamości delegowanej użytkownika) i przy użyciu przepływu poświadczeń klienta OAuth 2.0. Przepływ poświadczeń klienta nie jest taki sam jak przepływ w imieniu i przepływ w imieniu nie powinien być używany do uwierzytelniania między serwerami.

Mimo że przepływ poświadczeń klienta nie jest obecnie obsługiwany przez usługę Azure AD B2C, można skonfigurować przepływ poświadczeń klienta przy użyciu usługi Azure AD. Dzierżawa usługi Azure AD B2C udostępnia niektóre funkcje z dzierżawcami przedsiębiorstwa usługi Azure AD.  Przepływ poświadczeń klienta jest obsługiwany przy użyciu funkcji usługi Azure AD dzierżawy usługi Azure AD B2C.

Aby skonfigurować przepływ poświadczeń klienta, zobacz [Usługa Azure Active Directory w wersji 2.0 i przepływ poświadczeń klienta OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Pomyślne uwierzytelnianie powoduje otrzymanie tokenu sformatowanego, dzięki czemu może być używany przez usługę Azure AD zgodnie z opisem w [odwołaniu do tokenu usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

#### <a name="web-api-chains-on-behalf-of-flow"></a>Łańcuchy interfejsu API sieci Web (przepływ „w imieniu”)

Wiele architektur obejmuje interfejs API sieci Web, który musi wywołać inny podrzędny interfejs API sieci Web, przy czym oba interfejsy są zabezpieczane przez usługę Azure AD B2C. Ten scenariusz jest typowy w klientach natywnych, którzy mają zaplecza interfejsu API sieci Web i wywołuje usługę online firmy Microsoft, takich jak microsoft graph interfejsu API.

Ten scenariusz obejmujący łańcuch interfejsów API sieci Web może być obsługiwany przy użyciu przyznania poświadczeń elementu nośnego OAuth 2.0 JWT, określanego również jako przepływ „w imieniu”.  Jednak przepływ „w imieniu” nie jest obecnie wdrażany w usłudze Azure AD B2C.

### <a name="faulted-apps"></a>Uszkodzone aplikacje

Nie należy edytować aplikacji usługi Azure AD B2C w ten sposób:

- W innych portalach zarządzania aplikacjami, takich jak [Portal rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Korzystanie z interfejsu API programu Graph lub programu PowerShell.

Jeśli edytujesz aplikację usługi Azure AD B2C poza portalem Azure, stanie się ona aplikacją błędną i nie będzie już uszać z usługą Azure AD B2C. Usuń aplikację i utwórz ją ponownie.

Aby usunąć aplikację, przejdź do [portalu rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i usuń aplikację. Aby aplikacja była widoczna, musisz być jej właścicielem (a nie tylko administratorem dzierżawy).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wbudowanych zasadach dostarczanych przez [przepływy użytkowników w usłudze Azure Active Directory B2C](user-flow-overview.md).
