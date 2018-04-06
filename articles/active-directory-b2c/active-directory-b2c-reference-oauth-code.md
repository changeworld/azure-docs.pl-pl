---
title: Przepływu kodu autoryzacji — usługi Azure AD B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje sieci web przy użyciu protokołu uwierzytelniania usługi Azure AD B2C i OpenID Connect.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d8ed5747f29f969535bbafc1624d9d02e54c8418
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: Przepływu kodu autoryzacji protokołu OAuth 2.0
Udzielania kodu autoryzacji protokołu OAuth 2.0 w aplikacjach na urządzeniu służy do uzyskiwania dostępu do chronionych zasobów, takich jak interfejsów API sieci web. Za pomocą usługi Azure Active Directory B2C (Azure AD B2C) implementacji protokołu OAuth 2.0, można dodać rejestrację, logowanie i innych Zarządzanie tożsamościami zadań do aplikacji mobilnych i klasycznych. W tym artykule jest niezależny od języka. W artykule możemy opisują sposób wysyłania i odbierania wiadomości HTTP bez korzystania z żadnych bibliotek open source.

<!-- TODO: Need link to libraries -->

Przepływu kodu autoryzacji protokołu OAuth 2.0 jest opisany w [sekcji 4.1 specyfikacji protokołu OAuth 2.0](http://tools.ietf.org/html/rfc6749). Służy do uwierzytelniania i autoryzacji w większości typów aplikacji, w tym [sieci web apps](active-directory-b2c-apps.md#web-apps) i [natywnie zainstalowane aplikacje](active-directory-b2c-apps.md#mobile-and-native-apps). Można bezpiecznie uzyskać przepływu kodu autoryzacji protokołu OAuth 2.0 *tokenów dostępu* dla aplikacji, które mogą służyć do dostępu do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-b2c-reference-protocols.md#the-basics).

Ten artykuł skupia się na **klientów publicznych** przepływu kodu autoryzacji protokołu OAuth 2.0. Publiczny klient jest w dowolnej aplikacji klienta, która nie może być zaufany, aby bezpiecznie zachowanie spójności tajnego hasła. Obejmuje to aplikacje mobilne, aplikacje komputerowe i zasadniczo dowolnej aplikacji, która jest uruchamiana na urządzeniu oraz musi uzyskać tokeny dostępu. 

> [!NOTE]
> Aby dodać Zarządzanie tożsamościami do aplikacji sieci web przy użyciu usługi Azure AD B2C, użyj [OpenID Connect](active-directory-b2c-reference-oidc.md) zamiast protokołu OAuth 2.0.

Usługa Azure AD B2C rozszerza tego standardu OAuth 2.0 przechodzi do więcej niż prostego uwierzytelniania i autoryzacji. Podaj [parametru zasad](active-directory-b2c-reference-policies.md). Wbudowane zasady umożliwia OAuth 2.0 Dodawanie użytkowników do aplikacji, takich jak konta, logowania i zarządzania profilami. W tym artykule zostanie przedstawiony zostanie sposób użycia protokołu OAuth 2.0 i zasady do wdrożenia każdego z tych środowisk w natywnej aplikacji. Możemy również opisano, jak pobieranie tokenów dostępu do uzyskiwania dostępu do interfejsów API sieci web.

W żądaniach przykład HTTP w tym artykule, korzystamy z naszych katalog usługi Azure AD B2C przykładu **fabrikamb2c.onmicrosoft.com**. Możemy również użyć naszych przykładowej aplikacji i zasad. Można spróbować żądania samodzielnie przy użyciu tych wartości, lub należy je zastąpić własne wartości.
Dowiedz się, jak [uzyskać własnego katalogu usługi Azure AD B2C, aplikacji i zasad](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Pobierz kod autoryzacji
Przepływu kodu autoryzacji rozpoczyna się od klienta kierowanie użytkownikowi `/authorize` punktu końcowego. Jest interaktywny część przepływu, gdy użytkownik wykona akcję. W tym żądaniu klienta wskazuje w `scope` parametru uprawnienia, które należy uzyskać od użytkownika. W `p` parametr wskazuje zasad do wykonania. Poniższe trzy przykłady (podziałami wierszy dla czytelności) każdego używają różnych zasad.

### <a name="use-a-sign-in-policy"></a>Użyj zasad logowania
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Użyj zasad rejestracji
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Użyj zasad edycji profilu
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [portalu Azure](https://portal.azure.com). |
| response_type |Wymagane |Typ odpowiedzi, który musi zawierać `code` dla przepływu kodu autoryzacji. |
| redirect_uri |Wymagane |Identyfikator URI aplikacji, w którym wysyłanych i odbieranych przez aplikację uwierzytelniania odpowiedzi przekierowania. Go musi dokładnie odpowiadać jeden przekierowania URI, który został zarejestrowany w portalu, z wyjątkiem tego, że musi być zakodowane w adresie URL. |
| Zakres |Wymagane |Rozdzieloną spacjami listę zakresów. Wskazuje wartość pojedynczy zakres Azure Active Directory (Azure AD) oba te uprawnienia, które są żądane. Przy użyciu Identyfikatora klienta, jak zakres wskazuje, że Twoja aplikacja powinna tokenu dostępu, który może służyć przed własnych usług lub interfejsu API sieci web, reprezentowany przez ten sam identyfikator klienta.  `offline_access` Zakresu wskazuje, że aplikacja wymaga token odświeżania długotrwałe dostępu do zasobów. Można też użyć `openid` zakresu żądania tokenu identyfikator od usługi Azure AD B2C. |
| response_mode |Zalecane |Metoda używana do odesłania wynikowy kod autoryzacji do aplikacji. Można ją `query`, `form_post`, lub `fragment`. |
| state |Zalecane |Wartość zawarte w żądaniu, który jest zwracany w odpowiedzi tokenu. Można go ciągiem zawartość, która ma być używany. Zwykle losowo wygenerowany unikatową wartość jest używany, aby zapobiec fałszerstwie żądania międzywitrynowego. Stan jest również używany do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strona, do której użytkownik został na lub zasady, które było wykonywane. |
| p |Wymagane |Zasada, która jest wykonywana. Jest to nazwa zasad, który jest tworzony w katalogu usługi Azure AD B2C. Wartość Nazwa zasad powinny rozpoczynać się od **b2c\_1\_**. Aby dowiedzieć się więcej na temat zasad, zobacz [wbudowane zasady usługi Azure AD B2C](active-directory-b2c-reference-policies.md). |
| wiersz |Optional (Opcjonalność) |Typ interakcji z użytkownikiem, który jest wymagany. Obecnie jest jedyną poprawną wartością `login`, która wymusza na użytkowniku, aby wprowadzić swoje poświadczenia dla tego żądania. Logowanie jednokrotne nie zacznie obowiązywać. |

W tym momencie użytkownik jest proszony o ukończenia przepływu pracy zasad. Może to obejmować wprowadzić swoją nazwę użytkownika i hasło, użytkownik zalogować społecznościowych tożsamości, podczas tworzenia katalogu lub dowolnej liczby kroków. Akcje użytkownika zależą od tego, jak zdefiniowano zasad.

Po wypełnieniu zasad przez użytkownika, usługi Azure AD zwraca odpowiedź do aplikacji przy wartości używane dla `redirect_uri`. Używa metody określonej w `response_mode` parametru. Odpowiedź jest dokładnie taka sama dla każdego z scenariuszy akcji użytkownika, niezależnie od zasady, które zostało wykonane.

Odpowiedź oznaczająca Powodzenie, która używa `response_mode=query` wygląda podobnie do następującej:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Opis |
| --- | --- |
| kod |Kod autoryzacji żądanej aplikacji. Aplikację można użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji są bardzo krótkim okresie. Zazwyczaj wygasną po około 10 minut. |
| state |Zobacz pełny opis tabeli w poprzedniej sekcji. Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne. |

Odpowiedzi na błędy również mogą być wysyłane identyfikator URI przekierowania, dzięki czemu aplikacja może je odpowiednią obsługę:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typów błędów występujących. Możesz również umożliwia ciąg reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |
| state |Zobacz pełną opisu w powyższej tabeli. Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne. |

## <a name="2-get-a-token"></a>2. Uzyskaj token
Teraz, gdy zostały nabyte kod autoryzacji, można wykorzystać `code` dla tokenu do danego zasobu poprzez wysłanie żądania POST do `/token` punktu końcowego. W usłudze Azure AD B2C tylko zasób, jakiej może żądać tokenu dla jest własnych aplikacji zaplecza interfejsu API sieci web. Konwencja, która jest używana dla żądania tokenu do siebie ma użyć Identyfikatora klienta aplikacji jako zakres:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Zasady, które zostało użyte do uzyskania kod autoryzacji. Nie można użyć różnych zasad w tym żądaniu. Należy pamiętać, aby dodać ten parametr, aby *ciągu zapytania*, a nie w treści POST. |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [portalu Azure](https://portal.azure.com). |
| Typ grant_type |Wymagane |Typ grant. Dla przepływu kodu autoryzacji, musi być typu przydziału `authorization_code`. |
| Zakres |Zalecane |Rozdzieloną spacjami listę zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure AD zarówno uprawnienia, które są żądane. Przy użyciu Identyfikatora klienta, jak zakres wskazuje, że Twoja aplikacja powinna tokenu dostępu, który może służyć przed własnych usług lub interfejsu API sieci web, reprezentowany przez ten sam identyfikator klienta.  `offline_access` Zakresu wskazuje, że aplikacja wymaga token odświeżania długotrwałe dostępu do zasobów.  Można też użyć `openid` zakresu żądania tokenu identyfikator od usługi Azure AD B2C. |
| kod |Wymagane |Uzyskanego w pierwszego etap przepływu kodu autoryzacji. |
| redirect_uri |Wymagane |Identyfikator URI przekierowania w aplikacji, w którym odebrano kod autoryzacji. |

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
| not_before |Czas, w którym token jest uznawany za ważny w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest elementu nośnego. |
| access_token |Podpisem JSON Web Token (JWT) żądanych. |
| Zakres |Token jest prawidłowy dla zakresów. Możesz również zakresów można użyć do pamięci podręcznej tokenów do późniejszego użycia. |
| expires_in |Długość czasu, przez który token jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać ten token, aby uzyskać dodatkowe tokeny, po wygaśnięciu tokenu bieżącego. Tokeny odświeżania są długotrwałe. Można używać ich, aby zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typów błędów występujących. Możesz również umożliwia ciąg reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |

## <a name="3-use-the-token"></a>3. Użyj tokenu
Teraz, zostały pomyślnie uzyskano tokenu dostępu, można użyć tokenu w żądaniach na danym zapleczu interfejsów API sieci web przez włączenie jej w `Authorization` nagłówka:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Token odświeżania
Tokeny dostępu i identyfikator tokeny są krótkim okresie. Po wygasną, należy odświeżyć je, aby uzyskać dostęp do zasobów. W tym celu należy przesłać kolejne żądanie POST `/token` punktu końcowego. Podaj w tej chwili `refresh_token` zamiast `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Zasady, które zostało użyte do uzyskania oryginalnego token odświeżania. Nie można użyć różnych zasad w tym żądaniu. Należy pamiętać, aby dodać ten parametr, aby *ciągu zapytania*, a nie w treści POST. |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [portalu Azure](https://portal.azure.com). |
| client_secret |Wymagane |Client_secret skojarzonego z client_id w [portalu Azure](https://portal.azure.com). |
| Typ grant_type |Wymagane |Typ grant. Dla tej gałęzi przepływu kodu autoryzacji musi być typu przydziału `refresh_token`. |
| Zakres |Zalecane |Rozdzieloną spacjami listę zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure AD zarówno uprawnienia, które są żądane. Przy użyciu Identyfikatora klienta, jak zakres wskazuje, że Twoja aplikacja powinna tokenu dostępu, który może służyć przed własnych usług lub interfejsu API sieci web, reprezentowany przez ten sam identyfikator klienta.  `offline_access` Zakresu wskazuje, że aplikacji będą potrzebne token odświeżania długotrwałe dostępu do zasobów.  Można też użyć `openid` zakresu żądania tokenu identyfikator od usługi Azure AD B2C. |
| redirect_uri |Optional (Opcjonalność) |Identyfikator URI przekierowania w aplikacji, w którym odebrano kod autoryzacji. |
| refresh_token |Wymagane |Oryginalny token odświeżania uzyskanego w drugi etap przepływu. |

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
| not_before |Czas, w którym token jest uznawany za ważny w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest elementu nośnego. |
| access_token |Podpisany token JWT żądanych. |
| Zakres |Token jest prawidłowy dla zakresów. Również zakresów można użyć do tokenów pamięci podręcznej do późniejszego użycia. |
| expires_in |Długość czasu, przez który token jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać ten token, aby uzyskać dodatkowe tokeny, po wygaśnięciu tokenu bieżącego. Odśwież tokeny są to długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów występujących. Możesz również umożliwia ciąg reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Użyj katalogu usługi Azure AD B2C
Aby samodzielnie wypróbować te żądania, wykonaj następujące kroki. Zastąp przykładowe wartości, używane w tym artykule i własne wartości.

1. [Utwórz katalog usługi Azure AD B2C](active-directory-b2c-get-started.md). Użyj nazwy katalogu w żądaniach.
2. [Tworzenie aplikacji](active-directory-b2c-app-registration.md) uzyskać identyfikator aplikacji i identyfikator URI przekierowania. Uwzględnij klienta natywnego w aplikacji.
3. [Tworzenie zasad](active-directory-b2c-reference-policies.md) można uzyskać nazwy zasad.

