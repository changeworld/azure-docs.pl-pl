---
title: 'Usługa Azure Active Directory B2C: Jednostronicowej aplikacji przy użyciu niejawnego przepływu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak tworzyć aplikacje jednej strony bezpośrednio przez przy użyciu niejawnego przepływu OAuth 2.0 w usłudze Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 3347eac16e447091ffcaaf403e1291e2c7175a2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Usługa Azure AD B2C: Jednostronicowej aplikacji logowania przy użyciu niejawnego przepływu OAuth 2.0

Wiele nowoczesnych aplikacji ma jednostronicowej aplikacji fronton głównie napisany w języku JavaScript. Często napisano aplikację przy użyciu framework, takich jak AngularJS, Ember.js lub Durandal. Aplikacje jednej strony i innych aplikacji JavaScript, które można uruchamiać głównie w przeglądarce ma pewne dodatkowe problemy dotyczące uwierzytelniania:

* Właściwości zabezpieczeń te aplikacje są znacznie różni się od tradycyjnych na serwerze sieci web aplikacji.
* Wiele serwerów autoryzacji i dostawców tożsamości nie obsługują współużytkowanie zasobów między źródłami (CORS) żądania udostępniania.
* Przekierowania całej strony przeglądarki w kierunku od aplikacji może być znacznie inwazyjne środowiska użytkownika.

Aby umożliwić obsługę tych aplikacji usługi Azure Active Directory B2C (Azure AD B2C) używa niejawnego przepływu OAuth 2.0. Przepływ niejawne Przyznaj autoryzacji OAuth 2.0 jest opisany w [sekcji 4.2 specyfikacji protokołu OAuth 2.0](http://tools.ietf.org/html/rfc6749). W przepływu niejawnego aplikacja odbiera tokeny bezpośrednio z usługi Azure Active Directory (Azure AD) punktu końcowego, bez żadnych wymiany serwera serwera autoryzacji. Wszystkie logika uwierzytelniania i sesji obsługi ma umieścić w całości klienta JavaScript, bez dodatkowa strona przekierowania.

Usługa Azure AD B2C rozszerza standardowe niejawnego przepływu OAuth 2.0 do więcej niż prostego uwierzytelniania i autoryzacji. Usługa Azure AD B2C wprowadza [parametru zasad](active-directory-b2c-reference-policies.md). Z parametrem zasad umożliwia OAuth 2.0 Dodawanie użytkowników do aplikacji, takich jak konta, logowania i zarządzania profilami. W tym artykule zostanie przedstawiony zostanie sposób używania przepływu niejawnego i Azure AD do wdrożenia każdego z tych środowisk w aplikacji jednej strony. Aby pomóc Ci rozpocząć, Przyjrzyjmy się naszych [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) i [programu Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) próbek.

W żądaniach przykład HTTP w tym artykule, korzystamy z naszych katalog usługi Azure AD B2C przykładu **fabrikamb2c.onmicrosoft.com**. Możemy również użyć własnej przykładowej aplikacji i zasad. Można spróbować żądania samodzielnie przy użyciu tych wartości, lub należy je zastąpić własne wartości.
Dowiedz się, jak [uzyskać własnego katalogu usługi Azure AD B2C, aplikacji i zasad](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagram protokołu

Niejawne przepływu logowania może wyglądać poniższej ilustracji. Każdy krok jest szczegółowo opisane w dalszej części tego artykułu.

![OpenID Connect ścieżek](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Wysyłanie żądania uwierzytelniania
Gdy aplikacja sieci web musi uwierzytelnić użytkownika i wykonywanie zasad, kieruje go użytkownikowi `/authorize` punktu końcowego. Jest to interakcyjne część przepływu, gdy użytkownik wykona akcję w zależności od zasad. Użytkownik uzyskuje identyfikator tokenu z punktu końcowego usługi Azure AD.

W tym żądaniu klienta wskazuje w `scope` parametru uprawnienia, które należy uzyskać od użytkownika. W `p` parametr wskazuje zasad do wykonania. Poniższe trzy przykłady (podziałami wierszy dla czytelności) każdego używają różnych zasad. Aby uzyskać pewne pojęcie o sposób działania każdego żądania, spróbuj wkleić żądania do przeglądarki i uruchomić go.

### <a name="use-a-sign-in-policy"></a>Użyj zasad logowania
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Użyj zasad rejestracji
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Użyj zasad edycji profilu
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [portalu Azure](https://portal.azure.com). |
| response_type |Wymagane |Musi zawierać `id_token` OpenID Connect logowaniu. Ponadto może zawierać typ odpowiedzi `token`. Jeśli używasz `token`, aplikacja może bezpośrednio odbierać token dostępu z punktu końcowego autoryzacji bez wprowadzania drugie żądanie do punktu końcowego autoryzacji.  Jeśli używasz `token` typ odpowiedzi `scope` parametr musi zawierać zakres, który wskazuje, którego zasobu należy wystawić token dla. |
| redirect_uri |Zalecane |Identyfikator URI przekierowania w aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Go musi dokładnie odpowiadać jeden przekierowania URI, który został zarejestrowany w portalu, z wyjątkiem tego, że musi być zakodowane w adresie URL. |
| response_mode |Zalecane |Określa metodę używaną do wysyłania wynikowy token wstecz do aplikacji.  Niejawne przepływów, użyj `fragment`. |
| scope |Wymagane |Rozdzieloną spacjami listę zakresów. Wskazuje wartość pojedynczy zakres do usługi Azure AD zarówno uprawnienia, które są żądane. `openid` Uprawnienia, aby zalogować użytkownika i Pobierz dane o użytkowniku w formie tokenów identyfikator wskazuje zakres. (Będzie omawianiu to bardziej dalszej części tego artykułu.) `offline_access` Zakres jest opcjonalne dla aplikacji sieci web. Oznacza to, że Twoja aplikacja powinna token odświeżania długotrwałe dostępu do zasobów. |
| state |Zalecane |Wartość zawarte w żądaniu, który jest także zwracany w odpowiedzi tokenu. Można go ciągiem zawartość, która ma być używany. Zazwyczaj wartość losowo generowany, unikatowy jest używana, aby zapobiec fałszerstwie żądania międzywitrynowego. Stan jest również używany do kodowania informacje o stanie użytkownika w aplikacji, przed wystąpieniem żądania uwierzytelniania, takich jak znajdowały się na stronie. |
| Identyfikator jednorazowy |Wymagane |Wartość zawarte w żądaniu (wygenerowany przez aplikację), który znajduje się w jego identyfikator tokenu jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby ograniczyć ataków powtórzeń tokenów. Wartość jest zazwyczaj losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| p |Wymagane |Zasady do wykonania. Jest to nazwa zasad, która jest tworzona w dzierżawie usługi Azure AD B2C. Wartość Nazwa zasad powinny rozpoczynać się od **b2c\_1\_**. Aby uzyskać więcej informacji, zobacz [wbudowane zasady usługi Azure AD B2C](active-directory-b2c-reference-policies.md). |
| wiersz |Optional (Opcjonalność) |Typ interakcji z użytkownikiem, które są wymagane. Obecnie jest jedyną poprawną wartością `login`. Dzięki temu użytkownik musi wprowadzić swoje poświadczenia dla tego żądania. Logowanie jednokrotne nie zacznie obowiązywać. |

W tym momencie użytkownik jest proszony o ukończenia przepływu pracy zasad. Może to obejmować wprowadzić swoją nazwę użytkownika i hasło, użytkownik zalogować społecznościowych tożsamości, podczas tworzenia katalogu lub dowolnej liczby kroków. Akcje użytkownika zależą od tego, jak zdefiniowano zasad.

Po wypełnieniu zasad przez użytkownika, usługi Azure AD zwraca odpowiedź do aplikacji przy wartości używane dla `redirect_uri`. Używa metody określonej w `response_mode` parametru. Odpowiedź jest dokładnie taka sama dla każdego z scenariuszy akcji użytkownika, niezależnie od zasady, które zostało wykonane.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie, która używa `response_mode=fragment` i `response_type=id_token+token` wygląda podobnie do następującego podziałami wierszy dla czytelności:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametr | Opis |
| --- | --- |
| access_token |Token dostępu do żądanej aplikacji.  Nie można zdekodować lub w przeciwnym razie sprawdził tokenu dostępu. Może być traktowana jako ciąg z ogólnym opisem. |
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługę Azure AD jest elementu nośnego. |
| expires_in |Długość czasu, przez który token dostępu jest nieprawidłowy (w sekundach). |
| scope |Token jest prawidłowy dla zakresów. Możesz również zakresów można użyć do pamięci podręcznej tokenów do późniejszego użycia. |
| żądaniu |Token ID żądanej aplikacji. Token identyfikator służy do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. Aby uzyskać więcej informacji o identyfikatorze tokeny i ich zawartość, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy również mogą być wysyłane identyfikator URI przekierowania, dzięki czemu aplikacja może je odpowiednią obsługę:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Służy do klasyfikowania typy błędów występujących ciąg kod błędu. Kod błędu: można również użyć do obsługi błędów. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |
| state |Zobacz pełną opisu w powyższej tabeli. Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne.|

## <a name="validate-the-id-token"></a>Zweryfikuj token Identyfikatora
Odbieranie identyfikator tokenu jest niewystarczająca do uwierzytelnienia użytkownika. Musisz zweryfikować podpisu tokenu identyfikator i sprawdzić oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Używa usługi Azure AD B2C [tokenów sieci Web JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Wiele bibliotek open source dostępnych sprawdzania poprawności tokenów Jwt, w zależności od języka, w którym można skorzystać. Należy wziąć pod uwagę eksploracji dostępnych bibliotek open source, a nie implementacji logiki weryfikacji. Informacje w tym artykule służy do zapoznawania się jak poprawnie używać tych bibliotek.

Usługa Azure AD B2C ma punkt końcowy metadanych OpenID Connect. Aplikacja można używać punktu końcowego można pobrać informacji na temat usługi Azure AD B2C w czasie wykonywania. Informacje te obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Brak dokument metadanych JSON dla każdej zasady w dzierżawie usługi Azure AD B2C. Na przykład dokument metadanych dla zasad b2c_1_sign_in w dzierżawie fabrikamb2c.onmicrosoft.com znajduje się na:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z właściwości tego dokumentu konfiguracji jest `jwks_uri`. Wartość dla tych samych zasad, będzie:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Aby określić zasady, które zostało użyte do podpisywania tokenu identyfikator (i miejsca do pobierania metadanych z), są dostępne dwie opcje. Po pierwsze, nazwa zasady jest dołączona do `acr` oświadczeń w `id_token`. Informacje o tym, jak można przeanalizować oświadczeń z tokenem identyfikator, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md). Drugą opcją jest do kodowania zasad wartości `state` parametru w przypadku wysyłania żądania. Następnie dekodowania `state` parametr, aby określić zasady, które zostało użyte. Każda metoda jest prawidłowa.

Po zostały nabyte dokument metadanych z punktem końcowym metadanych OpenID Connect, możesz użyć klucze publiczne RSA-256 (znajdujące się w tym punkcie końcowym) można zweryfikować podpisu tokenu identyfikator. Może istnieć wiele kluczy wymienione w tym punkcie końcowym w dowolnym momencie, identyfikowanych przez `kid`. Nagłówek `id_token` zawiera także `kid` oświadczeń. Wskazuje on, która z tych kluczy został użyty do podpisania token Identyfikatora. Aby uzyskać więcej informacji, łącznie z zapoznawanie [sprawdzanie poprawności tokenów](active-directory-b2c-reference-tokens.md#token-validation), zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Po weryfikacji podpisu tokenu identyfikator oświadczenia kilka Żądaj weryfikacji. Na przykład:

* Sprawdź poprawność `nonce` oświadczeń zapobiec atakom powtórzeń tokenów. Jego wartość musi być określony w żądaniu logowania.
* Sprawdź poprawność `aud` oświadczeń upewnić się, czy identyfikator token został wystawiony dla aplikacji. Jego wartość powinna być identyfikator aplikacji.
* Sprawdź poprawność `iat` i `exp` oświadczeń upewnić się, że identyfikator token nie wygasł.

Kilka więcej operacji sprawdzania poprawności, które należy wykonać opisane szczegółowo w [OpenID Connect podstawowych specyfikacji](http://openid.net/specs/openid-connect-core-1_0.html). Można również sprawdzić dodatkowe roszczenia, w zależności od danego scenariusza. Niektóre typowe operacje sprawdzania poprawności obejmują:

* Zapewnienie, że użytkownik lub organizacja po zarejestrowaniu aplikacji.
* Zapewnienie, że użytkownik ma odpowiednią autoryzacją i uprawnienia.
* Zapewnienie, że niektóre siły uwierzytelniania wystąpił, takie jak przy użyciu usługi Azure Multi-Factor Authentication.

Aby uzyskać więcej informacji na temat oświadczenia w tokenie identyfikator, zobacz [odwołania do tokenu usługi Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po całkowicie poprawności tokenu identyfikator można rozpocząć sesji z użytkownikiem. W aplikacji należy użyć oświadczeń token identyfikator, aby uzyskać informacje o użytkowniku. Te informacje może służyć do wyświetlania, rekordy, autoryzacji i tak dalej.

## <a name="get-access-tokens"></a>Pobieranie tokenów dostępu
Jeśli zasady wykonywania jedynym elementem aplikacji sieci web musi wykonać, można pominąć kilka kolejnych sekcjach. Informacje w poniższych sekcjach mają zastosowanie tylko do aplikacji sieci web, które należy uwierzytelnieni wywołań interfejsu API sieci web, a które są chronione przez usługę Azure AD B2C.

Teraz, gdy użytkownik został zalogowany, aplikacji jednej strony, możesz też uzyskać tokenów dostępu do sieci web wywoływania interfejsów API, które są zabezpieczone przez usługę Azure AD. Nawet jeśli już uzyskały tokenu przy użyciu `token` typ odpowiedzi, można używać tej metody można uzyskać tokeny, aby uzyskać dodatkowe zasoby bez przekierowanie użytkownika do logowania się ponownie.

W typowej sieci web przepływu aplikacji, można to zrobić przez żądanie `/token` punktu końcowego.  Punkt końcowy nie obsługuje jednak żądań CORPS, więc wywołań AJAX do pobrania i tokenów odświeżania nie jest opcją. Zamiast tego można przepływu niejawnego w ukrytym elemencie iframe HTML pobrać nowe tokeny dla innych interfejsów API sieci web. Oto przykład podziałami wierszy dla czytelności:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [portalu Azure](https://portal.azure.com). |
| response_type |Wymagane |Musi zawierać `id_token` OpenID Connect logowaniu.  Może również zawierać typ odpowiedzi `token`. Jeśli używasz `token` , aplikacja może bezpośrednio odbierać token dostępu z punktu końcowego autoryzacji bez wprowadzania drugie żądanie do punktu końcowego autoryzacji. Jeśli używasz `token` typ odpowiedzi `scope` parametr musi zawierać zakres, który wskazuje, którego zasobu należy wystawić token dla. |
| redirect_uri |Zalecane |Identyfikator URI przekierowania w aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Go musi dokładnie odpowiadać jeden przekierowania URI zarejestrowany w portalu, z wyjątkiem tego, że musi być zakodowane w adresie URL. |
| scope |Wymagane |Rozdzieloną spacjami listę zakresów.  W przypadku uzyskiwania tokenów, zawiera wszystkie zakresy, które wymagają danego zasobu. |
| response_mode |Zalecane |Określa metodę, która służy do wysyłania wynikowy token wstecz do aplikacji.  Może być `query`, `form_post`, lub `fragment`. |
| state |Zalecane |Wartość zawarte w żądaniu, który jest zwracany w odpowiedzi tokenu.  Można go ciągiem zawartość, która ma być używany.  Zazwyczaj wartość losowo generowany, unikatowy jest używana, aby zapobiec fałszerstwie żądania międzywitrynowego.  Stan jest również używany do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strony lub widoku użytkownik był na. |
| Identyfikator jednorazowy |Wymagane |Wartość zawarte w żądaniu wygenerowany przez aplikację, który znajduje się w jego identyfikator tokenu jako oświadczenia.  Aplikacja może zweryfikować tę wartość, aby ograniczyć ataków powtórzeń tokenów. Wartość jest zazwyczaj losowego, unikatowy ciąg identyfikujący pochodzenia żądania. |
| wiersz |Wymagane |Aby odświeżyć i uzyskać tokenów w ukrytym iframe, użyj `prompt=none` do zapewnienia, że element iframe nie zostać zablokowane na stronie logowania i natychmiast zwraca. |
| login_hint |Wymagane |Aby odświeżyć i uzyskać tokenów w ukrytym iframe, dołączyć tę wskazówkę odróżnienie wiele sesji użytkownika nazwa użytkownika może być w danym momencie. Można wyodrębnić nazwy użytkownika z wcześniejszych logowanie przy użyciu `preferred_username` oświadczeń. |
| domain_hint |Wymagane |Możliwe wartości to `consumers` i `organizations`.  Odświeżanie i uzyskiwania tokenów w ukrytym iframe, należy uwzględnić `domain_hint` wartość w żądaniu.  Wyodrębnij `tid` oświadczeń z tokenu identyfikator wcześniejszych logowania można określić wartość, która do użycia.  Jeśli `tid` oświadczeń, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`, użyj `domain_hint=consumers`.  W przeciwnym razie użyj `domain_hint=organizations`. |

Przez ustawienie `prompt=none` , to żądanie albo powiedzie się albo natychmiast kończy się niepowodzeniem i zwraca do aplikacji.  Odpowiedź oznaczająca Powodzenie są wysyłane do aplikacji na wskazanych przekierowania URI, za pomocą metody określonej w `response_mode` parametru.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` wygląda podobnie do następującej:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametr | Opis |
| --- | --- |
| access_token |Token, który żądanej aplikacji. |
| token_type |Typ tokenu jest zawsze elementu nośnego. |
| state |Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikację należy sprawdzić, czy `state` wartości żądań i odpowiedzi są identyczne. |
| expires_in |Jak długo token dostępu jest nieprawidłowy (w sekundach). |
| scope |Token dostępu jest nieprawidłowy dla zakresów. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy również mogą być wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może je odpowiednią obsługę.  Aby uzyskać `prompt=none`, oczekiwany błąd wygląda następująco:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących. Możesz również umożliwia ciąg reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |

Jeśli ten błąd jest wyświetlany w żądaniu iframe, użytkownik interakcyjnie zalogować się ponownie do pobrania nowego tokenu. Problem można rozwiązać w taki sposób, który ma sens dla aplikacji.

## <a name="refresh-tokens"></a>Tokenów odświeżania
Tokeny Identyfikatora i tokenów dostępu wygasają po krótkim czasie. Aby odświeżyć te tokeny okresowo muszą być przygotowane aplikacji.  Aby odświeżyć albo typ tokenu, wykonania tego samego żądania ukryte iframe użyliśmy w poprzedniego przykładu, za pomocą `prompt=none` parametru do kontrolowania kroków usługi Azure AD.  Aby otrzymać nowe `id_token` wartość, należy użyć `response_type=id_token` i `scope=openid`, a `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Jeśli chcesz zalogować użytkownika poza aplikacją przekierowuje użytkownika do usługi Azure AD, aby się wylogować. Jeśli tego nie zrobisz, użytkownik może mieć możliwość ponownego uwierzytelnienia do aplikacji bez konieczności ponownego wprowadzania poświadczeń. Jest tak, ponieważ mają one nieprawidłowy jednej logowania jednokrotnego sesji z usługą Azure AD.

Po prostu można przekierować użytkownika do `end_session_endpoint` wymieniony w tej samej OpenID Connect dokument metadanych opisanego w [Zweryfikuj token Identyfikatora](#validate-the-id-token). Na przykład:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
| p |Wymagane |Zasady, które mają być używane do podpisywania użytkownika z aplikacji. |
| post_logout_redirect_uri |Zalecane |Adres URL, który użytkownik powinien być przekierowywany użytkownik po pomyślnym wylogowania. Jeśli nie zostanie włączony, usługi Azure AD B2C jest wyświetlany komunikat ogólny. |

> [!NOTE]
> Kierowanie użytkownikowi `end_session_endpoint` czyści niektóre użytkownika pojedynczego logowania jednokrotnego stanu z usługi Azure AD B2C. Jednak nie go podpisać użytkownika z sesji dostawcy społecznościowych tożsamości użytkownika. Jeśli użytkownik wybierze dostawcy takie same identyfikację podczas kolejnych logowanie, użytkownik jest ponownie uwierzytelnić bez wprowadzania poświadczeń. Jeśli użytkownik chce, aby wylogować się z aplikacji Azure AD B2C, go nie musi oznaczać, że mają być całkowicie wylogować się z konta usługi Facebook, na przykład. Jednak dla kont lokalnych sesji użytkownika zostanie zakończona poprawnie.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Użyj dzierżawy usługi Azure AD B2C
Aby samodzielnie wypróbować te żądania, wykonaj następujące trzy kroki. Zastąp przykładowe wartości używanej w tym artykule i własne wartości:

1. [Tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md). Użyj nazwy dzierżawy w żądaniach.
2. [Tworzenie aplikacji](active-directory-b2c-app-registration.md) uzyskać identyfikator aplikacji i `redirect_uri` wartość. Uwzględnij aplikację sieci web lub interfejsu API sieci web w aplikacji. Opcjonalnie możesz utworzyć klucz tajny aplikacji.
3. [Tworzenie zasad](active-directory-b2c-reference-policies.md) można uzyskać nazwy zasad.

## <a name="samples"></a>Przykłady

* [Tworzenie aplikacji jednej strony przy użyciu środowiska Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Tworzenie aplikacji jednej strony przy użyciu programu .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

