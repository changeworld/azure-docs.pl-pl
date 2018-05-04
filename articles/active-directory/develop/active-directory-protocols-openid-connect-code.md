---
title: Zrozumienie przepływu kodu uwierzytelniania OpenID Connect w usłudze Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia wiadomości HTTP do autoryzowania dostępu do aplikacji sieci web i interfejsów API sieci web w dzierżawie przy użyciu usługi Azure Active Directory i OpenID Connect.
services: active-directory
documentationcenter: .net
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4d9593aad789a9888c32297d634ba19e669bd461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoryzacja dostępu do aplikacji sieci web przy użyciu protokołu OpenID Connect i Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) to warstwa proste tożsamości oparty na protokole OAuth 2.0. OAuth 2.0 definiuje mechanizmy do użycia **tokenów dostępu** dostęp do chronionych zasobów, ale nie definiują standardowe metody w celu zapewnienia informacji o tożsamości. OpenID Connect implementuje uwierzytelnianie jako rozszerzenie procesu autoryzacji OAuth 2.0. Zawiera informacje na temat użytkownika końcowego w formie `id_token` weryfikuje tożsamość użytkownika i udostępnia informacje podstawowe profilu użytkownika.

OpenID Connect Oto Nasze zalecenia, jeśli tworzysz aplikację sieci web jest hostowany na serwerze i dostępne za pośrednictwem przeglądarki.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania przy użyciu protokołu OpenID Connect
Najbardziej podstawowa przepływu logowania zawiera następujące czynności — każdy z nich jest szczegółowo opisana poniżej.

![OpenId Connect przepływ uwierzytelniania](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadanych OpenID Connect

OpenID Connect zawiera opis dokument metadanych zawiera większość informacji wymaganych dla aplikacji wykonać logowania. W tym informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. Dokument metadanych OpenID Connect można znaleźć w folderze:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadane są proste dokumentu JavaScript Object Notation (JSON). Zobacz poniższy fragment kodu, na przykład. Fragment treści są szczegółowo opisane [OpenID Connect specyfikacji](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania
Gdy aplikacja sieci web musi uwierzytelnić użytkownika, należy skierować użytkownika `/authorize` punktu końcowego. To żądanie jest podobny do pierwszego etap [przepływu kodu autoryzacji OAuth 2.0](active-directory-protocols-oauth-code.md), z kilku ważnych różnice:

* Żądanie musi zawierać zakres `openid` w `scope` parametru.
* `response_type` Musi zawierać parametr `id_token`.
* Żądanie musi zawierać `nonce` parametru.

Dlatego przykładowe żądanie będzie wyglądać następująco:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parametr |  | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane |`{tenant}` Wartość w ścieżce żądania może być używana w celu kontrolowania, kto może zalogować się do aplikacji.  Dozwolone wartości to identyfikatory dzierżawy, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` tokenów niezależny od dzierżawcy |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji podczas rejestrowania z usługą Azure AD. To można znaleźć w portalu Azure. Kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji**, wybierz aplikację i zlokalizuj identyfikator aplikacji na stronie aplikacji. |
| response_type |Wymagane |Musi zawierać `id_token` OpenID Connect logowaniu.  Może również obejmować innych response_types, takich jak `code`. |
| scope |Wymagane |Rozdzieloną spacjami listę zakresów.  Dla protokołu OpenID Connect, musi zawierać zakres `openid`, co przekłada się uprawnienie "Logowanie się w" w zgody interfejsu użytkownika.  Można również użyć innych zakresów w tym żądaniu żądanych zgody. |
| Identyfikator jednorazowy |Wymagane |Wartość zawarte w żądaniu wygenerowany przez aplikację, który znajduje się w wynikowym `id_token` jako oświadczenia.  Aplikacja może zweryfikować tę wartość, aby ograniczyć ataków powtórzeń tokenów.  Wartość jest zazwyczaj losowy unikatowy ciąg lub identyfikator GUID, który może służyć do identyfikowania pochodzenia żądania. |
| redirect_uri |Zalecane |Redirect_uri aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania.  Go musi dokładnie pasować redirect_uris, który został zarejestrowany w portalu, z wyjątkiem musi być zakodowane w adresie url. |
| response_mode |Zalecane |Określa metodę, które mają być używane do odesłania wynikowy authorization_code do aplikacji.  Obsługiwane wartości to `form_post` dla *HTTP post formularza* i `fragment` dla *fragmentu adresu URL*.  Dla aplikacji sieci web, zaleca się używanie `response_mode=form_post` zapewnienie najbardziej bezpieczny transfer tokenów do aplikacji. Domyślnie, jeśli `response_mode` nie jest dołączana, jest `fragment`.|
| state |Zalecane |Wartość zawarte w żądaniu, który jest zwracany w odpowiedzi tokenu.  Można go ciągiem zawartość, która ma.  Losowo generowany unikatową wartość jest zazwyczaj używana w przypadku [zapobieganie fałszerstwie żądania międzywitrynowego](http://tools.ietf.org/html/rfc6749#section-10.12).  Stan służy także do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |
| wiersz |opcjonalne |Wskazuje typ interakcji z użytkownikiem, który jest wymagany.  Obecnie jedynymi prawidłowymi wartościami są "login", "none", "wyrazić zgodę".  `prompt=login` Wymusza na użytkowniku, aby wprowadzić swoje poświadczenia w tym żądaniu Negacja jednokrotnego.  `prompt=none` jest to odwrotność wzorca - gwarantuje, że użytkownik nie zobaczy jakiejkolwiek monitu interakcyjnego.  Jeśli żądanie nie można ukończyć w trybie dyskretnym za pośrednictwem jednokrotnego, punkt końcowy zwraca błąd.  `prompt=consent` Wyzwalacze uwierzytelniania OAuth zgoda okna dialogowego po użytkownik się zaloguje, użytkownikowi uprawnienia do aplikacji. |
| login_hint |opcjonalne |Można wstępnie wypełnić pole adresu e-mail/nazwa użytkownika strony logowania dla użytkownika, jeśli znasz swoją nazwę użytkownika wcześniejsze.  Aplikacje często tego parametru należy użyć podczas ponownego uwierzytelniania, już o wyodrębnić nazwy użytkownika z poprzedniej logowania przy użyciu `preferred_username` oświadczeń. |

W tym momencie użytkownik jest proszony o wprowadzenie poświadczeń i wykonania uwierzytelnienia.

### <a name="sample-response"></a>Przykładowa odpowiedź
Przykładowa odpowiedź, po uwierzytelnieniu użytkownika, może wyglądać następująco:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| żądaniu |`id_token` Żądany przez aplikację. Można użyć `id_token` do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. |
| state |Wartość zawarte w żądaniu, który jest także zwracany w odpowiedzi tokenu. Losowo generowany unikatową wartość jest zazwyczaj używana w przypadku [zapobieganie fałszerstwie żądania międzywitrynowego](http://tools.ietf.org/html/rfc6749#section-10.12).  Stan służy także do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` dzięki aplikacji można je odpowiednią obsługę:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji
W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametr odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, takie jak brak wymaganego parametru. |Usuń i ponownie prześlij żądanie. Jest to błąd programowanie i zwykle zostanie przechwycony podczas testowania początkowej. |
| unauthorized_client |Aplikacja kliencka nie jest dozwolone przy kod autoryzacji żądania. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikację można monitować użytkownika z instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| access_denied |Odmowa zgody właściciel zasobu |Aplikacja kliencka powiadamiać użytkownika, którego nie można kontynuować, chyba że użytkownik zgadza. |
| unsupported_response_type |Serwer autoryzacji w żądaniu nie obsługuje typ odpowiedzi. |Usuń i ponownie prześlij żądanie. Jest to błąd programowanie i zwykle zostanie przechwycony podczas testowania początkowej. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy może wynikać z tymczasowego warunków. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźnione z powodu tymczasowy błąd. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądania. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźniony ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, nie można znaleźć usługi Azure AD lub nie została poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikację można monitować użytkownika z instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |

## <a name="validate-the-idtoken"></a>Sprawdź poprawność żądaniu
Tylko odbieranie `id_token` nie wystarcza do uwierzytelniania użytkownika, musisz zweryfikować podpisu i sprawdzić oświadczeń w `id_token` na wymagania dotyczące Twojej aplikacji. Punkt końcowy usługi Azure AD używa tokenów sieci Web JSON (Jwt) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Możesz zweryfikować `id_token` w kliencie kod, ale popularną praktyką jest wysłanie `id_token` do serwera wewnętrznej bazy danych i wykonywać sprawdzanie poprawności. Po upewnieniu się podpis `id_token`, istnieje kilka oświadczenia są wymagane do zweryfikowania.

Możesz również sprawdzić dodatkowe roszczenia, w zależności od danego scenariusza. Niektóre typowe operacje sprawdzania poprawności obejmują:

* Zapewnienie użytkownika/organizacji po zarejestrowaniu aplikacji.
* Zapewnienie użytkownika ma właściwe autoryzacji/uprawnienia
* Zapewnienie siły uwierzytelniania wystąpił, takich jak uwierzytelnianie wieloskładnikowe.

Po zweryfikowaniu `id_token`, można rozpocząć sesji z użytkownikiem i korzystać z oświadczeń z `id_token` Aby uzyskać informacje o użytkowniku w aplikacji. Te informacje można służyć do wyświetlania rekordów, autoryzacje itp. Aby uzyskać więcej informacji o typach tokenów i oświadczeń, przeczytaj [obsługiwany Token i typy oświadczeń](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Jeśli chcesz zalogować użytkownika poza aplikacją nie jest wystarczające, aby wyczyścić pliki cookie aplikacji lub w inny sposób zakończenia sesji z użytkownikiem.  Należy również przekierować użytkownika do `end_session_endpoint` do wylogowania.  Jeśli nie to zrobić, użytkownik będzie mógł ponownego uwierzytelnienia do aplikacji bez ponownego wprowadzania poświadczeń, ponieważ mają one nieprawidłowy jednej logowania jednokrotnego sesji z punktem końcowym usługi Azure AD.

Można przekierowywać po prostu użytkownikowi `end_session_endpoint` wymienione w dokumencie metadanych OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Opis |
| --- | --- | --- |
| post_logout_redirect_uri |Zalecane |Adres URL użytkownika powinny być przekierowywany użytkownik po wylogowania się pomyślnie.  Jeśli nie zostanie uwzględniony, użytkownik jest wyświetlany komunikat ogólny. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne
Jeśli przekierowanie użytkownika do `end_session_endpoint`, usługi Azure AD czyści sesji użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają usługi Azure AD do uwierzytelniania. Aby włączyć tych aplikacji, aby jednocześnie wylogowanie użytkownika, usługi Azure AD wysyła żądanie HTTP GET do zarejestrowaną `LogoutUrl` wszystkich aplikacji, które użytkownik jest aktualnie zalogowany. Aplikacje należy odpowiedzieć na to żądanie przez wyczyszczenie wszelkich sesji, który identyfikuje użytkownika i zwracanie `200` odpowiedzi.  Jeśli chcesz limit obsługi funkcji logowania jednokrotnego do aplikacji, musisz zaimplementować takie `LogoutUrl` w kodzie aplikacji.  Można ustawić `LogoutUrl` z portalu Azure:

1. Przejdź do [portalu Azure](https://portal.azure.com).
2. Wybierz usługi Active Directory, klikając na Twoim koncie w prawym górnym rogu strony.
3. W panelu nawigacyjnym po lewej stronie wybierz opcje **usługi Azure Active Directory**, a następnie wybierz **rejestracji aplikacji** i wybierz aplikację.
4. Polecenie **ustawienia**, następnie **właściwości** i Znajdź **adresu URL wylogowania** pola tekstowego. 

## <a name="token-acquisition"></a>Token nabycia
Wiele aplikacji sieci web należy nie tylko zalogować użytkownika, ale także uzyskać dostęp do usługi sieci web w imieniu użytkownika, że w trybie OAuth. W tym scenariuszu łączy OpenID Connect do uwierzytelniania użytkowników podczas pobierania jednocześnie `authorization_code` można pobrać `access_tokens` przy użyciu [przepływu kodu autoryzacji OAuth](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Pobieranie tokenów dostępu
Uzyskanie tokenów dostępu, należy zmodyfikować żądanie logowania z powyżej:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

W tym zakresy uprawnień w żądaniu i używając `response_type=code+id_token`, `authorize` punktu końcowego gwarantuje, że użytkownik zgodził się na uprawnienia wskazanych w `scope` parametr zapytania i zwracają kod autoryzacji do programu exchange dla twojej aplikacji token dostępu.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=form_post` wygląda jak:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| żądaniu |`id_token` Żądany przez aplikację. Można użyć `id_token` do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. |
| kod |Authorization_code, żądany przez aplikację. Aplikację można użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkie żyła i zwykle wygaśnie po około 10 minut. |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikacja powinna Sprawdź, czy wartości stan żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` dzięki aplikacji można je odpowiednią obsługę:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu uwierzytelniania. |

Opis swoje działania zalecane klienta i kody błędów można znaleźć [kody błędów dla błędów punktu końcowego autoryzacji](#error-codes-for-authorization-endpoint-errors).

Po ich zaakceptujesz autoryzacji `code` i `id_token`, można zalogować użytkownika i uzyskać tokeny dostępu w ich imieniu.  Aby zalogować się użytkownik, musisz zweryfikować `id_token` dokładnie tak jak opisano powyżej. Aby uzyskać tokenów dostępu, można wykonać kroki opisane w sekcji "Użyj kodu autoryzacji do żądania tokenu dostępu" naszych [dokumentacji protokołu OAuth](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
