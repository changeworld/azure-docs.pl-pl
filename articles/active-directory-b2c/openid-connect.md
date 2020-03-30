---
title: Logowanie do sieci Web za pomocą usługi OpenID Connect — usługa Azure Active Directory B2C
description: Tworzenie aplikacji sieci web przy użyciu protokołu uwierzytelniania OpenID Connect w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264390"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Logowanie do sieci Web za pomocą usługi OpenID Connect w usłudze Azure Active Directory B2C

OpenID Connect to protokół uwierzytelniania, zbudowany na podstawie protokołu OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników do aplikacji sieci web. Korzystając z implementacji usługi Azure Active Directory B2C (Azure AD B2C) usługi OpenID Connect, można zlecić rejestrację, logowanie i inne środowiska zarządzania tożsamościami w aplikacjach sieci Web do usługi Azure Active Directory (Azure AD). W tym przewodniku pokazano, jak to zrobić w sposób niezależny od języka. Opisano w nim sposób wysyłania i odbierania wiadomości HTTP bez korzystania z naszych bibliotek typu open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół *autoryzacji* OAuth 2.0 do użycia jako protokół *uwierzytelniania.* Ten protokół uwierzytelniania umożliwia wykonywanie logowania jednokrotnego. Wprowadza pojęcie *tokenu identyfikatora*, który pozwala klientowi zweryfikować tożsamość użytkownika i uzyskać podstawowe informacje o profilu użytkownika.

Ponieważ rozszerza OAuth 2.0, umożliwia również aplikacjom bezpieczne *uzyskiwanie tokenów dostępu.* Tokeny dostępu można używać do uzyskiwania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji](protocols-overview.md). OpenID Connect jest zalecane, jeśli budujesz aplikację sieci web, która jest hostowana na serwerze i uzyskiwać dostęp za pośrednictwem przeglądarki. Aby uzyskać więcej informacji na temat tokenów, zobacz [Omówienie tokenów w usłudze Azure Active Directory B2C](tokens-overview.md)

Usługa Azure AD B2C rozszerza standardowy protokół OpenID Connect, aby wykonać więcej niż zwykłe uwierzytelnianie i autoryzację. Wprowadza parametr [przepływu użytkownika,](user-flow-overview.md)który umożliwia korzystanie z OpenID Connect, aby dodać środowisko użytkownika do aplikacji, takie jak rejestracja, logowanie i zarządzanie profilami.

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania

Gdy aplikacja sieci web musi uwierzytelnić użytkownika i uruchomić przepływ użytkownika, `/authorize` może skierować użytkownika do punktu końcowego. Użytkownik podejmuje działania w zależności od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia, które musi uzyskać od `scope` użytkownika w parametrze i określa przepływ użytkownika do uruchomienia. Aby dowiedzieć się, jak działa żądanie, spróbuj wkleić żądanie do przeglądarki i uruchomić go. Zamień `{tenant}` na nazwę dzierżawy. Zamień `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` na identyfikator aplikacji, którą wcześniej zarejestrowałeś w dzierżawie. Zmień również nazwę`{policy}`zasad ( ) na nazwę zasad, którą `b2c_1_sign_in`masz w dzierżawie, na przykład .

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Wymagany | Opis |
| --------- | -------- | ----------- |
| {dzierżawca} | Tak | Nazwa dzierżawy usługi Azure AD B2C |
| {zasady} | Tak | Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie usługi Azure AD B2C. Na `b2c_1_sign_in`przykład: `b2c_1_sign_up`, `b2c_1_edit_profile`, lub . |
| client_id | Tak | Identyfikator aplikacji przypisany do aplikacji [przez portal Azure portal.](https://portal.azure.com/) |
| nonce | Tak | Wartość uwzględniona w żądaniu (wygenerowanym przez aplikację), która jest uwzględniona w wynikowym tokenie identyfikatora jako oświadczenie. Aplikacja może następnie zweryfikować tę wartość, aby ograniczyć ataki powtarzania tokenów. Wartość jest zazwyczaj losowo unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| response_type | Tak | Musi zawierać token identyfikatora dla OpenID Connect. Jeśli aplikacja sieci web potrzebuje również tokenów do `code+id_token`wywoływania internetowego interfejsu API, można użyć . |
| scope | Tak | Oddzielona spacja lista zakresów. Zakres `openid` wskazuje uprawnienie do logowania się użytkownika i uzyskania danych o użytkowniku w postaci tokenów identyfikatorów. Zakres `offline_access` jest opcjonalny dla aplikacji sieci web. Oznacza to, że aplikacja będzie potrzebować *tokenu odświeżania* dla rozszerzonego dostępu do zasobów. |
| Wierszu | Nie | Typ interakcji użytkownika, który jest wymagany. Jedyną prawidłową wartością `login`w tej chwili jest , która wymusza na użytkowniku wprowadzenie poświadczeń w tym żądaniu. |
| redirect_uri | Nie | Parametr `redirect_uri` aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednemu `redirect_uri` z parametrów, które zostały zarejestrowane w witrynie Azure portal, z tą różnicą, że musi być zakodowany adres URL. |
| response_mode | Nie | Metoda, która jest używana do wysyłania wynikowy kod autoryzacji z powrotem do aplikacji. Może to być `query` `form_post`albo `fragment`, , lub .  Tryb `form_post` odpowiedzi jest zalecany dla najlepszego bezpieczeństwa. |
| state | Nie | Wartość uwzględniona w żądaniu, który jest również zwracany w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, która ma być. Losowo wygenerowana unikatowa wartość jest zwykle używana do zapobiegania atakom fałszerskości żądań między witrynami. Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strona, na których były. |

W tym momencie użytkownik jest proszony o ukończenie przepływu pracy. Użytkownik może być musiał wprowadzić swoją nazwę użytkownika i hasło, zalogować się za pomocą tożsamości społecznościowej lub zarejestrować się w katalogu. Może istnieć inna liczba kroków w zależności od sposobu definiowania przepływu użytkownika.

Po zakończeniu przepływu użytkownika, odpowiedź jest zwracana do aplikacji `redirect_uri` we wskazanym parametrze, przy użyciu `response_mode` metody, która jest określona w parametrze. Odpowiedź jest taka sama dla każdego z poprzednich przypadków, niezależnie od przepływu użytkownika.

Pomyślna odpowiedź `response_mode=fragment` za pomocą będzie wyglądać następująco:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| id_token | Token identyfikatora, o który prosiła aplikacja. Token identyfikatora służy do weryfikacji tożsamości użytkownika i rozpoczęcia sesji z użytkownikiem. |
| kod | Kod autoryzacji, którego zażądała aplikacja, jeśli została użyta `response_type=code+id_token`. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Kody autoryzacyjne zazwyczaj wygasają po około 10 minutach. |
| state | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |

Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do parametru, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| state | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu identyfikatora

Samo odebranie tokenu identyfikatora nie wystarczy do uwierzytelnienia użytkownika. Sprawdź poprawność podpisu tokenu identyfikatora i sprawdź oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Usługa Azure AD B2C używa [tokenów JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe. Istnieje wiele bibliotek typu open source, które są dostępne do sprawdzania poprawności JWTs, w zależności od języka preferencji. Zaleca się eksplorowanie tych opcji, a nie implementowanie własnej logiki sprawdzania poprawności.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect, który umożliwia aplikacji uzyskanie informacji o usłudze Azure AD B2C w czasie wykonywania. Informacje te obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenów. Istnieje dokument metadanych JSON dla każdego przepływu użytkownika w dzierżawie B2C. Na przykład dokument metadanych `b2c_1_sign_in` dla `fabrikamb2c.onmicrosoft.com` przepływu użytkownika znajduje się na poziomie:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jedną z właściwości tego dokumentu `jwks_uri`konfiguracji jest , którego wartość dla tego samego przepływu użytkownika będzie:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Aby określić, który przepływ użytkownika był używany podczas podpisywania tokenu identyfikatora (i skąd można uzyskać metadane), masz dwie opcje. Po pierwsze nazwa przepływu użytkownika jest `acr` uwzględniona w żądaniu w tokenie identyfikatora. Inną opcją jest zakodowanie przepływu użytkownika w `state` wartości parametru podczas wystawiania żądania, a następnie dekodowanie go w celu określenia, który przepływ użytkownika został użyty. Każda z tych metod jest prawidłowa.

Po nabyciu dokumentu metadanych z punktu końcowego metadanych OpenID Connect można użyć kluczy publicznych RSA 256 do sprawdzania poprawności podpisu tokenu identyfikatora. Może istnieć wiele kluczy wymienionych w tym `kid` punkcie końcowym, każdy identyfikowany przez oświadczenie. Nagłówek tokenu identyfikatora zawiera `kid` również oświadczenie, które wskazuje, który z tych kluczy został użyty do podpisania tokenu identyfikatora.

Aby zweryfikować tokeny z usługi Azure AD B2C, należy wygenerować klucz publiczny przy użyciu wykładnika(e) i modulus(n). Należy określić, jak to zrobić w odpowiednim języku programowania odpowiednio. Oficjalna dokumentacja dotycząca generowania klucza publicznego z protokołem RSA znajduje się tutaj:https://tools.ietf.org/html/rfc3447#section-3.1

Po sprawdzeniu poprawności podpisu tokenu identyfikatora istnieje kilka oświadczeń, które należy zweryfikować. Przykład:

- Sprawdź poprawność oświadczenia, `nonce` aby zapobiec atakom powtarzania tokenów. Jego wartość powinna być określona w żądaniu logowania.
- Sprawdź poprawność oświadczenia, `aud` aby upewnić się, że token identyfikatora został wystawiony dla aplikacji. Jego wartość powinna być identyfikator aplikacji aplikacji.
- Sprawdź `iat` poprawność `exp` i oświadczeń, aby upewnić się, że token identyfikatora nie wygasł.

Istnieje również kilka innych sprawdzania poprawności, które należy wykonać. Sprawdzanie poprawności są szczegółowo opisane w [specyfikacji openid connect core](https://openid.net/specs/openid-connect-core-1_0.html). Można również sprawdzić poprawność dodatkowych oświadczeń, w zależności od scenariusza. Niektóre typowe weryfikacje obejmują:

- Upewniając się, że użytkownik/organizacja zarejestrowała się w aplikacji.
- Zapewnienie, że użytkownik ma odpowiednie uprawnienia/uprawnienia.
- Zapewnienie, że wystąpiła pewna siła uwierzytelniania, na przykład uwierzytelnianie wieloskładnikowe platformy Azure.

Po sprawdzeniu poprawności tokenu identyfikatora można rozpocząć sesję z użytkownikiem. Oświadczeń w tokenie identyfikatora można użyć, aby uzyskać informacje o użytkowniku w aplikacji. Zastosowania tych informacji obejmują wyświetlanie, rekordy i autoryzację.

## <a name="get-a-token"></a>Zdobądź token

Jeśli potrzebujesz aplikacji sieci web, aby uruchamiać tylko przepływy użytkowników, możesz pominąć kilka następnych sekcji. Te sekcje mają zastosowanie tylko do aplikacji sieci web, które muszą wykonywać uwierzytelnione wywołania do internetowego interfejsu API i są również chronione przez usługę Azure AD B2C.

Można zrealizować kod autoryzacji, który `response_type=code+id_token`został nabyty (przy użyciu ) `POST` dla `/token` tokenu do żądanego zasobu, wysyłając żądanie do punktu końcowego. W usłudze Azure AD B2C można [zażądać tokenów dostępu dla innych interfejsów API](access-tokens.md#request-a-token) w zwykły sposób, określając ich zakresy w żądaniu.

Możesz również zażądać tokenu dostępu dla własnego interfejsu API sieci Web zaplecza aplikacji zgodnie z konwencją używania identyfikatora klienta aplikacji jako żądanego zakresu (co spowoduje token dostępu o tym identyfikatorze klienta jako "odbiorcy"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagany | Opis |
| --------- | -------- | ----------- |
| {dzierżawca} | Tak | Nazwa dzierżawy usługi Azure AD B2C |
| {zasady} | Tak | Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. W tym żądaniu nie można użyć innego przepływu użytkownika. Dodaj ten parametr do ciągu kwerendy, a nie do treści POST. |
| client_id | Tak | Identyfikator aplikacji przypisany do aplikacji [przez portal Azure portal.](https://portal.azure.com/) |
| client_secret | Tak, w aplikacjach sieci Web | Klucz tajny aplikacji, który został wygenerowany w [witrynie Azure portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W scenariuszach aplikacji natywnej (klienta publicznego) nie można bezpiecznie przechowywać wpisów tajnych klienta, nie używane w tym przepływie. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| kod | Tak | Kod autoryzacji, który został nabyty na początku przepływu użytkownika. |
| grant_type | Tak | Typ dotacji, który musi `authorization_code` być dla przepływu kodu autoryzacji. |
| redirect_uri | Tak | Parametr `redirect_uri` aplikacji, w której odebrano kod autoryzacji. |
| scope | Nie | Oddzielona spacja lista zakresów. Zakres `openid` wskazuje uprawnienie do logowania się użytkownika i uzyskania danych o użytkowniku w postaci parametrów id_token. Może służyć do uzyskania tokenów do własnego interfejsu API sieci web zaplecza aplikacji, który jest reprezentowany przez ten sam identyfikator aplikacji jako klienta. Zakres `offline_access` wskazuje, że aplikacja potrzebuje tokenu odświeżania dla rozszerzonego dostępu do zasobów. |

Pomyślna odpowiedź tokenu wygląda następująco:

```JSON
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
| --------- | ----------- |
| not_before | Czas, w którym token jest uważany za prawidłowy, w czasie epoki. |
| token_type | Wartość typu tokenu. `Bearer`jest jedynym typem, który jest obsługiwany. |
| access_token | Podpisany token JWT, o który prosiłeś. |
| scope | Zakresy, dla których token jest prawidłowy. |
| expires_in | Czas, przez który token dostępu jest prawidłowy (w sekundach). |
| refresh_token | Token odświeżania OAuth 2.0. Aplikacja może użyć tego tokenu do uzyskania dodatkowych tokenów po wygaśnięciu bieżącego tokenu. Tokeny odświeżania mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. Zakres `offline_access` musi być używany w żądaniach autoryzacji i tokenu w celu otrzymania tokenu odświeżania. |

Odpowiedzi na błędy wyglądają następująco:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Komunikat, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="use-the-token"></a>Użyj tokenu

Teraz, gdy pomyślnie uzyskałeś token dostępu, możesz użyć tokenu w żądaniach do interfejsów `Authorization` API sieci web zaplecza, dołączając go do nagłówka:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Odświeżanie tokenu

Tokeny identyfikatorów wygasają w krótkim czasie. Odśwież tokeny po ich wygaśnięciu, aby nadal mieć dostęp do zasobów. Token można odświeżyć, `POST` przesyłając `/token` inne żądanie do punktu końcowego. Tym razem podaj `refresh_token` parametr `code` zamiast parametru:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagany | Opis |
| --------- | -------- | ----------- |
| {dzierżawca} | Tak | Nazwa dzierżawy usługi Azure AD B2C |
| {zasady} | Tak | Przepływ użytkownika, który został użyty do uzyskania oryginalnego tokenu odświeżania. W tym żądaniu nie można użyć innego przepływu użytkownika. Dodaj ten parametr do ciągu kwerendy, a nie do treści POST. |
| client_id | Tak | Identyfikator aplikacji przypisany do aplikacji [przez portal Azure portal.](https://portal.azure.com/) |
| client_secret | Tak, w aplikacjach sieci Web | Klucz tajny aplikacji, który został wygenerowany w [witrynie Azure portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W scenariuszach aplikacji natywnej (klienta publicznego) nie można bezpiecznie przechowywać wpisów tajnych klienta, nie używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type | Tak | Typ grantu, który musi być tokenem odświeżania dla tej części przepływu kodu autoryzacji. |
| refresh_token | Tak | Oryginalny token odświeżania, który został nabyty w drugiej części przepływu. Zakres `offline_access` musi być używany zarówno w żądań autoryzacji i tokenu, aby otrzymać token odświeżania. |
| redirect_uri | Nie | Parametr `redirect_uri` aplikacji, w której odebrano kod autoryzacji. |
| scope | Nie | Oddzielona spacja lista zakresów. Zakres `openid` wskazuje uprawnienie do logowania się użytkownika i uzyskania danych o użytkowniku w postaci tokenów identyfikatorów. Może służyć do wysyłania tokenów do własnego interfejsu API sieci web zaplecza aplikacji, który jest reprezentowany przez ten sam identyfikator aplikacji jako klienta. Zakres `offline_access` wskazuje, że aplikacja potrzebuje tokenu odświeżania dla rozszerzonego dostępu do zasobów. |

Pomyślna odpowiedź tokenu wygląda następująco:

```JSON
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
| --------- | ----------- |
| not_before | Czas, w którym token jest uważany za prawidłowy, w czasie epoki. |
| token_type | Wartość typu tokenu. `Bearer`jest jedynym typem, który jest obsługiwany. |
| access_token | Wymagany token JWT podpisany. |
| scope | Zakres, dla którego token jest prawidłowy. |
| expires_in | Czas, przez który token dostępu jest prawidłowy (w sekundach). |
| refresh_token | Token odświeżania OAuth 2.0. Aplikacja może użyć tego tokenu do uzyskania dodatkowych tokenów po wygaśnięciu bieżącego tokenu. Tokeny odświeżania mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. |

Odpowiedzi na błędy wyglądają następująco:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Komunikat, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="send-a-sign-out-request"></a>Wysyłanie żądania wylogowywania

Jeśli chcesz wylogować użytkownika z aplikacji, nie wystarczy wyczyścić pliki cookie aplikacji lub w inny sposób zakończyć sesję z użytkownikiem. Przekieruj użytkownika do usługi Azure AD B2C, aby się wylogować. Jeśli to nie zrobisz, użytkownik może być w stanie ponownie uwierzytelnić do aplikacji bez wprowadzania ich poświadczenia ponownie.

Aby wylogować użytkownika, przekieruj go do punktu końcowego `end_session` wymienionego w opisanym wcześniej dokumencie metadanych OpenID Connect:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametr | Wymagany | Opis |
| --------- | -------- | ----------- |
| {dzierżawca} | Tak | Nazwa dzierżawy usługi Azure AD B2C |
| {zasady} | Tak | Przepływ użytkownika, który ma być używany do podpisywania użytkownika z aplikacji. |
| id_token_hint| Nie | Wcześniej wystawiony token identyfikatora do przekazania do punktu końcowego wylogowania jako wskazówka dotycząca bieżącej uwierzytelnionej sesji użytkownika końcowego z klientem. Zapewnia, `id_token_hint` że `post_logout_redirect_uri` jest zarejestrowany adres URL odpowiedzi w ustawieniach aplikacji usługi Azure AD B2C. |
| client_id | Nie* | Identyfikator aplikacji przypisany do aplikacji [przez portal Azure portal.](https://portal.azure.com/)<br><br>\**Jest to wymagane `Application` w przypadku korzystania z konfiguracji logowania sytego `No`izolacji i _wymagać tokenu identyfikatora_ w żądaniu wylogowania jest ustawiona na .* |
| post_logout_redirect_uri | Nie | Adres URL, do który użytkownik powinien zostać przekierowany po pomyślnym wylogowywaniu się. Jeśli nie jest uwzględniony, usługa Azure AD B2C pokazuje użytkownikowi ogólny komunikat. Jeśli nie `id_token_hint`podasz , nie należy rejestrować ten adres URL jako adres URL odpowiedzi w ustawieniach aplikacji usługi Azure AD B2C. |
| state | Nie | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |

### <a name="secure-your-logout-redirect"></a>Zabezpiecz przekierowanie wylogowania

Po wylogowaniu użytkownik jest przekierowywał `post_logout_redirect_uri` do identyfikatora URI określonego w parametrze, niezależnie od adresów URL odpowiedzi, które zostały określone dla aplikacji. Jednak jeśli prawidłowa `id_token_hint` jest przekazywana, usługa Azure AD `post_logout_redirect_uri` B2C sprawdza, czy wartość odpowiada jednej ze skonfigurowanych identyfikatorów URI przekierowania aplikacji przed wykonaniem przekierowania. Jeśli dla aplikacji nie skonfigurowano pasującego adresu URL odpowiedzi, zostanie wyświetlony komunikat o błędzie, a użytkownik nie zostanie przekierowany.

### <a name="external-identity-provider-sign-out"></a>Wylogowywanie się zewnętrznego dostawcy tożsamości

Kierowanie użytkownika do `end_session` punktu końcowego czyści niektóre stan logowania jednokrotnego użytkownika za pomocą usługi Azure AD B2C, ale nie podpisuje użytkownika z ich sesji dostawcy tożsamości społecznościowej (IDP). Jeśli użytkownik wybierze ten sam identyfikator tożsamości podczas późniejszego logowania, są one ponownie uwierzytelnione bez wprowadzania poświadczeń. Jeśli użytkownik chce wylogować się z aplikacji, nie musi to oznaczać, że chce wylogować się ze swojego konta na Facebooku. Jeśli jednak używane są konta lokalne, sesja użytkownika kończy się poprawnie.
