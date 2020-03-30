---
title: Logowanie jednostronicowe przy użyciu przepływu niejawnego
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dodać logowanie jednostronicowe przy użyciu niejawnego przepływu OAuth 2.0 za pomocą usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37b59c2a23a8f00e8376be2ac4a7b35a6d58aa28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398997"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Logowanie jednostronicowe przy użyciu niejawnego przepływu OAuth 2.0 w usłudze Azure Active Directory B2C

Wiele nowoczesnych aplikacji ma jednostronicowy fronton aplikacji, który jest napisany głównie w języku JavaScript. Często aplikacja jest zapisywana przy użyciu struktury, takich jak React, Angular lub Vue.js. Aplikacje jednostronicowe i inne aplikacje JavaScript, które działają głównie w przeglądarce, mają dodatkowe problemy z uwierzytelnianiem:

- Charakterystyki zabezpieczeń tych aplikacji różnią się od tradycyjnych aplikacji sieci web opartych na serwerze.
- Wiele serwerów autoryzacji i dostawców tożsamości nie obsługuje żądań udostępniania zasobów między źródłami (CORS).
- Przekierowania przeglądarki pełnostronicowej z dala od aplikacji mogą być inwazyjne dla środowiska użytkownika.

Do obsługi tych aplikacji usługa Azure Active Directory B2C (Azure AD B2C) używa przepływu niejawnego OAuth 2.0. Niejawny przepływ dotacji autoryzacji OAuth 2.0 jest opisany w [sekcji 4.2 specyfikacji OAuth 2.0](https://tools.ietf.org/html/rfc6749). W przepływie niejawnym aplikacja odbiera tokeny bezpośrednio z usługi Azure Active Directory (Azure AD) autoryzuje punkt końcowy, bez wymiany między serwerami. Cała logika uwierzytelniania i obsługa sesji odbywa się w całości w kliencie JavaScript z przekierowaniem strony lub wyskakującym okienkiem.

Usługa Azure AD B2C rozszerza standardowy przepływ niejawny OAuth 2.0 do więcej niż proste uwierzytelnianie i autoryzacja. Usługa Azure AD B2C wprowadza [parametr zasad](user-flow-overview.md). Za pomocą parametru zasad można użyć funkcji OAuth 2.0, aby dodać zasady do aplikacji, takie jak przepływy użytkowników rejestracji, logowania i zarządzania profilem. W przykładzie żądań HTTP w tym artykule **{tenant}.onmicrosoft.com** jest używany jako przykład. Zamień `{tenant}` na nazwę dzierżawy, jeśli ją masz i utworzono również przepływ użytkownika.

Niejawny przepływ logowania wygląda mniej więcej jak na poniższym rysunku. Każdy krok jest szczegółowo opisany w dalszej części artykułu.

![Diagram w stylu toru przedstawiający niejawny przepływ OpenID Connect](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania

Gdy aplikacja sieci web musi uwierzytelnić użytkownika i uruchomić przepływ użytkownika, `/authorize` może skierować użytkownika do punktu końcowego. Użytkownik podejmuje działania w zależności od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia, które musi uzyskać od `scope` użytkownika w parametrze i przepływu użytkownika do uruchomienia. Aby dowiedzieć się, jak działa żądanie, spróbuj wkleić żądanie do przeglądarki i uruchomić go. Zamień `{tenant}` na nazwę dzierżawy usługi Azure AD B2C. Zamień `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` na identyfikator aplikacji, którą wcześniej zarejestrowałeś w dzierżawie. Zamień `{policy}` na przykład `b2c_1_sign_in`nazwę zasady utworzonej w dzierżawie.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Wymagany | Opis |
| --------- | -------- | ----------- |
|{dzierżawca}| Tak | Nazwa dzierżawy usługi Azure AD B2C|
|{zasady}| Tak| Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie usługi Azure AD B2C. Na `b2c_1_sign_in`przykład: `b2c_1_sign_up`, `b2c_1_edit_profile`, lub . |
| client_id | Tak | Identyfikator aplikacji przypisany do aplikacji [przez portal Azure portal.](https://portal.azure.com/) |
| response_type | Tak | Musi `id_token` zawierać dla openid connect logowania. Może również zawierać typ `token`odpowiedzi . Jeśli używasz `token`aplikacji można natychmiast odbierać token dostępu z punktu końcowego autoryzacji, bez konieczności wysuwania drugiego żądania do punktu końcowego autoryzacji.  Jeśli używasz `token` typu odpowiedzi, `scope` parametr musi zawierać zakres, który wskazuje, który zasób do wydania tokenu dla. |
| redirect_uri | Nie | Identyfikator URI przekierowania aplikacji, w którym aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania, które zostały zarejestrowane w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| response_mode | Nie | Określa metodę, która ma być używana do wysyłania wynikowego tokenu z powrotem do aplikacji.  W przypadku przepływów niejawnych należy użyć pliku `fragment`. |
| scope | Tak | Oddzielona spacja lista zakresów. Wartość pojedynczego zakresu wskazuje usługi Azure AD zarówno uprawnienia, które są wymagane. Zakres `openid` wskazuje uprawnienie do logowania się użytkownika i uzyskania danych o użytkowniku w postaci tokenów identyfikatorów. Zakres `offline_access` jest opcjonalny dla aplikacji sieci web. Wskazuje, że aplikacja potrzebuje tokenu odświeżania dla długotrwałego dostępu do zasobów. |
| state | Nie | Wartość uwzględniona w żądaniu, która również jest zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, której chcesz użyć. Zazwyczaj używana jest losowo generowana, unikatowa wartość, aby zapobiec atakom fałszerstwa żądań między witrynami. Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, na przykład na stronie, na którą się znajdował. |
| nonce | Tak | Wartość uwzględniona w żądaniu (wygenerowanym przez aplikację), która jest uwzględniona w wynikowym tokenie identyfikatora jako oświadczenie. Aplikacja może następnie zweryfikować tę wartość, aby ograniczyć ataki powtarzania tokenów. Zazwyczaj wartość jest losowo, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| Wierszu | Nie | Typ interakcji użytkownika, który jest wymagany. Obecnie jedyną prawidłową `login`wartością jest . Ten parametr wymusza na użytkowniku wprowadzenie poświadczeń w tym żądaniu. Logowanie jednokrotne nie jest skuteczne. |

W tym momencie użytkownik jest proszony o ukończenie przepływu pracy zasad. Użytkownik może być musiał wprowadzić swoją nazwę użytkownika i hasło, zalogować się za pomocą tożsamości społecznościowej, zarejestrować się w katalogu lub dowolną inną liczbę kroków. Akcje użytkownika zależą od sposobu definiowania przepływu użytkownika.

Po zakończeniu przepływu użytkownika usługa Azure AD zwraca odpowiedź do aplikacji po `redirect_uri`wartości używanej dla . Używa metody określonej w `response_mode` parametrze. Odpowiedź jest dokładnie taka sama dla każdego scenariusza akcji użytkownika, niezależnie od przepływu użytkownika, który został wykonany.

### <a name="successful-response"></a>Skuteczna reakcja
Pomyślna odpowiedź, `response_mode=fragment` która `response_type=id_token+token` używa i wygląda następująco, z podziałami wierszy dla czytelności:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametr | Opis |
| --------- | ----------- |
| access_token | Token dostępu, o który prosiła aplikacja. |
| token_type | Wartość typu tokenu. Jedynym typem, który obsługuje usługi Azure AD jest Bearer. |
| expires_in | Czas, przez który token dostępu jest prawidłowy (w sekundach). |
| scope | Zakresy, dla których token jest prawidłowy. Można również użyć zakresów do pamięci podręcznej tokenów do późniejszego użycia. |
| id_token | Token identyfikatora, o który prosiła aplikacja. Token identyfikatora służy do weryfikacji tożsamości użytkownika i rozpoczęcia sesji z użytkownikiem. Aby uzyskać więcej informacji na temat tokenów identyfikatorów i ich zawartości, zobacz [odwołanie do tokenu usługi Azure AD B2C.](tokens-overview.md) |
| state | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd
Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod używany do klasyfikowania typów błędów, które występują. |
| error_description | Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| state | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne.|

## <a name="validate-the-id-token"></a>Sprawdzanie poprawności tokenu identyfikatora

Odbieranie tokenu identyfikatora nie wystarczy do uwierzytelnienia użytkownika. Sprawdź poprawność podpisu tokenu identyfikatora i sprawdź oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Usługa Azure AD B2C używa [tokenów JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe.

Wiele bibliotek typu open source jest dostępnych do sprawdzania poprawności jwts, w zależności od języka, którego wolisz używać. Należy rozważyć eksplorowanie dostępnych bibliotek typu open source, a nie implementowanie własnej logiki sprawdzania poprawności. Informacje zawarte w tym artykule ułatwiają prawidłowe korzystanie z tych bibliotek.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Aplikacja może używać punktu końcowego do pobierania informacji o usłudze Azure AD B2C w czasie wykonywania. Informacje te obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenów. Istnieje dokument metadanych JSON dla każdego przepływu użytkownika w dzierżawie usługi Azure AD B2C. Na przykład dokument metadanych dla przepływu użytkownika b2c_1_sign_in w dzierżawie fabrikamb2c.onmicrosoft.com znajduje się pod adresem:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jedną z właściwości tego dokumentu konfiguracji `jwks_uri`jest . Wartość dla tego samego przepływu użytkownika będzie:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Aby określić, który przepływ użytkownika został użyty do podpisania tokenu identyfikatora (i gdzie pobrać metadane), masz dwie opcje. Po pierwsze, nazwa przepływu użytkownika `acr` jest `id_token`uwzględniona w żądaniu w pliku . Aby uzyskać informacje dotyczące analizowanie oświadczeń z tokenu identyfikatora, zobacz [odwołanie do tokenu usługi Azure AD B2C.](tokens-overview.md) Inną opcją jest zakodowanie przepływu użytkownika w `state` wartości parametru podczas wystawiania żądania. Następnie należy zdekodować parametr, `state` aby określić, który przepływ użytkownika został użyty. Każda z tych metod jest prawidłowa.

Po nabyciu dokumentu metadanych z punktu końcowego metadanych OpenID Connect można użyć kluczy publicznych RSA-256 (znajdujących się w tym punkcie końcowym), aby sprawdzić poprawność podpisu tokenu identyfikatora. W danym momencie może znajdować się wiele kluczy wymienionych `kid`w tym punkcie końcowym, z których każdy jest identyfikowany przez program . Nagłówek `id_token` zawiera również `kid` oświadczenie. Wskazuje, który z tych kluczy został użyty do podpisania tokenu identyfikatora. Aby uzyskać więcej informacji, w tym informacje na temat [sprawdzania poprawności tokenów,](tokens-overview.md)zobacz [odwołanie do tokenu usługi Azure AD B2C.](tokens-overview.md)
<!--TODO: Improve the information on this-->

Po sprawdzeniu poprawności podpisu tokenu identyfikatora kilka oświadczeń wymaga weryfikacji. Przykład:

* Sprawdź poprawność oświadczenia, `nonce` aby zapobiec atakom powtarzania tokenów. Jego wartość powinna być określona w żądaniu logowania.
* Sprawdź poprawność oświadczenia, `aud` aby upewnić się, że token identyfikatora został wystawiony dla aplikacji. Jego wartość powinna być identyfikator aplikacji aplikacji.
* Sprawdź poprawność `exp` i oświadczeń, `iat` aby upewnić się, że token identyfikatora nie wygasł.

Kilka innych weryfikacji, które należy wykonać, opisano szczegółowo w [specyfikacji OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Można również sprawdzić poprawność dodatkowych oświadczeń, w zależności od scenariusza. Niektóre typowe weryfikacje obejmują:

* Upewniając się, że użytkownik lub organizacja zarejestrowała się w aplikacji.
* Zapewnienie, że użytkownik ma odpowiednie uprawnienia i uprawnienia.
* Zapewnienie, że wystąpiła pewna siła uwierzytelniania, na przykład przy użyciu uwierzytelniania wieloskładnikowego platformy Azure.

Aby uzyskać więcej informacji na temat oświadczeń w tokenie identyfikatora, zobacz [odwołanie do tokenu usługi Azure AD B2C.](tokens-overview.md)

Po sprawdzeniu poprawności tokenu identyfikatora można rozpocząć sesję z użytkownikiem. W aplikacji użyj oświadczeń w tokenie identyfikatora, aby uzyskać informacje o użytkowniku. Te informacje mogą być używane do wyświetlania, rekordów, autoryzacji i tak dalej.

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu
Jeśli jedyną rzeczą, którą muszą zrobić aplikacje internetowe, jest wykonywanie przepływów użytkowników, możesz pominąć kilka następnych sekcji. Informacje zawarte w poniższych sekcjach mają zastosowanie tylko do aplikacji sieci web, które wymagają uwierzytelniania wywołań do internetowego interfejsu API i które są chronione przez usługę Azure AD B2C.

Po podpisaniu użytkownika w aplikacji jednostronicowej można uzyskać tokeny dostępu do wywoływania interfejsów API sieci Web, które są zabezpieczone przez usługę Azure AD. Nawet jeśli już odebrano token `token` przy użyciu typu odpowiedzi, można użyć tej metody do uzyskania tokenów dla dodatkowych zasobów bez przekierowywania użytkownika, aby zalogować się ponownie.

W typowym przepływie aplikacji sieci web `/token` należy złożyć żądanie do punktu końcowego. Jednak punkt końcowy nie obsługuje żądań CORS, więc wykonywanie wywołań AJAX, aby uzyskać token odświeżania nie jest opcją. Zamiast tego można użyć przepływu niejawnego w ukrytym elemencie iframe HTML, aby uzyskać nowe tokeny dla innych internetowych interfejsów API. Oto przykład z podziałami wierszy dla czytelności:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|{dzierżawca}| Wymagany | Nazwa dzierżawy usługi Azure AD B2C|
{zasady}| Wymagany| Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie usługi Azure AD B2C. Na `b2c_1_sign_in`przykład: `b2c_1_sign_up`, `b2c_1_edit_profile`, lub . |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [witrynie Azure portal](https://portal.azure.com). |
| response_type |Wymagany |Musi `id_token` zawierać dla openid connect logowania.  Może również zawierać typ `token`odpowiedzi . Jeśli używasz `token` w tym miejscu, aplikacja może natychmiast odbierać token dostępu z punktu końcowego autoryzacji, bez konieczności wysuwania drugiego żądania do punktu końcowego autoryzacji. Jeśli używasz `token` typu odpowiedzi, `scope` parametr musi zawierać zakres, który wskazuje, który zasób do wydania tokenu dla. |
| redirect_uri |Zalecane |Identyfikator URI przekierowania aplikacji, w którym aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| scope |Wymagany |Oddzielona spacja lista zakresów.  Aby uzyskać tokeny, należy uwzględnić wszystkie zakresy, które są wymagane dla zamierzonego zasobu. |
| response_mode |Zalecane |Określa metodę, która jest używana do wysyłania tokenu wynikowego z powrotem do aplikacji. W przypadku przepływu `fragment`niejawnego należy użyć pliku . Można określić dwa inne `query` `form_post`tryby i , ale nie działają w przepływie niejawnym. |
| state |Zalecane |Wartość uwzględniona w żądaniu, który jest zwracany w odpowiedzi tokenu.  Może to być ciąg dowolnej zawartości, której chcesz użyć.  Zazwyczaj używana jest losowo generowana, unikatowa wartość, aby zapobiec atakom fałszerstwa żądań między witrynami.  Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strona lub widok, na który znajdował się użytkownik. |
| nonce |Wymagany |Wartość uwzględniona w żądaniu, wygenerowana przez aplikację, która jest uwzględniona w tokenie identyfikatora wynikowego jako oświadczenie.  Aplikacja może następnie zweryfikować tę wartość, aby ograniczyć ataki powtarzania tokenów. Zazwyczaj wartość jest losowo, unikatowy ciąg, który identyfikuje pochodzenie żądania. |
| Wierszu |Wymagany |Aby odświeżyć i uzyskać tokeny `prompt=none` w ukrytym iframe, należy użyć, aby upewnić się, że iframe nie utknie na stronie logowania i zwraca natychmiast. |
| login_hint |Wymagany |Aby odświeżyć i uzyskać tokeny w ukrytym iframe, należy uwzględnić nazwę użytkownika w tej podpowiedzi, aby odróżnić wiele sesji, które użytkownik może mieć w danym czasie. Możesz wyodrębnić nazwę użytkownika z wcześniejszego `preferred_username` logowania przy `profile` użyciu oświadczenia (zakres jest `preferred_username` wymagany do otrzymania oświadczenia). |
| domain_hint |Wymagany |Możliwe wartości to `consumers` i `organizations`.  Aby odświeżyć i uzyskać tokeny w `domain_hint` ukrytym iframe, należy uwzględnić wartość w żądaniu.  Wyodrębnij `tid` oświadczenie z tokenu identyfikatora wcześniejszego logowania, aby określić, która wartość ma być używana `profile` (zakres jest wymagany do otrzymania `tid` oświadczenia). Jeśli `tid` wartością `9188040d-6c67-4c5b-b112-36a304b66dad`oświadczenia `domain_hint=consumers`jest , użyj .  W przeciwnym `domain_hint=organizations`razie należy użyć pliku . |

Ustawiając `prompt=none` parametr, to żądanie zakończy się pomyślnie lub kończy się niepowodzeniem natychmiast i powraca do aplikacji.  Pomyślna odpowiedź jest wysyłana do aplikacji przy wskazanym identyfikatorze URI przekierowania przy użyciu metody określonej w parametrze. `response_mode`

### <a name="successful-response"></a>Skuteczna reakcja
Pomyślna odpowiedź `response_mode=fragment` przy użyciu wygląda następująco w tym przykładzie:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametr | Opis |
| --- | --- |
| access_token |Token, który aplikacja zażądała. |
| token_type |Typ tokenu zawsze będzie okaziciela. |
| state |Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| scope |Zakresy, dla których token dostępu jest prawidłowy. |

### <a name="error-response"></a>Odpowiedź na błąd
Odpowiedzi na błędy również mogą być wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może obsługiwać je odpowiednio.  Dla `prompt=none`, oczekiwany błąd wygląda następująco w tym przykładzie:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują. Można również użyć ciągu, aby reagować na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

Jeśli ten błąd w żądaniu elementu iframe, użytkownik musi interaktywnie zalogować się ponownie, aby pobrać nowy token.

## <a name="refresh-tokens"></a>Odśwież tokeny
Tokeny identyfikatorów i tokeny dostępu wygasają po krótkim czasie. Aplikacja musi być przygotowana do okresowego odświeżania tych tokenów.  Aby odświeżyć każdy typ tokenu, wykonaj to samo ukryte żądanie elementu `prompt=none` iframe, którego używaliśmy we wcześniejszym przykładzie, używając parametru do kontrolowania kroków usługi Azure AD.  Aby otrzymać `id_token` nową wartość, należy `response_type=id_token` `scope=openid`użyć i `nonce` , i parametr.

## <a name="send-a-sign-out-request"></a>Wysyłanie żądania wylogowywania
Jeśli chcesz wylogować użytkownika z aplikacji, przekieruj użytkownika do usługi Azure AD, aby się wylogować. Jeśli użytkownik nie zostanie przekierowany, może być w stanie ponownie uwierzytelnić do aplikacji bez wprowadzania ich poświadczeń ponownie, ponieważ mają prawidłową sesję logowania jednokrotnego z usługą Azure AD.

Możesz po prostu przekierować `end_session_endpoint` użytkownika do tego, który jest wymieniony w tym samym dokumencie metadanych OpenID Connect opisanym w [polu Sprawdź poprawność tokenu identyfikatora.](#validate-the-id-token) Przykład:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagany | Opis |
| --------- | -------- | ----------- |
| {dzierżawca} | Tak | Nazwa dzierżawy usługi Azure AD B2C |
| {zasady} | Tak | Przepływ użytkownika, który ma być używany do podpisywania użytkownika z aplikacji. |
| post_logout_redirect_uri | Nie | Adres URL, do który użytkownik powinien zostać przekierowany po pomyślnym wylogowywaniu się. Jeśli nie jest uwzględniony, usługa Azure AD B2C pokazuje użytkownikowi ogólny komunikat. |
| state | Nie | Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |


> [!NOTE]
> Kierowanie użytkownika do `end_session_endpoint` czyści niektóre stan logowania jednokrotnego użytkownika z usługi Azure AD B2C. Jednak nie podpisuje użytkownika z sesji dostawcy tożsamości społecznościowej użytkownika. Jeśli użytkownik wybierze tego samego dostawcę tożsamości podczas późniejszego logowania, użytkownik jest ponownie uwierzytelniony, bez wprowadzania poświadczeń. Jeśli użytkownik chce wylogować się z aplikacji usługi Azure AD B2C, nie musi to oznaczać, że chce całkowicie wylogować się z konta na Facebooku, na przykład. Jednak w przypadku kont lokalnych sesja użytkownika zostanie poprawnie zakończona.
>

## <a name="next-steps"></a>Następne kroki

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Przykładowy kod: usługa Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla języka JavaScript

[Aplikacja jednostronicowa zbudowana za pomocą pliku msal.js for Azure AD B2C][github-msal-js-example] (GitHub)

Ten przykład w usłudze GitHub ma na celu ułatwienie rozpoczęcia korzystania z usługi Azure AD B2C w prostej aplikacji sieci web utworzonej za pomocą [msal.js][github-msal-js] i przy użyciu uwierzytelniania w stylu wyskakującym.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
