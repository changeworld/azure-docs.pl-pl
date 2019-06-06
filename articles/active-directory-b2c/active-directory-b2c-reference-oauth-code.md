---
title: Przepływ kodu autoryzacji — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje sieci web przy użyciu protokołu uwierzytelniania usługi Azure AD B2C i OpenID Connect.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7157682d7952529f9dfa98e8bc8707df9cfe944f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509240"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Przepływ kodu autoryzacji OAuth 2.0 w usłudze Azure Active Directory B2C

Przyznawanie kodu autoryzacji OAuth 2.0 w aplikacje zainstalowane na urządzeniu umożliwia uzyskanie dostępu do chronionych zasobów, takich jak interfejsy API sieci web. Za pomocą usługi Azure Active Directory B2C (Azure AD B2C) implementacji protokołu OAuth 2.0, możesz dodawać rejestracji, logowania i zarządzania tożsamościami, inne zadania do aplikacji mobilnych i klasycznych. Ten artykuł jest niezależny od języka. W artykule opisano jak wysyłać i odbierać komunikaty HTTP bez użycia żadnych bibliotek typu open source.

Przepływ kodu autoryzacji OAuth 2.0 opisano w [sekcji 4.1 specyfikacji protokołu OAuth 2.0](https://tools.ietf.org/html/rfc6749). Służy do uwierzytelniania i autoryzacji w większości [typy aplikacji](active-directory-b2c-apps.md), w tym aplikacje sieci web i natywnie zainstalowanych aplikacji. Przepływ kodu autoryzacji OAuth 2.0 umożliwia bezpieczne uzyskiwanie tokenów dostępu i tokenów odświeżania dla Twoich aplikacji, które mogą być używane do dostępu do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-b2c-reference-protocols.md).  Token odświeżania umożliwia klienta do uzyskania nowego dostępu (i odświeżanie) tokeny po wygaśnięciu ważności tokenu dostępu, zwykle po godzinie.

Ten artykuł koncentruje się na **klientów publicznych** przepływ kodu autoryzacji OAuth 2.0. Publicznych klienta jest każda aplikacja kliencka, która nie jest zaufany, aby bezpiecznie zachować integralność klucza tajnego hasła. W tym aplikacje mobilne, aplikacje pulpitu i zasadniczo każda aplikacja, która jest uruchamiana na urządzeniu i należy uzyskiwanie tokenów dostępu. 

> [!NOTE]
> Aby dodać zarządzania tożsamościami do aplikacji sieci web za pomocą usługi Azure AD B2C, należy użyć [OpenID Connect](active-directory-b2c-reference-oidc.md) zamiast protokołu OAuth 2.0.

Usługa Azure AD B2C rozszerza tego standardu OAuth 2.0 są przekazywane do więcej niż proste uwierzytelnianie i autoryzacja. Wprowadza [parametr przepływ użytkownika](active-directory-b2c-reference-policies.md). Przy użyciu przepływów użytkownika można użyć protokołu OAuth 2.0 do dodania środowisk użytkowników do aplikacji, takich jak rejestracji, logowania i zarządzania profilami. Dostawcy tożsamości, które używają protokołu OAuth 2.0 obejmują [Amazon](active-directory-b2c-setup-amzn-app.md), [usługi Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [ Google](active-directory-b2c-setup-goog-app.md), i [LinkedIn](active-directory-b2c-setup-li-app.md).

W żądaniach przykład HTTP, w tym artykule, stosujemy nasze przykładowe katalogu usługi Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Możemy również użyć przepływów naszych przykładowych aplikacji i użytkownika. Możesz spróbować żądania samodzielnie za pomocą tych wartości, lub można je zastąpić własnymi wartościami.
Dowiedz się, jak [uzyskać własne przepływy katalogu, aplikacji i użytkownika usługi Azure AD B2C](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Pobieranie kodu autoryzacji
Przepływ kodu autoryzacji zaczyna się od klienta kierowanie użytkowników do `/authorize` punktu końcowego. To interakcyjne część przepływu, gdy użytkownik wykonuje działanie. W tym żądaniu określa klienta w programie `scope` parametru uprawnienia, które należy uzyskać od użytkownika. W `p` parametru, oznacza to, przepływ użytkownika do wykonania. Poniższe trzy przykłady (z podziałów wiersza, aby zwiększyć czytelność) każdego użyć przepływu innego użytkownika.

### <a name="use-a-sign-in-user-flow"></a>Za pomocą przepływu logowania użytkownika
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Za pomocą przepływu rejestracji użytkownika
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Użyj profilu edycji przepływu użytkownika
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [witryny Azure portal](https://portal.azure.com). |
| response_type |Wymagane |Typ odpowiedzi muszą zawierać `code` dla przepływ kodu autoryzacji. |
| redirect_uri |Wymagane |Identyfikator URI aplikacji, w którym wysyłanych i odbieranych przez aplikację uwierzytelniania odpowiedzi przekierowania. Jego musi dokładnie odpowiadać jeden przekierowania identyfikatory URI, który został zarejestrowany w portalu, z tą różnicą, że musi być zakodowane w adresie URL. |
| scope |Wymagane |Rozdzielonej spacjami listy zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure Active Directory (Azure AD) zarówno uprawnień, które są żądane. Przy użyciu Identyfikatora klienta, zgodnie z zakresem wskazuje, że Twoja aplikacja wymaga tokenu dostępu, który może zostać użyty dla własnych usług lub interfejsu API sieci web, reprezentowane przez ten sam identyfikator klienta.  `offline_access` Zakresu wskazuje, że Twoja aplikacja wymaga tokenu odświeżania długotrwałe dostępu do zasobów. Możesz również użyć `openid` zakresu do wysłania żądania tokenu Identyfikatora z usługi Azure AD B2C. |
| response_mode |Zalecane |Metody, która umożliwia wysyłanie wynikowy kod autoryzacji z powrotem do aplikacji. Może być `query`, `form_post`, lub `fragment`. |
| stan |Zalecane |Wartość uwzględnione w żądaniu, który może być ciągiem dowolnej zawartości, którego chcesz używać. Zazwyczaj losowo generowany unikatową wartość jest używana, aby zapobiec atakom fałszowanie żądań między witrynami. Stan jest również używane do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strony, do której użytkownik był na lub przepływ użytkownika, które było wykonywane. |
| p |Wymagane |Przepływ użytkownika, który jest wykonywany. To nazwa przepływu użytkownika, który jest tworzony w katalogu usługi Azure AD B2C. Wartość nazwy przepływu użytkownika powinien zaczynać się od **b2c\_1\_** . Aby uzyskać więcej informacji na temat przepływów użytkownika, zobacz [przepływy użytkownika usługi Azure AD B2C](active-directory-b2c-reference-policies.md). |
| wiersz |Optional (Opcjonalność) |Typ interakcji z użytkownikiem, który jest wymagany. Obecnie jest jedyną prawidłową wartością `login`, która wymusza użytkownika o wprowadzenie poświadczeń dla tego żądania. Logowanie jednokrotne nie zostanie zastosowana. |

W tym momencie użytkownik jest proszony do ukończenia przepływu pracy przepływu użytkownika. Może to obejmować użytkownika wprowadzania nazwy użytkownika i hasła, logowanie się przy użyciu tożsamości społecznościowej, założeniem katalogu lub dowolną liczbę kroków. Akcje użytkownika, zależą od tego, jak jest zdefiniowany przepływu użytkownika.

Po użytkownik kończy przepływ użytkownika, usługi Azure AD, zwraca odpowiedź do aplikacji w wartości używane dla `redirect_uri`. Używa metody podanej w `response_mode` parametru. Odpowiedź jest dokładnie taka sama dla wszystkich scenariuszy akcji użytkownika, niezależnie od przepływu użytkownika, który został wykonany.

Odpowiedź oznaczająca Powodzenie, który używa `response_mode=query` wygląda podobnie do następującego:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Opis |
| --- | --- |
| code |Kod autoryzacji, który zażądał aplikacji. Aplikacja może używać kodu autoryzacji do wysłania żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji są bardzo krótkotrwałe. Zazwyczaj wygasają po upływie około 10 minut. |
| stan |Zobacz pełny opis tabeli w poprzedniej sekcji. Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

Odpowiedzi na błędy również mogą być wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może je odpowiednio obsługiwać:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów, które występują. Możesz również użyć ciągu, aby reagować na błędy. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |
| stan |Zobacz pełny opis w powyższej tabeli. Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

## <a name="2-get-a-token"></a>2. Pobierz token
Teraz, gdy nabyciu kod autoryzacji, można zrealizować `code` dla tokenu do zasobu zamierzony przez wysłanie żądania POST na `/token` punktu końcowego. W usłudze Azure AD B2C tylko zasób, jakiej może żądać token dla jest własnych aplikacji zaplecza interfejsu API sieci web. Konwencji, który jest używany do żądania tokenu służącego do siebie jest użyć własnego Identyfikatora klienta aplikacji jako zakres:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. Nie można użyć przepływu innego użytkownika, w tym żądaniu. Należy pamiętać, dodawania tego parametru na *ciągu zapytania*, a nie w treść wpisu. |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [witryny Azure portal](https://portal.azure.com). |
| grant_type |Wymagane |Rodzaj przyznanie. Przepływ kodu autoryzacji, musi być typu przydziału `authorization_code`. |
| scope |Zalecane |Rozdzielonej spacjami listy zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure AD zarówno uprawnień, które są żądane. Przy użyciu Identyfikatora klienta, zgodnie z zakresem wskazuje, że Twoja aplikacja wymaga tokenu dostępu, który może zostać użyty dla własnych usług lub interfejsu API sieci web, reprezentowane przez ten sam identyfikator klienta.  `offline_access` Zakresu wskazuje, że Twoja aplikacja wymaga tokenu odświeżania długotrwałe dostępu do zasobów.  Możesz również użyć `openid` zakresu do wysłania żądania tokenu Identyfikatora z usługi Azure AD B2C. |
| code |Wymagane |Kod autoryzacji uzyskanego w pierwszej gałęzi przepływu. |
| redirect_uri |Wymagane |Identyfikator URI aplikacji, na którym odebrano kod autoryzacji przekierowania. |

Odpowiedź oznaczająca Powodzenie tokenu wygląda następująco:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Czas, w którym token jest uznawany za ważny, w czasie uniksowym. |
| token_type |Wartość atrybutu typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego. |
| access_token |Podpisem JSON Web Token (JWT) uzyskany. |
| scope |Token jest prawidłowy dla zakresów. Również zakresów można użyć do tokenów pamięci podręcznej do późniejszego użycia. |
| expires_in |Długość czasu, przez jaki token jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu, można uzyskać dodatkowe tokeny, po upływie bieżącego tokenu. Długotrwałe są tokeny odświeżania. Można użyć je, aby zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów, które występują. Możesz również użyć ciągu, aby reagować na błędy. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |

## <a name="3-use-the-token"></a>3. Użyj tokenu
Teraz, gdy token dostępu został pomyślnie uzyskana, można użyć tokenu w żądaniach wysyłanych do zaplecza internetowych interfejsów API, umieszczając go w `Authorization` nagłówka:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Odświeżenia tokenu
Tokeny dostępu i identyfikator tokeny są krótkotrwałe. Po ich wygaśnięciu, należy odświeżyć je w dalszym ciągu uzyskać dostęp do zasobów. Aby to zrobić, należy przesłać kolejne żądanie POST `/token` punktu końcowego. Tym razem zapewniają `refresh_token` zamiast `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Przepływ użytkownika, który został użyty do uzyskania oryginalnej token odświeżania. Nie można użyć przepływu innego użytkownika, w tym żądaniu. Należy pamiętać, dodawania tego parametru na *ciągu zapytania*, a nie w treść wpisu. |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [witryny Azure portal](https://portal.azure.com). |
| client_secret |Wymagane |Wartość client_secret powiązanych z Twojej client_id w [witryny Azure portal](https://portal.azure.com). |
| grant_type |Wymagane |Rodzaj przyznanie. Dla tej gałęzi przepływ kodu autoryzacji musi być typu przydziału `refresh_token`. |
| scope |Zalecane |Rozdzielonej spacjami listy zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure AD zarówno uprawnień, które są żądane. Przy użyciu Identyfikatora klienta, zgodnie z zakresem wskazuje, że Twoja aplikacja wymaga tokenu dostępu, który może zostać użyty dla własnych usług lub interfejsu API sieci web, reprezentowane przez ten sam identyfikator klienta.  `offline_access` Zakresu wskazuje, że potrzebuje aplikacja token odświeżania długotrwałe dostępu do zasobów.  Możesz również użyć `openid` zakresu do wysłania żądania tokenu Identyfikatora z usługi Azure AD B2C. |
| redirect_uri |Optional (Opcjonalność) |Identyfikator URI aplikacji, na którym odebrano kod autoryzacji przekierowania. |
| refresh_token |Wymagane |Oryginalny token odświeżania uzyskanego w drugim nogi przepływ. |

Odpowiedź oznaczająca Powodzenie tokenu wygląda następująco:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Czas, w którym token jest uznawany za ważny, w czasie uniksowym. |
| token_type |Wartość atrybutu typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest elementu nośnego. |
| access_token |Podpisany token JWT uzyskany. |
| scope |Token jest prawidłowy dla zakresów. Możesz również użyć zakresów do pamięci podręcznej tokenów do późniejszego użycia. |
| expires_in |Długość czasu, przez jaki token jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu, można uzyskać dodatkowe tokeny, po upływie bieżącego tokenu. Odśwież tokeny są długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania rodzajów błędów występujących. Możesz również użyć ciągu, aby reagować na błędy. |
| error_description |Komunikat błędu, który pomoże Ci identyfikować przyczyny błędu uwierzytelniania. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Użyj katalogu usługi Azure AD B2C
Aby samodzielnie wypróbować te żądania, wykonaj następujące kroki. Zastąp przykładowe wartości, która była używana w tym artykule własnymi wartościami.

1. [Tworzenie katalogu usługi Azure AD B2C](active-directory-b2c-get-started.md). Użyj nazwy katalogu w żądaniach.
2. [Tworzenie aplikacji](active-directory-b2c-app-registration.md) uzyskać identyfikator aplikacji i identyfikator URI przekierowania. Dołącz klienta natywnego w swojej aplikacji.
3. [Tworzenie przepływów użytkownika](active-directory-b2c-reference-policies.md) można uzyskać nazwy przepływu użytkownika.

