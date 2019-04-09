---
title: Zrozumieć przepływ kodu uwierzytelniania OpenID Connect w usłudze Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać wiadomości HTTP do autoryzacji dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie za pomocą usługi Azure Active Directory i OpenID Connect.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/4/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06639f943542e322e79e137e31be7b8954566a0f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261993"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OpenID Connect i Azure Active Directory

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) to warstwa proste tożsamości korzystających z protokołu OAuth 2.0. OAuth 2.0 definiuje mechanizmy, aby uzyskiwał i wykorzystywał [ **tokeny dostępu** ](access-tokens.md) dostęp do chronionych zasobów, ale ich nie definiują standardowych metod w celu zapewnienia informacji o tożsamości. OpenID Connect uwierzytelniania są implementowane jako rozszerzenie proces autoryzacji OAuth 2.0. Zawiera informacje o użytkowniku w formie [ `id_token` ](id-tokens.md) ulec weryfikuje tożsamość użytkownika, a także podstawowych informacji o profilu użytkownika.

OpenID Connect jest nasze zalecenie, jeśli tworzysz aplikację internetową, która jest przechowywana na serwerze i dostępne za pośrednictwem przeglądarki.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania w przypadku protokołu OpenID Connect

Najbardziej podstawowa przepływ logowania zawiera następujące czynności — każdego z nich jest opisany szczegółowo poniżej.

![OpenId Connect przepływ uwierzytelniania](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadanych protokołu OpenID Connect

OpenID Connect zawiera opis dokumentu metadanych, który zawiera większość informacji wymaganych dla aplikacji do wykonywania logowania. Obejmuje to informacje, takie jak adresy URL do użycia i lokalizację usługi publiczne klucze podpisywania. Dokument metadanych OpenID Connect znajduje się w temacie:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadane są proste dokumentu JavaScript Object Notation (JSON). Zobacz poniższy fragment kodu, na przykład. Zawartość w tym fragmencie kodu są szczegółowo opisane [specyfikacją z OpenID Connect](https://openid.net). Należy pamiętać, podając identyfikator dzierżawy zamiast `common` w miejscu {dzierżawa} powyżej spowoduje URI specyficznym dla dzierżawy w zwrócony obiekt JSON.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia [Mapowanie oświadczeń](active-directory-claims-mapping.md) funkcji, należy dołączyć `appid` parametr zawierający identyfikator aplikacji w celu uzyskania zapytania `jwks_uri` wskazujący aplikacji podpisywania klucza informacje. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` zawiera `jwks_uri` z `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Jeśli aplikacja sieci web musi uwierzytelnić użytkownika, należy go skierować użytkownika `/authorize` punktu końcowego. To żądanie jest podobna do pierwszej gałęzi [przepływ kodu autoryzacji OAuth 2.0](v1-protocols-oauth-code.md), za pomocą kilku ważne różnice:

* Żądanie musi zawierać w zakresie `openid` w `scope` parametru.
* `response_type` Parametr musi zawierać `id_token`.
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
| dzierżawa |wymagane |`{tenant}` Wartość w polu Ścieżka żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone są wartości identyfikatorów dzierżawy, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` tokenów niezależne od dzierżawcy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji podczas rejestrowania za pomocą usługi Azure AD. To można znaleźć w witrynie Azure portal. Kliknij przycisk **usługi Azure Active Directory**, kliknij przycisk **rejestracje aplikacji**, wybierz aplikację i zlokalizuj identyfikator aplikacji na stronie aplikacji. |
| response_type |wymagane |Musi zawierać `id_token` dla logowania OpenID Connect. Może to również obejmować inne response_types, takich jak `code` lub `token`. |
| scope | Zalecane | Specyfikacja protokołu OpenID Connect wymaga zakres `openid`, co przekłada się na uprawnienia "Logowanie się w" w zgody interfejsu użytkownika. To i inne zakresy OIDC są ignorowane w punkcie końcowym w wersji 1.0, ale nadal jest najlepszym rozwiązaniem dla klientów zgodnych ze standardami. |
| Identyfikator jednorazowy |wymagane |Wartości zawarte w żądaniu wygenerowane przez aplikację, która znajduje się w wynikowym `id_token` jako oświadczenia. Aplikacja może zweryfikować tę wartość, aby uniknąć powtarzania tokenu ataków. Wartość jest zazwyczaj losowy unikatowy ciąg lub identyfikator GUID, który może służyć do identyfikowania pochodzenia żądania. |
| redirect_uri | Zalecane |Redirect_uri aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Dokładnie musi odpowiadać jednej z redirect_uris, zarejestrowanych w portalu, z wyjątkiem musi być zakodowane w adresie url. Jeśli brakuje, agent użytkownika będą wysyłane do jednej zarejestrowanej aplikacji, w losowo wybranym momencie identyfikatory URI przekierowania. Maksymalna długość to 255 bajtów |
| response_mode |opcjonalne |Określa metodę, które mają być używane do wysyłania wynikowy authorization_code wróć do aplikacji. Obsługiwane wartości to `form_post` dla *HTTP post formularza* i `fragment` dla *fragmentu adresu URL*. Dla aplikacji sieci web, zaleca się używanie `response_mode=form_post` zapewnienie najbardziej bezpieczny transfer tokenów do aplikacji. Ustawieniem domyślnym dla dowolnego przepływu, w tym id_token jest `fragment`.|
| state |Zalecane |Wartość uwzględnione w żądaniu, który jest zwracany w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, który chcesz. Losowo generowany unikatową wartość jest zwykle używany podczas [zapobieganie atakom na fałszerstwo żądania międzywitrynowego](https://tools.ietf.org/html/rfc6749#section-10.12). Stan również jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| wiersz |opcjonalne |Wskazuje typ interakcji z użytkownikiem, który jest wymagany. Obecnie jedyne prawidłowe wartości to "login", "none" i "". `prompt=login` Wymusza na użytkowniku, aby wprowadzić swoje poświadczenia w tym żądaniu Negacja logowania jednokrotnego. `prompt=none` jest przeciwieństwem — zapewnia, że użytkownik nie zobaczy wszystkie interaktywne monity w inny sposób. Jeśli żądanie nie można ukończyć w trybie dyskretnym za pomocą logowania jednokrotnego, punkt końcowy zwraca błąd. `prompt=consent` Wyzwalacze uwierzytelniania OAuth zgoda okna dialogowego po użytkownik się zaloguje, monitem o nadanie uprawnień do aplikacji. |
| login_hint |opcjonalne |Można wstępnie wypełnić pola Adres e-mail/nazwy użytkownika strony logowania dla użytkownika, jeśli znasz swoją nazwę użytkownika, wcześniej. Aplikacje często tego parametru należy użyć podczas ponownego uwierzytelniania, mających już wyodrębnione nazwy użytkownika z poprzedniego logowania za pomocą `preferred_username` oświadczenia. |

W tym momencie użytkownik jest monitowany wprowadzić swoje poświadczenia i wykonania uwierzytelnienia.

### <a name="sample-response"></a>Przykładowa odpowiedź

Przykładowa odpowiedź, po użytkownik uwierzytelniony, może wyglądać następująco:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |`id_token` Żądany przez aplikację. Możesz użyć `id_token` do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. |
| state |Wartość uwzględnione w żądaniu, która jest także zwracany w odpowiedzi tokenu. Losowo generowany unikatową wartość jest zwykle używany podczas [zapobieganie atakom na fałszerstwo żądania międzywitrynowego](https://tools.ietf.org/html/rfc6749#section-10.12). Stan również jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |

### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , dzięki czemu aplikacja może je odpowiednio obsługiwać:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji

W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametru odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, np. Brak wymaganego parametru. |Usuń i ponownie prześlij żądanie. Jest to błąd programowania i zwykle jest przechwytywane w początkowej fazie testowania. |
| unauthorized_client |Aplikacja kliencka nie ma zezwolenia na żądanie kod autoryzacji. |Dzieje się tak zazwyczaj, gdy aplikacja kliencka nie jest zarejestrowany w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD przez użytkownika. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |
| access_denied |Właściciel zasobu odmowa zgody |Aplikacja kliencka może powiadomić użytkownika, którego nie można kontynuować, chyba że użytkownik wyrazi na to zgody. |
| unsupported_response_type |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Usuń i ponownie prześlij żądanie. Jest to błąd programowania i zwykle jest przechwytywane w początkowej fazie testowania. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy mogą być wynikiem tymczasowe warunki. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy błąd. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że odpowiedzi przez punkt końcowy jest opóźnione ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może okazać się lub nie jest poprawnie skonfigurowana. |Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w ramach dzierżawy. Aplikacja może monitować użytkownika przy użyciu instrukcji dotyczących instalowania aplikacji i dodanie go do usługi Azure AD. |

## <a name="validate-the-idtoken"></a>Sprawdź poprawność id_token

Otrzymywania `id_token` nie wystarcza do uwierzytelniania użytkownika, należy zweryfikować podpisu i weryfikować oświadczenia w `id_token` na wymagania dotyczące Twojej aplikacji. Punkt końcowy usługi Azure AD używa tokenów sieci Web JSON (tokenów Jwt) i kryptografii klucza publicznego do podpisywania tokenów i sprawdź, czy są prawidłowe.

Można wybrać sprawdzić poprawność `id_token` w kliencie kod, ale powszechną praktyką jest wysłanie `id_token` do serwera wewnętrznej bazy danych i zweryfikować istnieje. 

Możesz również sprawdzić dodatkowe oświadczenia w zależności od scenariusza. Niektórych typowych operacji sprawdzania poprawności obejmują:

* Zapewnienie użytkownika/organizacja po zarejestrowaniu w aplikacji.
* Zapewnienie użytkownik ma odpowiednie zezwolenia/uprawnień za pomocą `wids` lub `roles` oświadczeń. 
* Zapewnienie siły uwierzytelniania wystąpił, takie jak uwierzytelnianie wieloskładnikowe.

Po sprawdzeniu poprawności `id_token`, można rozpocząć sesji z użytkownikiem i korzystanie z oświadczeń w `id_token` do uzyskania informacji o użytkowniku w swojej aplikacji. Te informacje mogą służyć do wyświetlania rekordów, personalizacji itp. Aby uzyskać więcej informacji na temat `id_tokens` i oświadczenia, przeczytaj [AAD id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Jeśli chcesz zalogować się użytkownika z aplikacji, nie jest wystarczające, aby wyczyścić pliki cookie lub w przeciwnym razie zakończenia aplikacji sesji z użytkownikiem. Należy również przekierować użytkownika do `end_session_endpoint` do wylogowania. W przypadku awarii to zrobić, użytkownik będzie mógł ponownie uwierzytelniać do aplikacji bez konieczności wprowadzania ich poświadczenia ponownie, ponieważ mają one nieprawidłowy pojedynczego logowania jednokrotnego sesji z punktem końcowym usługi Azure AD.

Możesz po prostu przekierować użytkownika do `end_session_endpoint` wymienione w tym dokumencie metadanych OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Opis |
| --- | --- | --- |
| post_logout_redirect_uri |Zalecane |Adres URL, który użytkownik powinno zostać przekierowane do po wylogowaniu się pomyślnie. Jeśli nie zostanie uwzględniony, użytkownik jest wyświetlany komunikat ogólny. |

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

Gdy przekierowania użytkownika `end_session_endpoint`, usługi Azure AD czyści sesji użytkownika w przeglądarce. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają usługi Azure AD do uwierzytelniania. Aby włączyć te aplikacje jednocześnie wylogować użytkownika, usługa Azure AD wysyła żądanie HTTP GET do zarejestrowaną `LogoutUrl` wszystkich aplikacji, które użytkownik jest aktualnie zalogowany. Aplikacje należy odpowiedzieć na to żądanie, czyszcząc żadnych sesji, która identyfikuje użytkownika i zwracanie `200` odpowiedzi. Jeśli użytkownik chce się obsługi logowania jednokrotnego w aplikacji, musisz zaimplementować takie `LogoutUrl` w kodzie twojej aplikacji. Możesz ustawić `LogoutUrl` w witrynie Azure portal:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługi Active Directory, klikając swoje konto w prawym górnym rogu strony.
3. W panelu nawigacyjnym po lewej stronie ekranu wybierz **usługi Azure Active Directory**, następnie wybierz **rejestracje aplikacji** i wybierz swoją aplikację.
4. Kliknij pozycję **ustawienia**, następnie **właściwości** i Znajdź **adres URL wylogowania** pola tekstowego. 

## <a name="token-acquisition"></a>Uzyskanie tokenu
Wiele aplikacji sieci web należy nie tylko Zaloguj użytkownika, ale również dostęp do usługi sieci web w imieniu użytkownika, że przy użyciu protokołu OAuth. Ten scenariusz łączy OpenID Connect do uwierzytelniania użytkowników podczas uzyskiwania jednocześnie `authorization_code` można uzyskać `access_tokens` przy użyciu [przepływ kodu autoryzacji w OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Uzyskiwanie tokenów dostępu
Uzyskanie tokenów dostępu, należy zmodyfikować żądanie logowania z powyższych:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

W tym zakresy uprawnień w żądaniu oraz przy użyciu `response_type=code+id_token`, `authorize` punktu końcowego gwarantuje, że użytkownik wyraził zgodę na uprawnienia czcionką `scope` parametr zapytania i zwraca kod autoryzacji do wymiany dla aplikacji token dostępu.

### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca Powodzenie przy użyciu `response_mode=form_post` wyglądają następująco:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Opis |
| --- | --- |
| id_token |`id_token` Żądany przez aplikację. Możesz użyć `id_token` do zweryfikowania tożsamości użytkownika i rozpocząć sesję z użytkownikiem. |
| kod |Authorization_code, który zażądał aplikacji. Aplikacja może używać kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Authorization_codes są krótkie krótkotrwałe i zazwyczaj wygasają po upływie około 10 minut. |
| state |Jeśli parametr Stan jest uwzględniony w żądaniu, tę samą wartość powinna pojawić się w odpowiedzi. Aplikację należy sprawdzić, czy wartości stanu żądania i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedzi na błąd

Odpowiedzi na błędy mogą być również wysyłane do `redirect_uri` , dzięki czemu aplikacja może je odpowiednio obsługiwać:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper Identyfikuj główne przyczyny błędu uwierzytelniania. |

Opis kodów możliwy błąd i z tego działania zalecane klienta, zobacz [kody błędów dla błędów punktu końcowego autoryzacji](#error-codes-for-authorization-endpoint-errors).

Gdy trafiła do Ciebie autoryzacji `code` i `id_token`, można zalogować użytkownika i uzyskać [tokeny dostępu](access-tokens.md) w ich imieniu. Aby utworzyć użytkownika, należy sprawdzić, `id_token` dokładnie tak jak opisano powyżej. Uzyskiwanie tokenów dostępu, można wykonać czynności opisane w sekcji "Przy użyciu kodu autoryzacji żądania tokenu dostępu" nasze [dokumentacji przepływu kodu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [tokeny dostępu](access-tokens.md).
* Dowiedz się więcej o [ `id_token` i oświadczenia](id-tokens.md).
