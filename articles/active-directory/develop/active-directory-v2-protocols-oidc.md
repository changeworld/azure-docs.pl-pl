---
title: V2.0 usługi Azure Active Directory i protokołu OpenID Connect | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web przy użyciu usługi Azure AD w wersji 2.0 implementacji protokołu uwierzytelniania OpenID Connect.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: fd1f29f5c2920ea9956d883b9668f36c934a5e59
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>V2.0 usługi Azure Active Directory i protokołu OpenID Connect
OpenID Connect to protokół uwierzytelniania, oparty na OAuth 2.0, który służy do bezpiecznego logowania użytkownika do aplikacji sieci web. Korzystając z punktem końcowym v2.0 wdrażania protokołu OpenID Connect, logowania i dostępu do interfejsu API można dodać do aplikacji opartych na sieci web. W tym artykule firma Microsoft opisano, jak zrobić to niezależny od języka. Opisano sposób wysyłania i odbierania wiadomości HTTP bez korzystania z żadnych bibliotek open source firmy Microsoft.

> [!NOTE]
> Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół OAuth 2.0 *autoryzacji* protokół do użycia jako *uwierzytelniania* protokołu, dzięki czemu można wykonywać z jednego logowania w trybie OAuth. OpenID Connect pojęcia związane z *token Identyfikatora*, który jest token zabezpieczający, który umożliwia klientowi zweryfikowania tożsamości danego użytkownika. Token Identyfikatora pobiera również podstawowych informacji o profilu użytkownika. Ponieważ OpenID Connect rozszerza protokół OAuth 2.0, aplikacje mogą bezpiecznie uzyskiwać *tokenów dostępu*, które mogą służyć do dostępu do zasobów, które są zabezpieczone przez [serwera autoryzacji](active-directory-v2-protocols.md#the-basics). Punktu końcowego v2.0 umożliwia także aplikacji innych firm, które są zarejestrowane w usłudze Azure AD do wystawiania tokenów dostępu do zabezpieczonych zasobów, takich jak interfejsów API sieci Web. Aby uzyskać więcej informacji o tym, jak skonfigurować aplikację do wystawiania tokenów dostępu, zobacz [jak zarejestrować aplikację z punktem końcowym v2.0](active-directory-v2-app-registration.md). Zaleca się, że używasz OpenID Connect, jeśli tworzysz [aplikacji sieci web](active-directory-v2-flows.md#web-apps) który jest hostowany na serwerze i dostępne za pośrednictwem przeglądarki.

## <a name="protocol-diagram-sign-in"></a>Diagram protokołu: logowania
Najbardziej podstawowa przepływu logowania ma kroki opisane w następnej diagramu. Opisano każdy krok szczegółowo w tym artykule.

![Protokołu OpenID Connect: logowania](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Pobrać dokumentu metadanych OpenID Connect
OpenID Connect zawiera opis dokument metadanych zawiera większość informacji wymaganych dla aplikacji wykonać logowania. W tym informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. Dla punktu końcowego v2.0 jest OpenID Connect dokument metadanych, które powinny być używane:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP] 
> Wypróbuj! Kliknij przycisk [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) wyświetlić `common` konfiguracji dzierżaw. 
>

`{tenant}` Można wykonać jedną z czterech wartości:

| Wartość | Opis |
| --- | --- |
| `common` |Użytkownicy z osobistego konta Microsoft i konto służbowe z usługi Azure Active Directory (Azure AD) można zalogować się do aplikacji. |
| `organizations` |Tylko użytkownicy z pracy lub kont służbowych z usługi Azure AD można zalogować się do aplikacji. |
| `consumers` |Tylko użytkownicy z osobistego konta Microsoft można zalogować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` |Tylko użytkownicy przy użyciu konta służbowego z określonej usługi Azure AD dzierżawy może zalogować się do aplikacji. Można użyć nazwy domeny przyjazną dzierżawy usługi Azure AD lub identyfikator GUID dzierżawy. |

Metadane są proste dokumentu JavaScript Object Notation (JSON). Zobacz poniższy fragment kodu, na przykład. Fragment treści są szczegółowo opisane [OpenID Connect specyfikacji](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Zazwyczaj należy użyć ten dokument metadanych, aby skonfigurować OpenID Connect biblioteki lub zestawu SDK; biblioteki użyje metadanych, aby wykonać swoją pracę. Jednak jeśli nie używasz biblioteki OpenID Connect wstępnej kompilacji, można wykonać kroki opisane w dalszej części tego artykułu, aby wykonać logowania w aplikacji sieci web przy użyciu punktu końcowego v2.0.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania
Gdy aplikacja sieci web musi uwierzytelnić użytkownika, można kierować użytkownikowi `/authorize` punktu końcowego. To żądanie jest podobny do pierwszego etap [przepływu kodu autoryzacji protokołu OAuth 2.0](active-directory-v2-protocols-oauth-code.md), z tych istotne różnice:

* Żądanie musi zawierać `openid` zakresu w `scope` parametru.
* `response_type` Musi zawierać parametr `id_token`.
* Żądanie musi zawierać `nonce` parametru.

> [!IMPORTANT]
> Aby pomyślnie żądania tokenu identyfikator rejestracji aplikacji w [portal rejestracji](https://apps.dev.microsoft.com) musi mieć **[niejawne Przyznaj](active-directory-v2-protocols-implicit.md)** włączona dla klienta sieci Web.  Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: "podana wartość parametru wejściowego 'response_type' nie jest dozwolona dla tego klienta. Oczekiwana wartość to "code" "

Na przykład:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Kliknij poniższe łącze, aby wykonać tego żądania. Po zalogowaniu w przeglądarce zostanie przekierowany do https://localhost/myapp/, z tokenem identyfikator na pasku adresu. Należy pamiętać, że używa tego żądania `response_mode=fragment` (tylko w celach demonstracyjnych). Firma Microsoft zaleca użycie `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametr | Warunek | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane |Można użyć `{tenant}` wartość w polu Ścieżka żądania w celu kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości `common`, `organizations`, `consumers`i dzierżawców identyfikatorów. Aby uzyskać więcej informacji, zobacz [protokołu podstawy](active-directory-v2-protocols.md#endpoints). |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| response_type |Wymagane |Musi zawierać `id_token` OpenID Connect logowaniu. Może również zawierać inne `response_types` wartości, takich jak `code`. |
| redirect_uri |Zalecane |Identyfikator URI przekierowania w aplikacji, w którym można wysłanych i odebranych przez aplikację odpowiedzi uwierzytelniania. Go musi dokładnie odpowiadać jeden przekierowania URI zarejestrowany w portalu, z wyjątkiem tego, że musi być zakodowane w adresie URL. |
| scope |Wymagane |Rozdzieloną spacjami listę zakresów. Dla protokołu OpenID Connect, musi zawierać zakres `openid`, co przekłada się uprawnienie "Logowanie się w" w zgody interfejsu użytkownika. Mogą również obejmować innych zakresach, w tym żądaniu żądanych zgody. |
| Identyfikator jednorazowy |Wymagane |Wartość zawarte w żądaniu wygenerowany przez aplikację, która zostanie uwzględniona w wynikowej wartości żądaniu jako oświadczenia. Aplikację można sprawdzić tę wartość, aby ograniczyć ataków powtórzeń tokenów. Wartość jest zazwyczaj losowego, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. |
| response_mode |Zalecane |Określa metodę, które mają być używane do odesłania wynikowy kod autoryzacji do aplikacji. Możliwe wartości to `form_post` i `fragment`. Dla aplikacji sieci web, zaleca się używanie `response_mode=form_post`, aby zapewnić najbardziej bezpieczny transfer tokenów do aplikacji. |
| state |Zalecane |Wartość zawarte w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Można go ciągiem żadnej zawartości, który ma. Losowo generowany unikatową wartość jest zazwyczaj używane do [zapobiec fałszerstwie żądania międzywitrynowego](http://tools.ietf.org/html/rfc6749#section-10.12). Stan jest również używany do kodowania informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, który użytkownik był na. |
| wiersz |Optional (Opcjonalność) |Wskazuje typ interakcji z użytkownikiem, który jest wymagany. W tej chwili jedynymi prawidłowymi wartościami są `login`, `none`, i `consent`. `prompt=login` Oświadczeń wymusza użytkownika o wprowadzenie poświadczeń na żądanie i Negacja rejestracji jednokrotnej. `prompt=none` Oświadczenia jest przeciwieństwem. Tego oświadczenia gwarantuje, że użytkownik nie zobaczy jakiejkolwiek monitu interakcyjnego. Jeśli żądanie nie można ukończyć w trybie dyskretnym za pośrednictwem rejestracji jednokrotnej, punktu końcowego v2.0 zwraca błąd. `prompt=consent` Oświadczeń wyzwala okno dialogowe zgoda OAuth, gdy użytkownik się zaloguje. Okno dialogowe z monitem o uprawnienia do aplikacji. |
| login_hint |Optional (Opcjonalność) |Aby wstępnie wypełnić pola Nazwa użytkownika i adres e-mail adres strony logowania dla użytkownika, jeśli znasz nazwę użytkownika wcześniejsze, można użyć tego parametru. Często aplikacje tego parametru należy użyć podczas ponownego uwierzytelniania po już wyodrębniania nazwy użytkownika z wcześniejszych logowanie przy użyciu `preferred_username` oświadczeń. |
| domain_hint |Optional (Opcjonalność) |Ta wartość może być `consumers` lub `organizations`. Jeśli uwzględniona, pominie proces odnajdywania pocztą e-mail, które użytkownik przechodzi przez na v2.0 strony logowania, wymaga nieco więcej udoskonalone środowisko użytkownika. Często aplikacje tego parametru należy użyć podczas ponownego uwierzytelniania wyodrębniając `tid` oświadczeń z Identyfikatora tokenu. Jeśli `tid` oświadczeń, wartość jest `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft konsumenta dzierżawcy), użyj `domain_hint=consumers`. W przeciwnym razie użyj `domain_hint=organizations`. |

W tym momencie użytkownik jest monitowany o wprowadzenie poświadczeń i wykonania uwierzytelnienia. Punktu końcowego v2.0 sprawdza, czy użytkownik zgodził się na uprawnienia wskazanych w `scope` parametr zapytania. Jeśli użytkownik zgodził się nie do dowolnego z tych uprawnień, punktu końcowego v2.0 monituje użytkownika o zgodę na wymagane uprawnienia. Możesz przeczytać dodatkowe informacje [uprawnień, zgody i aplikacje wielodostępne](active-directory-v2-scopes.md).

Po uwierzytelnia użytkownika, a przyznaje zgody, punktu końcowego v2.0 zwraca odpowiedź do aplikacji na wskazany identyfikator URI przekierowania za pomocą metody określonej w `response_mode` parametru.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie, korzystając z `response_mode=form_post` wygląda podobnie do następującej:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| żądaniu |Token ID żądanej aplikacji. Można użyć `id_token` parametr do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. Aby uzyskać więcej szczegółów o identyfikatorze tokeny i ich zawartość, zobacz [punktu końcowego v2.0 tokeny odwołanie](active-directory-v2-tokens.md). |
| state |Jeśli `state` parametru jest zawarte w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikacja powinna Sprawdź, czy wartości stan żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może obsłużyć ich. Odpowiedzi na błąd wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów występujących i reagowanie na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji
W poniższej tabeli opisano kody błędów, które mogą być zwracane w `error` parametru odpowiedzi o błędzie:

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, takie jak brak wymaganego parametru. |Usuń i ponownie prześlij żądanie. Jest to błąd programistyczny, zwykle przechwycone podczas testowania początkowej. |
| unauthorized_client |Aplikacja kliencka nie może zażądać kod autoryzacji. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikację można wyświetlony monit z instrukcjami instalowania aplikacji i dodaj go do usługi Azure AD. |
| access_denied |Właściciel zasobu odmowa zgody. |Aplikacja kliencka powiadamiać użytkownika, którego nie można kontynuować, chyba że użytkownik zgadza. |
| unsupported_response_type |Serwer autoryzacji w żądaniu nie obsługuje typ odpowiedzi. |Usuń i ponownie prześlij żądanie. Jest to błąd programistyczny, zwykle przechwycone podczas testowania początkowej. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy może wynikać z tymczasowego warunków. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźnione z powodu tymczasowy błąd. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądania. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi jest opóźniony ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, nie można znaleźć usługi Azure AD, lub nie jest poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może być monitowany instrukcje dotyczące instalowania aplikacji i dodanie go do usługi Azure AD. |

## <a name="validate-the-id-token"></a>Zweryfikuj token Identyfikatora
Odbieranie token identyfikator nie jest wystarczające do uwierzytelnienia użytkownika. Musisz również zweryfikować podpisu tokenu identyfikator i sprawdzić oświadczenia w tokenie na wymagania dotyczące Twojej aplikacji. Korzysta z punktu końcowego v2.0 [tokenów sieci Web JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Można sprawdzić poprawności Identyfikatora tokenu w kodu klienta, ale jest typowym rozwiązaniem do wysyłania Identyfikatora token do serwera zaplecza i sprawdzania poprawności. Po upewnieniu podpisu tokenu Identyfikatora, należy sprawdzić kilka oświadczeń. Aby uzyskać więcej informacji, tym więcej o [sprawdzanie poprawności tokenów](active-directory-v2-tokens.md#validating-tokens) i [ważnych informacji na temat podpisywania Przerzucanie klucza](active-directory-v2-tokens.md#validating-tokens), zobacz [v2.0 tokeny odwołanie](active-directory-v2-tokens.md). Firma Microsoft zaleca, aby przeanalizować i sprawdzania poprawności tokenów przy użyciu biblioteki. Istnieje co najmniej jeden z tych bibliotek dostępne dla większości języków i platform.
<!--TODO: Improve the information on this-->

Można również sprawdzić dodatkowe roszczenia, w zależności od danego scenariusza. Niektóre typowe operacje sprawdzania poprawności obejmują:

* Upewnij się, że użytkownik lub organizacja po zarejestrowaniu aplikacji.
* Upewnij się, że użytkownik ma wymagane uprawnienia lub autoryzacji.
* Upewnij się, że niektóre siły uwierzytelniania wystąpił, takich jak uwierzytelnianie wieloskładnikowe.

Aby uzyskać więcej informacji na temat oświadczenia w tokenie identyfikator, zobacz [punktu końcowego v2.0 tokeny odwołanie](active-directory-v2-tokens.md).

Po całkowicie poprawności tokenu identyfikator można rozpocząć sesji z użytkownikiem. Użyj oświadczenia w tokenie identyfikator, aby uzyskać informacje o użytkowniku w aplikacji. Te informacje służy do wyświetlania, rekordy, autoryzacje i tak dalej.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Jeśli chcesz się wylogować użytkownika z aplikacji, nie jest wystarczające, aby wyczyścić pliki cookie aplikacji lub w przeciwnym razie kończenie sesji użytkownika. Należy również przekierować użytkownika do punktu końcowego v2.0, aby się wylogować. Jeśli nie zrobisz, użytkownik ponownie uwierzytelnia do aplikacji bez konieczności wprowadzania poświadczeń, ponieważ mają one nieprawidłowy jednej sesji logowania z punktem końcowym v2.0.

Można przekierować użytkownika do `end_session_endpoint` wymienione w dokumencie metadanych OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Warunek | Opis |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Zalecane | Adres URL, który jest przekierowywany użytkownik po wylogowaniu się pomyślnie. Jeśli parametr nie jest dołączony, użytkownik jest wyświetlany ogólny komunikat, który jest generowany przez punktu końcowego v2.0. Ten adres URL musi odpowiadać nazwie przekierowania, który zarejestrowane identyfikatory URI aplikacji w portalu rejestracji aplikacji.  |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne
Jeśli przekierowanie użytkownika do `end_session_endpoint`, punktu końcowego v2.0 czyści sesji użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają kont Microsoft do uwierzytelniania. Aby włączyć te aplikacje do podpisania użytkownika wychodzących jednocześnie v2.0 punktu końcowego wysyła żądanie HTTP GET do zarejestrowaną `LogoutUrl` wszystkich aplikacji, które użytkownik jest aktualnie zalogowany. Aplikacje należy odpowiedzieć na to żądanie przez wyczyszczenie wszelkich sesji, który identyfikuje użytkownika i zwracanie `200` odpowiedzi.  Jeśli chcesz limit obsługi funkcji logowania jednokrotnego do aplikacji, musisz zaimplementować takie `LogoutUrl` w kodzie aplikacji.  Można ustawić `LogoutUrl` z portalu rejestracji aplikacji.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokołu: nabycie token dostępu
Wiele aplikacji sieci web należy nie tylko zalogować użytkownika w, ale również dostęp do usługi sieci web w imieniu użytkownika przy użyciu uwierzytelniania OAuth. W tym scenariuszu łączy OpenID Connect do uwierzytelniania użytkowników jednocześnie uzyskanie kodu autoryzacji, która umożliwia pobieranie tokenów dostępu, jeśli używasz przepływu kodu autoryzacji OAuth.

Pełnego przepływu logowania i tokenu nabycia OpenID Connect wygląda podobnie do następnego diagramu. Opisano każdy krok szczegółowo w następnej sekcji tego artykułu.

![Protokołu OpenID Connect: Token nabycia](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Pobieranie tokenów dostępu
Uzyskanie tokenów dostępu, należy zmodyfikować żądanie logowania:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Kliknij poniższe łącze, aby wykonać tego żądania. Po zalogowaniu w przeglądarce jest przekierowywany do https://localhost/myapp/, ID token i kod na pasku adresu. Należy pamiętać, że używa tego żądania `response_mode=fragment` (tylko w celach demonstracyjnych). Firma Microsoft zaleca użycie `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

W tym zakresy uprawnień w żądaniu i za pomocą `response_type=id_token code`, punktu końcowego v2.0 gwarantuje, że użytkownik zgodził się na uprawnienia wskazanych w `scope` parametr zapytania. Zwraca kod autoryzacji do aplikacji z programem exchange dla tokenu dostępu.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie z za pomocą `response_mode=form_post` wygląda podobnie do następującej:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| żądaniu |Token ID żądanej aplikacji. Token identyfikator służy do weryfikacji tożsamości użytkownika i rozpocząć sesję użytkownika. Można znaleźć więcej szczegółów o identyfikatorze tokeny i ich zawartość w [punktu końcowego v2.0 tokeny odwołanie](active-directory-v2-tokens.md). |
| kod |Kod autoryzacji żądanej aplikacji. Aplikację można użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kod autoryzacji jest bardzo krótkim okresie. Zazwyczaj autoryzacji kod wygasa po upływie około 10 minut. |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna być widoczna w odpowiedzi. Aplikacja powinna Sprawdź, czy wartości stan żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może je odpowiednią obsługę. Odpowiedzi na błąd wygląda następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów występujących i reagowanie na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu uwierzytelniania. |

Opis możliwych kodów błędów i zalecane klienta odpowiedzi, zobacz [kody błędów dla błędów punktu końcowego autoryzacji](#error-codes-for-authorization-endpoint-errors).

Jeśli masz kod autoryzacji i tokena identyfikator możesz zalogować użytkownika i uzyskać tokeny dostępu w ich imieniu. Logowania użytkownika, musisz zweryfikować token Identyfikatora [dokładnie zgodnie z opisem](#validate-the-id-token). Aby uzyskać tokenów dostępu, wykonaj czynności opisane w naszym [dokumentacji protokołu OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
