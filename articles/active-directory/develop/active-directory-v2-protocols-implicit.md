---
title: Zabezpieczanie aplikacji jednej strony przy użyciu niejawnego przepływu v2.0 usługi Azure AD | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web przy użyciu usługi Azure AD w wersji 2.0 implementacji przepływu niejawnego dla aplikacji jednej strony.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 07fbda30cdc76e5e4e82b79954d0b0a56e032b50
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158391"
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>Protokoły - źródła przy użyciu niejawnego przepływu w wersji 2.0
Z punktem końcowym v2.0 można zalogować się użytkowników do aplikacji jednej strony z konta osobiste oraz pracy/służbowego firmy Microsoft. Jednej strony i innych aplikacji JavaScript, które działają głównie w kilka interesujące będzie wymagał uwierzytelniania w przypadku krój przeglądarki:

* Właściwości zabezpieczeń te aplikacje są znacznie różni się od tradycyjnych na serwerze sieci web aplikacji.
* Wiele serwerów autoryzacji & dostawców tożsamości nie obsługują żądania CORS.
* Przekierowuje przeglądarki całą stronę od aplikacji stają się szczególnie inwazyjne środowiska użytkownika.

W przypadku tych aplikacji (wziąć pod uwagę: AngularJS, Ember.js, React.js, itp.) usługi Azure AD obsługuje przepływ udzielania niejawne OAuth 2.0. Przepływu niejawnego jest opisany w [OAuth 2.0 specyfikacji](http://tools.ietf.org/html/rfc6749#section-4.2). Podstawowy korzyści jest możliwość aplikację, aby uzyskać tokenów z usługi Azure AD bez wykonywania serwera wewnętrznej bazy danych programu exchange poświadczeń. Umożliwia to aplikacji zalogować użytkownika, zachowania sesji i uzyskać tokeny dla innych interfejsów API sieci web w kliencie kodu JavaScript. Istnieje kilka ważne zagadnienia dotyczące zabezpieczeń wziąć pod uwagę, gdy przy użyciu niejawnego przepływu — w szczególności około [klienta](http://tools.ietf.org/html/rfc6749#section-10.3) i [personifikacji użytkownika](http://tools.ietf.org/html/rfc6749#section-10.3).

Jeśli chcesz użyć niejawnego przepływu i Azure AD do Dodawanie uwierzytelniania do aplikacji JavaScript, zalecane jest użycie nasza Biblioteka JavaScript typu open source [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Istnieje kilka samouczki AngularJS [tutaj](active-directory-appmodel-v2-overview.md#getting-started) ułatwiające rozpoczęcie pracy. 

Jednak jeśli użytkownik nie chce używać biblioteki w jednej strony aplikacji i wysyłać wiadomości protokołu samodzielnie, wykonaj poniższe kroki ogólne.

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariuszy i funkcji obsługiwanych przez punktu końcowego v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="protocol-diagram"></a>Diagram protokołu
Cały logowania przepływu niejawnego wygląda następująco — poszczególne kroki opisano szczegółowo poniżej.

![OpenId Connect ścieżek](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania
Początkowo zalogowania się użytkownika do aplikacji, możesz wysłać [OpenID Connect](active-directory-v2-protocols-oidc.md) żądania autoryzacji i get `id_token` z punktem końcowym v2.0:

> [!IMPORTANT]
> Aby pomyślnie żądania tokenu identyfikator rejestracji aplikacji w [portal rejestracji](https://apps.dev.microsoft.com) musi mieć **[niejawne Przyznaj](active-directory-v2-protocols-implicit.md)** włączona dla klienta sieci Web. Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: "podana wartość parametru wejściowego 'response_type' nie jest dozwolona dla tego klienta. Oczekiwana wartość to "code" "

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Kliknij poniższe łącze, aby wykonać tego żądania. Po zalogowaniu, przeglądarka powinno nastąpić przekierowanie do `https://localhost/myapp/` z `id_token` na pasku adresu.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametr |  | Opis |
| --- | --- | --- |
| dzierżawa |wymagane |`{tenant}` Wartość w ścieżce żądania może być używana w celu kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości `common`, `organizations`, `consumers`i dzierżawców identyfikatorów. Aby uzyskać więcej szczegółów, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| client_id |wymagane |Identyfikator aplikacji który portalu rejestracji ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) przypisany do aplikacji. |
| response_type |wymagane |Musi zawierać `id_token` OpenID Connect logowaniu. Mogą również obejmować response_type `token`. Przy użyciu `token` tutaj zezwala aplikacji na odbieranie token dostępu bezpośrednio z punktu końcowego autoryzacji bez konieczności drugie żądanie do punktu końcowego autoryzacji. Jeśli używasz `token` response_type, `scope` parametr może zawierać wskazująca, którego zasobu należy wystawić token dla zakresu. |
| redirect_uri |Zalecane |Redirect_uri aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Go musi dokładnie pasować redirect_uris, który został zarejestrowany w portalu, z wyjątkiem musi być zakodowane w adresie url. |
| scope |wymagane |Rozdzieloną spacjami listę zakresów. Dla protokołu OpenID Connect, musi zawierać zakres `openid`, co przekłada się uprawnienie "Logowanie się w" w zgody interfejsu użytkownika. Opcjonalnie możesz uwzględnienie `email` lub `profile` [zakresy](active-directory-v2-scopes.md) do uzyskania dostępu do danych użytkownika dodatkowe. Można również użyć innych zakresów w tym żądaniu żądanych zgodzić się na różnych zasobów. |
| response_mode |Zalecane |Określa metodę, które mają być używane do wysyłania wynikowy token wstecz do aplikacji. Powinien być `fragment` dla niejawnego przepływu. |
| state |Zalecane |Wartość zawarte w żądaniu zwracana w odpowiedzi tokenu. Można go ciągiem zawartość, która ma. Losowo generowany unikatową wartość jest zazwyczaj używana w przypadku [zapobieganie fałszerstwie żądania międzywitrynowego](http://tools.ietf.org/html/rfc6749#section-10.12). Stan służy także do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |
| Identyfikator jednorazowy |wymagane |Wartość zawarte w żądaniu wygenerowany przez aplikację, która zostanie uwzględniona w wynikowej żądaniu jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby ograniczyć ataków powtórzeń tokenów. Wartość jest zazwyczaj losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| wiersz |opcjonalne |Wskazuje typ interakcji z użytkownikiem, który jest wymagany. W tej chwili jedynymi prawidłowymi wartościami są "login", "none", "wyrazić zgodę". `prompt=login` Spowoduje to wymuszenie użytkownika o wprowadzenie poświadczeń w tym żądaniu Negacja jednokrotnego. `prompt=none` jest to odwrotność wzorca - zapewni, że użytkownik nie zobaczy jakiejkolwiek monitu interakcyjnego. Jeśli żądanie nie można ukończyć w trybie dyskretnym za pośrednictwem jednokrotnego, punktu końcowego v2.0 spowoduje zwrócenie błędu. `prompt=consent` wyzwoli okna dialogowego zgoda OAuth, po użytkownik się zaloguje, użytkownikowi uprawnienia do aplikacji. |
| login_hint |opcjonalne |Można wstępnie wypełnić pole adresu e-mail/nazwa użytkownika konta na stronie dla użytkownika, jeśli znasz swoją nazwę użytkownika wcześniejsze. Aplikacje często użyje tego parametru podczas ponownego uwierzytelniania już o wyodrębnić nazwy użytkownika z poprzedniej logowania przy użyciu `preferred_username` oświadczeń. |
| domain_hint |opcjonalne |Może być jednym z `consumers` lub `organizations`. Jeśli uwzględniona, pominie proces odnajdowania opartych na poczcie e-mail tego użytkownika odbywa się przez na stronie rejestracji v2.0, co może prowadzić do wymaga nieco więcej udoskonalone środowisko użytkownika. Aplikacje często użyje tego parametru podczas ponownego uwierzytelniania, wyodrębniając `tid` oświadczeń w żądaniu. Jeśli `tid` oświadczeń, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft konsumenta dzierżawcy), należy użyć `domain_hint=consumers`. W przeciwnym razie użyj `domain_hint=organizations`. |


W tym momencie użytkownik jest proszony o wprowadzenie poświadczeń i wykonania uwierzytelnienia. Punktu końcowego v2.0 będzie również upewnij się, że użytkownik zgodził się na uprawnienia wskazanych w `scope` parametr zapytania. Jeśli użytkownik zgodził się nie do dowolnego z tych uprawnień, poprosi użytkownika o zgodę na wymagane uprawnienia. Szczegóły [uprawnień, zgody i aplikacje wielodostępne znajdują się w tym miejscu](active-directory-v2-scopes.md).

Po przyznaje zgody i uwierzytelnia użytkownika, punktu końcowego v2.0 zwróci odpowiedzi do aplikacji na wskazany `redirect_uri`, za pomocą metody określonej w `response_mode` parametru.

#### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` i `response_type=id_token+token` wygląda podobnie do następującego podziałami wierszy dla czytelności:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Opis |
| --- | --- |
| access_token |Jeśli uwzględniona `response_type` obejmuje `token`. Token dostępu do tej aplikacji, w tym przypadku dla programu Microsoft Graph. Token dostępu nie powinna być zdekodowany lub w przeciwnym razie inspekcji, może być traktowana jako ciąg z ogólnym opisem. |
| token_type |Jeśli uwzględniona `response_type` obejmuje `token`. Zawsze będzie `Bearer`. |
| expires_in |Jeśli uwzględniona `response_type` obejmuje `token`. Określa liczbę sekund, przez które token jest prawidłowy na potrzeby buforowania. |
| scope |Jeśli uwzględniona `response_type` obejmuje `token`. Wskazuje zakresy, dla którego będzie obowiązywać ' access_token '. |
| żądaniu |Żądaniu, żądany przez aplikację. Żądaniu służy do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. Więcej informacji na temat id_tokens i ich zawartość znajduje się w [odwołania do tokenu punktu końcowego v2.0](active-directory-v2-tokens.md). |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikacja powinna Sprawdź, czy wartości stan żądania i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` dzięki aplikacji można je odpowiednią obsługę:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |

## <a name="validate-the-idtoken"></a>Sprawdź poprawność żądaniu
Tylko odbieranie żądaniu nie jest wystarczająca do uwierzytelnienia użytkownika; Musisz zweryfikować podpisu w żądaniu i sprawdzić oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Korzysta z punktu końcowego v2.0 [tokenów sieci Web JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Możesz zweryfikować `id_token` w kliencie kod, ale popularną praktyką jest wysłanie `id_token` do serwera wewnętrznej bazy danych i wykonywać sprawdzanie poprawności. Po upewnieniu się podpis żądaniu, istnieje kilka oświadczeń, który trzeba będzie można zweryfikować. Zobacz [odwołania do tokenu v2.0](active-directory-v2-tokens.md) uzyskać więcej informacji, łącznie z [sprawdzania poprawności tokenów](active-directory-v2-tokens.md#validating-tokens) i [ważne informacje dotyczące podpisywania klucza przerzucania](active-directory-v2-tokens.md#validating-tokens). Firma Microsoft zaleca korzystające z biblioteki do analizowania i weryfikowania tokenów — Brak co najmniej jeden dostępny dla większości języków i platform.
<!--TODO: Improve the information on this-->

Możesz również sprawdzić dodatkowe roszczenia, w zależności od danego scenariusza. Niektóre typowe operacje sprawdzania poprawności obejmują:

* Zapewnienie użytkownika/organizacji po zarejestrowaniu aplikacji.
* Zapewnienie użytkownika ma właściwe autoryzacji/uprawnienia
* Zapewnienie siły uwierzytelniania wystąpił, takich jak uwierzytelnianie wieloskładnikowe.

Aby uzyskać więcej informacji dotyczących oświadczeń w żądaniu, zobacz [odwołania do tokenu punktu końcowego v2.0](active-directory-v2-tokens.md).

Po zweryfikowaniu całkowicie żądaniu, można rozpocząć sesji z użytkownikiem i użyć oświadczeń w żądaniu, aby uzyskać informacje o użytkowniku w aplikacji. Te informacje można służyć do wyświetlania rekordów, autoryzacje itp.

## <a name="get-access-tokens"></a>Pobieranie tokenów dostępu
Teraz, kiedy się zalogowano użytkownika aplikacji jednej strony, możesz też uzyskać tokenów dostępu do wywoływania interfejsów API, zabezpieczone przez usługę Azure AD, takich jak sieci web [Microsoft Graph](https://graph.microsoft.io). Nawet jeśli masz już tokenu przy użyciu `token` response_type, korzystając tej metody można uzyskać tokeny do dodatkowych zasobów bez konieczności przekierowuje użytkownika do logowania się ponownie.

W normalnym przepływie OpenID Connect/OAuth, możesz to zrobić przy żądaniu skierowanym do v2.0 `/token` punktu końcowego. Jednak punktu końcowego v2.0 nie obsługuje żądań CORPS, więc wywołań AJAX do pobrania i tokenów odświeżania jest poza pytanie. Zamiast tego można przepływu niejawnego w ukrytym iframe pobrać nowe tokeny dla innych interfejsów API sieci web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Spróbuj wykonać kopiowanie i wklejanie poniżej żądania do karty przeglądarki! (Pamiętaj zastąpić `domain_hint` i `login_hint` wartości przy użyciu prawidłowych wartości dla użytkownika)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parametr |  | Opis |
| --- | --- | --- |
| dzierżawa |wymagane |`{tenant}` Wartość w ścieżce żądania może być używana w celu kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości `common`, `organizations`, `consumers`i dzierżawców identyfikatorów. Aby uzyskać więcej szczegółów, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| client_id |wymagane |Identyfikator aplikacji, która portalu rejestracji ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) przypisany do aplikacji. |
| response_type |wymagane |Musi zawierać `id_token` OpenID Connect logowaniu. Może również obejmować innych response_types, takich jak `code`. |
| redirect_uri |Zalecane |Redirect_uri aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Go musi dokładnie pasować redirect_uris, który został zarejestrowany w portalu, z wyjątkiem musi być zakodowane w adresie url. |
| scope |wymagane |Rozdzieloną spacjami listę zakresów. Uzyskiwania tokenów, Uwzględnij wszystkie [zakresy](active-directory-v2-scopes.md) wymagają zasobu zainteresowań. |
| response_mode |Zalecane |Określa metodę, które mają być używane do wysyłania wynikowy token wstecz do aplikacji. Może być jednym z `query`, `form_post`, lub `fragment`. |
| state |Zalecane |Wartość zawarte w żądaniu zwracana w odpowiedzi tokenu. Można go ciągiem zawartość, która ma. Losowo generowany unikatową wartość jest zwykle używany zapobiegania fałszerstwie żądania międzywitrynowego. Stan służy także do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |
| Identyfikator jednorazowy |wymagane |Wartość zawarte w żądaniu wygenerowany przez aplikację, która zostanie uwzględniona w wynikowej żądaniu jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby ograniczyć ataków powtórzeń tokenów. Wartość jest zazwyczaj losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| wiersz |wymagane |Aby odświeżyć & uzyskiwania tokenów w ukrytym iframe, należy użyć `prompt=none` do zapewnienia, że element iframe nie zostać zawieszone na stronę logowania w wersji 2.0 i natychmiast zwraca. |
| login_hint |wymagane |Odświeżanie i uzyskiwania tokenów w ukrytym iframe, nazwa użytkownika musi zawierać w tym warunku w celu odróżnienia wiele sesji, które użytkownik może mieć w danym punkcie w czasie. Nazwa użytkownika może wyodrębnić po poprzednim logowania za pomocą `preferred_username` oświadczeń. |
| domain_hint |wymagane |Może być jednym z `consumers` lub `organizations`. Odświeżanie i uzyskiwania tokenów w ukrytym iframe, musi zawierać domain_hint w żądaniu. Należy wyodrębnić `tid` oświadczeń w żądaniu z poprzednich logowanie do określenia wartości, które do użycia. Jeśli `tid` oświadczeń, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad`, należy użyć `domain_hint=consumers`. W przeciwnym razie użyj `domain_hint=organizations`. |

Podziękowania dla `prompt=none` , tego żądania zostanie albo powiedzie się albo natychmiast zakończyć się niepowodzeniem i wróć do aplikacji. Odpowiedź oznaczająca Powodzenie zostaną wysłane do aplikacji na wskazany `redirect_uri`, za pomocą metody określonej w `response_mode` parametru.

#### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=fragment` wygląda jak:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametr | Opis |
| --- | --- |
| access_token |Token, który żądanej aplikacji. |
| token_type |Zawsze będzie `Bearer`. |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikacja powinna Sprawdź, czy wartości stan żądania i odpowiedzi są identyczne. |
| expires_in |Jak długo token dostępu jest nieprawidłowy (w sekundach). |
| scope |Token dostępu jest nieprawidłowy dla zakresów. |

#### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` dzięki aplikacji można je odpowiednią obsługę. W przypadku liczby `prompt=none`, będzie oczekiwany błąd:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |

Jeśli ten błąd jest wyświetlany w żądaniu iframe, użytkownik interakcyjnie zalogować się ponownie do pobrania nowego tokenu. Można obsłużyć tego przypadku w dowolnie wybrany sposób sens dla aplikacji.

## <a name="validating-access-tokens"></a>Sprawdzanie poprawności tokenów dostępu

Po otrzymaniu ' access_token ', upewnij się, że sprawdzanie poprawności podpisu tokenu, a także następujące oświadczeń. Można również sprawdzić dodatkowe oświadczeń w danym scenariuszu. 

* **grupy odbiorców** oświadczeń, aby upewnić się, że token jest przeznaczona do aplikacji
* **Wystawca** oświadczeń, aby sprawdzić, czy token został wystawiony do aplikacji przez punktu końcowego v2.0
* **nie wcześniej niż** i **czas wygaśnięcia** oświadczeń, aby sprawdzić, czy token nie wygasł

Aby uzyskać więcej informacji na temat oświadczenia w tokenie dostępu, zobacz [tokenu odwołania do punktu końcowego v2.0](active-directory-v2-tokens.md)

## <a name="refreshing-tokens"></a>Odświeżanie tokenów
Niejawne Przyznaj nie zapewnia tokenów odświeżania. Zarówno `id_token`s i `access_token`s wygaśnie po krótkim czasie, więc aplikacji muszą być przygotowane, aby odświeżyć te tokeny okresowo. Aby odświeżyć albo typ tokenu, można wykonać tego samego żądania ukryte iframe powyższego przy użyciu `prompt=none` parametr do kontrolowania zachowania usługi Azure AD. Jeśli chcesz otrzymywać nową `id_token`, należy użyć `response_type=id_token` i `scope=openid`, a także `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie logowania
OpenIdConnect `end_session_endpoint` umożliwia aplikacji wysyłanie żądań do punktu końcowego v2.0, aby zakończyć sesję użytkownika i wyczyszczenie plików cookie, ustawione przez punktu końcowego v2.0. Pełni logowania użytkownika z aplikacji sieci web, aplikacji należy zakończyć sesję użytkownika (zwykle przez wyczyszczenie pamięci podręcznej tokenu lub usunięcie plików cookie), a następnie przekierować przeglądarkę, aby:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Opis |
| --- | --- | --- |
| dzierżawa |wymagane |`{tenant}` Wartość w ścieżce żądania może być używana w celu kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości `common`, `organizations`, `consumers`i dzierżawców identyfikatorów. Aby uzyskać więcej szczegółów, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | Zalecane | Adres URL, który użytkownik powinien nastąpić powrót do po zakończeniu Wyloguj. Ta wartość musi pasować przekierowania zarejestrowanych identyfikatorów URI dla aplikacji. Jeśli nie zostanie uwzględniony, zostanie wyświetlony komunikat ogólny przez punkt końcowy w wersji 2.0. |
