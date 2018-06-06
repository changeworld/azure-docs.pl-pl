---
title: Sieci Web logowania z OpenID Connect w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web przy użyciu usługi Azure Active Directory implementacji protokołu uwierzytelniania OpenID Connect.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ad4de055e4f951beb441af90c37ada11ad0abfb0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711294"
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Usługa Azure Active Directory B2C: Sieci Web logowania z OpenID Connect
OpenID Connect to protokół uwierzytelniania, rozszerzający OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników w aplikacji sieci web. Za pomocą usługi Azure Active Directory B2C (Azure AD B2C) wdrażania protokołu OpenID Connect, można zewnętrzny rejestrację, logowanie i wykonywania innych zarządzania tożsamościami w aplikacji sieci web do usługi Azure Active Directory (Azure AD). Ten przewodnik przedstawia, jak to zrobić w sposób niezależny od języka. Przedstawiono sposób wysyłania i odbierania wiadomości HTTP bez przy użyciu dowolnej z naszych bibliotekach open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół OAuth 2.0 *autoryzacji* protokół do użycia jako *uwierzytelniania* protokołu. Dzięki temu można wykonać logowanie jednokrotne przy użyciu uwierzytelniania OAuth. Ona pojęcia związane z *token Identyfikatora*, który jest token zabezpieczający, który umożliwia klientowi weryfikacji tożsamości użytkownika i uzyskać podstawowych informacji o profilu użytkownika.

Ponieważ rozszerza on OAuth 2.0, umożliwia również aplikacji można bezpiecznie uzyskać *tokenów dostępu*. Można użyć access_tokens dostępu do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-b2c-reference-protocols.md#the-basics). Zalecamy OpenID Connect, jeśli tworzysz aplikację sieci web jest hostowany na serwerze i dostępne za pośrednictwem przeglądarki. Jeśli chcesz dodać Zarządzanie tożsamościami do aplikacji mobilnych lub komputerowych przy użyciu usługi Azure AD B2C, należy użyć [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) zamiast OpenID Connect.

Usługa Azure AD B2C rozszerza standardowego protokołu OpenID Connect do więcej niż prostego uwierzytelniania i autoryzacji. Wprowadza ona [parametru zasady](active-directory-b2c-reference-policies.md), co umożliwia przy użyciu protokołu OpenID Connect Dodaj wrażenia użytkowników — takie jak rejestracji, logowania i zarządzania profilami — do aplikacji. W tym miejscu możemy opisano sposób korzystania z OpenID Connect i zasad do wdrożenia każdego z tych środowisk w aplikacji sieci web. Możemy również opisano sposób uzyskiwania tokenów dostępu do uzyskiwania dostępu do interfejsów API sieci web.

Żądania HTTP przykład w następnej sekcji przy użyciu katalogu B2C naszej próbki fabrikamb2c.onmicrosoft.com, jak również naszej aplikacji przykładowej https://aadb2cplayground.azurewebsites.neti zasad. Można go dowolnie wypróbowanie żądania samodzielnie przy użyciu tych wartości, lub można je zastąpić własnymi.
Dowiedz się, jak [uzyskać własne dzierżawy B2C, aplikacji i zasad](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Wysyłanie żądania uwierzytelniania
Gdy aplikacja sieci web musi uwierzytelnić użytkownika i wykonywanie zasad, można kierować użytkownikowi `/authorize` punktu końcowego. Jest to interakcyjne część przepływu, gdy użytkownik wykona akcję w zależności od zasad.

W tym żądaniu klient wskazuje uprawnienia, które należy uzyskać użytkownika w `scope` parametr i zasad do wykonania w `p` parametru. Trzy przedstawione przykłady w poniższych sekcjach (podziałami wierszy dla czytelności), używając inne zasady. Aby uzyskać pewne pojęcie o sposób działania każdego żądania, spróbuj wkleić żądania do przeglądarki i uruchomić go.

#### <a name="use-a-sign-in-policy"></a>Użyj zasad logowania
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Użyj zasad rejestracji
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Użyj zasad edycji profilu
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu Azure](https://portal.azure.com/) przypisany do aplikacji. |
| response_type |Wymagane |Typ odpowiedzi muszą zawierać token Identyfikatora dla OpenID Connect. Jeśli aplikacja sieci web musi również tokenów do wywoływania interfejsu API sieci web, możesz użyć `code+id_token`, jak firma Microsoft zostało wykonane w tym miejscu. |
| redirect_uri |Zalecane |`redirect_uri` Parametr aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Musi dokładnie odpowiadać jeden z `redirect_uri` parametrów, które zarejestrowano w portalu, z wyjątkiem tego, że musi być zakodowane w adresie URL. |
| scope |Wymagane |Rozdzieloną spacjami listę zakresów. Wartość pojedynczy zakres wskazuje do usługi Azure AD, oba uprawnienia, które są żądane. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i Pobierz dane o użytkowniku w formie tokeny Identyfikatora, (powszechnym na tym w dalszej części tego artykułu). `offline_access` Zakres jest opcjonalne dla aplikacji sieci web. Wskazuje, że aplikacja, należy *token odświeżania* długotrwałe dostępu do zasobów. |
| response_mode |Zalecane |Metody, które mają być używane do wysyłania wynikowy kod autoryzacji z powrotem do aplikacji. Może być albo `query`, `form_post`, lub `fragment`.  `form_post` Najlepiej zalecany jest tryb odpowiedzi. |
| state |Zalecane |Wartość zawarte w żądaniu, który jest także zwracany w odpowiedzi tokenu. Można go ciąg ma zawartość. Losowo generowany unikatową wartość jest zwykle używany zapobiegania fałszerstwie żądania międzywitrynowego. Stan służy także do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony, które były na. |
| Identyfikator jednorazowy |Wymagane |Wartość zawarte w żądaniu (wygenerowany przez aplikację), która zostanie uwzględniona w jego identyfikator tokenu jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby ograniczyć ataków powtórzeń tokenów. Wartość jest zazwyczaj losowego unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| p |Wymagane |Zasady, które zostaną wykonane. Jest to nazwa zasady, która jest tworzona w dzierżawie usługi B2C. Wartość Nazwa zasad powinny rozpoczynać się od `b2c\_1\_`. Dowiedz się więcej o zasadach i [rozszerzona platforma zasad](active-directory-b2c-reference-policies.md). |
| wiersz |Optional (Opcjonalność) |Typ interakcji z użytkownikiem, który jest wymagany. Jest jedyną poprawną wartością w tej chwili `login`, która wymusza na użytkowniku, aby wprowadzić swoje poświadczenia dla tego żądania. Logowanie jednokrotne nie zacznie obowiązywać. |

W tym momencie użytkownik jest proszony o ukończenia przepływu pracy zasad. Może to obejmować wprowadzić swoją nazwę użytkownika i hasło, użytkownik zalogować społecznościowych tożsamości, podczas tworzenia katalogu lub dowolną liczbę kroków w zależności od tego, jak zdefiniowano zasad.

Po wypełnieniu zasad przez użytkownika, usługi Azure AD zwraca odpowiedź do aplikacji na wskazany `redirect_uri` parametru za pomocą metody określonej w `response_mode` parametru. Odpowiedź jest taka sama dla każdego z powyższych przypadków, niezależnie od zasady, która jest wykonywana.

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` powinien wyglądać tak:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| żądaniu |Token ID żądanej aplikacji. Token identyfikator służy do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. Więcej informacji na temat identyfikator tokeny i ich zawartość znajdują się w [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| Kod |Kod autoryzacji żądanej aplikacji, jeśli używasz `response_type=code+id_token`. Aplikację można użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji są bardzo krótkim okresie. Zazwyczaj wygasną po około 10 minut. |
| state |Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne. |

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` parametru, dzięki czemu aplikacja może je odpowiednią obsługę:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kod błędu, który może służyć do klasyfikowania typy błędów, które występować i który może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |
| state |Zobacz pełny opis w pierwszej tabeli w tej sekcji. Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne. |

## <a name="validate-the-id-token"></a>Zweryfikuj token Identyfikatora
Tylko odbieranie identyfikator tokenu jest niewystarczająca do uwierzytelnienia użytkownika. Musisz zweryfikować podpisu tokenu identyfikator i sprawdzić oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Używa usługi Azure AD B2C [tokenów sieci Web JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Istnieje wiele bibliotekach open source dostępnych sprawdzania poprawności tokenów Jwt, w zależności od języka preferencji. Firma Microsoft zaleca eksploracji te opcje, a nie implementacji logiki weryfikacji. Informacje w tym miejscu będzie przydatna w ustaleniem, jak poprawnie używać tych bibliotek.

Usługa Azure AD B2C ma OpenID Connect punktu końcowego metadanych, dzięki czemu aplikacja może pobrać informacji na temat usługi Azure AD B2C w czasie wykonywania. Informacje te obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Brak dokument metadanych JSON dla każdej zasady w dzierżawie usługi B2C. Na przykład dokument metadanych dla `b2c_1_sign_in` zasad w `fabrikamb2c.onmicrosoft.com` znajduje się pod adresem:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z właściwości tego dokumentu konfiguracji jest `jwks_uri`, którego wartość jest takie same zasady:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Aby określić zasady, których użyto w identyfikatorze podpisywania tokenu i skąd można pobrać metadanych, masz dwie opcje. Po pierwsze, nazwa zasady jest dołączona do `acr` oświadczenia w tokenie identyfikator. Informacje o tym, jak można przeanalizować oświadczeń z tokenem identyfikator, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). Drugą opcją jest do kodowania zasad wartości `state` parametr podczas wystawiania żądania, a następnie dekodowania go, aby określić zasady, które zostało użyte. Każda metoda jest prawidłowa.

Po zostały nabyte dokument metadanych z punktem końcowym metadanych OpenID Connect, możesz użyć klucze publiczne RSA 256 (które znajdują się w tym punkcie końcowym) można zweryfikować podpisu tokenu identyfikator. Może istnieć wiele kluczy wymienione w tym punkcie końcowym w dowolny punkt w czasie, identyfikowanych przez `kid` oświadczeń. Zawiera także nagłówka Identyfikatora tokenu `kid` oświadczeń, która wskazuje, które z tych kluczy został użyty do podpisania token Identyfikatora. Aby uzyskać więcej informacji, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md) (sekcję [sprawdzanie poprawności tokenów](active-directory-b2c-reference-tokens.md#token-validation), w szczególności).
<!--TODO: Improve the information on this-->

Po upewnieniu podpisu tokenu identyfikator, istnieje kilka oświadczenia, które należy sprawdzić. Na wystąpienie:

* Należy sprawdzić, czy `nonce` oświadczeń zapobiec atakom powtórzeń tokenów. Jego wartość musi być określony w żądaniu logowania.
* Należy sprawdzić, czy `aud` oświadczeń upewnić się, czy identyfikator token został wystawiony dla aplikacji. Jego wartość powinna być identyfikator aplikacji.
* Należy sprawdzić, czy `iat` i `exp` oświadczeń upewnić się, że identyfikator token nie wygasł.

Istnieje kilka więcej operacji sprawdzania poprawności, które należy wykonać. Te ustawienia zostały opisane szczegółowo w [OpenID Connect podstawowych specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html).  Można również sprawdzić dodatkowe roszczenia, w zależności od danego scenariusza. Niektóre typowe operacje sprawdzania poprawności obejmują:

* Zapewnienie, że dla aplikacji po zarejestrowaniu użytkownika i/lub organizacji.
* Zapewnienie, że użytkownik ma odpowiednie autoryzacji/uprawnienia.
* Zapewnienie, że niektóre siły uwierzytelniania wystąpił, takich jak uwierzytelnianie wieloskładnikowe Azure.

Aby uzyskać więcej informacji na oświadczenia w tokenie identyfikator, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po sprawdzeniu poprawności tokenu identyfikator, można rozpocząć sesji z użytkownikiem. Oświadczenia w tokenie identyfikator służy do uzyskiwania informacji o użytkowniku w aplikacji. Zastosowania te informacje obejmują ekranu, rekordów i autoryzacji.

## <a name="get-a-token"></a>Uzyskaj token
Jeśli potrzebujesz aplikacji sieci web można wykonać tylko zasady można pominąć kilka kolejnych sekcjach. Sekcje te są stosowane tylko do sieci web, aplikacje, które należy podjąć uwierzytelnione wywołania interfejsu API sieci web i są chronione przez usługę Azure AD B2C.

Możesz zrealizować kod autoryzacji, które zostało zakupione (przy użyciu `response_type=code+id_token`) dla tokenu do żądanego zasobu, wysyłając `POST` żądanie `/token` punktu końcowego. Obecnie tylko zasób, jakiej może żądać tokenu dla jest własnych aplikacji zaplecza interfejsu API sieci web. Konwencja dla żądania tokenu do siebie ma użyć Identyfikatora klienta aplikacji jako zakres:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Zasady, które zostało użyte do uzyskania kod autoryzacji. Nie można użyć różnych zasad w tym żądaniu. Należy pamiętać, Dodaj ten parametr ciągu zapytania nie `POST` treści. |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu Azure](https://portal.azure.com/) przypisany do aplikacji. |
| Typ grant_type |Wymagane |Typ grant, który musi być `authorization_code` dla przepływu kodu autoryzacji. |
| scope |Zalecane |Rozdzieloną spacjami listę zakresów. Wartość pojedynczy zakres wskazuje do usługi Azure AD, oba uprawnienia, które są żądane. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i Pobierz dane o użytkowniku w formie parametrów w żądaniu. Może służyć do uzyskania tokenów do własnych aplikacji zaplecza interfejsu API sieci web, która jest reprezentowana przez ten sam identyfikator aplikacji, co klient. `offline_access` Zakresu wskazuje, że aplikacji będą potrzebne token odświeżania długotrwałe dostępu do zasobów. |
| Kod |Wymagane |Uzyskanego w pierwszego etap przepływu kodu autoryzacji. |
| redirect_uri |Wymagane |`redirect_uri` Parametr aplikacji, w którym odebrano kod autoryzacji. |
| client_secret |Wymagane |Klucz tajny aplikacji, która wygenerowała w [portalu Azure](https://portal.azure.com/). Ten klucz tajny aplikacji jest ważny artefaktu. Użytkownik powinien Zapisz w bezpiecznej lokalizacji na serwerze. Należy również Obróć ten klucz tajny klienta w regularnych odstępach czasu. |

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
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest `Bearer`. |
| access_token |Podpisany token JWT żądana. |
| scope |Zakresy, dla których token jest prawidłowy. Mogą być one używane do buforowania tokeny na potrzeby późniejszego użycia. |
| expires_in |Długość czasu, przez który token dostępu jest nieprawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać ten token, aby uzyskać dodatkowe tokeny, po wygaśnięciu tokenu bieżącego. Odśwież tokeny są to długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zapoznaj się [odwołania do tokenu usługi B2C](active-directory-b2c-reference-tokens.md). Uwaga musi użycie zakresu `offline_access` autoryzacji i żądania tokenów w celu odbierania token odświeżania. |

Błąd odpowiedzi następującą postać:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kod błędu, który może służyć do klasyfikowania typy błędów, które występować i który może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |

## <a name="use-the-token"></a>Użyj tokenu
Teraz, zostały pomyślnie uzyskano tokenu dostępu, można użyć tokenu w żądaniach na danym zapleczu interfejsów API sieci web przez włączenie jej w `Authorization` nagłówka:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Token odświeżania
Identyfikator tokeny są krótkim okresie. Należy je odświeżyć, po wygaśnięciu, aby kontynuować, będą mogli uzyskać dostęp do zasobów. Możesz to zrobić poprzez przesłanie innego `POST` żądanie `/token` punktu końcowego. Podaj w tej chwili `refresh_token` parametru zamiast `code` parametru:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Wymagane | Opis |
| --- | --- | --- |
| p |Wymagane |Zasady, które zostało użyte do uzyskania oryginalnego token odświeżania. Nie można użyć różnych zasad w tym żądaniu. Należy pamiętać, Dodaj ten parametr, ciąg zapytania, a nie do treść wpisu. |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu Azure](https://portal.azure.com/) przypisany do aplikacji. |
| Typ grant_type |Wymagane |Typ grant, który musi być token odświeżania dla tego etap przepływu kodu autoryzacji. |
| scope |Zalecane |Rozdzieloną spacjami listę zakresów. Wartość pojedynczy zakres wskazuje do usługi Azure AD, oba uprawnienia, które są żądane. `openid` Uprawnienia, aby zalogować użytkownika i Pobierz dane o użytkowniku w formie tokenów identyfikator wskazuje zakres. Może służyć do uzyskania tokenów do własnych aplikacji zaplecza interfejsu API sieci web, która jest reprezentowana przez ten sam identyfikator aplikacji, co klient. `offline_access` Zakresu wskazuje, że aplikacji będą potrzebne token odświeżania długotrwałe dostępu do zasobów. |
| redirect_uri |Zalecane |`redirect_uri` Parametr aplikacji, w którym odebrano kod autoryzacji. |
| refresh_token |Wymagane |Oryginalny token odświeżania uzyskanego w drugi etap przepływu. Uwaga musi użycie zakresu `offline_access` autoryzacji i żądania tokenów w celu odbierania token odświeżania. |
| client_secret |Wymagane |Klucz tajny aplikacji, która wygenerowała w [portalu Azure](https://portal.azure.com/). Ten klucz tajny aplikacji jest ważny artefaktu. Użytkownik powinien Zapisz w bezpiecznej lokalizacji na serwerze. Należy również Obróć ten klucz tajny klienta w regularnych odstępach czasu. |

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
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest `Bearer`. |
| access_token |Podpisany token JWT żądana. |
| scope |Zakres, który token jest prawidłowy, która może służyć do buforowania tokeny na potrzeby późniejszego użycia. |
| expires_in |Długość czasu, przez który token dostępu jest nieprawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać ten token, aby uzyskać dodatkowe tokeny, po wygaśnięciu tokenu bieżącego.  Odśwież tokeny są to długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej szczegółów, zobacz [odwołania do tokenu usługi B2C](active-directory-b2c-reference-tokens.md). |

Błąd odpowiedzi następującą postać:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kod błędu, który może służyć do klasyfikowania typy błędów, które występować i który może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Jeśli chcesz zalogować użytkownika poza aplikacją nie jest wystarczająco, aby wyczyścić pliki cookie aplikacji lub w inny sposób zakończenia sesji z użytkownikiem. Należy również przekierować użytkownika do usługi Azure AD, aby się wylogować. W przypadku awarii to zrobić, użytkownik może mieć możliwość ponownego uwierzytelnienia do aplikacji bez konieczności ponownego wprowadzania poświadczeń. Jest tak, ponieważ mają one nieprawidłowy jednej logowania jednokrotnego sesji z usługą Azure AD.

Po prostu można przekierować użytkownika do `end_session` punktu końcowego, który znajduje się w dokumencie metadanych OpenID Connect opisanego wcześniej w "Zweryfikuj token Identyfikatora" sekcji:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Zasada, która ma być używany do podpisywania użytkownika z aplikacji. |
| post_logout_redirect_uri |Zalecane |Adres URL, który użytkownik powinien być przekierowywany użytkownik po pomyślnym wylogowania. Jeśli nie zostanie włączony, usługi Azure AD B2C pokazuje użytkownikowi zostanie wyświetlony komunikat ogólny. |

> [!NOTE]
> Mimo że kierowanie użytkownikowi `end_session` punktu końcowego spowoduje wyczyszczenie niektóre użytkownika pojedynczego logowania jednokrotnego stanu z usługi Azure AD B2C, nie podpisze użytkownika poza swoją sesję dostawca tożsamości społecznościowych. Jeśli użytkownik wybierze tego samego IDP podczas kolejnych logowanie, ich będzie można ponownie uwierzytelnić, bez konieczności wprowadzania poświadczeń. Jeśli użytkownik chce, aby wylogować się z aplikacji B2C, go nie musi oznaczać, że chcą, aby wylogować się z kontem w serwisie Facebook. Jednak w przypadku lokalnego konta użytkownika sesja zostanie zakończona poprawnie.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Użyj dzierżawy usługi B2C
Jeśli chcesz wypróbować te żądania we własnym zakresie, musi najpierw wykonać następujące trzy kroki, a następnie zastąp przykładowe wartości opisanych wcześniej własnymi:

1. [Tworzenie dzierżawy B2C](active-directory-b2c-get-started.md)i użyj nazwy dzierżawy w żądaniach.
2. [Tworzenie aplikacji](active-directory-b2c-app-registration.md) uzyskać identyfikator aplikacji. Dołącz aplikacji sieci web aplikacji/składnika web API. Opcjonalnie: Utwórz klucz tajny aplikacji.
3. [Tworzenie zasad](active-directory-b2c-reference-policies.md) można uzyskać nazwy zasad.

