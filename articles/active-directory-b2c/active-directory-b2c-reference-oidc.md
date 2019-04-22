---
title: Zaloguj się przy użyciu protokołu OpenID Connect — Azure Active Directory B2C w sieci Web | Dokumentacja firmy Microsoft
description: Twórz aplikacje sieci web przy użyciu protokołu uwierzytelniania OpenID Connect w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6285a90a9dca305f3a9cd909af6c084c747daf99
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679061"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Logowanie w sieci Web z OpenID Connect w usłudze Azure Active Directory B2C

OpenID Connect to protokół uwierzytelniania, korzystających z protokołu OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników do aplikacji sieci web. Za pomocą usługi Azure Active Directory B2C (Azure AD B2C) wdrażania protokołu OpenID Connect, można zlecają obsługę tworzenia nowych kont i logowania oraz skuteczniejszego innych zarządzania tożsamościami w aplikacjach sieci web w usłudze Azure Active Directory (Azure AD). Ten przewodnik pokazuje, jak to zrobić w sposób niezależny od języka. Przedstawiono sposób wysyłać i odbierać komunikaty HTTP bez użycia jakichkolwiek skorzystać z bibliotek typu open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza OAuth 2.0 *autoryzacji* protokół do użycia jako *uwierzytelniania* protokołu. Ten protokół uwierzytelniania umożliwia wykonywanie logowania jednokrotnego. Wprowadza pojęcia *tokenu Identyfikacyjnego*, co pozwala klientowi zweryfikowanie tożsamości użytkownika i uzyskać podstawowych informacji o profilu użytkownika.

Ponieważ stanowi rozszerzenie protokołu OAuth 2.0, umożliwia również aplikacji w celu uzyskania bezpiecznego *tokeny dostępu*. Tokeny dostępu umożliwia dostęp do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-b2c-reference-protocols.md). OpenID Connect zaleca się, jeśli tworzysz aplikację internetową, która ma się na serwerze i dostępne za pośrednictwem przeglądarki. Jeśli chcesz dodać zarządzania tożsamościami do aplikacji mobilnych lub aplikacje klasyczne za pomocą usługi Azure AD B2C, należy użyć [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) zamiast protokołu OpenID Connect. Aby uzyskać więcej informacji na temat tokenów, zobacz [Przegląd tokenów w usłudze Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Usługa Azure AD B2C rozszerza ze standardowego protokołu OpenID Connect, aby zrobić więcej niż proste uwierzytelnianie i autoryzacja. Wprowadza [parametr przepływ użytkownika](active-directory-b2c-reference-policies.md), co umożliwia Dodaj użytkownika za pomocą protokołu OpenID Connect środowisk aplikacji, takich jak rejestracji, logowania i zarządzania profilami.

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania

W przypadku aplikacji sieci web wymaga uwierzytelnienia użytkownika i uruchomić przepływ użytkownika, można je skierować użytkownika `/authorize` punktu końcowego. Użytkownik wykonuje akcję, zależnie od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia, których potrzebuje, aby pobierać z użytkownikiem w `scope` parametr i przepływ użytkownika do uruchamiania w `p` parametru. Trzy przykłady znajdują się w poniższych sekcjach (z podziały wierszy dla czytelności), używając przepływu innego użytkownika. Aby uzyskać pewne pojęcie dla działania każdego żądania, spróbuj wklejanie żądania do przeglądarki i uruchomiania go. Możesz zastąpić `fabrikamb2c` nazwą dzierżawy, jeśli masz i utworzono przepływ użytkownika.

#### <a name="use-a-sign-in-user-flow"></a>Za pomocą przepływu logowania użytkownika
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Za pomocą przepływu rejestracji użytkownika
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Użyj profilu edycji przepływu użytkownika
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_id | Yes | Identyfikator aplikacji [witryny Azure portal](https://portal.azure.com/) przypisane do aplikacji. |
| response_type | Yes | Musi zawierać identyfikator tokenu dla protokołu OpenID Connect. Jeśli aplikacja sieci web musi również tokenów do wywoływania interfejsu API sieci web, możesz użyć `code+id_token`. |
| redirect_uri | Nie | `redirect_uri` Parametr aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednej z `redirect_uri` parametry, które są zarejestrowane w witrynie Azure portal, z tą różnicą, że musi być zakodowane w adresie URL. |
| scope | Yes | Rozdzielonej spacjami listy zakresów. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i pobieranie danych o użytkowniku w formie tokenów Identyfikatora. `offline_access` Zakres jest opcjonalny w przypadku aplikacji sieci web. Oznacza to, że Twoja aplikacja musi mieć *token odświeżania* rozszerzonej dostępu do zasobów. |
| response_mode | Nie | Metody, która jest używana do wysyłania wynikowy kod autoryzacji z powrotem do aplikacji. Może być albo `query`, `form_post`, lub `fragment`.  `form_post` Najlepiej zalecany jest tryb odpowiedzi. |
| state | Nie | Wartość uwzględnione w żądaniu, która jest także zwracany w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, która ma. Zazwyczaj służy losowo generowany unikatową wartość dla zapobieganie atakom na fałszerstwo żądania międzywitrynowego. Stan również jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony, w której znajdowały się w. |
| Identyfikator jednorazowy | Yes | Wartość uwzględnione w żądaniu (generowany przez aplikację), który znajduje się w jego identyfikator tokenu jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby uniknąć powtarzania tokenu ataków. Wartość jest zazwyczaj losowy unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| p | Yes | Przepływ użytkownika, który jest uruchamiany. To nazwa przepływu użytkownika, który jest tworzony w dzierżawie usługi Azure AD B2C. Nazwa przepływu użytkownika powinien zaczynać się `b2c\_1\_`. |
| wiersz | Nie | Typ interakcji z użytkownikiem, który jest wymagany. To jedyna prawidłowa wartość w tej chwili `login`, która wymusza użytkownika o wprowadzenie poświadczeń dla tego żądania. |

W tym momencie użytkownik jest monitowany ukończenia przepływu pracy. Użytkownik może być konieczne wprowadzanie nazwy użytkownika i hasła, zaloguj się przy użyciu tożsamości dla sieci społecznościowej lub zaloguj w katalogu. Może być dowolną liczbę kroków w zależności od tego, jak jest zdefiniowany przepływu użytkownika.

Po użytkownik kończy przepływ użytkownika, odpowiedź jest zwracana do aplikacji w wskazany `redirect_uri` parametru za pomocą metody, która została określona w `response_mode` parametru. Odpowiedź jest taka sama dla każdego z powyższych przypadkach niezależny od przepływu użytkownika.

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` powinien wyglądać tak:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| id_token | Identyfikator tokenu, który zażądał aplikacji. Identyfikator tokenu można użyć do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. |
| kod | Kod autoryzacji, który zażądał aplikacji, jeśli użyto `response_type=code+id_token`. Aplikacja może używać kodu autoryzacji do wysłania żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji zazwyczaj wygasają po upływie około 10 minut. |
| state | Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

Odpowiedzi na błędy mogą również zostać wysłane do `redirect_uri` parametru, aby aplikacja może je odpowiednio obsługiwać:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typy występujące błędy. |
| error_description | Komunikat błędu, który może ułatwić identyfikację przyczyny błędu uwierzytelniania. |
| state | Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu Identyfikacyjnego

Po prostu odbiera token Identyfikatora nie wystarcza do uwierzytelnienia użytkownika. Weryfikowanie podpisu tokenu Identyfikacyjnego i sprawdź oświadczenia w tokenie dla wymagań aplikacji. Usługa Azure AD B2C używa [tokenów sieci Web JSON (tokenów Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe. Istnieje wiele bibliotek typu open source, które są dostępne do weryfikacji tokenów Jwt, w zależności od języka, preferencji. Firma Microsoft zaleca Eksplorowanie tych opcji, a nie Implementowanie logiki walidacji. 

Usługa Azure AD B2C ma OpenID Connect punkt końcowy metadanych, który umożliwia aplikacji w celu uzyskania informacji na temat usługi Azure AD B2C w środowisku uruchomieniowym. Informacje te obejmują punkty końcowe, zawartość tokenów i tokenów kluczy podpisywania. Brak dokumentu metadanych JSON dla każdego przepływu użytkownika w dzierżawie usługi B2C. Na przykład dokument metadanych dla `b2c_1_sign_in` przepływu użytkownika w `fabrikamb2c.onmicrosoft.com` znajduje się w folderze:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z właściwości tego dokumentu konfiguracji jest `jwks_uri`, którego wartość jest taki sam przepływ użytkownika:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Aby określić, które przepływ użytkownika została użyta w podpisywania identyfikator tokenu (i skąd można uzyskać metadanych), masz dwie opcje. Po pierwsze, przepływ użytkownika znajduje się w `acr` oświadczenia w tokenie identyfikator. Drugą opcją jest do zakodowania przepływu użytkownika w wartości `state` parametru podczas wystawiania żądania, a następnie dekodowania go, aby określić, które przepływ użytkownika zostało użyte. Każda z tych metod jest prawidłowy.

Po nabyciu dokumentu metadanych z punktu końcowego metadanych OpenID Connect, umożliwia klucze publiczne RSA 256 zweryfikować podpisu tokenu Identyfikacyjnego. Może istnieć wiele kluczy wymienione w tym punkcie końcowym, identyfikowanych przez `kid` oświadczenia. Zawiera także nagłówek tokenu Identyfikacyjnego `kid` oświadczenia, która wskazuje, które z tych kluczy był używany do podpisywania tokenu Identyfikacyjnego.

Po zweryfikowaniu podpis tokenu Identyfikacyjnego, istnieje kilka oświadczenia, które należy sprawdzić. Na wystąpienie:

- Sprawdź poprawność `nonce` oświadczenia zapobiec atakom powtarzania tokenu. Wartość powinna być określona w żądaniu logowania.
- Sprawdź poprawność `aud` oświadczenia upewnić się, że tokenu Identyfikacyjnego został wystawiony dla aplikacji. Jej wartość powinna być identyfikator aplikacji.
- Sprawdź poprawność `iat` i `exp` oświadczeń upewnić się, że identyfikator tokenu nie wygasło.

Dostępne są także kilka więcej operacji sprawdzania poprawności, które należy wykonać. Operacje sprawdzania poprawności są szczegółowo opisane w [OpenID Connect podstawowej specyfikacji](https://openid.net/specs/openid-connect-core-1_0.html). Można również sprawdzić dodatkowe oświadczenia, zależnie od scenariusza. Niektórych typowych operacji sprawdzania poprawności obejmują:

- Zapewnienie, że użytkownik/organizacja zarejestrowała się dla aplikacji.
- Zapewnienie, że użytkownik ma odpowiednie zezwolenia/uprawnienia.
- Zapewnienie, że niektóre siły uwierzytelniania wystąpił, takich jak uwierzytelnianie wieloskładnikowe systemu Azure.

Po sprawdzeniu tokenu Identyfikacyjnego można rozpocząć sesji z użytkownikiem. Aby uzyskać informacje o użytkowniku w aplikacji, można użyć oświadczenia w tokenie identyfikator. Wykorzystuje te informacje obejmują wyświetlanie, rekordy i autoryzacji.

## <a name="get-a-token"></a>Pobierz token

Jeśli potrzebujesz aplikacji sieci web można uruchamiać tylko przepływy użytkownika, możesz pominąć kilka następnych sekcji. Poniższe sekcje mają zastosowanie tylko do sieci web, aplikacji, które muszą być uwierzytelnione wywołania internetowego interfejsu API i jednocześnie są chronione przez usługę Azure AD B2C.

Można zrealizować kod autoryzacji, które zostało zakupione (przy użyciu `response_type=code+id_token`) dla tokenu do żądanego zasobu, wysyłając `POST` limit czasu żądania `/token` punktu końcowego. Obecnie tylko zasób, jakiej może żądać token dla jest własnych aplikacji zaplecza interfejsu API sieci web. Konwencja do żądania tokenu służącego do siebie jest użyć własnego Identyfikatora klienta aplikacji jako zakres:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| p | Yes | Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. Nie można użyć przepływu innego użytkownika, w tym żądaniu. Ten parametr należy dodać do ciągu zapytania, a nie do treść wpisu. |
| client_id | Yes | Identyfikator aplikacji [witryny Azure portal](https://portal.azure.com/) przypisane do aplikacji. |
| grant_type | Yes | Typ przydział, który musi być `authorization_code` dla przepływ kodu autoryzacji. |
| scope | Nie | Rozdzielonej spacjami listy zakresów. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i pobieranie danych o użytkowniku w formie id_token parametrów. Może służyć do uzyskania tokenów do własnych aplikacji zaplecza interfejsu API sieci web, który jest reprezentowany przez ten sam identyfikator aplikacji, co klient. `offline_access` Zakresu oznacza, że aplikacja musi token odświeżania, rozszerzone dostępu do zasobów. |
| kod | Yes | Kod autoryzacji, który uzyskanych na początku przepływu użytkownika. |
| redirect_uri | Yes | `redirect_uri` Parametr aplikacji, na którym odebrano kod autoryzacji. |
| client_secret | Yes | Klucz tajny aplikacji, który został wygenerowany w [witryny Azure portal](https://portal.azure.com/). Wpis tajny aplikacji jest ważny artefakt. Przechowuj je bezpiecznie na serwerze. Zmień ten klucz tajny klienta w regularnych odstępach czasu. |

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
| --------- | ----------- |
| not_before | Czas, w którym token jest uznawany za ważny, w czasie uniksowym. |
| token_type | Wartość atrybutu typ tokenu. `Bearer` jest to jedyny typ, który jest obsługiwany. |
| access_token | Podpisany token JWT zażądano. |
| scope | Zakresy, dla których token jest prawidłowy. |
| expires_in | Długość czasu, przez jaki token dostępu jest prawidłowy (w sekundach). |
| refresh_token | Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu można uzyskać dodatkowe tokeny, po upływie bieżącego tokenu. Odświeżanie tokenów można zachować dostęp do zasobów przez dłuższy czas. Zakres `offline_access` należy użyć w autoryzacji i żądania tokenu w celu odbierania token odświeżania. |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typy występujące błędy. |
| error_description | Komunikat, który może ułatwić identyfikację przyczyny błędu uwierzytelniania. |

## <a name="use-the-token"></a>Użyj tokenu

Teraz, gdy token dostępu został pomyślnie uzyskana, można użyć tokenu w żądaniach wysyłanych do zaplecza internetowych interfejsów API, umieszczając go w `Authorization` nagłówka:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Odświeżenia tokenu

Identyfikator tokeny tracą ważność w krótkim czasie. Po wygaśnięciu, aby kontynuować, będzie mogła uzyskiwać dostęp do zasobów, należy odświeżyć tokenów. Można odświeżyć tokenu, przesyłając innego `POST` limit czasu żądania `/token` punktu końcowego. Tym razem zapewniają `refresh_token` parametr zamiast `code` parametru:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| p | Yes | Przepływ użytkownika, który został użyty do uzyskania oryginalnej token odświeżania. Nie można użyć przepływu innego użytkownika, w tym żądaniu. Ten parametr należy dodać do ciągu zapytania, a nie do treść wpisu. |
| client_id | Yes | Identyfikator aplikacji [witryny Azure portal](https://portal.azure.com/) przypisane do aplikacji. |
| grant_type | Yes | Typ przydział, który musi być tokenu odświeżania w tej części przepływ kodu autoryzacji. |
| scope | Nie | Rozdzielonej spacjami listy zakresów. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i pobieranie danych o użytkowniku w formie tokenów Identyfikatora. Może służyć do wysyłania tokenów do własnych aplikacji zaplecza interfejsu API sieci web, który jest reprezentowany przez ten sam identyfikator aplikacji, co klient. `offline_access` Zakresu oznacza, że aplikacja musi token odświeżania, rozszerzone dostępu do zasobów. |
| redirect_uri | Nie | `redirect_uri` Parametr aplikacji, na którym odebrano kod autoryzacji. |
| refresh_token | Yes | Oryginalny token odświeżania został uzyskany w drugiej części przepływu. `offline_access` Zakres musi być używany w autoryzacji i żądania tokenu, aby można było odebrać token odświeżania. |
| client_secret | Yes | Klucz tajny aplikacji, który został wygenerowany w [witryny Azure portal](https://portal.azure.com/). Wpis tajny aplikacji jest ważny artefakt. Przechowuj je bezpiecznie na serwerze. Zmień ten klucz tajny klienta w regularnych odstępach czasu. |

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
| --------- | ----------- |
| not_before | Czas, w którym token jest uznawany za ważny, w czasie uniksowym. |
| token_type | Wartość atrybutu typ tokenu. `Bearer` jest to jedyny typ, który jest obsługiwany. |
| access_token | Podpisany token JWT, którego zażądano. |
| scope | Zakres, dla którego token jest prawidłowy. |
| expires_in | Długość czasu, przez jaki token dostępu jest prawidłowy (w sekundach). |
| refresh_token | Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu można uzyskać dodatkowe tokeny, po upływie bieżącego tokenu. Odświeżanie tokenów można zachować dostęp do zasobów przez dłuższy czas. |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typy występujące błędy. |
| error_description | Komunikat, który może ułatwić identyfikację przyczyny błędu uwierzytelniania. |

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Utworzyć użytkownika z aplikacji, nie jest wystarczająco dużo, wyczyść pliki cookie w aplikacji lub w przeciwnym razie kończenie sesji użytkownika. Przekierowanie użytkownika do usługi Azure AD B2C, aby się wylogować. W przypadku awarii to zrobić, użytkownik może być możliwe ponowne uwierzytelnianie do aplikacji bez konieczności ponownego wprowadzania poświadczeń.

Możesz po prostu przekierować użytkownika do `end_session` punktu końcowego, który znajduje się w dokumencie metadanych OpenID Connect, opisanych wcześniej:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| p | Yes | Przepływ użytkownika, którego chcesz użyć do logowania użytkownika z aplikacji. |
| post_logout_redirect_uri | Nie | Adres URL, który użytkownik powinno zostać przekierowane do po wylogowaniu się pomyślnie. Jeśli nie jest dołączony, program Azure AD B2C jest wyświetlana nazwa użytkownika zostanie wyświetlony komunikat ogólny. |

Kierowanie użytkowników do `end_session` punktu końcowego czyści część użytkownika pojedynczego logowania jednokrotnego stanu za pomocą usługi Azure AD B2C, ale nie Zaloguj do się użytkownika z sesji dostawcy tożsamości społecznościowych. Jeśli użytkownik wybierze tego samego dostawcy tożsamości, podczas późniejszego logowania, ich jest ponownie uwierzytelniane — bez konieczności wprowadzania swoich poświadczeń. Jeśli użytkownik chce Wyloguj się z aplikacji, nie niekoniecznie oznacza, że mają być Wyloguj się z konta w serwisie Facebook. Jednak jeśli używane są konta lokalnego, sesja użytkownika kończy się poprawnie.

