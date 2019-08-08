---
title: Logowanie w sieci Web za pomocą OpenID Connect Connect-Azure Active Directory B2C | Microsoft Docs
description: Twórz aplikacje sieci Web przy użyciu protokołu uwierzytelniania OpenID Connect Connect w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6b5157a71ce15d4dfd199b6826be22235e61df97
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848546"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Logowanie w sieci Web za pomocą OpenID Connect Connect in Azure Active Directory B2C

OpenID Connect Connect to protokół uwierzytelniania oparty na protokole OAuth 2,0, który może służyć do bezpiecznego podpisywania użytkowników w aplikacjach sieci Web. Korzystając z implementacji Azure Active Directory B2C (Azure AD B2C) OpenID Connect Connect, możesz w swoich aplikacjach sieci Web utworzyć konto, zalogować się i inne funkcje zarządzania tożsamościami, aby Azure Active Directory (Azure AD). W tym przewodniku pokazano, jak to zrobić w sposób niezależny od języka. Opisano w nim, jak wysyłać i odbierać komunikaty HTTP bez używania bibliotek typu open-source.

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół *autoryzacji* OAuth 2,0 do użycia jako protokół *uwierzytelniania* . Ten protokół uwierzytelniania umożliwia logowanie jednokrotne. Wprowadza koncepcję *tokenu identyfikatora*, dzięki czemu klient może weryfikować tożsamość użytkownika i uzyskać podstawowe informacje o profilu użytkownika.

Ponieważ rozszerza on protokół OAuth 2,0, umożliwia również aplikacjom bezpieczne pozyskiwanie *tokenów dostępu*. Za pomocą tokenów dostępu można uzyskać dostęp do zasobów zabezpieczonych przez [serwer autoryzacji](active-directory-b2c-reference-protocols.md). Połączenie OpenID Connect jest zalecane, jeśli tworzysz aplikację sieci Web, która jest hostowana na serwerze i jest dostępna za pomocą przeglądarki. Jeśli chcesz dodać Zarządzanie tożsamościami do aplikacji mobilnych lub klasycznych przy użyciu Azure AD B2C, Użyj protokołu [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) zamiast opcji OpenID Connect Connect. Aby uzyskać więcej informacji na temat tokenów, zobacz [Omówienie tokenów w Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C rozszerza standardowy protokół połączenia OpenID Connect, aby wykonywać więcej niż proste uwierzytelnianie i autoryzację. Wprowadza [parametr przepływu użytkownika](active-directory-b2c-reference-policies.md), który umożliwia korzystanie z programu OpenID Connect Connect w celu dodawania środowisk użytkownika do aplikacji, takich jak rejestrowanie, logowanie i Zarządzanie profilem.

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania

Gdy aplikacja sieci Web wymaga uwierzytelnienia użytkownika i uruchomienia przepływu użytkownika, może kierować użytkownika do `/authorize` punktu końcowego. Użytkownik wykonuje akcję w zależności od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia wymagane do uzyskania od użytkownika w `scope` parametrze i przepływ użytkownika do uruchomienia `p` w parametrze. Trzy przykłady są podane w poniższych sekcjach (z podziałami wierszy na potrzeby czytelności), z których każdy korzysta z innego przepływu użytkownika. Aby poznać sposób działania poszczególnych żądań, spróbuj wkleić żądanie do przeglądarki i uruchomić ją. Możesz zamienić `fabrikamb2c` na nazwę dzierżawy, jeśli istnieje, i utworzyć przepływ użytkownika. Należy również zamienić `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`. Zastąp ten identyfikator klienta IDENTYFIKATORem aplikacji utworzonej przez Ciebie rejestracji aplikacji. Należy również zmienić nazwę `b2c_1_sign_in` zasad na nazwę zasady, która znajduje się w dzierżawie. 

#### <a name="use-a-sign-in-user-flow"></a>Korzystanie z przepływu użytkownika logowania
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

#### <a name="use-a-sign-up-user-flow"></a>Korzystanie z przepływu użytkownika rejestracji
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

#### <a name="use-an-edit-profile-user-flow"></a>Korzystanie z przepływu edycji profilu użytkownika
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
| client_id | Yes | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| response_type | Yes | Musi zawierać token identyfikatora dla OpenID Connect Connect. Jeśli aplikacja sieci Web wymaga również tokenów do wywoływania internetowego interfejsu API, można użyć `code+id_token`programu. |
| redirect_uri | Nie | `redirect_uri` Parametr aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednemu z `redirect_uri` parametrów zarejestrowanych w Azure Portal, z tą różnicą, że musi on być zakodowany w adresie URL. |
| scope | Yes | Rozdzielana spacjami lista zakresów. `openid` Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci tokenów identyfikatorów. `offline_access` Zakres jest opcjonalny dla aplikacji sieci Web. Wskazuje, że aplikacja będzie potrzebować *tokenu odświeżania* na potrzeby rozszerzonego dostępu do zasobów. |
| response_mode | Nie | Metoda, która jest używana do wysyłania podanego kodu autoryzacji z powrotem do aplikacji. Może to być albo `query`, `form_post`, lub `fragment`.  W celu uzyskania najlepszych zabezpieczeń zaleca się używanie trybu odpowiedzi.`form_post` |
| state | Nie | Wartość zawarta w żądaniu, która jest również zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu zapobiegania atakom na fałszerstwo żądań między witrynami. Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, na przykład na stronie, w której znajdowały się. |
| nonce | Tak | Wartość dołączona do żądania (wygenerowanego przez aplikację), która jest uwzględniona w tokenie zwracanego identyfikatora jako jako element Claim. Aplikacja może następnie zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Wartość jest zazwyczaj losowym unikatowym ciągiem, który może służyć do identyfikowania pochodzenia żądania. |
| p | Yes | Przepływ użytkownika, który jest uruchamiany. Jest to nazwa przepływu użytkownika utworzonego w dzierżawie Azure AD B2C. Nazwa przepływu użytkownika powinna zaczynać `b2c\_1\_`się od. |
| pytać | Nie | Typ interakcji z użytkownikiem, która jest wymagana. Jedyną prawidłową wartością w tym momencie jest `login`to, co wymusza użytkownikowi wprowadzanie poświadczeń dla tego żądania. |

W tym momencie użytkownik zostanie poproszony o ukończenie przepływu pracy. Użytkownik może wprowadzić nazwę użytkownika i hasło, zalogować się przy użyciu tożsamości społecznościowej lub zarejestrować się w katalogu. Może istnieć jakakolwiek inna liczba kroków w zależności od sposobu definiowania przepływu użytkownika.

Gdy użytkownik ukończy przepływ użytkownika, odpowiedź jest zwracana do aplikacji w wskazanym `redirect_uri` parametrze przy użyciu metody określonej `response_mode` w parametrze. Odpowiedź jest taka sama dla każdego z powyższych przypadków, niezależnie od przepływu użytkownika.

Pomyślna odpowiedź `response_mode=fragment` przy użyciu powinna wyglądać następująco:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| id_token | Token identyfikatora, którego żądała aplikacja. Możesz użyć tokenu identyfikatora, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. |
| code | Kod autoryzacji, którego żądał aplikacja, jeśli został użyty `response_type=code+id_token`. Aplikacja może używać kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji zwykle wygasają po około 10 minutach. |
| state | `state` Jeśli parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

Do `redirect_uri` parametru można także wysyłać odpowiedzi na błędy, aby aplikacja mogła je odpowiednio obsłużyć:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| błąd | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Określony komunikat o błędzie, który może pomóc identyfikować główną przyczynę błędu uwierzytelniania. |
| state | `state` Jeśli parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

## <a name="validate-the-id-token"></a>Weryfikowanie tokenu identyfikatora

Tylko otrzymanie tokenu identyfikatora jest za mało do uwierzytelnienia użytkownika. Sprawdź poprawność podpisu tokenu identyfikatora i sprawdź oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Azure AD B2C używa [tokenów sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe. Istnieje wiele bibliotek typu "open source", które są dostępne do sprawdzania poprawności JWTs, w zależności od języka preferencji. Zalecamy Eksplorowanie tych opcji zamiast implementowania własnej logiki walidacji. 

Azure AD B2C ma punkt końcowy metadanych połączenia OpenID Connect, który umożliwia aplikacji pobieranie informacji o Azure AD B2C w czasie wykonywania. Te informacje obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Istnieje dokument metadanych JSON dla każdego przepływu użytkowników w dzierżawie usługi B2C. Na przykład dokument metadanych dla `b2c_1_sign_in` przepływu użytkownika w programie `fabrikamb2c.onmicrosoft.com` znajduje się w lokalizacji:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedną z właściwości tego dokumentu konfiguracji jest `jwks_uri`, której wartość tego samego przepływu użytkownika byłaby:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Aby określić, który przepływ użytkownika był używany podczas podpisywania tokenu identyfikatora (i skąd uzyskać metadane), dostępne są dwie opcje. Najpierw nazwa przepływu użytkownika jest uwzględniana w `acr` podaniu w tokenie identyfikatora. Innym rozwiązaniem jest zakodowanie przepływu użytkownika w wartości `state` parametru podczas wysyłania żądania, a następnie dekodowanie go w celu określenia, który przepływ użytkownika został użyty. Każda metoda jest prawidłowa.

Po uzyskaniu dokumentu metadanych z punktu końcowego metadanych OpenID Connect Connect można użyć kluczy publicznych RSA 256 do weryfikacji podpisu tokenu identyfikatora. W tym punkcie końcowym mogą znajdować się wiele kluczy, z których `kid` każdy jest identyfikowany przez element Claims. Nagłówek tokenu identyfikatora zawiera również element `kid` Claim wskazujący, który z tych kluczy został użyty do podpisania tokenu identyfikatora.

Aby sprawdzić tokeny z Azure AD B2C, należy wygenerować klucz publiczny przy użyciu wykładnika (e) i modulo (n). Należy określić, jak to zrobić w odpowiednim języku programowania. Oficjalną dokumentację dotyczącą generowania klucza publicznego przy użyciu protokołu RSA można znaleźć tutaj: https://tools.ietf.org/html/rfc3447#section-3.1

Po sprawdzeniu poprawności sygnatury tokenu identyfikatora istnieje kilka oświadczeń, które należy zweryfikować. Na wystąpienie:

- Sprawdź poprawność roszczeń, `nonce` aby zapobiec atakom metodą powtórzeń tokenu. Jej wartość powinna być określona w żądaniu logowania.
- Sprawdź poprawność `aud` roszczeń, aby upewnić się, że token identyfikatora został wystawiony dla aplikacji. Jej wartość powinna być IDENTYFIKATORem aplikacji.
- Sprawdź poprawność `exp`ioświadczenia , aby upewnić się, że token identyfikatora nie wygasł. `iat`

Istnieje również kilka dodatkowych poprawń, które należy wykonać. Walidacje są szczegółowo opisane w [specyfikacji OpenID Connect Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń, w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

- Upewnienie się, że użytkownik/organizacja zarejestrowali się w aplikacji.
- Upewnienie się, że użytkownik ma odpowiednie uprawnienia/autoryzacja.
- Upewnienie się, że wystąpiła pewna siła uwierzytelniania, taka jak uwierzytelnianie wieloskładnikowe systemu Azure.

Po sprawdzeniu poprawności tokenu identyfikatora można rozpocząć sesję z użytkownikiem. Oświadczenia w tokenie identyfikatora mogą służyć do uzyskiwania informacji o użytkowniku w aplikacji. Program używa tych informacji, takich jak wyświetlanie, rekordy i autoryzacja.

## <a name="get-a-token"></a>Pobierz token

Jeśli potrzebujesz aplikacji sieci Web tylko do uruchamiania przepływów użytkowników, możesz pominąć kilka następnych sekcji. Te sekcje mają zastosowanie tylko do aplikacji sieci Web, które muszą wykonać uwierzytelnione wywołania do internetowego interfejsu API, a także są chronione przez Azure AD B2C.

Możesz zrealizować pobrany kod autoryzacji (za pomocą `response_type=code+id_token`) dla tokenu do żądanego zasobu, `POST` wysyłając żądanie do `/token` punktu końcowego. W Azure AD B2C można zażądać [tokenów dostępu dla innych interfejsów API](active-directory-b2c-access-tokens.md#request-a-token) , jak zwykle, określając ich zakresy w żądaniu.

Możesz również zażądać tokenu dostępu dla własnego internetowego interfejsu API zaplecza w ramach Konwencji przy użyciu identyfikatora klienta aplikacji jako żądanego zakresu (co spowoduje użycie tokenu dostępu z IDENTYFIKATORem klienta jako "odbiorcy"):

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| p | Tak | Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. Nie można użyć innego przepływu użytkownika w tym żądaniu. Dodaj ten parametr do ciągu zapytania, a nie do treści wpisu. |
| client_id | Tak | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| grant_type | Tak | Typ dotacji, który musi być `authorization_code` przeznaczony dla przepływu kodu autoryzacji. |
| scope | Nie | Rozdzielana spacjami lista zakresów. `openid` Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci parametrów id_token. Może służyć do uzyskiwania tokenów dla własnego interfejsu API zaplecza aplikacji, który jest reprezentowany przez ten sam identyfikator aplikacji co klient. `offline_access` Zakres wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby rozszerzonego dostępu do zasobów. |
| code | Tak | Kod autoryzacji uzyskany na początku przepływu użytkownika. |
| redirect_uri | Yes | `redirect_uri` Parametr aplikacji, w której został otrzymany kod autoryzacji. |
| client_secret | Tak | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Ten klucz tajny aplikacji jest ważnym artefaktem zabezpieczeń. Należy bezpiecznie przechowywać ją na serwerze. Należy okresowo zmieniać ten klucz tajny klienta. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

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
| not_before | Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type | Wartość typu tokenu. `Bearer`jest jedynym obsługiwanym typem. |
| access_token | Zażądano podpisanego tokenu JWT. |
| scope | Zakresy, dla których token jest prawidłowy. |
| expires_in | Długość czasu ważności tokenu dostępu (w sekundach). |
| refresh_token | Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. Zakres `offline_access` musi być używany w żądaniach autoryzacji i tokenu, aby można było odebrać token odświeżania. |

Odpowiedzi na błędy wyglądają następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| błąd | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Komunikat, który może pomóc identyfikować główną przyczynę błędu uwierzytelniania. |

## <a name="use-the-token"></a>Użyj tokenu

Po pomyślnym pobraniu tokenu dostępu można użyć tokenu w żądaniach do interfejsów API sieci Web zaplecza, dołączając je do `Authorization` nagłówka:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Odświeżanie tokenu

Tokeny identyfikatora wygasają w krótkim czasie. Odśwież tokeny po wygaśnięciu, aby nadal mogły uzyskiwać dostęp do zasobów. Można odświeżyć token, przesyłając kolejne `POST` żądanie `/token` do punktu końcowego. Tym razem Podaj `refresh_token` parametr zamiast `code` parametru:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| p | Tak | Przepływ użytkownika, który został użyty do uzyskania oryginalnego tokenu odświeżania. Nie można użyć innego przepływu użytkownika w tym żądaniu. Dodaj ten parametr do ciągu zapytania, a nie do treści wpisu. |
| client_id | Tak | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| grant_type | Tak | Typ dotacji, który musi być tokenem odświeżania dla tej części przepływu kodu autoryzacji. |
| scope | Nie | Rozdzielana spacjami lista zakresów. `openid` Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci tokenów identyfikatorów. Może służyć do wysyłania tokenów do własnego interfejsu API zaplecza aplikacji, który jest reprezentowany przez ten sam identyfikator aplikacji co klient. `offline_access` Zakres wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby rozszerzonego dostępu do zasobów. |
| redirect_uri | Nie | `redirect_uri` Parametr aplikacji, w której został otrzymany kod autoryzacji. |
| refresh_token | Tak | Oryginalny token odświeżania, który został pobrany w drugiej części przepływu. `offline_access` Zakres musi być używany w żądaniach autoryzacji i tokenu, aby można było odebrać token odświeżania. |
| client_secret | Tak | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Ten klucz tajny aplikacji jest ważnym artefaktem zabezpieczeń. Należy bezpiecznie przechowywać ją na serwerze. Należy okresowo zmieniać ten klucz tajny klienta. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

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
| not_before | Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type | Wartość typu tokenu. `Bearer`jest jedynym obsługiwanym typem. |
| access_token | Zażądano podpisanego tokenu JWT. |
| scope | Zakres, dla którego token jest prawidłowy. |
| expires_in | Długość czasu ważności tokenu dostępu (w sekundach). |
| refresh_token | Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. |

Odpowiedzi na błędy wyglądają następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| błąd | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Komunikat, który może pomóc identyfikować główną przyczynę błędu uwierzytelniania. |

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Jeśli chcesz podpisać użytkownika poza aplikacją, nie wystarczy wyczyścić plików cookie aplikacji lub zakończyć sesję z użytkownikiem. Przekieruj użytkownika do Azure AD B2C, aby się wylogować. W przeciwnym razie użytkownik może być w stanie ponownie uwierzytelnić się w aplikacji bez konieczności ponownego wprowadzania poświadczeń.

Można po prostu przekierować użytkownika do `end_session` punktu końcowego, który jest wymieniony w dokumencie OpenID Connect Connect Metadata opisanego wcześniej:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| p | Yes | Przepływ użytkownika, którego chcesz użyć do podpisania użytkownika z poziomu aplikacji. |
| post_logout_redirect_uri | Nie | Adres URL, do którego użytkownik powinien zostać przekierowany po pomyślnym wylogowaniu. Jeśli ta wartość nie jest uwzględniona, Azure AD B2C pokazuje, że użytkownik jest komunikatem ogólnym. |

Kierowanie użytkownika do `end_session` punktu końcowego czyści część stanu logowania jednokrotnego użytkownika przy użyciu Azure AD B2C, ale nie podpisuje użytkownika poza sesją dostawcy tożsamości (Social Identity Provider). Jeśli użytkownik wybierze ten sam dostawcy tożsamości podczas kolejnego logowania, zostanie ponownie uwierzytelniony, bez wprowadzania poświadczeń. Jeśli użytkownik chce wylogować się z aplikacji, nie musi to oznaczać, że chce się wylogować z konta w serwisie Facebook. Jeśli jednak są używane konta lokalne, sesja użytkownika zostanie zakończona prawidłowo.

