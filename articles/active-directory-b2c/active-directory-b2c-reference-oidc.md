---
title: Zaloguj się przy użyciu protokołu OpenID Connect w usłudze Azure Active Directory B2C w sieci Web | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web za pomocą usługi Azure Active Directory implementacji protokołu uwierzytelniania OpenID Connect.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e689f93150d225d5b8c9ee9d5cfc422a1154c45a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724557"
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Usługa Azure Active Directory B2C: Web zaloguj się przy użyciu protokołu OpenID Connect
OpenID Connect to protokół uwierzytelniania, korzystających z protokołu OAuth 2.0, który może służyć do bezpiecznego logowania użytkowników do aplikacji sieci web. Za pomocą usługi Azure Active Directory B2C (Azure AD B2C) wdrażania protokołu OpenID Connect, można zlecają obsługę tworzenia nowych kont i logowania oraz skuteczniejszego innych zarządzania tożsamościami w aplikacjach sieci web w usłudze Azure Active Directory (Azure AD). Ten przewodnik pokazuje, jak to zrobić w sposób niezależny od języka. Przedstawiono sposób wysyłać i odbierać komunikaty HTTP bez użycia jakichkolwiek skorzystać z bibliotek typu open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) rozszerza OAuth 2.0 *autoryzacji* protokół do użycia jako *uwierzytelniania* protokołu. Dzięki temu można wykonać logowanie jednokrotne przy użyciu protokołu OAuth. Wprowadza pojęcia *tokenu Identyfikacyjnego*, który jest token zabezpieczający, który umożliwia klientowi do zweryfikowania tożsamości danego użytkownika i uzyskania podstawowych informacji o profilu użytkownika.

Ponieważ stanowi rozszerzenie protokołu OAuth 2.0, umożliwia ona także aplikacje, które można bezpiecznie uzyskać *tokeny dostępu*. Możesz użyć access_tokens dostępu do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-b2c-reference-protocols.md#the-basics). Zalecamy OpenID Connect, jeśli tworzysz aplikację internetową, która jest przechowywana na serwerze i dostępne za pośrednictwem przeglądarki. Jeśli chcesz dodać zarządzania tożsamościami do aplikacji mobilnych i klasycznych za pomocą usługi Azure AD B2C, należy użyć [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) zamiast protokołu OpenID Connect.

Usługa Azure AD B2C rozszerza ze standardowego protokołu OpenID Connect, aby zrobić więcej niż proste uwierzytelnianie i autoryzacja. Wprowadza [parametr przepływ użytkownika](active-directory-b2c-reference-policies.md), który pozwala dodać środowisk użytkowników — takich jak za pomocą protokołu OpenID Connect rejestracji, logowania i zarządzania profilami — do swojej aplikacji. W tym miejscu pokazujemy, jak zaimplementuj każdą z tych środowisk w aplikacji sieci web za pomocą protokołu OpenID Connect i użytkownika przepływów. Ponadto pokażemy sposób uzyskiwania tokenów dostępu do uzyskiwania dostępu do interfejsów API sieci web.

Żądania przykład HTTP w następnej sekcji przy użyciu naszej przykładowej katalogu B2C, fabrikamb2c.onmicrosoft.com, a także naszą przykładową aplikacją https://aadb2cplayground.azurewebsites.neti przepływy użytkownika. Możesz wypróbować żądania samodzielnie za pomocą tych wartości, lub można je zastąpić własnymi.
Dowiedz się, jak [uzyskać własne przepływy dzierżawy, aplikacji i użytkownika B2C](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania
W przypadku aplikacji sieci web wymaga uwierzytelnienia użytkownika i wykonywanie przepływu użytkownika, można je skierować użytkownika `/authorize` punktu końcowego. Jest to interaktywne części przepływu, gdy użytkownik wykona akcję, zależnie od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia, których potrzebuje, aby pobierać z użytkownikiem w `scope` parametr i przepływ użytkownika do wykonania w `p` parametru. Trzy przykłady znajdują się w poniższych sekcjach (z podziały wierszy dla czytelności), używając przepływu innego użytkownika. Aby uzyskać pewne pojęcie dla działania każdego żądania, spróbuj wklejanie żądania do przeglądarki i uruchomiania go.

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

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji [witryny Azure portal](https://portal.azure.com/) przypisany do aplikacji. |
| response_type |Wymagane |Typ odpowiedzi musi zawierać identyfikator tokenu dla protokołu OpenID Connect. Jeśli Twoja aplikacja sieci web musi również tokenów do wywoływania interfejsu API sieci web, możesz użyć `code+id_token`, podobnie jak w tym miejscu. |
| redirect_uri |Zalecane |`redirect_uri` Parametr swojej aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednej z `redirect_uri` parametry, które są zarejestrowane w portalu, z tą różnicą, że musi być zakodowane w adresie URL. |
| scope |Wymagane |Rozdzielonej spacjami listy zakresów. Wartość pojedynczy zakres wskazuje do usługi Azure AD, zarówno uprawnienia, które są żądane. `openid` Zakres Określa uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w formie tokeny Identyfikatora, (więcej wejść w tym w dalszej części tego artykułu). `offline_access` Zakres jest opcjonalny w przypadku aplikacji sieci web. Oznacza to, że potrzebuje aplikacja *token odświeżania* długotrwałe dostępu do zasobów. |
| response_mode |Zalecane |Metody, które mają być używane do wysyłania wynikowy kod autoryzacji z powrotem do aplikacji. Może być albo `query`, `form_post`, lub `fragment`.  `form_post` Najlepiej zalecany jest tryb odpowiedzi. |
| state |Zalecane |Wartość uwzględnione w żądaniu, która jest także zwracany w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, która ma. Zazwyczaj służy losowo generowany unikatową wartość dla zapobieganie atakom na fałszerstwo żądania międzywitrynowego. Stan również jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony, w której znajdowały się w. |
| Identyfikator jednorazowy |Wymagane |Wartość objęte żądania (generowany przez aplikację), które zostaną uwzględnione w powstały token Identyfikatora jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby uniknąć powtarzania tokenu ataków. Wartość jest zazwyczaj losowy unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| p |Wymagane |Przepływ użytkownika, która zostanie wykonana. To nazwa przepływu użytkownika, który jest tworzony w ramach dzierżawy B2C. Wartość nazwy przepływu użytkownika powinien zaczynać się od `b2c\_1\_`. Dowiedz się więcej na temat zasad i [struktura przepływ użytkownika extensible](active-directory-b2c-reference-policies.md). |
| wiersz |Optional (Opcjonalność) |Typ interakcji z użytkownikiem, który jest wymagany. To jedyna prawidłowa wartość w tej chwili `login`, która wymusza użytkownika o wprowadzenie poświadczeń dla tego żądania. Logowanie jednokrotne nie zostanie zastosowana. |

W tym momencie użytkownik jest proszony do ukończenia przepływu pracy przepływu użytkownika. Może to obejmować użytkownika wprowadzania nazwy użytkownika i hasła, logowanie się przy użyciu tożsamości społecznościowej, założeniem katalogu lub dowolną liczbę kroków, w zależności od tego, jak jest zdefiniowany przepływu użytkownika.

Po użytkownik kończy przepływ użytkownika, usługa Azure AD, zwraca odpowiedź do aplikacji na wskazanych `redirect_uri` parametru za pomocą metody, która została określona w `response_mode` parametru. Odpowiedź jest taka sama dla każdego z powyższych przypadkach niezależny od przepływu użytkownika, który jest wykonywany.

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` powinien wyglądać tak:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| id_token |Identyfikator tokenu, który zażądał aplikacji. Identyfikator tokenu można użyć do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. Szczegółowe informacje na temat tokeny Identyfikatora i ich zawartość są objęte [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| Kod |Kod autoryzacji, który zażądał aplikacji, jeśli użyto `response_type=code+id_token`. Aplikacja może używać kodu autoryzacji do wysłania żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji są bardzo krótkotrwałe. Zazwyczaj wygasają po upływie około 10 minut. |
| state |Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

Odpowiedzi na błędy mogą również zostać wysłane do `redirect_uri` parametru, aby mógł być je obsłużyć odpowiednio w aplikacji:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kod błędu, który może służyć do klasyfikowania typy błędów, które występują, i który może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |
| state |Zobacz pełny opis w pierwszej tabeli w tej sekcji. Jeśli `state` parametru jest uwzględnione w żądaniu, tę samą wartość powinna zostać wyświetlona w odpowiedzi. Aplikacja powinna upewnij się, że `state` wartości żądania i odpowiedzi są identyczne. |

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu Identyfikacyjnego
Po prostu odbiera token Identyfikatora nie wystarcza do uwierzytelnienia użytkownika. Należy sprawdzić poprawności podpisu tokenu Identyfikacyjnego i weryfikować oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Usługa Azure AD B2C używa [tokenów sieci Web JSON (tokenów Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Istnieje wiele bibliotek typu open source, które są dostępne do weryfikacji tokenów Jwt, w zależności od języka, preferencji. Firma Microsoft zaleca Eksplorowanie tych opcji, a nie Implementowanie logiki walidacji. W tym miejscu informacje będą przydatne ustalenie, jak prawidłowo używać tych bibliotek.

Usługa Azure AD B2C ma OpenID Connect punkt końcowy metadanych, które umożliwia aplikacji można pobrać informacji na temat usługi Azure AD B2C w środowisku uruchomieniowym. Informacje te obejmują punkty końcowe, zawartość tokenów i tokenów kluczy podpisywania. Brak dokumentu metadanych JSON dla każdego przepływu użytkownika w dzierżawie usługi B2C. Na przykład dokument metadanych dla `b2c_1_sign_in` przepływu użytkownika w `fabrikamb2c.onmicrosoft.com` znajduje się w folderze:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z właściwości tego dokumentu konfiguracji jest `jwks_uri`, którego wartość jest taki sam przepływ użytkownika:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Aby określić, które przepływ użytkownika została użyta w podpisywania identyfikator tokenu (i skąd można pobrać metadanych), masz dwie opcje. Po pierwsze, przepływ użytkownika znajduje się w `acr` oświadczenia w tokenie identyfikator. Aby uzyskać informacje na temat sposobu analizowania oświadczeń z tokenu Identyfikacyjnego, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). Drugą opcją jest do zakodowania przepływu użytkownika w wartości `state` parametru podczas wystawiania żądania, a następnie dekodowania go, aby określić, które przepływ użytkownika zostało użyte. Każda z tych metod jest prawidłowy.

Po nabyciu dokumentu metadanych z punktu końcowego metadanych OpenID Connect, możesz użyć klucze publiczne RSA 256, (które znajdują się w tym punkcie końcowym) można zweryfikować podpisu tokenu Identyfikacyjnego. Może istnieć wiele kluczy wymienione w tym punkcie końcowym w dowolnym danym momencie w czasie, identyfikowanych przez `kid` oświadczenia. Zawiera także nagłówek tokenu Identyfikacyjnego `kid` oświadczenia, która wskazuje, które z tych kluczy był używany do podpisywania tokenu Identyfikacyjnego. Aby uzyskać więcej informacji, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md) (sekcję [sprawdzanie poprawności tokenów](active-directory-b2c-reference-tokens.md#token-validation), a zwłaszcza sekcję).
<!--TODO: Improve the information on this-->

Po zweryfikowaniu podpis tokenu Identyfikacyjnego, istnieje kilka oświadczenia, które należy sprawdzić. Na wystąpienie:

* Należy sprawdzić, czy `nonce` oświadczenia zapobiec atakom powtarzania tokenu. Wartość powinna być określona w żądaniu logowania.
* Należy sprawdzić, czy `aud` oświadczenia upewnić się, czy token identyfikator został wystawiony dla aplikacji. Jej wartość powinna być identyfikator aplikacji dla swojej aplikacji.
* Należy sprawdzić, czy `iat` i `exp` oświadczeń upewnić się, że tokenu Identyfikacyjnego nie wygasł.

Dostępne są także kilka więcej operacji sprawdzania poprawności, które należy wykonać. Te ustawienia są opisane szczegółowo w temacie [OpenID Connect podstawowej specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html).  Można również sprawdzić dodatkowe oświadczenia, zależnie od scenariusza. Niektórych typowych operacji sprawdzania poprawności obejmują:

* Zapewnienie, że użytkownik/organizacja zarejestrowała się w aplikacji.
* Zapewnienie, że użytkownik ma odpowiednie zezwolenia/uprawnienia.
* Zapewnienie, że niektóre siły uwierzytelniania wystąpił, takich jak uwierzytelnianie wieloskładnikowe systemu Azure.

Aby uzyskać więcej informacji na oświadczeniach w tokenie Identyfikatora, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po zweryfikowaniu tokenu Identyfikacyjnego, można rozpocząć sesji z użytkownikiem. Aby uzyskać informacje o użytkowniku w swojej aplikacji, można użyć oświadczenia w tokenie identyfikator. Wykorzystuje te informacje obejmują wyświetlanie, rekordy i autoryzacji.

## <a name="get-a-token"></a>Pobierz token
Jeśli potrzebujesz aplikacji sieci web można wykonywać tylko przepływy użytkownika, możesz pominąć kilka następnych sekcji. Poniższe sekcje mają zastosowanie tylko do sieci web, aplikacje, które muszą być uwierzytelnione wywołania internetowego interfejsu API i jednocześnie są chronione przez usługę Azure AD B2C.

Można zrealizować kod autoryzacji, które zostało zakupione (przy użyciu `response_type=code+id_token`) dla tokenu do żądanego zasobu, wysyłając `POST` limit czasu żądania `/token` punktu końcowego. Obecnie tylko zasób, jakiej może żądać token dla jest własnych aplikacji zaplecza interfejsu API sieci web. Konwencja do żądania tokenu służącego do siebie jest użyć własnego Identyfikatora klienta aplikacji jako zakres:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. Nie można użyć przepływu innego użytkownika, w tym żądaniu. Zauważ, że możesz dodać ten parametr do ciągu zapytania nie `POST` treści. |
| client_id |Wymagane |Identyfikator aplikacji [witryny Azure portal](https://portal.azure.com/) przypisany do aplikacji. |
| grant_type |Wymagane |Typ przydział, który musi być `authorization_code` dla przepływ kodu autoryzacji. |
| scope |Zalecane |Rozdzielonej spacjami listy zakresów. Wartość pojedynczy zakres wskazuje do usługi Azure AD, zarówno uprawnienia, które są żądane. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i pobieranie danych o użytkowniku w formie id_token parametrów. Może służyć do uzyskania tokenów do własnych aplikacji zaplecza interfejsu API sieci web, który jest reprezentowany przez ten sam identyfikator aplikacji, co klient. `offline_access` Zakresu wskazuje, że potrzebuje aplikacja token odświeżania długotrwałe dostępu do zasobów. |
| Kod |Wymagane |Kod autoryzacji uzyskanego w pierwszej gałęzi przepływu. |
| redirect_uri |Wymagane |`redirect_uri` Parametr aplikacji, na którym odebrano kod autoryzacji. |
| client_secret |Wymagane |Klucz tajny aplikacji, który został wygenerowany w [witryny Azure portal](https://portal.azure.com/). Wpis tajny aplikacji jest ważny artefakt. Przechowuj je bezpiecznie na serwerze. Należy również obrócić ten klucz tajny klienta w regularnych odstępach czasu. |

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
| token_type |Wartość atrybutu typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest `Bearer`. |
| access_token |Podpisany token JWT zażądano. |
| scope |Zakresy, dla których token jest prawidłowy. Mogą one być używane do buforowania tokenów w celu późniejszego użycia. |
| expires_in |Długość czasu, przez jaki token dostępu jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu, można uzyskać dodatkowe tokeny, po upływie bieżącego tokenu. Odśwież tokeny są długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zapoznaj się [odwołania do tokenu B2C](active-directory-b2c-reference-tokens.md). Uwaga musi użycie zakresu `offline_access` w autoryzacji i żądania tokenu, aby można było odebrać token odświeżania. |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kod błędu, który może służyć do klasyfikowania typy błędów, które występują, i który może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

## <a name="use-the-token"></a>Użyj tokenu
Teraz, gdy token dostępu został pomyślnie uzyskana, można użyć tokenu w żądaniach wysyłanych do zaplecza internetowych interfejsów API, umieszczając go w `Authorization` nagłówka:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Odświeżenia tokenu
Identyfikator tokeny są krótkotrwałe. Należy je odświeżyć, po ich wygaśnięciu, aby kontynuować, będzie mogła uzyskiwać dostęp do zasobów. Możesz to zrobić, przesyłając innego `POST` limit czasu żądania `/token` punktu końcowego. Tym razem zapewniają `refresh_token` parametr zamiast `code` parametru:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Wymagane | Opis |
| --- | --- | --- |
| p |Wymagane |Przepływ użytkownika, który został użyty do uzyskania oryginalnej token odświeżania. Nie można użyć przepływu innego użytkownika, w tym żądaniu. Należy pamiętać, Dodaj ten parametr, ciąg zapytania, a nie do treść wpisu. |
| client_id |Wymagane |Identyfikator aplikacji [witryny Azure portal](https://portal.azure.com/) przypisany do aplikacji. |
| grant_type |Wymagane |Typ przydział, który musi być tokenu odświeżania do tej gałęzi przepływ kodu autoryzacji. |
| scope |Zalecane |Rozdzielonej spacjami listy zakresów. Wartość pojedynczy zakres wskazuje do usługi Azure AD, zarówno uprawnienia, które są żądane. `openid` Zakresu wskazuje uprawnienia, aby zalogować użytkownika i pobieranie danych o użytkowniku w formie tokenów Identyfikatora. Może służyć do uzyskania tokenów do własnych aplikacji zaplecza interfejsu API sieci web, który jest reprezentowany przez ten sam identyfikator aplikacji, co klient. `offline_access` Zakresu wskazuje, że potrzebuje aplikacja token odświeżania długotrwałe dostępu do zasobów. |
| redirect_uri |Zalecane |`redirect_uri` Parametr aplikacji, na którym odebrano kod autoryzacji. |
| refresh_token |Wymagane |Oryginalny token odświeżania uzyskanego w drugim nogi przepływ. Uwaga musi użycie zakresu `offline_access` w autoryzacji i żądania tokenu, aby można było odebrać token odświeżania. |
| client_secret |Wymagane |Klucz tajny aplikacji, który został wygenerowany w [witryny Azure portal](https://portal.azure.com/). Wpis tajny aplikacji jest ważny artefakt. Przechowuj je bezpiecznie na serwerze. Należy również obrócić ten klucz tajny klienta w regularnych odstępach czasu. |

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
| token_type |Wartość atrybutu typ tokenu. Jedynym typem, który obsługuje usługi Azure AD jest `Bearer`. |
| access_token |Podpisany token JWT zażądano. |
| scope |Zakres, który token jest prawidłowy, która może służyć do buforowania tokenów w celu późniejszego użycia. |
| expires_in |Długość czasu, przez jaki token dostępu jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może używać tego tokenu, można uzyskać dodatkowe tokeny, po upływie bieżącego tokenu.  Odśwież tokeny są długotrwałe i pozwala zachować dostęp do zasobów przez dłuższy czas. Aby uzyskać więcej szczegółów, zobacz [odwołania do tokenu B2C](active-directory-b2c-reference-tokens.md). |

Odpowiedzi na błędy wyglądać następująco:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kod błędu, który może służyć do klasyfikowania typy błędów, które występują, i który może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Utworzyć użytkownika z aplikacji, nie jest wystarczająco dużo wyczyść pliki cookie swojej aplikacji lub zakończenia w przeciwnym razie sesji z użytkownikiem. Należy również przekierować użytkownika do usługi Azure AD, aby się wylogować. W przypadku awarii to zrobić, użytkownik może być możliwe ponowne uwierzytelnianie do aplikacji bez konieczności ponownego wprowadzania poświadczeń. Jest tak, ponieważ mają one nieprawidłowy pojedynczego logowania jednokrotnego sesji z usługą Azure AD.

Możesz po prostu przekierować użytkownika do `end_session` punktu końcowego, który znajduje się w dokumencie metadanych OpenID Connect opisanego wcześniej w "Weryfikuj tokenu Identyfikacyjnego" sekcji:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Przepływ użytkownika, którego chcesz użyć do logowania użytkownika z aplikacji. |
| post_logout_redirect_uri |Zalecane |Adres URL, który użytkownik powinno zostać przekierowane do po pomyślnym wylogowania. Jeśli nie zostanie włączony, usługa Azure AD B2C jest wyświetlana nazwa użytkownika zostanie wyświetlony komunikat ogólny. |

> [!NOTE]
> Mimo że kierowanie użytkowników do `end_session` punkt końcowy usunie część użytkownika pojedynczego logowania jednokrotnego stanu za pomocą usługi Azure AD B2C, nie podpisze użytkownika z sesji dostawcy tożsamości społecznościowych. Jeśli użytkownik wybierze tego samego dostawcy tożsamości, podczas późniejszego logowania, ich zostanie ponownie uwierzytelniane — bez konieczności wprowadzania swoich poświadczeń. Jeśli użytkownik chce, aby wylogować aplikacji B2C, go nie musi oznaczać, że mają być Wyloguj się z konta w serwisie Facebook. Jednak w przypadku kont lokalnych sesji użytkownika zostanie zakończona prawidłowo.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Użyć własnej dzierżawy usługi B2C
Jeśli chcesz wypróbować te żądania dla siebie, należy najpierw wykonać następujące trzy kroki, a następnie zastąp przykładowe wartości opisanych wcześniej za pomocą własnych:

1. [Tworzenie dzierżawy B2C](active-directory-b2c-get-started.md)i użyj nazwy dzierżawy w żądaniach.
2. [Tworzenie aplikacji](active-directory-b2c-app-registration.md) uzyskać identyfikator aplikacji. Dołącz aplikację sieci web/internetowego interfejsu API aplikacji. Opcjonalnie utwórz wpis tajny aplikacji.
3. [Tworzenie przepływów użytkownika](active-directory-b2c-reference-policies.md) można uzyskać nazwy przepływu użytkownika.

